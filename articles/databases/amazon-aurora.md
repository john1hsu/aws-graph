---
id: kt:amazon-aurora
type: Service
title: Amazon Aurora
aliases: [Aurora, Aurora MySQL, Aurora PostgreSQL]
domain: databases
summary: >-
  AWS's MySQL- and PostgreSQL-compatible relational database engine, managed through Amazon RDS,
  that separates compute (a writer plus up to 15 reader instances) from a distributed cluster
  volume replicated six ways across Availability Zones and growing automatically to as much as 256 TiB.
verified: 2026-11-10
status: current
broader: [kt:purpose-built-databases, kt:amazon-rds]
related: [kt:aurora-serverless-v2, kt:aurora-global-database, kt:rds-proxy]
relations:
  - {predicate: governedBy, object: kt:aurora-quotas}
  - {predicate: alternativeTo, object: kt:amazon-dynamodb}
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: dependsOn, object: kt:amazon-vpc}
  - {predicate: pricedBy, object: kt:reserved-instances}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is Amazon Aurora?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "High availability for Amazon Aurora"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.AuroraHighAvailability.md
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Quotas and constraints for Amazon Aurora"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Amazon Aurora storage"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Amazon Aurora Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/rds/aurora/pricing/
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "Amazon Aurora MySQL database clusters now support up to 256 TiB of storage volume"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-aurora-mysql-database-clusters-256-tib-storage
    type: official-blog
    accessed: 2026-11-10
  - id: s7
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate"
    publisher: Amazon Web Services (AWS Certification)
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# Amazon Aurora

Amazon Aurora is a fully managed relational database engine that AWS treats as part of Amazon RDS. It comes in two editions, Aurora MySQL and Aurora PostgreSQL, so existing drivers, tools and application code for those engines generally work unchanged. AWS's user guide claims up to six times the throughput of stock MySQL and of stock PostgreSQL on similar hardware (a vendor figure; real results depend on the workload). Provisioning, patching, backup, failure detection and repair use the same RDS console, CLI and APIs, but they act on whole clusters rather than single instances. AWS runs the infrastructure; query tuning and performance monitoring remain the customer's job.[^s1]

## How it works

Aurora separates compute from storage. A DB cluster has one primary (writer) instance and up to 15 read-only Aurora Replicas, also called reader instances, all attached to a shared **cluster volume**.[^s2] Writes to the primary are synchronously replicated to six storage nodes spread across Availability Zones in the Region, and those copies exist even if every instance sits in one AZ. Replicas read from the same volume and are fed asynchronously, so they can absorb `SELECT` traffic without slowing the writer.[^s2]

Applications connect through endpoints rather than instance hostnames. The **cluster endpoint** always points at the current writer, so the connection string survives a failover.[^s2] When the primary fails, Aurora promotes a replica, picking by a priority tier from 0 (highest) to 15 (lowest) and then by instance size. Service is typically back in under 60 seconds and often under 30. With no replicas, Aurora recreates the primary in the same AZ, which typically takes under 10 minutes. AWS therefore recommends at least one replica in a different AZ. For cross-Region resilience, Aurora global databases replicate asynchronously from a primary Region to secondary Regions.[^s2]

The cluster volume grows automatically as data is added. On engine versions with dynamic resizing, dropping or truncating tables shrinks the allocated space again.[^s4] The maximum volume size doubled from 128 TiB to 256 TiB in July 2025, and you pay only for storage actually used.[^s6]

## Quotas, pricing and gotchas

Default per-account, per-Region quotas (as of 2026-11, from the Aurora user guide) are 40 DB clusters and 40 DB instances (20 in ap-south-1), both adjustable through Service Quotas. Replicas count against the DB-instance quota, and the user guide says the 15-replica limit can't be raised for Aurora.[^s3] The 256 TiB volume limit applies only to Aurora PostgreSQL 17.5+, 16.9+ and 15.13+ and to Aurora MySQL 3.10+. Older versions stop at 128 TiB. Individual tables are capped at 64 TiB on Aurora MySQL and 32 TiB on Aurora PostgreSQL.[^s3]

Storage is billed under one of two cluster configurations. **Aurora Standard** charges for instances, storage and each I/O request. **Aurora I/O-Optimized** has no read/write I/O charges in exchange for higher storage and compute rates.[^s4] In the us-east-1 pricing examples (as of 2026-11), Standard storage is $0.10 per GB-month plus $0.20 per million I/Os, and I/O-Optimized storage is $0.225 per GB-month with no I/O charge. Aurora serverless capacity costs $0.12 per ACU-hour on Standard and $0.156 per ACU-hour on I/O-Optimized.[^s5] AWS's rule of thumb is to choose I/O-Optimized once I/O makes up about 25% or more of total Aurora spend.[^s4]

You can move from I/O-Optimized back to Standard at any time, but from Standard to I/O-Optimized only once every 30 days. On NVMe-based instance classes, switching in either direction restarts the engine.[^s4] Existing Reserved Instances still apply after a switch to I/O-Optimized, but AWS says you need about 30% more RI coverage to keep the full discount.[^s5] Backup storage is billed separately.[^s4]

## When to use it (and when not)

Aurora suits relational workloads that have outgrown a single RDS instance or that need fast failover, many read replicas or very large volumes, and that run on MySQL or PostgreSQL.[^s1][^s2] For Oracle, SQL Server or Db2, or for a small database where Aurora's cluster model costs more than it adds, standard Amazon RDS is the simpler choice. For key-value access at massive scale with no joins, a NoSQL store such as Amazon DynamoDB is the usual alternative. AWS provides migration paths from RDS for MySQL and RDS for PostgreSQL, including snapshots and one-way replication.[^s1]

## Connections

Aurora sits under **purpose-built databases** and is managed through **Amazon RDS**, which it shares consoles, APIs and RDS Proxy with.[^s1][^s2] It is **governedBy** the Aurora quotas above and **scopedTo** **Availability Zones**, because both the six storage copies and replica placement are AZ-based.[^s2][^s3] It runs inside an **Amazon VPC** and can be **pricedBy** **Reserved Instances**.[^s5] The related nodes, Aurora Serverless v2, Aurora global databases and RDS Proxy, are features that change how capacity, Regions and connections behave.[^s2][^s5] Amazon Aurora and Amazon Aurora Serverless both appear in the in-scope services list for AWS Certified Solutions Architect - Associate (SAA-C03).[^s7]

## Sources
[^s1]: Amazon Web Services, "What is Amazon Aurora?". https://docs.aws.amazon.com/en_us/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html (accessed 2026-11-10)
[^s2]: Amazon Web Services, "High availability for Amazon Aurora". https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.AuroraHighAvailability.md (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Quotas and constraints for Amazon Aurora". https://docs.aws.amazon.com/en_us/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Amazon Aurora storage". https://docs.aws.amazon.com/en_us/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Amazon Aurora Pricing". https://aws.amazon.com/rds/aurora/pricing/ (accessed 2026-11-10)
[^s6]: Amazon Web Services, "Amazon Aurora MySQL database clusters now support up to 256 TiB of storage volume". https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-aurora-mysql-database-clusters-256-tib-storage (accessed 2026-11-10)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-10)
