---
id: kt:ec2-placement-groups
type: Feature
title: Amazon EC2 placement groups
aliases: [EC2 placement groups, placement groups, cluster placement group, partition placement group, spread placement group, precision time placement group]
domain: compute
summary: >-
  An optional Amazon EC2 feature that tells EC2 how to position a set of interdependent instances on
  the underlying hardware: packed together (cluster), split across rack-isolated partitions
  (partition), each on distinct hardware (spread), or on hardware with high-precision time sources
  (precision time).
verified: 2026-11-12
status: current
broader: [kt:amazon-ec2]
related: [kt:ec2-instance-types, kt:aws-outposts, kt:reliability-pillar, kt:spot-instances]
relations:
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: integratesWith, object: kt:on-demand-capacity-reservations}
  - {predicate: integratesWith, object: kt:aws-resource-access-manager}
  - {predicate: dependsOn, object: kt:aws-nitro-system}
sources:
  - id: s1
    title: "Placement groups for your Amazon EC2 instances"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Placement strategies for your placement groups"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/us_en/AWSEC2/latest/UserGuide/placement-strategies.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Shared placement groups"
    publisher: Amazon Web Services
    url: https://docs.aws.eu/AWSEC2/latest/UserGuide/share-placement-group.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "Using partition placement groups for large distributed and replicated workloads in Amazon EC2"
    publisher: Amazon Web Services (AWS Compute Blog)
    url: https://aws.amazon.com/blogs/compute/using-partition-placement-groups-for-large-distributed-and-replicated-workloads-in-amazon-ec2
    type: official-blog
    accessed: 2026-11-12
  - id: s5
    title: "Amazon Time Sync Service adds microsecond-accurate time to 26 additional EC2 instance types"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/ec2-time-sync-precision-time-placement-group
    type: official-blog
    accessed: 2026-11-12
created: 2026-11-12
---

# Amazon EC2 placement groups

A placement group is an optional Amazon EC2 construct that influences where a group of related instances lands on AWS hardware.[^s1] Without one, EC2 already spreads instances out to reduce correlated failures; a placement group lets you override that default with a specific strategy.[^s1] There are four strategies: **cluster**, **partition**, **spread** and **precision time**.[^s1] Creating a placement group is free; you pay only for the instances in it.[^s1][^s2]

## How the strategies work

**Cluster** groups pack instances close together inside one Availability Zone, in the same high-bisection-bandwidth network segment, for low latency and high throughput between nodes, which is typical of HPC jobs.[^s1][^s2] A cluster group cannot span Availability Zones, though it can span peered VPCs in the same Region.[^s2] With enhanced networking, a single flow between instances in the group can reach up to 10 Gbps, versus up to 5 Gbps outside a cluster group; traffic to the internet or over Direct Connect stays limited to 5 Gbps.[^s2]

**Partition** groups divide instances into logical partitions, each on its own racks with separate network and power, and no two partitions share a rack.[^s2] A hardware fault therefore hits only one partition.[^s2] This suits replicated systems such as HDFS, HBase, Cassandra and Kafka: AWS suggests matching the partition count to the replication factor (three partitions for HDFS with three replicas) and letting rack-aware software treat the partition number as its rack ID.[^s1][^s4] You can let EC2 distribute instances or target a specific partition at launch.[^s2][^s4]

**Spread** groups put each instance on distinct hardware, which suits a small number of critical instances that must not fail together, and they tolerate mixed instance types or instances launched over time.[^s2] In Regions the spread is at rack level; on AWS Outposts you can also choose host-level spread.[^s2]

**Precision time** groups place instances on hardware with direct access to AWS's high-precision time sources, giving an enhanced Amazon Time Sync Service endpoint and, on Linux, a PTP hardware clock and hardware packet timestamping.[^s2] AWS announced on 2026-06-30 that this extended microsecond-accurate time to 26 more instance types across all commercial Regions, and that a precision time group can be paired with a cluster group when low latency is also needed.[^s5] A precision time group is attached as the parent of a cluster group at creation, and the parent cannot be deleted while that link exists.[^s2]

## Quotas, rules and gotchas

Rules and limits as documented on 2026-11-12:[^s1][^s2]

- An instance belongs to at most one placement group, and groups cannot be merged.[^s1]
- Partition groups allow at most **7 partitions per Availability Zone** (only 2 when using Dedicated Instances); the instance count is bounded only by account limits.[^s2]
- Spread groups in a Region allow at most **7 running instances per Availability Zone**, so a three-AZ Region gives 21; for more, use several spread groups, accepting that instances in different groups are not guaranteed to be apart.[^s2] Spread groups do not support Dedicated Instances.[^s2]
- Dedicated Hosts cannot be launched in placement groups, nor can Spot Instances set to stop or hibernate on interruption.[^s1]
- Cluster groups exclude burstable types such as T2, plus Mac1 and M7i-flex.[^s2] Launch all instances in one request with one instance type; adding instances later raises the odds of an insufficient-capacity error.[^s2]
- Only On-Demand Capacity Reservations can reserve capacity inside a cluster group; zonal Reserved Instances cannot, Capacity Blocks for ML don't support placement groups, and no reservation reserves capacity in partition or spread groups.[^s2]
- Partition and spread launches fail if EC2 lacks enough distinct hardware; retrying later can succeed.[^s2]

## Sharing across accounts

An owner can share a placement group through AWS Resource Access Manager with specific accounts, an OU or the whole organization.[^s3] Sharing does not relax the 7-partition or 7-instance limits, participants must refer to the group by ID in the CLI, and each account sees only its own instances.[^s3] Because AZ names map differently per account, use AZ IDs.[^s3]

## When to use which

Choose cluster for tightly coupled, latency-sensitive workloads in one AZ; partition for large replicated data stores that need fault isolation across hundreds of instances; spread for a handful of critical nodes; and precision time when ordering events by clock matters.[^s2][^s4][^s5] Cluster placement trades away AZ-level resilience for speed, so pair it with a multi-AZ design if availability matters.[^s2]

## Connections

Placement groups are a feature of **Amazon EC2** whose rules depend on **EC2 instance types**, and they are **scoped to** **Availability Zones** (cluster to one AZ; partition and spread per AZ).[^s1][^s2] They **integrate with** **On-Demand Capacity Reservations** for guaranteed cluster capacity and with **AWS Resource Access Manager** for sharing.[^s2][^s3] Spread groups offer host-level placement on **AWS Outposts**, and precision time groups **depend on** the **AWS Nitro System** reference clocks.[^s2][^s5] Partition and spread groups support the fault-isolation goals of the **Reliability Pillar**, and **Spot Instances** come with the interruption-behaviour restriction above.[^s1][^s4]

## Sources
[^s1]: Amazon Web Services, "Placement groups for your Amazon EC2 instances". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Placement strategies for your placement groups". https://docs.aws.amazon.com/us_en/AWSEC2/latest/UserGuide/placement-strategies.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "Shared placement groups". https://docs.aws.eu/AWSEC2/latest/UserGuide/share-placement-group.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, AWS Compute Blog, "Using partition placement groups for large distributed and replicated workloads in Amazon EC2" (2019-04-22). https://aws.amazon.com/blogs/compute/using-partition-placement-groups-for-large-distributed-and-replicated-workloads-in-amazon-ec2 (accessed 2026-11-12)
[^s5]: Amazon Web Services, What's New with AWS, "Amazon Time Sync Service adds microsecond-accurate time to 26 additional EC2 instance types" (2026-06-30). https://aws.amazon.com/about-aws/whats-new/2026/06/ec2-time-sync-precision-time-placement-group (accessed 2026-11-12)
