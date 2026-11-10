---
id: kt:amazon-eks
type: Service
title: Amazon Elastic Kubernetes Service
aliases: [Amazon EKS, EKS, AWS EKS]
domain: containers
summary: >-
  AWS's managed, certified Kubernetes-conformant service: AWS runs the Kubernetes control plane
  and, in EKS Auto Mode, the worker nodes too, while you deploy standard Kubernetes workloads on
  EC2, Fargate or your own on-premises machines (Hybrid Nodes).
verified: 2026-11-10
status: current
broader: [kt:aws-container-services]
related: [kt:amazon-ecr, kt:eks-auto-mode]
relations:
  - {predicate: alternativeTo, object: kt:amazon-ecs}
  - {predicate: runsOn, object: kt:amazon-ec2}
  - {predicate: integratesWith, object: kt:aws-fargate}
  - {predicate: integratesWith, object: kt:elastic-load-balancing}
  - {predicate: governedBy, object: kt:eks-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is Amazon EKS?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Automate cluster infrastructure with EKS Auto Mode"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/automode.html
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Understand the Kubernetes version lifecycle on EKS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/kubernetes-versions.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Amazon Elastic Kubernetes Service endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/eks.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Amazon EKS pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/eks/pricing/
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "Choosing an AWS container service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/containers-on-aws-how-to-choose/choosing-aws-container-service.html
    type: official-docs
    accessed: 2026-11-10
  - id: s7
    title: "Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# Amazon Elastic Kubernetes Service

Amazon Elastic Kubernetes Service (Amazon EKS) is AWS's managed Kubernetes offering. AWS operates the Kubernetes control plane (the components that schedule workloads, store cluster state and integrate with AWS), and the service is certified Kubernetes-conformant, so applications and community tooling built for upstream Kubernetes run without refactoring.[^s1] Clusters run in the AWS cloud, and EKS Anywhere and EKS Hybrid Nodes extend the same model into your own data centers.[^s1] AWS's container decision guide describes EKS as providing a highly available, secure control plane and positions it as the Kubernetes choice in the orchestration layer, next to Amazon ECS.[^s6]

## How it works

EKS has two operating modes. In **EKS standard**, AWS manages the control plane and you manage the data plane: the worker nodes your pods run on. In **EKS Auto Mode**, AWS also manages the nodes, choosing instance types, scaling, patching the operating system and wiring in AWS security services.[^s1] Nodes can be any Amazon EC2 instance type, including Graviton, and EKS also supports AWS Fargate for serverless pods and Hybrid Nodes for on-premises machines.[^s1][^s5]

Auto Mode extends AWS management into the cluster's compute, storage, networking and load balancing. It uses Karpenter to add nodes for pods that can't be scheduled and to remove nodes that are no longer needed. It provisions Application and Network Load Balancers for Kubernetes Services and Ingresses and includes EBS CSI storage, pod networking and network-policy enforcement. Its nodes run Bottlerocket with a read-only root file system and no SSH or SSM access. Each node is replaced after at most 21 days, and replacements respect Pod Disruption Budgets.[^s2] AWS owns the default NodePools and NodeClasses; to customize instance types or isolation, you add your own rather than editing the defaults.[^s2]

EKS also offers managed **EKS Capabilities**: Argo CD for GitOps deployment, AWS Controllers for Kubernetes (ACK) for managing AWS resources from Kubernetes, and kro for composing custom resources.[^s1]

## Version lifecycle

Each Kubernetes minor version gets 14 months of **standard support** after its EKS release, then 12 months of **extended support**, 26 months in all. During extended support, AWS still ships security patches for the control plane, the VPC CNI, kube-proxy, CoreDNS and the EKS-optimized AMIs.[^s3] The cluster upgrade policy defaults to `EXTENDED` for new and existing clusters. If you opt out, EKS auto-upgrades the control plane when standard support ends instead of letting the cluster enter paid extended support.[^s3] When extended support ends, EKS upgrades the control plane on its own schedule, with no advance notice and no rollback. Nodes and add-ons stay on the old version until you update them, although Auto Mode nodes may update automatically.[^s3] As of 2026-11, versions 1.34 to 1.37 are in standard support and 1.31 to 1.33 in extended support; version 1.31's extended support ends on November 26, 2026.[^s3]

## Quotas, pricing and gotchas

Default quotas per Region (as of 2026-11): 100 clusters (adjustable), 30 managed node groups per cluster and 450 nodes per managed node group (both adjustable), 10 Fargate profiles per cluster with 5 selectors each (adjustable), 3,000 access entries per cluster, 4 control-plane security groups per cluster and 40 public-endpoint CIDR ranges per cluster (these three are fixed).[^s4] Fargate pods also count against the account's Fargate On-Demand vCPU quota, which starts at 6 vCPUs (adjustable) and may be lower in new accounts.[^s4]

Pricing (as of 2026-11; the pricing page gives these fees without Regional variation): each cluster costs $0.10 per hour in standard support and $0.60 per hour in extended support.[^s5] Auto Mode adds a per-instance management fee that depends on the EC2 instance type (for example, $0.02064 per hour for an m5a.xlarge). It's billed per second with a one-minute minimum, on top of the EC2 price.[^s5] Hybrid Nodes are billed per vCPU-hour, starting at $0.020 for the first 576,000 vCPU-hours a month and falling in tiers to $0.006.[^s5] Optional Provisioned Control Plane tiers, from XL at $1.65 to 8XL at $13.90 per cluster-hour, are charged in addition to the cluster fee.[^s5] EC2 instances, EBS volumes and public IPv4 addresses are billed separately.[^s1]

Gotchas: because extended support is the default, a cluster left on an aging version moves to the $0.60 rate when its standard support ends, and AWS starts billing extended support from the beginning of that day.[^s3][^s5] Kubernetes ships three releases a year, so plan for regular upgrades.[^s6]

## When to use it (and when not)

AWS's decision guide frames the first choice as a serverless operating model versus a Kubernetes one. It recommends EKS for organizations with the skills to run Kubernetes that value portable Kubernetes skills and the control Kubernetes gives over deployment and orchestration.[^s6] Teams without that experience are pointed to Amazon ECS, which is fully managed and AWS-native, or to higher-level services such as AWS App Runner or AWS Lambda.[^s6] Auto Mode narrows the operations gap by handing node management to AWS.[^s2] The Cloud Practitioner exam asks candidates to recognize when ECS or EKS is the appropriate container option.[^s7]

## Connections

- **Broader: AWS container services** (`kt:aws-container-services`), the orchestration layer EKS shares with ECS.[^s6]
- **Alternative to Amazon ECS** (`kt:amazon-ecs`), the AWS-native orchestrator.[^s6]
- **Runs on Amazon EC2** (`kt:amazon-ec2`) and **integrates with AWS Fargate** (`kt:aws-fargate`) for worker capacity.[^s1][^s6]
- **Integrates with Elastic Load Balancing** (`kt:elastic-load-balancing`), which Auto Mode provisions for Services and Ingresses.[^s2]
- **Related to EKS Auto Mode** (`kt:eks-auto-mode`) and **Amazon ECR** (`kt:amazon-ecr`), the registry for the images it pulls.[^s2][^s6]
- **Governed by EKS quotas** (`kt:eks-quotas`).[^s4]
- **Covered by AWS Certified Cloud Practitioner** (`kt:aws-certified-cloud-practitioner`); Task Statement 3.3 names Amazon EKS.[^s7]

## Sources
[^s1]: Amazon Web Services, "What is Amazon EKS?". https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Automate cluster infrastructure with EKS Auto Mode". https://docs.aws.amazon.com/eks/latest/userguide/automode.html (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Understand the Kubernetes version lifecycle on EKS". https://docs.aws.amazon.com/eks/latest/userguide/kubernetes-versions.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Amazon Elastic Kubernetes Service endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/eks.html (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Amazon EKS pricing". https://aws.amazon.com/eks/pricing/ (accessed 2026-11-10)
[^s6]: Amazon Web Services, "Choosing an AWS container service". https://docs.aws.amazon.com/decision-guides/latest/containers-on-aws-how-to-choose/choosing-aws-container-service.html (accessed 2026-11-10)
[^s7]: Amazon Web Services, "Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html (accessed 2026-11-10)
