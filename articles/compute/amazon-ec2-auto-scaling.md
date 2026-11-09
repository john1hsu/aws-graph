---
id: kt:amazon-ec2-auto-scaling
type: Service
title: Amazon EC2 Auto Scaling
aliases: [EC2 Auto Scaling, Auto Scaling groups, ASG]
domain: compute
summary: >-
  The AWS service that keeps a fleet of EC2 instances (an Auto Scaling group) between a minimum and
  maximum size, replacing unhealthy instances and adding or removing capacity through dynamic,
  predictive, scheduled or manual scaling, at no charge beyond the resources it launches.
verified: 2026-11-09
status: current
broader: [kt:aws-compute-services]
related: [kt:amazon-ec2, kt:elastic-load-balancing, kt:spot-instances, kt:aws-auto-scaling]
relations:
  - {predicate: dependsOn, object: kt:amazon-ec2}
  - {predicate: integratesWith, object: kt:elastic-load-balancing}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: pricedBy, object: kt:savings-plans}
  - {predicate: governedBy, object: kt:ec2-auto-scaling-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is Amazon EC2 Auto Scaling? - Amazon EC2 Auto Scaling"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "Dynamic scaling for Amazon EC2 Auto Scaling"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scale-based-on-demand.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Quotas for Auto Scaling resources and groups - Amazon EC2 Auto Scaling"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-quotas.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Amazon EC2 Auto Scaling Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ec2/autoscaling/pricing/
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "Amazon EC2 Auto Scaling FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ec2/autoscaling/faqs/
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "Decrease latency for applications with long boot times using warm pools"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-warm-pools.html
    type: official-docs
    accessed: 2026-11-09
  - id: s7
    title: "EC2 Auto Scaling announces warm pool support for Auto Scaling groups that have mixed instances policies"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/ec2-auto-scaling-warm-pool-mixed-instances-policies/
    type: official-blog
    accessed: 2026-11-09
  - id: s8
    title: "EC2 Auto Scaling now offers a synchronous API to launch instances inside an Auto Scaling group"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/ec2-auto-scaling-synchronous-api-launch-instances/
    type: official-blog
    accessed: 2026-11-09
created: 2026-11-09
---

# Amazon EC2 Auto Scaling

Amazon EC2 Auto Scaling manages collections of EC2 instances called **Auto Scaling groups**, so that a fleet is treated as one logical unit for scaling and health management.[^s1] Each group has a **minimum** size it never drops below, a **maximum** it never exceeds, and an optional **desired capacity** the service works to maintain; scaling policies move the instance count between those bounds.[^s1] It is the standard way to make an EC2-based tier self-healing and elastic rather than a set of hand-managed servers.

## How it works

A group launches instances from a **launch template** (or an older launch configuration) that fixes the AMI, instance type, networking and other settings.[^s1] The service runs EC2 health checks, and optionally custom or load balancer health checks, and replaces terminated or impaired instances to restore desired capacity.[^s1] As it scales, it balances instances evenly across the Availability Zones configured for the group, and when attached to Elastic Load Balancing it registers new instances with the load balancer and deregisters terminating ones automatically.[^s1]

Capacity changes come from several mechanisms:

- **Dynamic scaling** reacts to CloudWatch metrics through three policy types: target tracking (hold a metric at a target value, like a thermostat), step scaling (adjust in steps sized by how far an alarm is breached) and simple scaling (one adjustment followed by a cooldown).[^s2] AWS strongly recommends target tracking with a metric that falls proportionally as capacity rises, such as average CPU utilization or request count per target; the service then creates and manages the alarms itself.[^s2]
- **Predictive scaling** is a native policy type that forecasts load and raises capacity ahead of predictable demand, configurable from the console, CLI or SDKs.[^s5]
- **Scheduled actions** and **manual** changes to desired capacity cover known events and one-off adjustments.[^s1][^s5]

When several policies fire at once, the service picks the one yielding the largest capacity, for scale-out and scale-in alike.[^s2]

Groups can mix instance types and purchase options, combining On-Demand and Spot Instances while still receiving Reserved Instance and Savings Plans discounts; for Spot, the group requests replacement capacity after interruptions and can use Capacity Rebalancing to replace instances at elevated interruption risk.[^s1] **Instance refresh** rolls a new AMI or launch template across the group, with canary-style deployments, and **lifecycle hooks** run custom actions as instances launch or before they terminate.[^s1]

**Warm pools** keep pre-initialized instances beside the group (stopped, hibernated or running) so scale-out can skip long boot sequences; by default the pool is sized to maximum minus desired capacity.[^s6] Stopped and hibernated instances still incur EBS and Elastic IP charges, and AWS strongly discourages the running state because you pay for the full instance.[^s6]

## Quotas, pricing and gotchas

As of 2026-11, defaults per Region are 500 Auto Scaling groups and 200 launch configurations; these can be raised through Service Quotas.[^s3] Per-group limits are fixed: 50 scaling policies, 125 scheduled actions, 50 lifecycle hooks, 50 ELB target groups, 5 VPC Lattice target groups and 20 step adjustments per step policy.[^s3]

The service itself has no extra charge; you pay for the EC2 instances, EBS volumes and CloudWatch alarms and monitoring it uses (as of 2026-11, all Regions).[^s4][^s1]

Gotchas: combining target tracking and step policies on the same metric can make them fight, with one scaling in before the other is ready.[^s2] Warm pools need an EBS root volume, are not supported with weighted mixed-instances groups, and a depleted pool falls back to cold starts; user data may be cut off when an instance is stopped into the pool unless you use lifecycle hooks.[^s6] Don't create a warm pool unless boot latency is actually a problem, since it adds cost.[^s6]

## What changed (2025-11)

In November 2025 AWS added warm pool support for groups using mixed instances policies with multiple On-Demand instance types, in all public AWS Regions and AWS GovCloud (US).[^s7] It also introduced a synchronous `LaunchInstances` API that lets you target a specific Availability Zone or subnet and get immediate feedback on capacity, initially in four Regions (us-east-1, us-west-2, eu-west-1 and ap-southeast-1).[^s8]

## When to use it (and when not)

Use EC2 Auto Scaling for any EC2 tier that should survive instance failure or follow demand: web and API fleets behind a load balancer, batch workers, and container hosts.[^s1] The separate **AWS Auto Scaling** service coordinates scaling plans across several resource types but supports only target tracking; Auto Scaling groups must still be created in EC2 Auto Scaling, which alone offers scheduled and step policies.[^s5] For workloads that do not need servers at all, event-driven services such as AWS Lambda remove fleet management entirely.

## Connections

EC2 Auto Scaling **depends on** Amazon EC2 for the instances it launches and **integrates with** Elastic Load Balancing for traffic distribution and health checks.[^s1] It is **monitored by** Amazon CloudWatch, whose metrics and alarms drive dynamic scaling.[^s2] Its groups can draw on Spot capacity and are **priced by** commitments such as Savings Plans.[^s1] Its fixed per-group and adjustable per-Region limits form the EC2 Auto Scaling quotas node, and groups are **scoped to** a single AWS Region, spread across its Availability Zones.[^s3][^s1] AWS Auto Scaling is a related, broader scaling service.[^s5]

## Sources
[^s1]: Amazon Web Services, "What is Amazon EC2 Auto Scaling?". https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html (accessed 2026-11-09)
[^s2]: Amazon Web Services, "Dynamic scaling for Amazon EC2 Auto Scaling". https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scale-based-on-demand.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Quotas for Auto Scaling resources and groups". https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-quotas.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Amazon EC2 Auto Scaling Pricing". https://aws.amazon.com/ec2/autoscaling/pricing/ (accessed 2026-11-09)
[^s5]: Amazon Web Services, "Amazon EC2 Auto Scaling FAQs". https://aws.amazon.com/ec2/autoscaling/faqs/ (accessed 2026-11-09)
[^s6]: Amazon Web Services, "Decrease latency for applications with long boot times using warm pools". https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-warm-pools.html (accessed 2026-11-09)
[^s7]: Amazon Web Services, "EC2 Auto Scaling announces warm pool support for Auto Scaling groups that have mixed instances policies" (What's New, 2025-11-04). https://aws.amazon.com/about-aws/whats-new/2025/11/ec2-auto-scaling-warm-pool-mixed-instances-policies/ (accessed 2026-11-09)
[^s8]: Amazon Web Services, "EC2 Auto Scaling now offers a synchronous API to launch instances inside an Auto Scaling group" (What's New, 2025-11-18). https://aws.amazon.com/about-aws/whats-new/2025/11/ec2-auto-scaling-synchronous-api-launch-instances/ (accessed 2026-11-09)
