---
id: kt:container-networking-on-aws
type: Concept
title: Container networking on AWS
aliases: [ECS network modes, EKS pod networking, awsvpc networking, container network modes on AWS]
domain: containers
summary: >-
  How containers on Amazon ECS and Amazon EKS get IP addresses, security groups and service-to-service
  connectivity inside an Amazon VPC: ECS task network modes (awsvpc, bridge, host), the Amazon VPC CNI
  for Kubernetes pods (secondary IPs, prefix delegation, security groups for pods) and ECS Service Connect.
verified: 2026-11-14
status: concept
broader: [kt:aws-container-services, kt:amazon-vpc]
related: [kt:amazon-ecs, kt:amazon-eks, kt:aws-fargate, kt:eks-auto-mode]
relations:
  - {predicate: dependsOn, object: kt:subnets-and-route-tables}
  - {predicate: securedBy, object: kt:security-groups-vs-network-acls}
  - {predicate: integratesWith, object: kt:elastic-load-balancing}
  - {predicate: integratesWith, object: kt:aws-cloud-map}
sources:
  - id: s1
    title: "Amazon ECS task networking options for EC2"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest//developerguide/task-networking.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "AWSVPC mode (Amazon ECS Best Practices Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/bestpracticesguide/networking-networkmode-awsvpc.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Amazon VPC CNI (Amazon EKS Best Practices Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/best-practices/vpc-cni.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Prefix Mode for Linux (Amazon EKS Best Practices Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/best-practices/prefix-mode-linux.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "Security Groups Per Pod (Amazon EKS Best Practices Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/best-practices/sgpp.html
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "Use Service Connect to connect Amazon ECS services with short names"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect.html
    type: official-docs
    accessed: 2026-11-14
created: 2026-11-14
---

# Container networking on AWS

Containers on AWS do not get a separate overlay network by default: both Amazon ECS (with the `awsvpc` mode) and Amazon EKS (with the Amazon VPC CNI) hand each task or pod a real, VPC-routable IP address from your subnets.[^s1][^s3] That makes security groups, route tables and load balancers work for containers just as they do for EC2 instances. The trade-off is that container density is bounded by ENI and IP limits per instance and by free addresses in your subnets.[^s2][^s3]

## Amazon ECS network modes

A task definition's `networkMode` picks one of several modes. `awsvpc` gives each task its own elastic network interface (ENI) and primary private IPv4 or IPv6 address, and works on Linux and Windows. `bridge` uses Docker's virtual bridge network and is the Linux default when no mode is set. `host` maps container ports straight onto the instance's ENI, and `none` has no external connectivity. Windows supports only `awsvpc` and `default` (Docker's `nat` driver).[^s1] AWS recommends `awsvpc` unless you have a specific reason otherwise. In `host` mode each container needs a fixed host port, so one instance can't run two copies of the same task definition.[^s1]

In `awsvpc` mode, containers in a task share the ENI and talk over `localhost`. ECS creates, attaches and deletes the ENI for you, and you pass up to 16 subnets and 5 security groups in the service's network configuration.[^s2] Load balancing works only with Application and Network Load Balancers, using `ip` target groups, not `instance`.[^s2]

### Gotchas

- **ENI density.** The instance's primary ENI counts against its ENI quota. A `c5.large` allows 3 ENIs by default, so it fits only 2 `awsvpc` tasks. The `awsvpcTrunking` account setting adds a managed trunk ENI and raises density on supported Linux instance types; Windows doesn't support trunking.[^s2]
- **No public IPs on EC2.** Task ENIs on EC2 container instances get no public IP. Outbound internet access goes through a private subnet and a NAT gateway, or through an internet gateway or egress-only internet gateway for dual-stack tasks with IPv6.[^s2]
- **IPv6-only tasks** need ECS-optimized Linux AMIs, container agent 1.99.1 or later, and dualstack Amazon ECR endpoints. ECS Exec doesn't work there, and you need DNS64/NAT64 to reach IPv4-only endpoints.[^s1]

## Amazon EKS pod networking with the VPC CNI

The Amazon VPC CNI has two parts. A CNI binary is invoked by the kubelet as pods start and stop. The `aws-node` DaemonSet runs **ipamd**, which attaches ENIs and keeps a warm pool of IPs.[^s3] By default each pod gets one secondary private IP from an attached ENI, and ipamd attaches another ENI as the pool runs low, up to the instance's ENI limit. Warm-pool behaviour is tuned with `WARM_ENI_TARGET`, `WARM_IP_TARGET` and `MINIMUM_IP_TARGET`; warm addresses still consume your VPC CIDR.[^s3] Default max pods per node is (ENIs × (IPs per ENI − 1)) + 2.[^s3]

**Prefix delegation** (`ENABLE_PREFIX_DELEGATION=true`, VPC CNI 1.9.0+) assigns /28 IPv4 prefixes of 16 addresses to each ENI slot instead of single IPs. That raises pod density without more ENIs, and attaching a prefix typically takes under a second versus up to 10 seconds for a new ENI.[^s4] The catches: each prefix needs a contiguous /28, so fragmented subnets fail with `InsufficientCidrBlocks`; a node can strand up to 15 unused IPs; and you must raise kubelet `max-pods` yourself. Dedicated pod subnets or VPC subnet CIDR reservations avoid the fragmentation. IPv6 clusters use prefix mode by default, with a /80 per slot.[^s4]

**Security groups for pods** (`ENABLE_POD_ENI=true`) let the VPC Resource Controller attach a trunk ENI and per-pod branch ENIs, so a `SecurityGroupPolicy` can give individual pods their own security groups. Without it, every pod shares the node's primary-ENI security groups.[^s5] It needs Nitro-based Linux instances. In the default strict mode, source NAT is off, so pods need private subnets with NAT for internet access.[^s5] Use it to reuse existing security-group rules for AWS resources such as RDS; use Kubernetes network policies for pod-to-pod (east-west) rules.[^s5] In EKS Auto Mode, AWS manages these CNI settings for you.[^s3]

## Service-to-service traffic on ECS

**ECS Service Connect** adds a proxy sidecar to each task and registers services in an AWS Cloud Map namespace. Clients then call short names such as `http://blog:80`, and the proxy does round-robin load balancing with outlier detection.[^s6] A namespace is Regional, can span clusters, and can be shared across accounts through AWS RAM. Services outside the namespace, or outside ECS, can't resolve its names.[^s6] Service Connect and its Cloud Map use carry no extra charge, but the proxy shares your task's vCPU and memory. TLS through AWS Private CA is billed per CA and per certificate, and certificates rotate every five days.[^s6]

## Connections

This concept sits under `kt:aws-container-services` and `kt:amazon-vpc`: every mode described here places tasks or pods in your VPC's subnets and routes (`dependsOn` subnets and route tables).[^s2][^s3] Security groups secure tasks and pods at the ENI level, per task on ECS and per pod with branch ENIs on EKS (`securedBy`).[^s2][^s5] Elastic Load Balancing reaches `awsvpc` tasks through `ip` targets (`integratesWith`).[^s2] Service Connect builds on AWS Cloud Map namespaces (`integratesWith`).[^s6] It relates to `kt:amazon-ecs`, `kt:amazon-eks`, `kt:aws-fargate` and `kt:eks-auto-mode`, which each configure part of this networking stack.[^s1][^s3]

## Sources
[^s1]: Amazon Web Services, "Amazon ECS task networking options for EC2". https://docs.aws.amazon.com/AmazonECS/latest//developerguide/task-networking.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "AWSVPC mode", Amazon ECS Best Practices Guide. https://docs.aws.amazon.com/AmazonECS/latest/bestpracticesguide/networking-networkmode-awsvpc.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, "Amazon VPC CNI", Amazon EKS Best Practices Guide. https://docs.aws.amazon.com/eks/latest/best-practices/vpc-cni.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, "Prefix Mode for Linux", Amazon EKS Best Practices Guide. https://docs.aws.amazon.com/eks/latest/best-practices/prefix-mode-linux.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, "Security Groups Per Pod", Amazon EKS Best Practices Guide. https://docs.aws.amazon.com/eks/latest/best-practices/sgpp.html (accessed 2026-11-14)
[^s6]: Amazon Web Services, "Use Service Connect to connect Amazon ECS services with short names". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect.html (accessed 2026-11-14)
