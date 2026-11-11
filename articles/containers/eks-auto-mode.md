---
id: kt:eks-auto-mode
type: Feature
title: Amazon EKS Auto Mode
aliases: [EKS Auto Mode, EKS Auto]
domain: containers
summary: >-
  An Amazon EKS operating mode in which AWS also runs the cluster's data plane: it launches,
  scales, patches and retires EC2 worker nodes through Karpenter-style NodePools and NodeClasses,
  and supplies built-in networking, load balancing and storage, for a per-instance management fee.
verified: 2026-11-11
status: current
broader: [kt:amazon-eks]
related: [kt:spot-instances, kt:aws-govcloud-us, kt:karpenter]
relations:
  - {predicate: runsOn, object: kt:amazon-ec2}
  - {predicate: alternativeTo, object: kt:aws-fargate}
  - {predicate: integratesWith, object: kt:elastic-load-balancing}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: dependsOn, object: kt:amazon-vpc}
sources:
  - id: s1
    title: "Create a Node Pool for EKS Auto Mode"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/create-node-pool.md
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Create a Node Class for Amazon EKS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/create-node-class.md
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Learn about VPC Networking and Load Balancing in EKS Auto Mode"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/us_en/eks/latest/userguide/auto-networking.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Cost optimization in EKS Auto Mode"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/auto-cost-control.html
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Amazon EKS pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/eks/pricing/
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "Announcing Amazon EKS Auto Mode"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2024/12/amazon-eks-auto-mode/
    type: official-blog
    accessed: 2026-11-11
  - id: s7
    title: "Amazon EKS Auto Mode now available in AWS GovCloud (US-East) and (US-West)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/10/amazon-eks-auto-mode-aws-govcloud-us-east-west
    type: official-blog
    accessed: 2026-11-11
  - id: s8
    title: "Amazon EKS Auto Mode reduces GPU management fees by up to 60%"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/07/amazon-eks-auto-mode-gpu-price
    type: official-blog
    accessed: 2026-11-11
created: 2026-11-11
---

# Amazon EKS Auto Mode

Amazon EKS Auto Mode is a cluster setting, announced at re:Invent on December 1, 2024, that hands the worker-node layer of an EKS cluster to AWS. AWS selects EC2 instances for pending workloads, scales capacity with demand and applies operating-system patches, on clusters running Kubernetes 1.29 or later.[^s6] There are no upfront commitments: you pay a management charge for the instances Auto Mode provisions on top of their normal EC2 price.[^s6][^s5]

## How it works

Capacity is described with two Kubernetes custom resources. A **NodePool** (the Karpenter `karpenter.sh/v1` API) states *what* may be launched: instance categories, CPU counts, Availability Zones, architectures (arm64 or amd64) and capacity type (`on-demand`, `spot` or `reserved`), plus CPU and memory ceilings.[^s1] Auto Mode uses its own `eks.amazonaws.com/` labels for instance attributes, not Karpenter's, and only Linux nodes are supported.[^s1] A **NodeClass** (`eks.amazonaws.com/v1`) states *how* nodes are built: the node IAM role, subnet and security-group selectors, ephemeral storage size, IOPS and throughput, an optional KMS key, SNAT and network-policy defaults, proxies, placement groups, and kubelet and sysctl overrides.[^s2]

Two built-in NodePools, `system` and `general-purpose`, can be enabled or disabled but not edited; enabling either also creates a NodeClass named `default`, so custom NodeClasses must use a different name.[^s1][^s2] The built-in pools are On-Demand only and limited to C, M and R families of generation 5 or newer, so Spot or GPU capacity needs a custom NodePool.[^s4] A custom NodeClass also needs an EKS access entry of type `EC2` carrying the `AmazonEKSAutoNodePolicy`, which EKS creates automatically only for the built-in pools.[^s2]

Networking is a managed capability that replaces the open-source VPC CNI's settings. It uses /28 prefix delegation by default, falling back to individual secondary IPs when the subnet is fragmented; a NodeClass can switch to secondary-IP mode (`ipv4PrefixSize: "32"`) to conserve addresses for pod-sparse fleets.[^s3][^s2] Load balancers come from Service annotations (NLB) or an IngressClass (ALB), target pods in IP mode by default, and each node runs CoreDNS locally as a system service.[^s3]

## Defaults, pricing and gotchas

By default (as of 2026-11), Auto Mode consolidates underutilized nodes, terminates every node after 336 hours, limits voluntary disruption to 10% of nodes at a time, and replaces nodes for drift when a new Auto Mode AMI ships, about weekly. A NodeClaim gets a 24-hour termination grace period unless one is set.[^s1] Pods are capped at 110 per node.[^s2]

Pricing (as of 2026-11): the fee depends on instance type, is billed per second with a one-minute minimum, and is independent of the EC2 purchase option, so Spot, Savings Plans and Reserved Instances discount the instance but not the fee.[^s5] AWS's US West (Oregon) example lists $0.02064 per hour for an m5a.xlarge and $0.07344 for a c6a.4xlarge.[^s5] From July 1, 2026, fees for G-series instances fell 35% and for P-series and Trainium 60%, in every Region offering Auto Mode.[^s8]

Gotchas:
- Security groups for pods and `ENIConfig` custom networking are not supported; use `podSubnetSelectorTerms` and `podSecurityGroupSelectorTerms` instead. Alternate CNIs such as Cilium or Calico are also unsupported.[^s3]
- Load balancers created by a self-managed AWS Load Balancer Controller cannot be migrated to Auto Mode.[^s3]
- Restrictive Pod Disruption Budgets, or the `karpenter.sh/do-not-disrupt` annotation, block consolidation, and NodePool `limits` are hard: pods stay Pending once a ceiling is reached.[^s4]
- Changing kubelet settings or sysctls in a NodeClass marks its nodes as drifted and replaces them; nothing is applied in place.[^s2]

## When to use it (and when not)

Choose Auto Mode when you want Kubernetes APIs without running node groups, Karpenter, the VPC CNI or the load balancer controller yourself, and you can accept AWS-owned node images.[^s6][^s3] Its cost levers are bin-packing, consolidation and Spot in custom pools; restrict instance categories so a quota shortfall doesn't push launches onto unexpected types.[^s4] Stay with self-managed or managed node groups if you depend on Windows nodes, an alternate CNI or security groups for pods.[^s1][^s3] Auto Mode reached AWS GovCloud (US-East and US-West) on October 22, 2025, with FIPS-validated cryptographic modules in its AMIs.[^s7]

## Connections

- **Broader: Amazon EKS** (`kt:amazon-eks`); Auto Mode is one of its cluster operating modes.[^s6]
- **Runs on Amazon EC2** (`kt:amazon-ec2`) and **depends on Amazon VPC** (`kt:amazon-vpc`) subnets and security groups chosen by the NodeClass.[^s2]
- **Alternative to AWS Fargate** (`kt:aws-fargate`), the other way to avoid managing EKS worker nodes.[^s6]
- **Integrates with Elastic Load Balancing** (`kt:elastic-load-balancing`) through Services and Ingresses.[^s3]
- **Secured by AWS KMS** (`kt:aws-kms`) keys that encrypt node volumes.[^s2]
- **Related to Spot Instances** (`kt:spot-instances`), **Karpenter** (`kt:karpenter`) and **AWS GovCloud (US)** (`kt:aws-govcloud-us`).[^s1][^s7]

## Sources
[^s1]: Amazon Web Services, "Create a Node Pool for EKS Auto Mode". https://docs.aws.amazon.com/eks/latest/userguide/create-node-pool.md (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Create a Node Class for Amazon EKS". https://docs.aws.amazon.com/eks/latest/userguide/create-node-class.md (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Learn about VPC Networking and Load Balancing in EKS Auto Mode". https://docs.aws.amazon.com/us_en/eks/latest/userguide/auto-networking.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Cost optimization in EKS Auto Mode". https://docs.aws.amazon.com/eks/latest/userguide/auto-cost-control.html (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Amazon EKS pricing". https://aws.amazon.com/eks/pricing/ (accessed 2026-11-11)
[^s6]: Amazon Web Services, "Announcing Amazon EKS Auto Mode". https://aws.amazon.com/about-aws/whats-new/2024/12/amazon-eks-auto-mode/ (accessed 2026-11-11)
[^s7]: Amazon Web Services, "Amazon EKS Auto Mode now available in AWS GovCloud (US-East) and (US-West)". https://aws.amazon.com/about-aws/whats-new/2025/10/amazon-eks-auto-mode-aws-govcloud-us-east-west (accessed 2026-11-11)
[^s8]: Amazon Web Services, "Amazon EKS Auto Mode reduces GPU management fees by up to 60%". https://aws.amazon.com/about-aws/whats-new/2026/07/amazon-eks-auto-mode-gpu-price (accessed 2026-11-11)
