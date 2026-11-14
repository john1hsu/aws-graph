---
id: kt:amazon-documentdb
type: Service
title: Amazon DocumentDB (with MongoDB compatibility)
aliases: [Amazon DocumentDB, DocumentDB, DocDB, DocumentDB elastic clusters, DocumentDB Serverless]
domain: databases
summary: >-
  AWS's fully managed, MongoDB API-compatible JSON document database, which separates compute
  instances from a six-way replicated cluster storage volume and comes as instance-based clusters,
  serverless capacity, or sharded elastic clusters.
verified: 2026-11-14
status: current
broader: [kt:purpose-built-databases]
related: [kt:amazon-dynamodb, kt:amazon-aurora, kt:aws-database-migration-service]
relations:
  - {predicate: alternativeTo, object: kt:amazon-dynamodb}
  - {predicate: governedBy, object: kt:documentdb-quotas}
  - {predicate: dependsOn, object: kt:amazon-vpc}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
sources:
  - id: s1
    title: "What is Amazon DocumentDB (with MongoDB compatibility)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/documentdb/latest/devguide/what-is.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "Amazon DocumentDB elastic clusters: how it works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/documentdb/latest/devguide/elastic-how-it-works.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "How Amazon DocumentDB serverless works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/documentdb/latest/devguide/docdb-serverless-how-it-works.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Amazon DocumentDB quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/documentdb/latest/devguide/limits.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "Amazon DocumentDB Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/documentdb/pricing/
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "Amazon DocumentDB (with MongoDB compatibility) FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/documentdb/faqs/
    type: official-docs
    accessed: 2026-11-14
created: 2026-11-14
---

# Amazon DocumentDB (with MongoDB compatibility)

Amazon DocumentDB is a fully managed NoSQL document database that speaks the MongoDB wire protocol, so existing MongoDB drivers, tools and much application code work against it unchanged.[^s1] It is not MongoDB itself: AWS says it uses no MongoDB SSPL code and instead implements the Apache 2.0-licensed MongoDB APIs, and that not every MongoDB API is supported.[^s6] Drivers for MongoDB 3.6 through 8.0 are supported; DocumentDB engine 8.0 is the version that accepts workloads from MongoDB 6.0, 7.0 and 8.0.[^s6]

## How it works

An instance-based cluster splits compute from storage. Data lives in a cluster volume that keeps six copies across three Availability Zones (even in a Region with only two AZs for compute), grows automatically, and tops out at 256 TiB on engine 8.0 and later or 128 TiB on earlier engines.[^s1] On top of the volume sit 0–16 instances: one primary that takes writes and up to 15 read replicas that share the same storage, so adding a replica does not copy data.[^s1] If the primary fails, DocumentDB promotes a replica in another AZ, typically within 30 seconds; with no replicas it has to build a new instance, which is much slower.[^s6][^s1] Backups go to Amazon S3 with point-in-time restore over a retention window of up to 35 days.[^s1] Global clusters replicate a cluster to up to five Regions for disaster recovery and local reads.[^s6]

**DocumentDB Serverless** replaces fixed instance classes with capacity measured in DocumentDB Capacity Units (about 2 GiB of memory plus matching CPU and networking each). A cluster sets one range between 0.5 and 256 DCUs, scaling moves in steps as small as 0.5 DCU, and serverless and provisioned instances can be mixed in the same cluster, for example a provisioned writer with serverless readers. It runs on engines 5.0 and 8.0.[^s3]

**Elastic clusters** are the scale-out option for workloads that outgrow one writer. They hash-partition data by a required shard key across up to 32 shards; each shard has one writer and up to 15 replicas, with up to 64 vCPUs per shard instance, and you scale out (more shards) or up (more vCPUs per shard) independently of storage.[^s2] Choosing a shard key with many distinct values is what keeps load even across shards.[^s2]

## Quotas, pricing and gotchas

Default Regional quotas per account are 40 instance-based clusters, 40 instances and 15 read replicas per cluster; elastic clusters default to 20 per Region and 1,024 total vCPUs. Regional quotas can be raised through Service Quotas where adjustable, while document limits are fixed: 16 MiB per document, 200 nesting levels (100 on elastic clusters), 64 indexes per collection and 32 TiB per unsharded collection (as of 2026-11).[^s4] Elastic clusters reach 4 PiB of storage when shard keys distribute evenly.[^s4]

Pricing has two storage configurations. In US East (N. Virginia), as of 2026-11, Standard bills storage at $0.10 per GB-month plus $0.20 per million I/Os, while I/O-Optimized charges $0.30 per GB-month with no I/O fee and a higher instance rate (db.r5.large: $0.277 vs $0.3047 per hour on demand). AWS positions I/O-Optimized for clusters whose I/O exceeds about 25% of spend.[^s5] Serverless costs $0.0822 (Standard) or $0.0905 (I/O-Optimized) per DCU-hour, and elastic clusters $0.132 per vCPU-hour, in the same Region; instances bill per second with a 10-minute minimum, and backup storage is free up to 100% of cluster storage.[^s5] New customers get a one-month free trial, excluding GovCloud (US) and China (Ningxia).[^s5]

Gotchas: compatibility is API-level, so test unsupported operators before migrating; elastic clusters differ in some behaviours (for instance `$elemMatch` and `$` projections) from instance-based engine 4.0;[^s2] cross-Region replication I/O is charged even on I/O-Optimized;[^s6] and per-instance connection and cursor limits scale with instance size (a db.t4g.medium allows 1,000 connections on engine 8.0).[^s4]

## When to use it (and when not)

Choose DocumentDB when an application already uses MongoDB drivers and a flexible JSON document model with rich secondary indexes and aggregation, and you want AWS to run storage, replication, patching and backups.[^s1][^s6] If the workload is not tied to the MongoDB API, compare it with DynamoDB for key-value access patterns and with Aurora when the data is relational. Use Serverless for spiky or multi-tenant traffic, and elastic clusters only when one writer can't absorb the write rate or data volume.[^s3][^s2] AWS lists FedRAMP High authorization in the GovCloud (US) Regions and FedRAMP Moderate in the US East and West Regions.[^s1]

## Connections

DocumentDB is one of AWS's **purpose-built databases** and the managed document-model **alternative to Amazon DynamoDB**; its separated compute and shared six-way storage design parallels **Amazon Aurora**.[^s1] Clusters run inside an **Amazon VPC**, spread copies across **Availability Zones**, and encrypt data at rest with **AWS KMS** keys.[^s1] Self-managed MongoDB replica sets or sharded clusters migrate in with **AWS Database Migration Service** with minimal downtime.[^s6] Its quotas are captured in **DocumentDB quotas**, and its FedRAMP scope ties it to **FedRAMP on AWS**.[^s4][^s1]

## Sources
[^s1]: Amazon Web Services, "What is Amazon DocumentDB (with MongoDB compatibility)". https://docs.aws.amazon.com/documentdb/latest/devguide/what-is.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "Amazon DocumentDB elastic clusters: how it works". https://docs.aws.amazon.com/documentdb/latest/devguide/elastic-how-it-works.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, "How Amazon DocumentDB serverless works". https://docs.aws.amazon.com/documentdb/latest/devguide/docdb-serverless-how-it-works.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, "Amazon DocumentDB quotas". https://docs.aws.amazon.com/documentdb/latest/devguide/limits.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, "Amazon DocumentDB Pricing". https://aws.amazon.com/documentdb/pricing/ (accessed 2026-11-14)
[^s6]: Amazon Web Services, "Amazon DocumentDB (with MongoDB compatibility) FAQs". https://aws.amazon.com/documentdb/faqs/ (accessed 2026-11-14)
