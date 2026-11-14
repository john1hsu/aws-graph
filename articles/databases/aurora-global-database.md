---
id: kt:aurora-global-database
type: Feature
title: Amazon Aurora Global Database
aliases: [Aurora Global Database, Aurora global databases, Aurora global cluster]
domain: databases
summary: >-
  An Amazon Aurora feature that spans one database across AWS Regions: a single writable primary
  cluster plus up to 10 read-only secondary clusters, kept in sync by storage-level asynchronous
  replication that typically lags under a second, with switchover and failover to move the primary.
verified: 2026-11-14
status: current
broader: [kt:amazon-aurora]
related: [kt:aurora-serverless-v2, kt:rds-proxy, kt:reliability-pillar]
relations:
  - {predicate: implements, object: kt:disaster-recovery-strategies}
  - {predicate: alternativeTo, object: kt:dynamodb-global-tables}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: governedBy, object: kt:aurora-quotas}
sources:
  - id: s1
    title: "Using Amazon Aurora Global Database"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/AmazonRDS/latest/AuroraUserGuide/aurora-global-database.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "Using switchover or failover in an Amazon Aurora global database"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database-disaster-recovery.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Aurora global databases (Replication options for Amazon Aurora)"
    publisher: Amazon Web Services (AWS Prescriptive Guidance)
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/aurora-replication-options/aurora-global-database.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Amazon Aurora Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/rds/aurora/pricing
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "Amazon Aurora Global Database introduces support for up to 10 secondary Region clusters"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/05/amazon-aurora-global-database-support-10-secondary-region-clusters
    type: official-blog
    accessed: 2026-11-14
  - id: s6
    title: "Amazon Aurora Global Database introduces support for up to 10 secondary Regions"
    publisher: Amazon Web Services (AWS Database Blog)
    url: https://aws.amazon.com/blogs/database/amazon-aurora-global-database-introduces-support-for-up-to-10-secondary-regions
    type: official-blog
    accessed: 2026-11-14
  - id: s7
    title: "Amazon Aurora Global Database now supports managed minor version upgrades"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/02/amazon-aurora-global-database-managed-minor
    type: official-blog
    accessed: 2026-11-14
created: 2026-11-14
---

# Amazon Aurora Global Database

Amazon Aurora Global Database stretches one Aurora MySQL- or Aurora PostgreSQL-compatible database across several AWS Regions. It has one **primary** Region, where all writes happen, and up to **10 read-only secondary Regions**, each holding its own Aurora cluster that applications can read from locally.[^s1] The ceiling was raised from five secondaries to ten in May 2025; some older AWS guidance still quotes five.[^s5][^s3] The feature serves two jobs: low-latency reads for users and offices in many Regions, and recovery from the loss of an entire Region.[^s1][^s6]

## How it works

Replication runs in Aurora's **cluster storage layer** on dedicated infrastructure rather than in the database engine, so it barely touches the primary's compute and is typically under one second behind.[^s1][^s3] It is asynchronous: commits on the primary do not wait for secondaries, so write latency is unaffected by Region distance, but secondaries can trail the primary.[^s3][^s6] Each secondary cluster scales independently with up to 16 reader instances (one more than the usual 15 for a single Aurora cluster), and those readers may be Aurora Serverless instances.[^s1]

Applications write through the **global writer endpoint**, which always resolves to the current primary's writer, even after the primary moves.[^s1][^s2] With **write forwarding** turned on, a secondary can pass writes to the primary on the application's behalf; without it, only the primary accepts writes.[^s1] AWS describes a "follow the sun" pattern in which you periodically switch the primary to the Region where most activity is happening, so both reads and writes stay local.[^s6]

## Switchover and failover

- **Switchover** (formerly "managed planned failover") is for healthy clusters: Aurora waits for the chosen secondary to catch up, then swaps roles with zero data loss. On recent engine versions (for example Aurora MySQL 3.09+ or Aurora PostgreSQL 16.8+), write downtime is typically under 30 seconds.[^s2]
- **Managed failover** (`--allow-data-loss`) is for a Regional outage. It promotes a secondary without waiting for sync, so RPO is non-zero and typically measured in seconds; promotion usually takes a few minutes. Aurora tries to fence writes on the old primary and snapshot its volume, and re-adds the old Region as a secondary once it recovers; rebuilding it can take from minutes to hours depending on volume size and distance.[^s2]
- **Manual failover** detaches a secondary into a standalone cluster, for cases where managed failover is not possible, such as mismatched engine versions.[^s2]

For Aurora PostgreSQL, the `rds.global_db_rpo` parameter (20 seconds or more) sets an upper bound on data loss: if every secondary lags beyond it, commits on the primary block until one catches up. Watch lag with the CloudWatch metric `AuroraGlobalDBRPOLag` (or `AuroraGlobalDBReplicationLag` on older Aurora MySQL versions), and if clients cache DNS, lower the TTL to about 5 seconds so they follow the global writer endpoint quickly.[^s2]

## Quotas, pricing and gotchas

- **Limits (as of 2026-11):** 1 primary plus up to 10 secondary Regions; up to 16 readers per secondary cluster. Only certain Regions and engine versions support the feature.[^s1][^s5]
- **Pricing (as of 2026-11):** on top of normal instance, storage and I/O charges, you pay for **replicated write I/Os** in each secondary at $0.20 per million (AWS's example uses a US West (Oregon) secondary; this applies under both Aurora Standard and I/O-Optimized), plus cross-Region data transfer from the primary at $0.02 per GB in AWS's US East (N. Virginia) example.[^s4]
- **Unsupported or restricted:** Backtrack, Aurora Auto Scaling on secondary clusters, and Secrets Manager-managed master passwords (turn that off before adding a Region). Clusters cannot be stopped or started individually, and a restart or failover of the primary writer also restarts the readers in secondary Regions.[^s1]
- **Upgrades:** managed switchover and failover need matching engine versions in every Region, and automatic minor version upgrades have no effect on global clusters.[^s1][^s2] Since February 2026, Aurora PostgreSQL global clusters can run a single managed minor version upgrade across all Regions.[^s7]

## When to use it (and when not)

Choose Global Database when a relational workload needs Region-level disaster recovery with an RPO of seconds and an RTO of minutes, or local read latency in several geographies.[^s1][^s2] It is still single-writer: users far from the primary pay cross-Region latency on every write unless you switch over, so designs that need writes accepted locally in every Region should look at other options, such as DynamoDB global tables, elsewhere in this graph.[^s6] If an application only runs in one Region and needs no cross-Region recovery, the replicated-write I/O and data transfer charges buy nothing.[^s4]

## Connections

Global Database is a feature of **Amazon Aurora** (its broader node) and is **scopedTo** **AWS Regions**, since each member cluster lives in one Region.[^s1] It **implements** cross-Region **disaster recovery strategies** and supports the **reliability pillar** through switchover and failover.[^s2] It is **monitoredBy** **Amazon CloudWatch** lag metrics and **governedBy** the **Aurora quotas** on secondary Regions and readers.[^s1][^s2] **Aurora Serverless** can supply its readers, while **RDS Proxy** has specific limitations with global databases.[^s1] **DynamoDB global tables** is linked as an **alternativeTo** for globally distributed data when a single write Region is too limiting.[^s6]

## Sources
[^s1]: Amazon Web Services, "Using Amazon Aurora Global Database". https://docs.aws.amazon.com/en_us/AmazonRDS/latest/AuroraUserGuide/aurora-global-database.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "Using switchover or failover in an Amazon Aurora global database". https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database-disaster-recovery.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, "Aurora global databases", AWS Prescriptive Guidance. https://docs.aws.amazon.com/prescriptive-guidance/latest/aurora-replication-options/aurora-global-database.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, "Amazon Aurora Pricing". https://aws.amazon.com/rds/aurora/pricing (accessed 2026-11-14)
[^s5]: Amazon Web Services, "Amazon Aurora Global Database introduces support for up to 10 secondary Region clusters" (What's New, 2025-05-21). https://aws.amazon.com/about-aws/whats-new/2025/05/amazon-aurora-global-database-support-10-secondary-region-clusters (accessed 2026-11-14)
[^s6]: Amazon Web Services, "Amazon Aurora Global Database introduces support for up to 10 secondary Regions", AWS Database Blog. https://aws.amazon.com/blogs/database/amazon-aurora-global-database-introduces-support-for-up-to-10-secondary-regions (accessed 2026-11-14)
[^s7]: Amazon Web Services, "Amazon Aurora Global Database now supports managed minor version upgrades" (What's New, 2026-02-10). https://aws.amazon.com/about-aws/whats-new/2026/02/amazon-aurora-global-database-managed-minor (accessed 2026-11-14)
