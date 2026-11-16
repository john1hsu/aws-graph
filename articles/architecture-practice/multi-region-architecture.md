---
id: kt:multi-region-architecture
type: Pattern
title: Multi-Region architecture
aliases: [multi-Region architectures, multi-Region deployment, multi-Region workloads, cross-Region architecture]
domain: architecture-practice
summary: >-
  An architecture pattern in which a workload is deployed in two or more AWS Regions, with replicated data
  and Region-independent components, to bound recovery from a Regional impairment, meet data residency
  needs or serve users with lower latency; AWS recommends it only when a Multi-AZ design in one Region
  cannot meet the requirement.
verified: 2026-11-16
status: concept
broader: [kt:reliability-pillar, kt:aws-regions]
related: [kt:disaster-recovery-strategies, kt:dynamodb-global-tables, kt:aurora-global-database, kt:amazon-route-53, kt:aws-global-accelerator]
relations:
  - {predicate: dependsOn, object: kt:aws-regions}
  - {predicate: integratesWith, object: kt:amazon-application-recovery-controller}
  - {predicate: alternativeTo, object: kt:multi-az-architecture}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: integratesWith, object: kt:cell-based-architecture}
sources:
  - id: s1
    title: "AWS multi-Region fundamentals (AWS Prescriptive Guidance, PDF)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/prescriptive-guidance/latest/aws-multi-region-fundamentals/aws-multi-region-fundamentals.pdf
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "REL10-BP01 Deploy the workload to multiple locations - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_fault_isolation_multiaz_region_system.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Disaster recovery options in the cloud - Disaster Recovery of Workloads on AWS (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "About Region switch - Amazon Application Recovery Controller (ARC)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/r53recovery/latest/dg/region-switch-plans.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "Disaster Recovery (DR) Architecture on AWS, Part IV: Multi-site Active/Active"
    publisher: AWS Architecture Blog
    url: https://aws.amazon.com/blogs/architecture/disaster-recovery-dr-architecture-on-aws-part-iv-multi-site-active-active/
    type: official-blog
    accessed: 2026-11-16
created: 2026-11-16
---

# Multi-Region architecture

A multi-Region architecture runs a workload in two or more AWS Regions so that an impairment in one Region does not take the workload down. AWS's Prescriptive Guidance names three reasons customers adopt it: resilience and continuity of operations with a bounded recovery time, data residency or sovereignty requirements, and lower latency for users far from a single Region. The same guidance is blunt that these designs are hard, and that a poorly built multi-Region system can end up *less* available than a well-built single-Region one.[^s1]

## Multi-AZ first, multi-Region by exception

The Well-Architected Reliability Pillar treats deployment across at least two Availability Zones as the baseline for every production workload, and reserves multiple Regions for workloads with extreme resilience needs (critical infrastructure, health-related systems, stringent availability targets) that one Region cannot satisfy and that data-residency rules allow. Going multi-Region when Multi-AZ already meets requirements is listed as an anti-pattern; a cheaper middle ground is a secondary Region used only for disaster recovery in pilot-light or warm-standby form.[^s2] AWS suggests setting availability, RTO and RPO targets per workload tier; its example tiers range from a "Platinum" 99.99% target (about 52.6 minutes of downtime a year, RTO 15 minutes, RPO 5 minutes) down to "Silver" workloads where simply waiting for the Region to recover can be the right call.[^s1]

## Topologies

Multi-Region designs sit on the disaster-recovery spectrum. Backup and restore, pilot light and warm standby are active/passive: a standby Region serves no traffic until failover. In multi-site active/active, every Region serves traffic, so there is no failover as such; testing instead checks that traffic drains from a failed Region and that the survivors can absorb the full load. Active/active is the most complex and costly option, but it can bring recovery time close to zero for most disasters.[^s3]

## Data: the hard part

Because Regions are separated by a network that can partition, the design must trade consistency against availability. Asynchronous replication keeps write latency low but means in-flight writes can be lost on failover, so a reconciliation process is needed; synchronous replication avoids loss but can raise latency by an order of magnitude and typically needs three Regions with a two-of-three quorum.[^s1] Three write patterns are common:[^s5]

- **Write global (read local):** all writes go to one Region, which is promoted if it fails. Aurora global database fits this model and supports write forwarding from secondary Regions.[^s3]
- **Write local:** each Region accepts writes. DynamoDB global tables allow this, resolving conflicts with last-writer-wins, so concurrent updates to the same item can silently lose one write.[^s5]
- **Write partitioned:** each record has a "home" Region chosen by a key such as user ID, avoiding conflicts but requiring repartitioning when a Region is lost.[^s5]

Replication is not a backup: corruption or a malicious delete replicates too, so point-in-time backups remain necessary.[^s3]

## Dependencies, failover and gotchas

- **No cross-Region dependencies.** Each Region should run independently: deploy every microservice everywhere, avoid cross-Region calls, and keep certificates, secrets, AMIs, container images and parameters Region-specific (staggering certificate expiry so one lapse cannot hit every Region).[^s1] Well-Architected adds Region-scoped KMS keys for local data and multi-Region keys for replicated data, plus infrastructure as code such as CloudFormation StackSets to stop drift.[^s2]
- **Watch the failover path itself.** The Route 53 control plane is hosted in us-east-1, so AWS advises against relying on Route 53 control-plane changes for failover when us-east-1 is the primary Region. Prefer data-plane mechanisms during an event: Route 53 health checks, or ARC routing controls that act as on/off switches through a highly available data-plane API. Global Accelerator traffic dials avoid DNS caching but are a control-plane action.[^s1][^s3]
- **Quotas and capacity.** Scaling up a standby is itself a control-plane activity; confirm that service quotas in the standby Region allow full production capacity, or pre-provision it (hot standby).[^s3]
- **Cost.** A pre-provisioned standby roughly doubles infrastructure cost, before the extra operational and testing effort.[^s1]
- **Operations.** Deploy one Region at a time, build CloudWatch dashboards and alarms in every Region (CloudWatch is Regional), run canaries from the standby Region, automate the failover action but let a small predetermined group decide when to trigger it, and rehearse with the same runbook used for real events.[^s1]

## Orchestrating recovery

Amazon Application Recovery Controller's Region switch feature packages this runbook as a plan: workflows of steps and execution blocks that scale resources, switch databases and shift traffic, supporting active/passive and active/active plans between any two Regions where it is available. It re-evaluates plans every 30 minutes, compares service quotas across Regions, and writes a PDF report of each execution to S3.[^s4]

## Connections

Multi-Region architecture is a reliability pattern built on the isolation of AWS Regions and refines the Well-Architected Reliability Pillar.[^s2] Its active/passive and active/active forms are the upper end of disaster recovery strategies.[^s3] Data replication typically relies on DynamoDB global tables or Aurora global database; traffic is steered by Amazon Route 53, AWS Global Accelerator and Amazon Application Recovery Controller, and replicated data is encrypted with AWS KMS multi-Region keys.[^s2][^s5] Single-Region Multi-AZ design is the default alternative, and assigning client shards to different primary Regions links it to cell-based architecture.[^s1]

## Sources
[^s1]: Amazon Web Services, "AWS multi-Region fundamentals" (AWS Prescriptive Guidance). https://docs.aws.amazon.com/pdfs/prescriptive-guidance/latest/aws-multi-region-fundamentals/aws-multi-region-fundamentals.pdf (accessed 2026-11-16)
[^s2]: Amazon Web Services, "REL10-BP01 Deploy the workload to multiple locations", AWS Well-Architected Framework. https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_fault_isolation_multiaz_region_system.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Disaster recovery options in the cloud", Disaster Recovery of Workloads on AWS whitepaper. https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "About Region switch", Amazon Application Recovery Controller Developer Guide. https://docs.aws.amazon.com/r53recovery/latest/dg/region-switch-plans.html (accessed 2026-11-16)
[^s5]: Seth Eliot, "Disaster Recovery (DR) Architecture on AWS, Part IV: Multi-site Active/Active", AWS Architecture Blog. https://aws.amazon.com/blogs/architecture/disaster-recovery-dr-architecture-on-aws-part-iv-multi-site-active-active/ (accessed 2026-11-16)
