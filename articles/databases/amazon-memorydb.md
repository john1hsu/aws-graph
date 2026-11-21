---
id: kt:amazon-memorydb
type: Service
title: Amazon MemoryDB
aliases: [MemoryDB, Amazon MemoryDB for Redis, MemoryDB for Redis, Amazon MemoryDB for Valkey, MemoryDB for Valkey, MemoryDB Multi-Region]
domain: databases
summary: >-
  AWS's durable, Valkey- and Redis OSS-compatible in-memory database, which persists every write to a
  Multi-AZ transactional log so it can serve as a primary database with microsecond reads rather than
  only as a cache.
verified: 2026-11-21
status: current
broader: [kt:purpose-built-databases]
related: [kt:amazon-dynamodb, kt:database-caching-strategies, kt:availability-zones]
relations:
  - {predicate: alternativeTo, object: kt:amazon-elasticache}
  - {predicate: governedBy, object: kt:memorydb-quotas}
  - {predicate: dependsOn, object: kt:amazon-vpc}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
sources:
  - id: s1
    title: "What is MemoryDB - Amazon MemoryDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/memorydb/latest/devguide/what-is-memorydb.html
    type: official-docs
    accessed: 2026-11-21
  - id: s2
    title: "Features of MemoryDB - Amazon MemoryDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/memorydb/latest/devguide/servicename-feature-overview.html
    type: official-docs
    accessed: 2026-11-21
  - id: s3
    title: "MemoryDB core components - Amazon MemoryDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/memorydb/latest/devguide/components.html
    type: official-docs
    accessed: 2026-11-21
  - id: s4
    title: "Quotas for MemoryDB - Amazon MemoryDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/memorydb/latest/devguide/quota-limits.html
    type: official-docs
    accessed: 2026-11-21
  - id: s5
    title: "Pricing for Amazon MemoryDB"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/memorydb/pricing/
    type: official-docs
    accessed: 2026-11-21
  - id: s6
    title: "Amazon MemoryDB FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/memorydb/faqs/
    type: official-docs
    accessed: 2026-11-21
  - id: s7
    title: "Related services - Amazon MemoryDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/memorydb/latest/devguide/related-services-choose-between-memorydb-and-redis.html
    type: official-docs
    accessed: 2026-11-21
  - id: s8
    title: "Get started with Amazon MemoryDB for Valkey"
    publisher: Amazon Web Services (AWS Database Blog)
    url: https://aws.amazon.com/blogs/database/get-started-with-amazon-memorydb-for-valkey/
    type: official-blog
    accessed: 2026-11-21
  - id: s9
    title: "Amazon ElastiCache for Valkey now supports durability"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/durability-amazon-elasticache/
    type: official-blog
    accessed: 2026-11-21
created: 2026-11-21
---

# Amazon MemoryDB

Amazon MemoryDB is a managed in-memory database that speaks the Valkey and Redis OSS protocols but, unlike a cache, is designed to be the system of record. Every write is stored across multiple Availability Zones in a Multi-AZ transactional log, which AWS uses for fast failover, recovery and node restarts. The goal is to let a microservice keep its data in one ultra-fast store instead of running a cache in front of a separate durable database.[^s1] AWS quotes microsecond reads and single-digit-millisecond writes.[^s1][^s2]

## How it works

A **cluster** serves one dataset, partitioned into **shards**. Each shard has one read/write primary node and up to five read-only replicas. A primary can fail over to one of its replicas. All nodes in a cluster use the same node type, and each runs on an Amazon EC2 instance. A cluster can have up to 500 shards; a 500-node cluster ranges from 83 shards (five replicas each) to 500 shards with no replicas.[^s3] You add shards to scale writes and replicas to scale reads, and change node type to scale vertically.[^s2] Parameter groups hold engine settings, subnet groups place nodes in your VPC, and access control lists (ACLs) group the users who may connect.[^s3]

Consistency differs by role. Reads on a primary are strongly consistent with read-after-write semantics, while replicas are eventually consistent.[^s2] Only writes that have been persisted to the transaction log become visible, and replica lag is published to CloudWatch.[^s6] AWS says unplanned failover typically finishes in under 20 seconds, and planned failover in under 200 milliseconds.[^s6]

Security covers TLS in transit, encryption at rest with AWS KMS keys, and ACL-based user authentication. Management APIs integrate with IAM and tag-based access control.[^s2] Automatic snapshots go to Amazon S3 and can be kept for up to 35 days.[^s2] The FAQ also describes built-in vector search (cosine, dot product and Euclidean distance) at no extra cost. It also describes **MemoryDB Multi-Region**, an active-active option that replicates asynchronously across up to five Regions and resolves conflicts with CRDTs, with availability of up to 99.999%.[^s6]

## Quotas, pricing and gotchas

Default quotas per Region (as of 2026-11) include 300 nodes, 90 nodes per cluster in cluster mode, 300 parameter groups, 300 subnet groups with 20 subnets each, 2,000 users and 200 user groups of up to 100 users each. AWS says some quotas can be raised and others cannot, but the table does not mark which.[^s4] The features page quotes up to 500 nodes and more than 100 TB per cluster with one replica per shard. That is above the 90-node default, so check Service Quotas before you design a very large cluster.[^s2][^s4]

You pay for on-demand node-hours (or 1- or 3-year reserved nodes), for data written per GB (reads are free), for snapshot storage beyond the free allowance, and for data transfer. Multi-Region clusters also pay for cross-Region transfer.[^s5] Snapshot storage up to 100% of a Region's cluster storage costs nothing, as does a one-day retention.[^s5] When AWS launched the Valkey engine it priced Valkey node-hours 30% below Redis OSS. Valkey's first 10 TB of data written per month is free, and writes beyond that cost $0.04/GB.[^s8] Node prices vary by Region and type, so price a design with the pricing page for your Region.[^s5]

Durable writes are the trade-off: they are slower than an in-memory cache's because each write must reach the Multi-AZ log first.[^s1][^s6]

## When to use it (and when not)

AWS positions MemoryDB as the durable primary database for workloads that need in-memory speed. It can simplify an architecture, and cut cost, by replacing a database-plus-cache pair. ElastiCache stays the choice for caching data that lives in another primary database.[^s7] That line has blurred. In June 2026 AWS added durability to node-based ElastiCache for Valkey (Valkey 9.0 and later). That uses the same kind of Multi-AZ transactional log, with synchronous mode for zero data loss or asynchronous mode for microsecond writes.[^s9] The MemoryDB FAQ now recommends ElastiCache for new workloads. As of 2026-11 AWS has not announced any change to MemoryDB's availability.[^s6]

## Connections

MemoryDB is one of the **purpose-built databases** AWS offers for in-memory workloads, alongside its main alternative **Amazon ElastiCache**. The two share engines, so the choice comes down to durability needs and the patterns in **database caching strategies**.[^s7][^s9] Nodes run inside your **Amazon VPC** through subnet groups, and shards spread across **Availability Zones** for the transaction log and failover.[^s1][^s3] Encryption at rest uses **AWS KMS**, and metrics such as replica lag go to **Amazon CloudWatch**.[^s2][^s6] Default limits are in **MemoryDB quotas**.[^s4]

## Sources
[^s1]: Amazon Web Services, "What is MemoryDB - Amazon MemoryDB". https://docs.aws.amazon.com/memorydb/latest/devguide/what-is-memorydb.html (accessed 2026-11-21)
[^s2]: Amazon Web Services, "Features of MemoryDB - Amazon MemoryDB". https://docs.aws.amazon.com/memorydb/latest/devguide/servicename-feature-overview.html (accessed 2026-11-21)
[^s3]: Amazon Web Services, "MemoryDB core components - Amazon MemoryDB". https://docs.aws.amazon.com/memorydb/latest/devguide/components.html (accessed 2026-11-21)
[^s4]: Amazon Web Services, "Quotas for MemoryDB - Amazon MemoryDB". https://docs.aws.amazon.com/memorydb/latest/devguide/quota-limits.html (accessed 2026-11-21)
[^s5]: Amazon Web Services, "Pricing for Amazon MemoryDB". https://aws.amazon.com/memorydb/pricing/ (accessed 2026-11-21)
[^s6]: Amazon Web Services, "Amazon MemoryDB FAQs". https://aws.amazon.com/memorydb/faqs/ (accessed 2026-11-21)
[^s7]: Amazon Web Services, "Related services - Amazon MemoryDB". https://docs.aws.amazon.com/memorydb/latest/devguide/related-services-choose-between-memorydb-and-redis.html (accessed 2026-11-21)
[^s8]: Amazon Web Services, "Get started with Amazon MemoryDB for Valkey" (AWS Database Blog, 2024-10-08). https://aws.amazon.com/blogs/database/get-started-with-amazon-memorydb-for-valkey/ (accessed 2026-11-21)
[^s9]: Amazon Web Services, "Amazon ElastiCache for Valkey now supports durability" (What's New, 2026-06-02). https://aws.amazon.com/about-aws/whats-new/2026/06/durability-amazon-elasticache/ (accessed 2026-11-21)
