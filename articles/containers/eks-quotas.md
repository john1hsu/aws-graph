---
id: kt:eks-quotas
type: Limit
title: Amazon EKS quotas
aliases: [EKS quotas, EKS service quotas, EKS limits, Amazon EKS service limits, Amazon Elastic Kubernetes Service quotas]
domain: containers
summary: >-
  The per-account, per-Region limits on Amazon EKS resources (clusters, managed node groups, nodes,
  Fargate profiles, access entries, hybrid-node networks) plus the control-plane capacity ceilings set
  by Standard mode or a Provisioned Control Plane tier; many cluster-at-scale limits actually come
  from EC2, VPC, ELB and IAM quotas.
verified: 2026-11-16
status: current
broader: [kt:amazon-eks]
related: [kt:eks-auto-mode, kt:aws-fargate, kt:iam-quotas, kt:amazon-vpc, kt:elastic-load-balancing]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:ecs-quotas}
  - {predicate: contrastsWith, object: kt:fargate-quotas}
sources:
  - id: s1
    title: "Amazon Elastic Kubernetes Service endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/eks.md
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "View and manage Amazon EKS and Fargate service quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/service_limits.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Known Limits and Service Quotas (Amazon EKS Best Practices Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/best-practices/known_limits_and_service_quotas.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Learn how EKS Pod Identity grants pods access to AWS services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/pod-identities.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "Amazon EKS Provisioned Control Plane"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/eks-provisioned-control-plane.html
    type: official-docs
    accessed: 2026-11-16
created: 2026-11-16
---

# Amazon EKS quotas

Amazon EKS quotas are the limits AWS places on EKS resources in each account and Region. They appear under "Amazon Elastic Kubernetes Service (Amazon EKS)" in the Service Quotas console (service code `eks`), and the defaults AWS publishes are only starting values: your applied value can differ after an increase.[^s1][^s2] In practice an EKS cluster is bounded by three layers of limits: the EKS API's own resource quotas, the capacity of the Kubernetes control plane, and the quotas of the AWS services the cluster's nodes and pods consume.[^s3][^s5]

## EKS resource quotas

As of 2026-11 the General Reference lists the same defaults in every supported Region:[^s1]

| Quota | Default | Adjustable |
|---|---|---|
| Clusters per account | 100 | Yes |
| Managed node groups per cluster | 30 | Yes |
| Nodes per managed node group | 450 | Yes |
| Fargate profiles per cluster | 10 | Yes |
| Selectors per Fargate profile | 5 | Yes |
| Label pairs per Fargate profile selector | 5 | Yes |
| Registered clusters (EKS Connector) | 10 | Yes |
| Remote node networks / remote pod networks per cluster (Hybrid Nodes) | 15 each | Yes |
| EKS Anywhere Enterprise Subscriptions | 10 | Yes |
| Access entries per cluster | 3,000 | No |
| Control plane security groups per cluster | 4 | No |
| Public endpoint access CIDR ranges per cluster | 40 | No[^s1] |

The fixed ones are worth designing around. The access entry ceiling caps how many IAM principals you can map to a cluster individually, so large organizations usually grant access through a handful of roles rather than per-user entries.[^s1] The 40-CIDR limit on the public endpoint allow-list pushes teams with many egress ranges toward a private endpoint instead.[^s1] The user guide also notes two limits that cannot be changed through Service Quotas at all: Pod Identity associations per cluster, and the hybrid-node CIDR settings for remote node and pod networks.[^s2] The Pod Identity page puts that association limit at 5,000 per cluster, and each service account can be tied to only one IAM role.[^s4]

For pods on Fargate, the relevant quota lives in the separate AWS Fargate namespace: the Fargate On-Demand vCPU resource count defaults to 6 concurrent vCPUs per account per Region (adjustable), shared with ECS tasks. New accounts may start lower, and Fargate raises the value automatically as usage grows; launch-rate throttling applies on top.[^s1][^s2]

## Control plane capacity

The Kubernetes API server and etcd have their own ceilings. In the default Standard mode the control plane scales automatically but supports a cluster database of up to 8 GB.[^s5] Provisioned Control Plane lets you pay for a fixed tier instead; for Kubernetes 1.34 and later, AWS lists API request concurrency from 2,000 seats (XL) to 16,000 (8XL), pod scheduling rates from 167 to 400 pods per second, a 16 GB database and a 99.99% SLA in 1-minute intervals, versus 99.95% in 5-minute intervals for Standard.[^s5] AWS stresses that these are capacity bounds, not guaranteed throughput, and a cluster whose database has grown past 8 GB cannot return to Standard until usage shrinks.[^s5]

## Limits inherited from other services

At scale, EKS clusters more often run into neighbouring quotas than EKS ones. The EKS Best Practices Guide lists defaults such as 5 VPCs per Region, 5 Elastic IPs (which also caps NAT gateways), 5,000 network interfaces per Region, 1,000 targets per Application Load Balancer, 1,000 IAM roles and 100 OIDC providers per account, and a 2,048-character role trust policy, which matters when IRSA trust policies list many clusters.[^s3] It also flags EC2 API throttling on network-interface and describe calls during rapid scaling, roughly 5 requests per second on the Route 53 API (a problem for tools such as ExternalDNS), and the 28-attachment limit on Nitro instances shared by EBS volumes, network interfaces and instance store.[^s3] The guide's upstream etcd guidance of 8 GiB matches the Standard-mode database ceiling.[^s3][^s5]

## Gotchas and practice

Check quotas before you need them: the console shows each quota's applied value, AWS default and adjustability, and the CLI's `list-service-quotas` returns applied values while `list-aws-default-service-quotas` returns only defaults.[^s2] Set CloudWatch alarms on Fargate usage metrics so you hear about the vCPU ceiling before pods sit in Pending.[^s2] Because quotas are regional and default values are low for several dependent services, request increases in every Region you plan to fail over into.[^s3]

## Connections

- **Broader: Amazon EKS** (`kt:amazon-eks`), whose clusters, node groups and access configuration these quotas bound.[^s1]
- **Configured via Service Quotas** (`kt:service-quotas`), where the adjustable EKS and Fargate values are raised.[^s2]
- **Scoped to AWS Regions** (`kt:aws-regions`): every value is per account per Region.[^s1]
- **Contrasts with ECS quotas** (`kt:ecs-quotas`) and **Fargate quotas** (`kt:fargate-quotas`): the Fargate vCPU quota is shared by EKS pods and ECS tasks.[^s1][^s2]
- **Related to EKS Auto Mode** (`kt:eks-auto-mode`) and **AWS Fargate** (`kt:aws-fargate`), the managed capacity options, and to **IAM quotas** (`kt:iam-quotas`), **Amazon VPC** (`kt:amazon-vpc`) and **Elastic Load Balancing** (`kt:elastic-load-balancing`), whose limits commonly cap large clusters.[^s3]

## Sources
[^s1]: Amazon Web Services, AWS General Reference, "Amazon Elastic Kubernetes Service endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/eks.md (accessed 2026-11-16)
[^s2]: Amazon Web Services, Amazon EKS User Guide, "View and manage Amazon EKS and Fargate service quotas". https://docs.aws.amazon.com/eks/latest/userguide/service_limits.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, Amazon EKS Best Practices Guide, "Known Limits and Service Quotas". https://docs.aws.amazon.com/eks/latest/best-practices/known_limits_and_service_quotas.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, Amazon EKS User Guide, "Learn how EKS Pod Identity grants pods access to AWS services". https://docs.aws.amazon.com/eks/latest/userguide/pod-identities.html (accessed 2026-11-16)
[^s5]: Amazon Web Services, Amazon EKS User Guide, "Amazon EKS Provisioned Control Plane". https://docs.aws.amazon.com/eks/latest/userguide/eks-provisioned-control-plane.html (accessed 2026-11-16)
