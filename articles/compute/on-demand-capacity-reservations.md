---
id: kt:on-demand-capacity-reservations
type: Feature
title: Amazon EC2 On-Demand Capacity Reservations
aliases: [On-Demand Capacity Reservations, EC2 Capacity Reservations, ODCR, ODCRs, Capacity Reservations, future-dated Capacity Reservations, interruptible Capacity Reservations]
domain: compute
summary: >-
  An Amazon EC2 feature that holds compute capacity for a specific instance type, platform,
  tenancy and Availability Zone for as long as you need it, billed at the On-Demand rate whether
  or not instances are running; it guarantees capacity but gives no discount on its own.
verified: 2026-11-17
status: current
broader: [kt:amazon-ec2]
related: [kt:ec2-placement-groups, kt:amazon-ec2-auto-scaling, kt:spot-instances, kt:disaster-recovery-strategies]
relations:
  - {predicate: contrastsWith, object: kt:reserved-instances}
  - {predicate: pricedBy, object: kt:savings-plans}
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: integratesWith, object: kt:aws-resource-access-manager}
  - {predicate: governedBy, object: kt:service-quotas}
  - {predicate: monitoredBy, object: kt:amazon-eventbridge}
sources:
  - id: s1
    title: "Reserve compute capacity with EC2 On-Demand Capacity Reservations - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-capacity-reservations.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Concepts for Amazon EC2 Capacity Reservations - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.eu/AWSEC2/latest/UserGuide/cr-concepts.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Capacity Reservation pricing and billing - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/capacity-reservations-pricing-billing.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "Shared Capacity Reservations - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.eu/AWSEC2/latest/UserGuide/capacity-reservation-sharing.html
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "Amazon EC2 Future-dated Capacity Reservations Now Supports Cancellation"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-ec2-fcr-cancellation/
    type: official-blog
    accessed: 2026-11-17
  - id: s6
    title: "Optimize unused capacity with Amazon EC2 interruptible Capacity Reservations"
    publisher: AWS Compute Blog
    url: https://aws.amazon.com/blogs/compute/optimize-unused-capacity-with-amazon-ec2-interruptible-capacity-reservations
    type: official-blog
    accessed: 2026-11-17
created: 2026-11-17
---

# Amazon EC2 On-Demand Capacity Reservations

On-Demand Capacity Reservations (ODCRs) let you set aside Amazon EC2 capacity in one specific Availability Zone, for any duration, so that launches don't fail for lack of On-Demand capacity at the moment you need it.[^s1] A reservation is defined by four attributes (Availability Zone, instance type, platform and tenancy), and only instances that match all four can use it.[^s2] The key idea: an ODCR is a capacity guarantee, not a discount, which is what separates it from **Reserved Instances** and **Savings Plans**.[^s1]

## How it works

There are two ways to start a reservation. An **immediate** reservation is available as soon as it is created, has no term commitment, and can be modified or cancelled at any time.[^s1] A **future-dated** reservation names a start date and a commitment duration; EC2 assesses the request (typically within 5 days), and requests can be made 5 to 120 days ahead, with at least 56 days recommended.[^s2] The minimum commitment is 14 days for most instance types (12 weeks for U and X instances), and during it you can raise but not lower the instance count.[^s2] Future-dated reservations need at least 32 vCPUs per request, cover only incremental capacity (not instances you already run), and are limited to the C, G, I, M, R, T, U and X families.[^s1] Since June 2026 a future-dated reservation can be cancelled, possibly with a cancellation charge that EC2 quotes before you accept.[^s5]

Reservations either end at a set time (within an hour of it) or run until you cancel them. When one ends, running instances keep running, but stopped instances that target it can't restart until their targeting is changed.[^s2]

**Matching** is open or targeted. An open reservation (the default) automatically absorbs any new or running instance with matching attributes; a targeted reservation is used only by instances that explicitly name it or its resource group, which stops other workloads from consuming capacity you meant to keep free.[^s2] Future-dated reservations must be targeted so that the delivered capacity is additional to existing usage.[^s2] Instances launched by AWS services — EC2 Auto Scaling, Amazon ECS, Amazon EKS, Amazon EMR, AWS Batch, Elastic Beanstalk, ParallelCluster and AWS PCS — can run in reservations too.[^s1]

## Pricing, quotas and gotchas

As of 2026-11, a reservation is charged at the equivalent On-Demand rate (plus any Regional surcharge for Dedicated Instances) whether or not instances occupy it, billed per second with a 60-second minimum.[^s3] When a matching instance runs in the reservation you pay for the instance, not twice; idle slots show as "unused reservation" on the bill. AWS's example: reserve 20 m4.large Linux instances, run 15, and you pay for 15 instances plus 5 unused slots.[^s3] Savings Plans and **Regional** Reserved Instances discount reservations automatically (instance usage first, then unused capacity); **zonal** RI discounts do not apply.[^s3]

There is no separate ODCR count limit: reservations draw on your On-Demand Instance quota for the Region, and active-but-unused capacity plus reservations in the assessing, scheduled, pending, active and delayed states all count against it.[^s1] Other limits worth knowing: reservations can't be moved to another account (only shared), work in cluster placement groups but not spread or partition groups, can't use Dedicated Hosts (Dedicated Instances are fine), can't be used with Windows BYOL, and don't guarantee that a hibernated instance can resume.[^s1] The reservation's platform must match the AMI's platform details exactly, so check whether a Linux AMI reports the generic Linux/UNIX value or a specific distribution before you reserve.[^s1]

## Sharing and interruptible capacity

Owners can share a reservation through AWS Resource Access Manager with individual accounts, an OU or the whole organization. By default the owner pays for unused capacity and each consumer pays for its own instances; the owner can instead assign billing for the available capacity to one consumer.[^s4] Only shared-tenancy reservations in the active or scheduled state can be shared, AZs are matched by AZ ID rather than name, and all usage counts against the owner's On-Demand limits.[^s4]

**Interruptible Capacity Reservations**, announced in November 2025, let an owner carve idle capacity out of an ODCR and share it while keeping the right to reclaim it; consumer instances get a 2-minute warning through Amazon EventBridge before EC2 shuts them down, and both sides pay On-Demand rates for what they use or hold.[^s6]

## When to use it (and when not)

Use ODCRs for capacity you cannot afford to miss: a disaster-recovery footprint in a second Region, a planned launch or seasonal event, or a tightly coupled job in a cluster placement group. Pair them with Savings Plans or Regional RIs so the guaranteed capacity is also discounted.[^s1][^s3] If you only want lower cost and can tolerate launch failures, a Savings Plan alone is cheaper because you never pay for idle slots; Unused reservations are pure cost, so cancel or right-size them when they sit idle.[^s3]

## Connections

ODCRs are a feature of **Amazon EC2** and are **scoped to** a single **Availability Zone**.[^s1] They **contrast with** **Reserved Instances** (RIs are a term-based billing discount, while immediate ODCRs carry no term and no discount) and are **priced by** **Savings Plans**, which supply the discount ODCRs lack.[^s1][^s3] They **integrate with** **AWS Resource Access Manager** for cross-account sharing, are **governed by** On-Demand quotas managed in **Service Quotas**, and interruptible reservations signal reclaims through **Amazon EventBridge**.[^s1][^s4][^s6] Cluster **placement groups** and **Amazon EC2 Auto Scaling** groups can launch into them, which is why they are a common building block for **disaster recovery strategies**.[^s1]

## Sources
[^s1]: Amazon Web Services, "Reserve compute capacity with EC2 On-Demand Capacity Reservations". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-capacity-reservations.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Concepts for Amazon EC2 Capacity Reservations". https://docs.aws.eu/AWSEC2/latest/UserGuide/cr-concepts.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Capacity Reservation pricing and billing". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/capacity-reservations-pricing-billing.html (accessed 2026-11-17)
[^s4]: Amazon Web Services, "Shared Capacity Reservations". https://docs.aws.eu/AWSEC2/latest/UserGuide/capacity-reservation-sharing.html (accessed 2026-11-17)
[^s5]: Amazon Web Services, "Amazon EC2 Future-dated Capacity Reservations Now Supports Cancellation" (What's New, 2026-06-10). https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-ec2-fcr-cancellation/ (accessed 2026-11-17)
[^s6]: AWS Compute Blog, "Optimize unused capacity with Amazon EC2 interruptible Capacity Reservations" (2025-11-24). https://aws.amazon.com/blogs/compute/optimize-unused-capacity-with-amazon-ec2-interruptible-capacity-reservations (accessed 2026-11-17)
