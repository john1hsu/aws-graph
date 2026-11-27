---
id: kt:amazon-eks-anywhere
type: Service
title: Amazon EKS Anywhere
aliases: [EKS Anywhere, EKS Anywhere Enterprise Subscription]
domain: containers
summary: >-
  An open-source Kubernetes deployment option from AWS, built on Cluster API and Amazon EKS
  Distro, that creates and upgrades customer-managed clusters on your own infrastructure
  (vSphere, bare metal, Nutanix, CloudStack, Snow), including fully air-gapped sites, with
  optional paid Enterprise Subscriptions for support and curated packages.
verified: 2026-11-27
status: current
broader: [kt:amazon-eks, kt:aws-container-services]
related: [kt:bottlerocket, kt:aws-snowball-edge, kt:hybrid-cloud]
relations:
  - {predicate: dependsOn, object: kt:amazon-eks-distro}
  - {predicate: alternativeTo, object: kt:eks-hybrid-nodes}
  - {predicate: alternativeTo, object: kt:aws-outposts}
  - {predicate: runsOn, object: kt:bottlerocket}
  - {predicate: dependsOn, object: kt:aws-support-plans}
sources:
  - id: s1
    title: "Deploy Amazon EKS clusters across cloud and on-premises environments"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/eks-deployment-options.html
    type: official-docs
    accessed: 2026-11-27
  - id: s2
    title: "Amazon EKS Anywhere FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/eks/eks-anywhere/faqs
    type: official-docs
    accessed: 2026-11-27
  - id: s3
    title: "Amazon EKS Anywhere pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/eks/eks-anywhere/pricing
    type: official-docs
    accessed: 2026-11-27
  - id: s4
    title: "Announcing extended support for Kubernetes versions for Amazon EKS Anywhere"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/02/kubernetes-versions-amazon-eks-anywhere
    type: official-blog
    accessed: 2026-11-27
  - id: s5
    title: "One Amazon EKS, many edges: how to choose your edge container strategy on AWS"
    publisher: AWS Containers Blog
    url: https://aws.amazon.com/blogs/containers/one-amazon-eks-many-edges-how-to-choose-your-edge-container-strategy-on-aws/
    type: official-blog
    accessed: 2026-11-27
  - id: s6
    title: "Amazon EKS Anywhere – Now Generally Available to Create and Manage Kubernetes Clusters on Premises"
    publisher: AWS News Blog
    url: https://aws.amazon.com/blogs/aws/amazon-eks-anywhere-now-generally-available-to-create-and-manage-kubernetes-clusters-on-premises/
    type: official-blog
    accessed: 2026-11-27
created: 2026-11-27
---

# Amazon EKS Anywhere

Amazon EKS Anywhere is AWS's tooling for running Kubernetes clusters on infrastructure you own, in a data center or at an edge site. It automates infrastructure setup and cluster lifecycle operations, but unlike Amazon EKS in a Region, both the control plane and the worker nodes are yours to run: AWS doesn't manage the clusters or their capacity, and you remain responsible for lifecycle and maintenance.[^s1][^s2] It launched as generally available in September 2021, initially for VMware vSphere only.[^s6] Its defining trait among the EKS deployment options is that it is designed to work with no connection to an AWS Region at all.[^s1]

## How it works

EKS Anywhere is built on the Kubernetes Cluster API (CAPI) and installs Amazon EKS Distro, the same open-source Kubernetes components (etcd, kube-apiserver, scheduler, controller manager, CoreDNS, kube-proxy, CNI plugins) that Amazon EKS uses.[^s1][^s2] Infrastructure providers, as of 2026-11, are VMware vSphere, bare metal, Nutanix, Apache CloudStack and AWS Snow devices.[^s1][^s2] Nodes run Bottlerocket by default, with Ubuntu and Red Hat Enterprise Linux as alternatives.[^s2]

You drive it with the `eksctl anywhere` CLI. At GA, creating a cluster spun up a temporary bootstrap cluster, installed the Cluster API providers, created the target cluster and then moved its management objects onto it; Flux could be bootstrapped for GitOps-style updates, and Cilium was the CNI.[^s6] AWS recommended at least three control-plane and three worker nodes for production high availability and rolling upgrades.[^s6] Clusters can be fully disconnected; optionally they can connect to a Region for IAM authentication, observability tools, and the EKS Connector, which shows the cluster in the EKS console but doesn't let you modify it from there.[^s1][^s2]

## Pricing, support and gotchas

The software is open source and free to run.[^s3][^s5] AWS support, access to AWS-vended curated packages (add-ons) and extended Kubernetes support need an **EKS Anywhere Enterprise Subscription**, priced per cluster regardless of cluster size. As listed in 2026-11 (no Region applies, since clusters run on your hardware): a 1-year term is $24,000 per cluster ($2,000 per month), and a 3-year term is $18,000 per cluster per year ($1,500 per month).[^s3] Subscriptions require AWS Enterprise Support or Enterprise On-Ramp, are bought through the EKS console, API or CLI, and can be cancelled free within 7 days.[^s3] They can auto-renew; when one lapses, curated-package access is revoked, and AWS sends reminders three months, one month and one week ahead.[^s2]

Since February 2025, each Kubernetes version gets 14 months of standard support from its EKS Anywhere release plus 12 months of extended patching (up to 26 months) for Kubernetes 1.28 and later, at no added cost for subscribers.[^s4] The 12 extra months are a subscriber benefit, so unsubscribed clusters should plan to upgrade within the standard window. Plain EKS Distro deployments, by contrast, aren't covered by AWS Support plans at all.[^s1] EKS Anywhere is not supported on AWS Cloud or on AWS Outposts, and, like Hybrid Nodes, it runs only Linux workers.[^s2][^s5]

## When to use it (and when not)

AWS's 2026 edge guidance puts connectivity first: a site with no persistent, reliable link to an AWS Region (air-gapped, disconnected or intermittent) qualifies only for EKS Anywhere.[^s5] For connected sites, AWS recommends **EKS Hybrid Nodes** as the default when you already own servers (AWS runs the control plane in the Region; billed at $0.10 per cluster-hour plus a per-vCPU-hour node fee starting at $0.020, per the post's table), and **EKS on Outposts** when you want AWS-owned hardware.[^s5][^s1] Choose EKS Anywhere at a connected site only when policy requires air-gapped operation or that you own the control-plane lifecycle.[^s5] If you want no AWS tooling at all, raw EKS Distro is the fully self-managed option.[^s1]

## Connections

EKS Anywhere sits under `kt:amazon-eks` and `kt:aws-container-services` as the customer-managed, on-premises end of the EKS family.[^s1] It **depends on** `kt:amazon-eks-distro` for its Kubernetes components and, for paid subscriptions, on `kt:aws-support-plans` (Enterprise or Enterprise On-Ramp).[^s1][^s3] Its nodes **run on** `kt:bottlerocket` by default.[^s2] It is an **alternative to** `kt:eks-hybrid-nodes` and to EKS on `kt:aws-outposts`, chosen mainly by connectivity and hardware ownership.[^s5] It relates to `kt:aws-snowball-edge` (one of its infrastructure providers) and to `kt:hybrid-cloud` designs generally.[^s2][^s5]

## Sources

[^s1]: Amazon Web Services, "Deploy Amazon EKS clusters across cloud and on-premises environments". https://docs.aws.amazon.com/eks/latest/userguide/eks-deployment-options.html (accessed 2026-11-27)
[^s2]: Amazon Web Services, "Amazon EKS Anywhere FAQs". https://aws.amazon.com/eks/eks-anywhere/faqs (accessed 2026-11-27)
[^s3]: Amazon Web Services, "Amazon EKS Anywhere pricing". https://aws.amazon.com/eks/eks-anywhere/pricing (accessed 2026-11-27)
[^s4]: Amazon Web Services, "Announcing extended support for Kubernetes versions for Amazon EKS Anywhere" (2025-02-28). https://aws.amazon.com/about-aws/whats-new/2025/02/kubernetes-versions-amazon-eks-anywhere (accessed 2026-11-27)
[^s5]: AWS Containers Blog, "One Amazon EKS, many edges: how to choose your edge container strategy on AWS" (2026-09-25). https://aws.amazon.com/blogs/containers/one-amazon-eks-many-edges-how-to-choose-your-edge-container-strategy-on-aws/ (accessed 2026-11-27)
[^s6]: AWS News Blog, "Amazon EKS Anywhere – Now Generally Available to Create and Manage Kubernetes Clusters on Premises" (2021-09-08). https://aws.amazon.com/blogs/aws/amazon-eks-anywhere-now-generally-available-to-create-and-manage-kubernetes-clusters-on-premises/ (accessed 2026-11-27)
