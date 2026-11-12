---
id: kt:local-zones
type: InfrastructureConcept
title: AWS Local Zones
aliases: [Local Zones, Local Zone, AWS Local Zone, Local Zone group]
domain: global-infrastructure
summary: >-
  AWS Local Zones are extensions of an AWS Region placed in or near large metro areas, running a core
  subset of services such as EC2, EBS, VPC subnets and Application Load Balancer so latency-sensitive
  parts of a workload can sit close to users while the rest stays in the parent Region.
verified: 2026-11-12
status: current
broader: [kt:aws-global-infrastructure]
related: [kt:amazon-vpc, kt:aws-direct-connect, kt:edge-locations, kt:ec2-placement-groups]
relations:
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:availability-zones}
  - {predicate: alternativeTo, object: kt:aws-outposts}
  - {predicate: alternativeTo, object: kt:aws-wavelength}
  - {predicate: integratesWith, object: kt:amazon-ec2}
sources:
  - id: s1
    title: "Regions and Zones - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Getting started with AWS Local Zones - AWS Local Zones User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/local-zones/latest/ug/getting-started.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "AWS Local Zones FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/global-infrastructure/localzones/faqs/
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "AWS Local Zones Features"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/global-infrastructure/localzones/features/
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "Resiliency at the edge - AWS Prescriptive Guidance (Best practices for building a hybrid cloud architecture)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/hybrid-cloud-best-practices/resiliency.html
    type: official-docs
    accessed: 2026-11-12
  - id: s6
    title: "AWS announces the general availability of a new AWS Local Zone in Las Vegas, Nevada"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/08/aws-local-zones-las-vegas-nevada/
    type: official-blog
    accessed: 2026-11-12
created: 2026-11-12
---

# AWS Local Zones

An AWS Local Zone is an extension of an AWS Region built close to a concentration of users. Each Local Zone has its own internet connectivity and supports AWS Direct Connect, so resources placed there can answer local users with low latency, while the zone remains logically part of its parent Region and is addressed with a code formed from the Region code plus a location identifier, such as `us-west-2-lax-1` for Los Angeles under US West (Oregon).[^s1] AWS positions Local Zones for workloads that need single-digit millisecond latency (media rendering, graphics-heavy applications, virtual desktops, real-time gaming), for hybrid migrations where an application must stay close to on-premises systems, and for keeping data inside a particular metro or jurisdiction.[^s3] As of August 2026 AWS described Local Zones as available in more than 30 metro areas worldwide; that announcement added Las Vegas (`us-west-2-las-2a`).[^s6]

## How it works

A Local Zone is not on by default. You enable its **zone group** (for example `us-west-2-lax-1`) from the Regions and Zones tab of AWS Global View or with the `ModifyAvailabilityZoneGroup` API; enabling it from the console also enables the parent Region if it is not already enabled.[^s2][^s6] The zone group is what you opt into, while subnets and instances reference the individual zone name (for example `us-west-2-lax-1a`).[^s2] You then extend an existing VPC from the parent Region by creating a subnet in the Local Zone and launch resources into that subnet; gateways and route tables carry over, so the Local Zone subnet behaves like another subnet of the same VPC.[^s2][^s3] Public IPv4 addresses for Local Zone resources are allocated from a **network border group**, the set of zones from which AWS advertises those addresses.[^s1]

Each Local Zone is anchored to a parent Availability Zone, which handles some control-plane operations such as API calls.[^s3] Services that are not hosted locally, such as Amazon S3 and Amazon Aurora, are reached privately from the Local Zone over AWS's network back to the Region.[^s3]

## Services, pricing and gotchas

- **Service set varies by zone.** AWS lists EC2, EBS, VPC, Application Load Balancer, Direct Connect, Route 53, ECS and EKS among the core services, while FSx, EMR, ElastiCache, RDS, NAT gateways, Network Load Balancer and others appear only in selected zones (features page as of 2026-11).[^s4] Instance families and EBS volume types also differ: some newer zones offer gp3, gp2, io1, st1 and sc1, while many older ones list only gp2.[^s4] Check `DescribeInstanceTypeOfferings` for the specific zone before designing.[^s3][^s2]
- **Billing.** Enabling a Local Zone has no extra charge; you pay for what you run there.[^s4] Resource prices differ from the parent Region, and EC2 in Local Zones can be bought On-Demand, with Savings Plans or as Spot.[^s3] On-Demand Capacity Reservations are supported.[^s4]
- **Opting out is not self-service.** Disabling a zone group requires an AWS Support case after you delete the resources in it.[^s2]
- **Single-site by default.** Instances can land on the same rack or host unless you use partition or spread placement groups.[^s5]

## Resilience

AWS Prescriptive Guidance treats Local Zones much like any other edge location: for high availability, pair two Local Zones in the same parent Region that are anchored to *different* parent Availability Zones, which gives a Multi-AZ design from the control-plane perspective (`describe-availability-zones` returns the `ParentZoneName` and `ParentZoneId`).[^s5] The parent Region is fixed and cannot be changed by the customer.[^s5] On-premises connectivity is the customer's responsibility; where an international Local Zone has only one Direct Connect location, AWS suggests a VPN running on EC2 over Direct Connect instead of the usual dual-location Direct Connect pattern.[^s5] Route 53 health-checked failover and an Application Load Balancer in front of an EC2 Auto Scaling group are the recommended building blocks inside the zone.[^s5]

## When to use it (and when not)

Choose a Local Zone when users or on-premises systems in a specific metro need lower latency than the nearest Region can provide and you do not want to run your own data center.[^s3] AWS Outposts fits instead when the hardware itself must sit on your premises, and AWS Wavelength when the users are mobile devices on a carrier's 5G network.[^s3][^s1] Workloads that need the full service catalog, or that are not latency-sensitive, are usually better left in a Region's Availability Zones, which offer the complete set of services rather than a core subset.[^s3]

## Connections

Local Zones are part of the **AWS global infrastructure** and are scoped to a parent **AWS Region**, whose VPCs they extend through **Amazon VPC** subnets.[^s2] They contrast with **Availability Zones**: both host subnets, but AZs carry the full service range and Local Zones a latency-focused subset.[^s3] They are alternatives to **AWS Outposts** and AWS Wavelength for edge placement.[^s3] Most workloads in them run on **Amazon EC2**, are reached through **AWS Direct Connect** or the zone's local internet, and use **EC2 placement groups** to spread instances.[^s1][^s5] Unlike **edge locations**, which host CloudFront and other edge services, a Local Zone runs your own compute and storage.[^s1][^s4]

## Sources
[^s1]: Amazon Web Services, "Regions and Zones - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Getting started with AWS Local Zones". https://docs.aws.amazon.com/local-zones/latest/ug/getting-started.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "AWS Local Zones FAQs". https://aws.amazon.com/about-aws/global-infrastructure/localzones/faqs/ (accessed 2026-11-12)
[^s4]: Amazon Web Services, "AWS Local Zones Features". https://aws.amazon.com/about-aws/global-infrastructure/localzones/features/ (accessed 2026-11-12)
[^s5]: Amazon Web Services, "Resiliency at the edge - AWS Prescriptive Guidance". https://docs.aws.amazon.com/prescriptive-guidance/latest/hybrid-cloud-best-practices/resiliency.html (accessed 2026-11-12)
[^s6]: Amazon Web Services, "AWS announces the general availability of a new AWS Local Zone in Las Vegas, Nevada" (What's New, 2026-08-20). https://aws.amazon.com/about-aws/whats-new/2026/08/aws-local-zones-las-vegas-nevada/ (accessed 2026-11-12)
