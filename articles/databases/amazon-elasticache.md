---
id: kt:amazon-elasticache
type: Service
title: Amazon ElastiCache
aliases: [ElastiCache, ElastiCache Serverless, ElastiCache for Valkey, ElastiCache for Redis OSS, ElastiCache for Memcached]
domain: databases
summary: >-
  AWS's fully managed in-memory data store and cache service, compatible with the Valkey, Redis OSS
  and Memcached engines, offered either as a pay-per-use serverless cache or as node-based clusters
  you size yourself.
verified: 2026-11-11
status: current
broader: [kt:purpose-built-databases]
related: [kt:amazon-rds, kt:amazon-aurora, kt:amazon-dynamodb, kt:database-caching-strategies]
relations:
  - {predicate: governedBy, object: kt:elasticache-quotas}
  - {predicate: dependsOn, object: kt:amazon-vpc}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is Amazon ElastiCache?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/WhatIs.md
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Choosing between deployment options - Amazon ElastiCache"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/WhatIs.deployment.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Quotas for ElastiCache"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/quota-limits.md
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Amazon ElastiCache pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/elasticache/pricing/
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Amazon ElastiCache version 8.0 for Valkey brings faster scaling and improved memory efficiency"
    publisher: Amazon Web Services (AWS Database Blog)
    url: https://aws.amazon.com/blogs/database/amazon-elasticache-version-8-0-for-valkey-brings-faster-scaling-and-improved-memory-efficiency
    type: official-blog
    accessed: 2026-11-11
  - id: s6
    title: "Caching patterns - Database Caching Strategies Using Redis (AWS whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/database-caching-strategies-using-redis/caching-patterns.html
    type: official-docs
    accessed: 2026-11-11
  - id: s7
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# Amazon ElastiCache

Amazon ElastiCache is AWS's managed service for running a distributed in-memory data store or cache. It works with three open-source-compatible engines — Valkey, Redis OSS and Memcached — and can be consumed in two forms: a serverless cache that AWS sizes for you, or a node-based cluster whose node type, node count and Availability Zone placement you choose.[^s1] In both forms AWS handles hardware provisioning, monitoring, node replacement and software patching.[^s1]

## How it works

**Serverless caches** are created by naming them; ElastiCache provisions a highly available cache in under a minute, watches memory, compute and network use, and scales to match the application behind a single endpoint.[^s1] Data is stored redundantly across three Availability Zones with a 99.99% availability SLA, encryption at rest and in transit is always on, and serverless runs only in cluster mode enabled. Supported engines are Valkey 7.2 and later, Redis OSS 7.1 and Memcached 1.6.22 and later.[^s2]

**Node-based clusters** trade that simplicity for control. You can run single-AZ or Multi-AZ, enable cluster mode to scale horizontally by adding shards and replicas, or disable it and scale vertically by node size; Auto Scaling can act on a schedule or on CPU and memory metrics, though Memcached does not support these scaling changes.[^s2] Only node-based clusters offer Global Datastore (cross-Region replication) and data tiering, and node-based Valkey clusters can enable durability, which persists writes in a distributed Multi-AZ transactional log so data survives even the loss of every cache node.[^s1][^s2] Clients connect to individual nodes and must rediscover topology after a replacement, whereas serverless hides this behind one endpoint.[^s2]

Valkey is the open-source key-value store stewarded by the Linux Foundation that AWS positions as a drop-in replacement for Redis OSS. AWS's Database Blog reports that with Valkey 8.0, serverless caches scale from zero to 5 million requests per second in under 13 minutes (versus about 50 minutes on 7.2), and a per-slot dictionary saves roughly 32 bytes of overhead per key on node-based clusters.[^s5]

## Quotas, pricing and gotchas

Default quotas are per Region, and some can be raised through Service Quotas (as of 2026-11): 300 non-reserved nodes per Region, 90 nodes per Valkey or Redis OSS cluster in cluster mode, 60 nodes per Memcached cluster, 40 serverless caches per Region, 24 serverless snapshots per cache per day, and 20 subnets per subnet group.[^s3]

Serverless is billed on GB-hours of data stored plus ElastiCache Processing Units (ECPUs); node-based clusters are billed per node-hour.[^s2] In US East (N. Virginia), as of 2026-11, serverless Valkey costs $0.084 per GB-hour and $0.0023 per million ECPUs with a 100 MB minimum per cache, while serverless Memcached costs $0.125 per GB-hour and $0.0034 per million ECPUs with a 1 GB minimum; an on-demand cache.r7g.large node is $0.1752 per hour in the page's example.[^s4] Reserved nodes discount on-demand rates by up to 55% (All Upfront), and Redis OSS versions on extended support carry an 80% premium in years 1–2 and 160% in year 3.[^s4] AWS prices serverless Valkey 33% below its Redis OSS equivalent and node-based Valkey 20% lower, which makes engine choice a cost decision as well as a technical one.[^s5]

Gotchas: a cache is not the system of record. With lazy loading (cache-aside), only requested data is cached and an empty replacement node merely slows the app, but each miss costs three round trips and values can go stale; write-through keeps data fresh but adds a write penalty, leaves gaps after node replacement, and fills memory with unread data. Pairing either pattern with a TTL bounds staleness.[^s6]

## When to use it (and when not)

Choose serverless for new, spiky or unpredictable workloads, or when you want the simplest start; choose node-based when traffic is predictable enough to forecast capacity, or when you need Global Datastore, data tiering, Memcached, or Valkey durability.[^s2] Put ElastiCache in front of relational databases or other slow back ends to cut read latency and offload repeated queries.[^s6] If in-memory data must survive the loss of every node, enable durability on a node-based Valkey cluster rather than relying on a plain cache.[^s1]

## Connections

ElastiCache sits under **purpose-built databases** and most often fronts **Amazon RDS**, **Amazon Aurora** or **Amazon DynamoDB** using the patterns in the caching-strategies whitepaper.[^s6] Its node-based clusters live in subnet groups inside an **Amazon VPC** and are placed across **Availability Zones**; its default limits are tracked as **ElastiCache quotas**.[^s3] Amazon ElastiCache is listed as an in-scope Database service in the **AWS Certified Solutions Architect – Associate** (SAA-C03) exam guide.[^s7]

## Sources
[^s1]: Amazon Web Services, "What is Amazon ElastiCache?". https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/WhatIs.md (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Choosing between deployment options - Amazon ElastiCache". https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/WhatIs.deployment.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Quotas for ElastiCache". https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/quota-limits.md (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Amazon ElastiCache pricing". https://aws.amazon.com/elasticache/pricing/ (accessed 2026-11-11)
[^s5]: Amazon Web Services (AWS Database Blog), "Amazon ElastiCache version 8.0 for Valkey brings faster scaling and improved memory efficiency". https://aws.amazon.com/blogs/database/amazon-elasticache-version-8-0-for-valkey-brings-faster-scaling-and-improved-memory-efficiency (accessed 2026-11-11)
[^s6]: Amazon Web Services, "Caching patterns - Database Caching Strategies Using Redis". https://docs.aws.amazon.com/whitepapers/latest/database-caching-strategies-using-redis/caching-patterns.html (accessed 2026-11-11)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-11)
