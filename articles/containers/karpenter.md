---
id: kt:karpenter
type: Tool
title: Karpenter
aliases: [Karpenter autoscaler, Karpenter node autoscaler]
domain: containers
summary: >-
  An open-source Kubernetes node lifecycle manager, created by AWS and now developed under Kubernetes
  SIG Autoscaling, that watches for unschedulable pods and launches right-sized EC2 instances for them
  directly, then consolidates, replaces and retires nodes; it is the engine behind EKS Auto Mode.
verified: 2026-11-17
status: current
broader: [kt:amazon-eks]
related: [kt:eks-auto-mode, kt:bottlerocket, kt:spot-instances, kt:amazon-ec2-auto-scaling]
relations:
  - {predicate: integratesWith, object: kt:amazon-ec2}
  - {predicate: alternativeTo, object: kt:cluster-autoscaler}
  - {predicate: dependsOn, object: kt:amazon-sqs}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: securedBy, object: kt:aws-iam}
sources:
  - id: s1
    title: "Karpenter (Amazon EKS Best Practices Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/best-practices/karpenter.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Scale cluster compute with Karpenter and Cluster Autoscaler"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/autoscaling.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Announcing Karpenter 1.0"
    publisher: Amazon Web Services (AWS Containers Blog)
    url: https://aws.amazon.com/blogs/containers/announcing-karpenter-1-0
    type: official-blog
    accessed: 2026-11-17
  - id: s4
    title: "Disruption (Karpenter documentation)"
    publisher: Kubernetes SIG Autoscaling / Karpenter project
    url: https://karpenter.sh/docs/concepts/disruption/
    type: community
    accessed: 2026-11-17
  - id: s5
    title: "Karpenter v1.0.0 beta (CNCF member post)"
    publisher: Cloud Native Computing Foundation
    url: https://www.cncf.io/blog/2024/11/06/karpenter-v1-0-0-beta
    type: community
    accessed: 2026-11-17
created: 2026-11-17
---

# Karpenter

Karpenter is an open-source node lifecycle manager for Kubernetes. It notices pods the scheduler cannot place, reads their resource requests, node selectors, affinities, tolerations and topology spread constraints, launches nodes sized to fit them, and removes nodes that are no longer needed.[^s1] AWS created and open-sourced it in 2021 (v0.5 launched in November 2021), it reached beta in October 2023, and AWS contributed its vendor-neutral core to the CNCF through Kubernetes SIG Autoscaling.[^s3] It is Apache 2.0 licensed and splits generic scheduling logic from cloud-specific providers; AWS is the primary provider and Microsoft released one for AKS in 2023.[^s5] Amazon EKS describes it as a flexible, high-performance autoscaler that can bring up right-sized EC2 capacity in under a minute, and notes it works with any conformant Kubernetes cluster.[^s2]

## How it works

You describe what Karpenter may launch rather than pre-building node groups. A **NodePool** sets constraints: allowed instance types, Availability Zones, capacity type (Spot or On-Demand), OS and architecture, plus taints, labels and per-pool resource limits. An **EC2NodeClass** holds the AWS-specific settings such as AMI selection and user data.[^s1] Each launched node is represented by a **NodeClaim**. Since v1.0 (announced August 2024) the NodePool and EC2NodeClass APIs are stable across 1.x minor releases, NodeClaims are immutable, `amiSelectorTerms` is required (with an `alias` term like `family@version` for pinning), and new EC2NodeClasses block pod access to instance metadata by default.[^s3]

Karpenter keeps reshaping capacity after launch through **disruption**. Consolidation deletes nodes whose pods fit elsewhere or replaces them with a cheaper node, governed by `consolidationPolicy` (`WhenEmpty`, `Balanced` or `WhenEmptyOrUnderutilized`) and `consolidateAfter`.[^s4] Drift replaces nodes whose NodePool or EC2NodeClass changed, and is on by default since v1.0.[^s3] Expiration force-drains nodes older than `expireAfter`, which defaults to 720h (30 days) in the current docs, and `terminationGracePeriod` caps how long a drain may take.[^s4][^s3] With no budget defined, Karpenter disrupts at most 10% of a NodePool's nodes at a time; pods can opt out of voluntary disruption with the `karpenter.sh/do-not-disrupt` annotation.[^s4]

Interruption handling is optional. You create an SQS queue and EventBridge rules that forward Spot interruption warnings, scheduled maintenance, and instance stopping and terminating events to it, then pass the queue via `--interruption-queue`.[^s4] On a Spot warning Karpenter uses the two-minute notice to start a replacement node while draining the old one; AWS advises not to run the Node Termination Handler alongside it.[^s1] Spot launches use the price-capacity-optimized allocation strategy.[^s1]

## Pricing, support and gotchas

Karpenter itself is free open-source software that you install and operate; you pay for the EC2 instances and other resources it launches. AWS gives technical support only for an unmodified, compatible version running on EKS, offers no SLA for it, and leaves availability, security and upgrade testing of the controller to you (as of 2026-11).[^s2] Run the controller on a small managed node group or on Fargate, never on nodes it manages.[^s1] Other practical advice from the EKS Best Practices Guide:[^s1]

- Pin tested AMIs instead of `@latest`, which can roll untested images into production.[^s1]
- Set per-NodePool CPU and memory limits and billing alarms, because Karpenter will keep adding capacity up to the limit.[^s1]
- Consolidation sizes nodes by pod *requests*; set requests equal to limits for memory, or bursting pods may be OOM-killed after tight packing.[^s1]
- Keep instance-type requirements broad, especially for Spot, and make overlapping NodePools mutually exclusive or weighted, since Karpenter otherwise picks one at random.[^s1]
- In private clusters, add STS and SSM VPC endpoints; the Price List API has no endpoint, so on-demand prices ship inside the binary and only update when you upgrade.[^s1]

## When to use it (and when not)

Compared with Cluster Autoscaler, which scales EC2 Auto Scaling groups or managed node groups and needs many groups to offer instance diversity, Karpenter launches instances directly from one flexible NodePool, reacts to pod scheduling constraints natively, and has built-in consolidation.[^s1][^s2] AWS recommends it for spiky or diverse workloads and suggests keeping managed node groups for steady, static capacity; mixing the two is supported.[^s1] If you would rather not run Karpenter at all, EKS Auto Mode builds on it and has AWS operate the node lifecycle for you.[^s2]

## Connections

`kt:karpenter` is a tool within the `kt:amazon-eks` ecosystem and is the open-source engine that `kt:eks-auto-mode` manages for you.[^s2] It `integratesWith` `kt:amazon-ec2`, launching instances directly, and is an `alternativeTo` `kt:cluster-autoscaler`, which instead resizes groups built on `kt:amazon-ec2-auto-scaling`.[^s1][^s2] Its interruption handling `dependsOn` `kt:amazon-sqs` and `integratesWith` `kt:amazon-eventbridge`, which deliver Spot and health events, so it pairs naturally with `kt:spot-instances`.[^s4][^s1] It is `securedBy` `kt:aws-iam`, since the controller needs AWS permissions to launch and terminate instances and read instance status.[^s4] It is related to `kt:bottlerocket` as a node-image choice: the AMI a Karpenter node boots is picked by the EC2NodeClass's `amiSelectorTerms`, ideally pinned to a tested version.[^s1][^s3]

## Sources
[^s1]: Amazon Web Services, "Karpenter (Amazon EKS Best Practices Guide)". https://docs.aws.amazon.com/eks/latest/best-practices/karpenter.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Scale cluster compute with Karpenter and Cluster Autoscaler". https://docs.aws.amazon.com/eks/latest/userguide/autoscaling.html (accessed 2026-11-17)
[^s3]: Amazon Web Services (AWS Containers Blog), "Announcing Karpenter 1.0". https://aws.amazon.com/blogs/containers/announcing-karpenter-1-0 (accessed 2026-11-17)
[^s4]: Kubernetes SIG Autoscaling / Karpenter project, "Disruption". https://karpenter.sh/docs/concepts/disruption/ (accessed 2026-11-17)
[^s5]: Cloud Native Computing Foundation (member post by AWS), "Karpenter v1.0.0 beta". https://www.cncf.io/blog/2024/11/06/karpenter-v1-0-0-beta (accessed 2026-11-17)
