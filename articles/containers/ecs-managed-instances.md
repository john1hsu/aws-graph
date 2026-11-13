---
id: kt:ecs-managed-instances
type: Feature
title: Amazon ECS Managed Instances
aliases: [ECS Managed Instances, ECS MI]
domain: containers
summary: >-
  A fully managed compute option for Amazon ECS in which AWS provisions, scales, patches and
  replaces Bottlerocket-based EC2 instances in your account to run your tasks, while you keep
  EC2-level choice of instance types, GPUs and purchase options for a per-instance management fee.
verified: 2026-11-13
status: current
broader: [kt:amazon-ecs]
related: [kt:aws-fargate, kt:eks-auto-mode, kt:aws-container-services]
relations:
  - {predicate: partOf, object: kt:amazon-ecs}
  - {predicate: runsOn, object: kt:amazon-ec2}
  - {predicate: runsOn, object: kt:bottlerocket}
  - {predicate: alternativeTo, object: kt:aws-fargate}
  - {predicate: pricedBy, object: kt:spot-instances}
  - {predicate: pricedBy, object: kt:savings-plans}
  - {predicate: governedBy, object: kt:ecs-quotas}
sources:
  - id: s1
    title: "Architect for Amazon ECS Managed Instances"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ManagedInstances.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Announcing Amazon ECS Managed Instances for containerized applications"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/aws/announcing-amazon-ecs-managed-instances-for-containerized-applications
    type: official-blog
    accessed: 2026-11-13
  - id: s3
    title: "Amazon ECS Managed Instances Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ecs/managed-instances/pricing
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "Amazon ECS Managed Instances now supports Amazon EC2 Spot Instances (Indonesian edition)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/id/about-aws/whats-new/2025/12/amazon-ecs-managed-instances-ec2-spot-instances/
    type: official-blog
    accessed: 2026-11-13
  - id: s5
    title: "Amazon ECS announces Managed Daemons for ECS Managed Instances"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/04/amazon-ecs-managed-daemons
    type: official-blog
    accessed: 2026-11-13
  - id: s6
    title: "Amazon ECS Managed Instances adds cross-instance and Spot consolidation"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/10/ecs-managed-instances-consolidation/
    type: official-blog
    accessed: 2026-11-13
created: 2026-11-13
---

# Amazon ECS Managed Instances

Amazon ECS Managed Instances is a compute option for Amazon ECS that sits between Fargate and self-managed EC2 capacity. You declare what your tasks need, and AWS launches, scales, patches and maintains the EC2 instances that run them. Unlike Fargate, you can steer which instance types and hardware features (GPUs, CPU architecture, large memory, high network throughput) are used; unlike the classic EC2 option, you never build AMIs or run Auto Scaling groups yourself.[^s1] AWS announced it on 30 September 2025, initially in six Regions including US East (N. Virginia), US West (Oregon) and Europe (Ireland).[^s2]

## How it works

Everything is driven by a Managed Instances **capacity provider**. The default provider picks the most cost-optimized general-purpose instance types that fit the task definition's CPU and memory; a custom provider narrows the choice with `instanceRequirements` attributes such as vCPU and memory ranges, CPU manufacturer, accelerator type, explicit instance types or local storage. A single capacity provider strategy cannot mix Managed Instances with Fargate or Auto Scaling group providers.[^s1] Task definitions opt in by listing `MANAGED_INSTANCES` in `requiresCompatibilities`, and one task definition can declare both `FARGATE` and `MANAGED_INSTANCES`; Fargate task definitions on platform version 1.4.0 carry over unchanged.[^s1]

Instances run only Bottlerocket, AWS's container-focused OS, on x86_64 or Arm64, with an immutable root filesystem, SELinux enforcement and no SSH; you debug through ECS Exec instead.[^s1] AWS applies security patches roughly every 14 days, honouring maintenance windows you define with EC2 event windows; after 14 days ECS starts draining an instance and moves service tasks to a fresh one.[^s2][^s1] Tasks use `awsvpc` or `host` networking, and load balancer target groups must use the `ip` target type.[^s1] Setup needs two IAM roles: an infrastructure role that lets ECS manage instances for you, and an instance profile for the workloads.[^s1]

By default ECS bin-packs several small tasks onto larger instances, terminates idle ones and re-sizes the fleet as demand changes, while respecting service minimums, start-before-stop deployments and task protection; you can instead ask for one task per instance when you need VM-level isolation.[^s1] In October 2026 AWS added cross-instance consolidation, which repacks tasks from several lightly loaded instances together, and the same logic for Spot capacity, both automatic and free; in AWS's own test a 261-task fleet shrank from 19 to 13 instances.[^s6] Since April 2026, **Managed Daemons** let platform teams run one agent task (security, observability, networking) on every managed instance, started before application tasks and rolled out separately from application deployments.[^s5]

## Quotas, pricing and gotchas

You pay for the whole EC2 instance ECS launches plus a management fee charged on top of the EC2 price, billed per second with a one-minute minimum; the fee is independent of whether the instance is On-Demand, Spot or covered by Savings Plans or Reserved Instances (as of 2026-11; rates vary by Region and instance type, see the pricing page).[^s3][^s1] Compute and EC2 Instance Savings Plans discount the EC2 portion.[^s1] Spot support arrived in December 2025 through a `capacityOptionType` setting on the capacity provider.[^s4]

Fixed limits to plan for: the shared EBS data volume is 30–16,384 GiB (80 GiB default) and also holds container images; custom AMIs are not supported; instances last at most about 14 days, so long-lived tasks must tolerate replacement; and only Linux containers run. Capacity also counts against your EC2 On-Demand instance quotas and ECS service quotas.[^s1] Organizations that restrict AMIs with service control policies must allow the AWS-owned ECS Managed Instances AMI accounts, or instance launches fail.[^s1]

## When to use it (and when not)

Choose Managed Instances when Fargate's sizes or hardware are too limiting (GPUs, specific instance families, large memory or local storage) or when you want EC2 purchase options, but you do not want to own AMI pipelines and patching.[^s1][^s2] Stay on Fargate when you do not need instance-level choices, since Managed Instances bills whole instances rather than per task; stay on self-managed EC2 when you need custom AMIs, Windows containers, SSH access or hosts that live longer than two weeks.[^s1]

## Connections

It is **part of** Amazon ECS and **runs on** EC2 instances using the Bottlerocket OS.[^s1] It is an **alternative to** AWS Fargate within ECS, and the ECS counterpart of Amazon EKS Auto Mode, which similarly manages EC2 nodes for Kubernetes.[^s1] Its EC2 cost is **priced by** Spot Instances and Savings Plans, while the management fee is not discounted.[^s4][^s3] Its capacity is **governed by** ECS and EC2 service quotas.[^s1]

## Sources

[^s1]: Amazon Web Services, "Architect for Amazon ECS Managed Instances", Amazon ECS Developer Guide. https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ManagedInstances.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, AWS News Blog, "Announcing Amazon ECS Managed Instances for containerized applications" (2025-09-30). https://aws.amazon.com/blogs/aws/announcing-amazon-ecs-managed-instances-for-containerized-applications (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Amazon ECS Managed Instances Pricing". https://aws.amazon.com/ecs/managed-instances/pricing (accessed 2026-11-13)
[^s4]: Amazon Web Services, What's New with AWS, "Amazon ECS Managed Instances now supports Amazon EC2 Spot Instances" (2025-12-18). https://aws.amazon.com/id/about-aws/whats-new/2025/12/amazon-ecs-managed-instances-ec2-spot-instances/ (accessed 2026-11-13)
[^s5]: Amazon Web Services, What's New with AWS, "Amazon ECS announces Managed Daemons for ECS Managed Instances" (2026-04-01). https://aws.amazon.com/about-aws/whats-new/2026/04/amazon-ecs-managed-daemons (accessed 2026-11-13)
[^s6]: Amazon Web Services, What's New with AWS, "Amazon ECS Managed Instances adds cross-instance and Spot consolidation" (2026-10-28). https://aws.amazon.com/about-aws/whats-new/2026/10/ecs-managed-instances-consolidation/ (accessed 2026-11-13)
