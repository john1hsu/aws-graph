---
id: kt:availability-zones
type: InfrastructureConcept
title: Availability Zones
aliases: [Availability Zone, AWS Availability Zones, AZ, AZs, AZ ID]
domain: global-infrastructure
summary: >-
  An Availability Zone is one or more discrete data centers with independent power, cooling and
  networking inside an AWS Region; every Region has at least three, and spreading a workload across
  AZs is AWS's baseline for high availability.
verified: 2026-11-10
status: concept
broader: [kt:aws-global-infrastructure]
related: [kt:reliability-pillar, kt:disaster-recovery-strategies, kt:elastic-load-balancing, kt:amazon-application-recovery-controller]
relations:
  - {predicate: partOf, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:local-zones}
sources:
  - id: s1
    title: "AWS Availability Zones - AWS Regions and Availability Zones"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-availability-zones.md
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Availability Zones - AWS Fault Isolation Boundaries (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/availability-zones.html
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Zonal services - AWS Fault Isolation Boundaries (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/zonal-services.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Availability Zone IDs for your AWS resources - AWS Resource Access Manager"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ram/latest/userguide/working-with-az-ids
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "REL10-BP01 Deploy the workload to multiple locations - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_fault_isolation_multiaz_region_system.html
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "Starting, updating, or canceling a zonal shift - Amazon Application Recovery Controller (ARC)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/r53recovery/latest/dg/arc-zonal-shift.start-cancel.md
    type: official-docs
    accessed: 2026-11-10
  - id: s7
    title: "Improving performance and reducing cost using Availability Zone Affinity"
    publisher: Amazon Web Services (AWS Architecture Blog)
    url: https://aws.amazon.com/blogs/architecture/improving-performance-and-reducing-cost-using-availability-zone-affinity
    type: official-blog
    accessed: 2026-11-10
  - id: s8
    title: "Overview of Data Transfer Costs for Common Architectures"
    publisher: Amazon Web Services (AWS Architecture Blog)
    url: https://aws.amazon.com/blogs/architecture/overview-of-data-transfer-costs-for-common-architectures
    type: official-blog
    accessed: 2026-11-10
  - id: s9
    title: "Welcome - Amazon Application Recovery Controller zonal shift API Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/arc-zonal-shift/latest/api/Welcome.html
    type: official-docs
    accessed: 2026-11-10
created: 2026-11-10
---

# Availability Zones

An Availability Zone (AZ) is one or more discrete data centers inside an AWS Region, each with its own independent and redundant power, networking and connectivity.[^s2] Every Region has at least three AZs, which is what lets you build highly available applications without leaving the Region.[^s1] Where a Region is the unit you choose for latency and data residency, the AZ is the unit you design around for everyday fault tolerance: lose one, and a correctly built workload keeps serving from the others.[^s2][^s5]

## How it works

AZs in a Region sit up to about 100 km (60 miles) apart, far enough that floods, fires, earthquakes, utility power loss or fiber cuts should not hit two at once. They do not share generators or cooling, and each is designed to draw from a different power substation.[^s2] Redundant dedicated metro fiber links them with enough bandwidth and low enough latency (single-digit milliseconds) for synchronous replication, and each AZ reaches the internet through two transit centers.[^s2][^s5] AWS calls this isolation **Availability Zone Independence**, and it also staggers software deployments so two AZs in a Region are not updated at the same time.[^s2]

Services are either regional or **zonal**. Zonal services, such as Amazon EC2 and Amazon EBS, let you pick the AZ for each resource; their data planes run independently per AZ, and EC2 also has zonal control planes behind a regional endpoint.[^s3] Many managed services replicate across AZs for you (S3, EFS, Aurora, DynamoDB, SQS, Kinesis Data Streams); RDS, Redshift and ElastiCache need Multi-AZ switched on; and EBS volumes and instance storage are yours to replicate.[^s5]

## Names, IDs and gotchas

- **AZ names vs AZ IDs.** A name is the Region code plus a letter (`us-east-2a`); an AZ ID such as `use2-az1` is the same physical location in every account.[^s1] For accounts created before November 2025, AWS mapped names to physical AZs independently per account in its oldest Regions, so `us-east-1a` in two accounts may differ; accounts created from November 2025 get the same mapping.[^s1] When sharing subnets through AWS RAM or coordinating across accounts, compare AZ IDs, not names.[^s4]
- **AZ count varies.** As of 2026-11, AWS's tables show six AZ IDs in us-east-1 and four in us-west-2, while newer accounts can use only two AZs in US West (N. California).[^s1] AWS may also stop new zonal resources in a **constrained** AZ unless you already run resources there, and can later hide it from new accounts, so two accounts can see different AZ counts in one Region.[^s1]
- **Cross-AZ traffic costs money.** Data transfer within one AZ is free, while traffic crossing an AZ boundary typically incurs a charge (see the EC2 and RDS pricing pages for current per-GB rates); replication between an RDS Multi-AZ primary and standby is not charged.[^s8] Cross-AZ charges apply in both directions.[^s7]
- **Latency.** AWS's own test figures put cross-AZ round trips in the low milliseconds versus sub-millisecond within an AZ, which compounds when a request crosses AZs several times.[^s7]

## Designing for AZ failure

The Well-Architected reliability guidance makes deploying production across at least two AZs the baseline; running in a single AZ is an anti-pattern, and load balancers should route traffic only to healthy AZs.[^s5] Two refinements follow:

- **Static stability.** Pre-provision enough capacity that losing one AZ needs no scaling action. AWS's example: a workload needing six instances runs three in each of three AZs (nine total, about 50% extra), because auto scaling during an event depends on the EC2 control plane and takes time.[^s3]
- **AZ affinity and evacuation.** Keeping a request inside one AZ (zonal DNS names, cross-zone load balancing disabled, AZ-aware service discovery) cut latency and data transfer cost in AWS's example by about 40% each, at the price of planning explicit failover.[^s7] To evacuate an impaired AZ, Amazon Application Recovery Controller **zonal shift** moves load balancer traffic away from it for 1 minute up to three days, renewable, and **zonal autoshift** lets AWS start that shift from its own telemetry.[^s6][^s9]

Go multi-Region only when AZ-level redundancy can't meet your recovery objectives or residency rules require it.[^s5]

## Connections

- **Part of an AWS Region** (`kt:aws-regions`), which contains three or more AZs, and **broader: AWS global infrastructure** (`kt:aws-global-infrastructure`).[^s1][^s2]
- **Contrasts with Local Zones** (`kt:local-zones`), which bring select data planes closer to users but keep control planes, and a dependency on a parent AZ, in the Region.[^s3]
- **Related to the Reliability Pillar** (`kt:reliability-pillar`) and **disaster recovery strategies** (`kt:disaster-recovery-strategies`), where multi-AZ is the first resilience step.[^s5]
- **Related to Elastic Load Balancing** (`kt:elastic-load-balancing`), which spreads and steers traffic across AZs, and **Amazon Application Recovery Controller** (`kt:amazon-application-recovery-controller`), whose zonal shift evacuates one.[^s6][^s7]

## Sources
[^s1]: Amazon Web Services, "AWS Availability Zones - AWS Regions and Availability Zones". https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-availability-zones.md (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Availability Zones - AWS Fault Isolation Boundaries". https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/availability-zones.html (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Zonal services - AWS Fault Isolation Boundaries". https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/zonal-services.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Availability Zone IDs for your AWS resources - AWS Resource Access Manager". https://docs.aws.amazon.com/ram/latest/userguide/working-with-az-ids (accessed 2026-11-10)
[^s5]: Amazon Web Services, "REL10-BP01 Deploy the workload to multiple locations". https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_fault_isolation_multiaz_region_system.html (accessed 2026-11-10)
[^s6]: Amazon Web Services, "Starting, updating, or canceling a zonal shift - Amazon Application Recovery Controller (ARC)". https://docs.aws.amazon.com/r53recovery/latest/dg/arc-zonal-shift.start-cancel.md (accessed 2026-11-10)
[^s7]: Amazon Web Services, AWS Architecture Blog, "Improving performance and reducing cost using Availability Zone Affinity". https://aws.amazon.com/blogs/architecture/improving-performance-and-reducing-cost-using-availability-zone-affinity (accessed 2026-11-10)
[^s8]: Amazon Web Services, AWS Architecture Blog, "Overview of Data Transfer Costs for Common Architectures". https://aws.amazon.com/blogs/architecture/overview-of-data-transfer-costs-for-common-architectures (accessed 2026-11-10)
[^s9]: Amazon Web Services, "Welcome - Amazon Application Recovery Controller zonal shift API Reference". https://docs.aws.amazon.com/arc-zonal-shift/latest/api/Welcome.html (accessed 2026-11-10)
