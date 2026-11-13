---
id: kt:aurora-serverless-v2
type: Feature
title: Amazon Aurora Serverless
aliases: [Aurora Serverless v2, Aurora serverless, Amazon Aurora Serverless v2, db.serverless]
domain: databases
summary: >-
  An on-demand, autoscaling capacity mode for Amazon Aurora in which each writer or reader runs as a
  db.serverless instance that scales in place, in steps as small as 0.5 Aurora capacity units (ACUs),
  and can pause at 0 ACUs when idle; billed per second of ACU usage.
verified: 2026-11-13
status: current
broader: [kt:amazon-aurora]
related: [kt:aurora-global-database, kt:rds-proxy, kt:aws-serverless-services]
relations:
  - {predicate: governedBy, object: kt:aurora-quotas}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: dependsOn, object: kt:amazon-vpc}
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "How Aurora serverless works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless-v2.how-it-works.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Scaling to Zero ACUs with automatic pause and resume for Aurora serverless"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless-v2-auto-pause.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "Requirements and limitations for Aurora serverless"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless-v2.requirements.html
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "Amazon Aurora Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/rds/aurora/pricing/
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "Amazon Aurora serverless now scales faster to support agentic AI and other bursty workloads"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/08/aurora-serverless-instant-12-acu-scaling/
    type: official-blog
    accessed: 2026-11-13
  - id: s6
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate"
    publisher: Amazon Web Services (AWS Certification)
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-13
created: 2026-11-13
---

# Amazon Aurora Serverless

Aurora Serverless is the autoscaling compute option of Amazon Aurora. AWS's current documentation simply calls it "Aurora serverless"; the "v2" survives in URLs and API names such as `ServerlessV2ScalingConfiguration`, and the docs warn not to confuse it with Aurora Serverless v1, a deprecated product with a different architecture.[^s1] Instead of choosing an instance class, you add instances of class `db.serverless` to an `aurora-mysql` or `aurora-postgresql` cluster and give the cluster a capacity range.[^s3]

## How it works

Capacity is measured in **Aurora capacity units (ACUs)**; each ACU is roughly 2 GiB of memory with matching CPU and networking. Aurora watches CPU, memory and network load and scales each writer and reader up or down in steps as small as 0.5 ACU, with bigger steps at higher capacity. Scaling happens in place while connections, transactions and locks stay open, so clients need no new endpoint, and it emits no discrete events: you watch the CloudWatch metrics `ServerlessDatabaseCapacity` and `ACUUtilization`.[^s1] Since an August 2026 update, clusters on platform version 3 or 4 can reach up to 12 ACUs within a second when a burst arrives, with no configuration change.[^s5]

All instances share one minimum/maximum range, but each has its own current capacity. Readers in failover tiers 0–1 track the writer's capacity so they can take over on failover; readers in tiers 2–15 scale independently, which suits reporting replicas. A cluster can mix provisioned and serverless instances, for example a large provisioned writer with serverless readers. Storage is separate from compute and keeps its six copies across three AZs whatever the compute choice.[^s1]

## Scaling to zero

Setting the minimum to **0 ACUs** turns on automatic pause and resume (Aurora MySQL 3.08.0+, Aurora PostgreSQL 13.15/14.12/15.7/16.3 or higher). An instance pauses after an idle timeout between 300 seconds (the default) and 86,400 seconds, and a new connection resumes it in about 15 seconds; after more than 24 hours paused, resuming can take 30 seconds or more, so client connect timeouts should allow for that. While paused the instance charge is zero, but storage and other cluster-level charges continue.[^s2]

Several things silently prevent pausing: any open user connection, an attached RDS Proxy, binlog or logical replication, a zero-ETL integration, being the primary of an Aurora global database (secondaries never pause), or provisioned instances in the cluster. Scheduled jobs (`pg_cron`, the MySQL event scheduler) do not wake a paused instance, so AWS suggests, among other options, a scheduled Lambda function to resume it.[^s2]

## Quotas, pricing and gotchas

- **Capacity range (as of 2026-11):** 0 or 0.5 ACU minimum depending on version; maximum 256 ACUs for Aurora MySQL and 384 ACUs for Aurora PostgreSQL on supporting versions and platform version 4. Platform versions are upgraded automatically, and the effective range is capped by the lower of engine and platform version.[^s1][^s3]
- **Pricing (us-east-1 example, as of 2026-11):** $0.12 per ACU-hour on Aurora Standard and $0.156 per ACU-hour on Aurora I/O-Optimized, billed per second.[^s4]
- **Cost envelope:** an idle cluster costs about n × minimum ACUs and a busy one at most n × maximum, where n counts writers and readers.[^s1]
- **Unsupported:** Database Activity Streams, Aurora PostgreSQL cluster cache management, and Aurora Auto Scaling of replicas. Lowering the maximum on Aurora PostgreSQL can be rolled back if memory-hungry parameters such as `max_connections` block scale-down.[^s3]
- **Sizing:** set the minimum high enough to keep the working set in the buffer pool, because the cache shrinks as an instance scales down.[^s1]

## When to use it (and when not)

Aurora Serverless fits spiky or unpredictable load, dev/test and multi-tenant databases that sit idle, and readers that only occasionally run heavy queries.[^s1][^s2] For steady, predictable load a provisioned instance (optionally covered by Reserved Instances) is usually simpler to reason about, and you can mix both in one cluster.[^s1] If you need a database that never pauses for replication, a proxy or a global primary, plan on a nonzero minimum.[^s2]

## Connections

The node is a capacity mode of **Amazon Aurora** (its broader node) and is bounded by the **Aurora quotas** on capacity range.[^s1][^s3] It is **monitoredBy** **Amazon CloudWatch** through capacity and utilisation metrics, runs in an **Amazon VPC** like every Aurora cluster, and **integratesWith** **AWS Lambda**, which AWS suggests for waking paused instances on a schedule.[^s1][^s2] **RDS Proxy** and **Aurora global databases** are related because both work with serverless instances yet keep them from pausing.[^s2] It belongs with the other **AWS serverless services**, and "Amazon Aurora Serverless" is listed among the in-scope database services for AWS Certified Solutions Architect - Associate (SAA-C03).[^s6]

## Sources
[^s1]: Amazon Web Services, "How Aurora serverless works". https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless-v2.how-it-works.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Scaling to Zero ACUs with automatic pause and resume for Aurora serverless". https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless-v2-auto-pause.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Requirements and limitations for Aurora serverless". https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless-v2.requirements.html (accessed 2026-11-13)
[^s4]: Amazon Web Services, "Amazon Aurora Pricing". https://aws.amazon.com/rds/aurora/pricing/ (accessed 2026-11-13)
[^s5]: Amazon Web Services, "Amazon Aurora serverless now scales faster to support agentic AI and other bursty workloads" (What's New, 2026-08-05). https://aws.amazon.com/about-aws/whats-new/2026/08/aurora-serverless-instant-12-acu-scaling/ (accessed 2026-11-13)
[^s6]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-13)
