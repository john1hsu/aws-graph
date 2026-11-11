---
id: kt:spot-instances
type: PricingModel
title: Amazon EC2 Spot Instances
aliases: [Spot Instances, EC2 Spot, Spot capacity]
domain: cost-management
summary: >-
  An EC2 purchasing option that runs instances on spare EC2 capacity at a steep discount to
  On-Demand, in exchange for letting Amazon EC2 reclaim the capacity with a two-minute
  interruption notice. Suited to stateless, fault-tolerant and flexible workloads.
verified: 2026-11-11
status: current
broader: [kt:aws-pricing-fundamentals]
related: [kt:amazon-ec2, kt:amazon-ec2-auto-scaling, kt:amazon-emr, kt:amazon-ecs, kt:amazon-eks]
relations:
  - {predicate: alternativeTo, object: kt:reserved-instances}
  - {predicate: alternativeTo, object: kt:savings-plans}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Amazon EC2 Spot Instances"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ec2/spot/
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Amazon EC2 Spot Instances Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ec2/spot/pricing
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Spot Instance interruptions - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-interruptions.md
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Spot Instance interruption notices - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-instance-termination-notices.md
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Best practices for Amazon EC2 Spot - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-best-practices.html
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "Spot Instance quotas - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-limits.md
    type: official-docs
    accessed: 2026-11-11
  - id: s7
    title: "AWS Certified Cloud Practitioner (CLF-C02) exam guide - Content Domain 4: Billing, Pricing, and Support"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.md
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# Amazon EC2 Spot Instances

Spot Instances let you run EC2 instances on unused EC2 capacity at a large discount. AWS advertises savings of up to 90% compared with On-Demand prices.[^s1][^s2] The catch is that Amazon EC2 can take the capacity back when it needs it, an event AWS calls a Spot Instance interruption.[^s3] Spot is not a separate kind of server: it is a purchasing option for the same instance types you would otherwise run On-Demand or cover with a commitment.

## How it works

**Pricing.** Amazon EC2 sets the Spot price, and it moves gradually with long-term trends in supply and demand. You pay the Spot price in effect while your instances run. There is no bidding; AWS describes it as a no-bidding pricing model.[^s1][^s2] The pricing page publishes current prices per Region and instance type and links to the Spot Instance Advisor for comparing them with On-Demand rates. Because prices differ by Region, instance type and time, this article quotes no specific figures.[^s2]

**Capacity pools.** A Spot capacity pool is the set of unused instances of one instance type in one Availability Zone. Each pool is available independently, so the more types and Availability Zones a request can use, the more pools Spot can draw from.[^s5]

**Interruptions.** EC2 can interrupt a Spot Instance for three reasons:

- **Capacity:** EC2 needs the capacity back. This is the main reason, but host maintenance or hardware decommissioning can also cause it.[^s3]
- **Price:** the Spot price rises above a maximum price you set.[^s3]
- **Constraints:** a launch group or Availability Zone group constraint can no longer be met, so the whole group is terminated.[^s3]

Depending on the interruption behavior you chose, EC2 terminates, stops or hibernates the instance.[^s3]

**Interruption notices.** EC2 warns you two minutes before it stops or terminates an instance. The warning arrives as an "EC2 Spot Instance Interruption Warning" event in Amazon EventBridge and as a `spot/instance-action` item in instance metadata. AWS recommends checking for it every 5 seconds. Notices are best effort. With hibernation there is no two-minute lead time, because hibernation starts immediately.[^s4]

## Quotas, pricing and gotchas

- **vCPU-based quotas.** Spot quotas count vCPUs per account per Region. Both running Spot Instances and open requests count toward the quota.[^s6] As of 2026-11, the documented default for "All Standard (A, C, D, H, I, M, R, T, Z) Spot Instance Requests" is 5 vCPUs. Accelerated families such as G and VT, P, Inf, Trn, F, DL and X default to 0. All of these quotas are adjustable in Service Quotas, and EC2 also raises them automatically as you use more.[^s6]
- **Open requests use up quota.** If you terminate Spot Instances but don't cancel their requests, the requests keep counting against the quota until EC2 closes them.[^s6]
- **A maximum price causes more interruptions.** Setting a maximum price is optional. If you set one, your instances are interrupted more often than if you leave it unset.[^s3]
- **Don't fall back to On-Demand.** AWS strongly advises against running inflexible, stateful or tightly coupled workloads on Spot. It also advises against failing over to On-Demand to cope with interruptions, because that can trigger interruptions for other Spot users.[^s5]

## Using Spot well

AWS's best practices start with flexibility. Use at least 10 instance types per workload, enable every Availability Zone in the VPC, and consider attribute-based instance type selection so that new instance types are added automatically.[^s5] Launch through EC2 Auto Scaling groups or EC2 Fleet with the `price-capacity-optimized` allocation strategy. These replace interrupted capacity for you. Avoid the legacy RequestSpotFleet and RequestSpotInstances APIs.[^s5] Spot placement scores (1–10) show how likely a request is to succeed in a Region or Availability Zone. They are a snapshot and don't guarantee capacity.[^s5] Use rebalance recommendations and the two-minute notice, through EventBridge rules, to checkpoint work or drain connections.[^s5]

## When to use it (and when not)

Spot suits stateless, fault-tolerant or flexible work. Examples are big data, containers, CI/CD, stateless web servers, HPC, rendering, and test and development.[^s1][^s5] For a steady baseline that must not be interrupted, use On-Demand covered by Savings Plans or Reserved Instances. Many fleets combine a committed baseline with Spot for burst capacity. The Cloud Practitioner exam guide lists Spot among the compute purchasing options candidates should be able to choose between.[^s7]

## Connections

Spot is one of the **AWS pricing fundamentals** and an alternative to **Reserved Instances** and **Savings Plans**, which trade a commitment for a discount instead of trading interruptibility.[^s7] It applies to **Amazon EC2** capacity, organized into pools per **Availability Zone**.[^s5] **EC2 Auto Scaling** groups are the recommended way to run mixed Spot fleets.[^s5] AWS's Spot page lists **Amazon EMR**, **Amazon ECS** and **Amazon EKS** among the services that use Spot.[^s1] Interruption warnings reach you through **Amazon EventBridge**.[^s4] Spot is covered by the **AWS Certified Cloud Practitioner** exam.[^s7]

## Sources
[^s1]: Amazon Web Services, "Amazon EC2 Spot Instances". https://aws.amazon.com/ec2/spot/ (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Amazon EC2 Spot Instances Pricing". https://aws.amazon.com/ec2/spot/pricing (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Spot Instance interruptions - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-interruptions.md (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Spot Instance interruption notices - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-instance-termination-notices.md (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Best practices for Amazon EC2 Spot - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-best-practices.html (accessed 2026-11-11)
[^s6]: Amazon Web Services, "Spot Instance quotas - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-limits.md (accessed 2026-11-11)
[^s7]: Amazon Web Services, "AWS Certified Cloud Practitioner (CLF-C02) exam guide - Content Domain 4: Billing, Pricing, and Support". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.md (accessed 2026-11-11)
