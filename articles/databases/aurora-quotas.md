---
id: kt:aurora-quotas
type: Limit
title: Amazon Aurora quotas
aliases: [Aurora quotas, Aurora limits, Aurora service quotas, Aurora quotas and constraints, Amazon Aurora service limits]
domain: databases
summary: >-
  The per-account, per-Region resource quotas (clusters, instances, snapshots, parameter groups, proxies),
  the shared RDS API token bucket, and the fixed engine constraints (cluster volume size, table size,
  naming rules) that bound Amazon Aurora deployments; most resource counts are adjustable, the
  engine constraints are not.
verified: 2026-11-16
status: current
broader: [kt:amazon-aurora]
related: [kt:aurora-serverless-v2, kt:aurora-global-database, kt:rds-proxy, kt:amazon-rds, kt:aurora-limitless-database]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:amazon-s3-quotas}
sources:
  - id: s1
    title: "Quotas and constraints for Amazon Aurora"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Amazon Aurora endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/aurora.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Request throttling for the Amazon RDS API"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-api-throttling.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Amazon Aurora MySQL database clusters now support up to 256 TiB of storage volume"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-aurora-mysql-database-clusters-256-tib-storage-volume
    type: official-blog
    accessed: 2026-11-16
created: 2026-11-16
---

# Amazon Aurora quotas

Amazon Aurora is bounded by three different kinds of limit, and confusing them is the usual source of surprises. First, **resource quotas** cap how many clusters, instances, snapshots, parameter groups and similar objects one AWS account can hold in one Region; most are adjustable through Service Quotas.[^s1] Second, **API rate limits** throttle control-plane calls through a token bucket that Aurora shares with the rest of Amazon RDS and that is managed outside Service Quotas.[^s3] Third, **engine constraints**, such as maximum cluster volume and table size, are fixed by the engine version and cannot be raised by any request.[^s1]

## Resource quotas (per account, per Region, as of 2026-11)

AWS lists the following defaults, which apply in every supported Region unless noted.[^s1][^s2]

- **DB clusters:** 40, adjustable.
- **DB instances:** 40 (20 in ap-south-1), adjustable. This count is shared: RDS, Aurora, Amazon Neptune and Amazon DocumentDB instances all draw from the same quota.[^s1]
- **Read replicas per primary:** 15. The table marks it adjustable, but the description states it cannot be adjusted for Aurora, so plan on 15 Aurora Replicas per cluster as a hard ceiling.[^s1][^s2]
- **Custom endpoints per DB cluster:** 5, adjustable; the General Reference notes the usage figure reflects whichever cluster in the account has the most.[^s2]
- **Manual DB cluster snapshots:** 100, adjustable. Snapshots managed by AWS Backup are counted as manual cluster snapshots but do not count against this quota.[^s1]
- **DB cluster parameter groups:** 50; **parameter groups:** 50 (20 in ap-south-1); **DB subnet groups:** 50 (20 in ap-south-1); **event subscriptions:** 20; **proxies:** 20; **IAM roles per cluster or instance:** 5 — all adjustable.[^s1]
- **Fixed limits:** 20 subnets per DB subnet group, 20 authorizations per DB security group and 100 integrations per Region cannot be raised.[^s1][^s2]
- **DB shard groups:** 5, adjustable; this applies only to Aurora Limitless Database.[^s2]

The RDS Data API quotas (4 MB request body, 1 MB result set, 1,000 requests per second, 500 concurrent requests per cluster-secret pair) are documented as applying to Aurora Serverless v1 clusters and are not adjustable.[^s1][^s2] Note that the 100,000 GB "total storage for all DB instances" quota does not apply to Aurora at all.[^s2]

## API throttling

All RDS API actions, Aurora's included, share one token bucket per account per Region: a maximum capacity of 300 tokens refilling at 12 per second, so a drained bucket takes about 25 seconds to refill.[^s3] Calls from the console, CLI, SDKs and third-party tooling all spend tokens, while calls AWS services such as CloudFormation make on your behalf do not.[^s3] Exceeding it returns `ThrottlingException: Rate exceeded`.[^s1][^s3] These limits are not in Service Quotas; an increase needs an AWS Support case backed by measured peak rates, and AWS recommends first caching `Describe*` results, using EventBridge events instead of polling, and adding jitter to scheduled jobs.[^s3]

## Engine constraints

The maximum cluster volume depends on engine version: 256 TiB for Aurora PostgreSQL 15.13, 16.9 and 17.5 or higher and Aurora MySQL 3.10 or higher; 128 TiB for earlier versions.[^s1] The 256 TiB ceiling arrived in July 2025; reaching it requires an engine upgrade, and you pay only for storage actually used.[^s4] Individual tables top out at 64 TiB on Aurora MySQL and 32 TiB on Aurora PostgreSQL, and Aurora MySQL tablespace IDs cannot exceed 2,147,483,647, which matters for workloads that constantly create and drop tables.[^s1] AWS suggests watching the `AuroraVolumeBytesLeftTotal` metric to track headroom.[^s1] Cluster identifiers are 1–63 letters, digits or hyphens starting with a letter, unique per account per Region; master passwords are 8–41 characters on Aurora MySQL and 8–99 on Aurora PostgreSQL.[^s1] Maximum connections are not a quota: they come from the instance class and engine parameters.[^s1]

## Gotchas and planning

- The shared 40-instance pool fills fast in accounts that also run RDS, Neptune or DocumentDB; request increases before migrations rather than during them.[^s1][^s3]
- Mumbai (ap-south-1) has lower defaults for instances, subnet groups, parameter groups, reserved instances and security groups.[^s2]
- A cluster that needs more than 15 readers, or more write throughput than one writer gives, needs a different design (sharding, Limitless Database, or another engine) rather than a quota request.[^s1][^s2]

## Connections

These limits sit under **Amazon Aurora**, and constrain its variants — **Aurora Serverless v2**, **Aurora Global Database** and **Aurora Limitless Database** (shard-group quota) — as well as **RDS Proxy** (20 proxies per Region).[^s1][^s2] The adjustable ones are requested through **Service Quotas**, and all are scoped to an account in one of the **AWS Regions**.[^s2] Aurora's control plane is that of **Amazon RDS**, which is why the API bucket is shared.[^s3] Unlike other quota nodes such as **Amazon S3 quotas**, much of what binds Aurora is a fixed engine size rather than an adjustable count.[^s1]

## Sources
[^s1]: Amazon Web Services, "Quotas and constraints for Amazon Aurora". https://docs.aws.amazon.com/en_us/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Amazon Aurora endpoints and quotas (AWS General Reference)". https://docs.aws.amazon.com/general/latest/gr/aurora.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Request throttling for the Amazon RDS API". https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-api-throttling.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Amazon Aurora MySQL database clusters now support up to 256 TiB of storage volume" (What's New, 2025-07-30). https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-aurora-mysql-database-clusters-256-tib-storage-volume (accessed 2026-11-16)
