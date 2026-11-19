---
id: kt:amazon-dynamodb-accelerator
type: Service
title: Amazon DynamoDB Accelerator (DAX)
aliases: [DynamoDB Accelerator, DAX, DynamoDB DAX]
domain: databases
summary: >-
  A fully managed, DynamoDB API-compatible in-memory cache that runs as a cluster of nodes in
  your VPC and serves eventually consistent DynamoDB reads with microsecond latency, using a
  write-through item cache and a separate query cache.
verified: 2026-11-19
status: current
broader: [kt:amazon-dynamodb]
related: [kt:database-caching-strategies, kt:dynamodb-partition-key-design]
relations:
  - {predicate: alternativeTo, object: kt:amazon-elasticache}
  - {predicate: runsOn, object: kt:amazon-vpc}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "In-memory acceleration with DynamoDB Accelerator (DAX) - Amazon DynamoDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DAX.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "DAX: How it works - Amazon DynamoDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DAX.concepts.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "DAX cluster components - Amazon DynamoDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DAX.concepts.cluster.md
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "DAX and DynamoDB consistency models - Amazon DynamoDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DAX.consistency.md
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Amazon DynamoDB Accelerator (DAX) Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/dynamodbaccelerator/pricing/
    type: official-docs
    accessed: 2026-11-19
  - id: s6
    title: "Amazon DynamoDB Accelerator (DAX) is now available in additional Regions"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-dynamodb-accelerator/
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# Amazon DynamoDB Accelerator (DAX)

DAX is a managed in-memory cache that speaks the DynamoDB API. Applications send their DynamoDB calls through the DAX client to a DAX cluster; DAX answers eventually consistent reads from memory when it can and forwards everything else to DynamoDB. AWS positions it as cutting read latency for eventually consistent reads by an order of magnitude, from single-digit milliseconds to microseconds, and says a Multi-AZ cluster can serve millions of requests per second. Because the API is compatible, existing DynamoDB code needs only small changes to adopt it. Encryption at rest and TLS in transit are supported. AWS ships DAX clients for Go, Java, Node.js, Python and .NET.[^s1]

## How it works

A cluster is one or more nodes of the same node type. One node is the primary and the rest are read replicas. The primary serves cached reads, performs writes to DynamoDB and handles eviction; replicas serve reads and evict but never write to DynamoDB. Changes on the primary reach replicas through replication logs. A cluster holds at most 11 nodes (a primary plus up to 10 replicas). If the primary fails, DAX promotes a replica. Applications connect through a single cluster endpoint, and the client discovers nodes and balances load across them. You scale out by adding replicas for read throughput, or scale up by creating a new cluster with a larger node type.[^s3]

DAX runs only inside a VPC. You choose a subnet group (usually private subnets) when creating the cluster and open TCP port 8111 in a security group so application instances can reach it. A cluster can only serve tables in its own Region, so multi-Region applications need a cluster per Region.[^s3]

Each cluster keeps two independent caches. The **item cache** stores results of `GetItem` and `BatchGetItem`, keyed by primary key, with a default TTL of 5 minutes. The **query cache** stores result sets of `Query` and `Scan`, keyed by the request parameters, with a TTL set at cluster creation. Both caches evict with a least-recently-used algorithm that is always on and cannot be configured. On a miss, DAX reads from DynamoDB, caches the result on the primary and returns it. `PutItem`, `UpdateItem`, `DeleteItem` and `BatchWriteItem` are write-through: DynamoDB is written first, then the item cache, and the call succeeds only if both do. Table-management calls such as `CreateTable` are not handled by DAX and must go straight to DynamoDB.[^s2]

## Quotas, pricing and gotchas

- **Consistency is the main trap.** Strongly consistent reads and `TransactGetItems` pass through to DynamoDB and are never cached. Writes made directly to DynamoDB, bypassing DAX, leave the item cache stale until its TTL expires. Writes never invalidate the query cache, so a `Query` can return an old result set even after an item cache update. Replication between nodes is eventually consistent and usually completes in under a second, so two clients can briefly see different values.[^s4]
- **Negative caching.** If an item or result is not found, DAX caches the empty result too, until the TTL expires or (for items) the item is written through DAX.[^s4]
- **Sizing and limits.** AWS strongly recommends at least three nodes in different Availability Zones for production; one- and two-node clusters are not fault tolerant. A cluster supports at most 500 DynamoDB tables, and AWS says performance and availability can degrade beyond that.[^s3] Requests beyond a node's capacity return `ThrottlingException`, visible in the CloudWatch `ThrottledRequestCount` metric.[^s2] DAX keeps metadata for every top-level attribute name indefinitely, so schemas that generate unbounded attribute names (timestamps or UUIDs used as names) can exhaust memory.[^s1]
- **Pricing (as of 2026-11).** DAX bills per node-hour at a rate set by node type, rounds partial hours up and charges for every node in the cluster. There is no long-term commitment. Data transfer between EC2 and DAX in the same Availability Zone is free; cross-AZ traffic incurs EC2 data transfer charges on the EC2 side only. T3 nodes run in unlimited mode, and CPU use above baseline averaged over 24 hours is billed at $0.096 per vCPU-hour in all Regions.[^s5]
- **Availability.** On 2026-10-01 AWS added DAX to 17 more Regions, including AWS GovCloud (US-East) and AWS GovCloud (US-West).[^s6]

## When to use it (and when not)

DAX fits read-heavy, latency-sensitive workloads such as real-time bidding, gaming and trading; tables with hot keys that draw far more reads than the rest; and read-intensive applications where buying extra read capacity would cost more than a cache. AWS says DAX works best when the cache hit rate exceeds 90%.[^s1] It is a poor fit for applications that need strongly consistent reads, for write-intensive workloads (heavy writes increase replication load across nodes), and for access patterns with few repeated reads.[^s1] For bulk loads, AWS suggests a write-around strategy, writing straight to DynamoDB so the cache holds only data that is actually read.[^s4] Because DAX speaks only the DynamoDB API and keeps its item cache in sync on writes made through it, it removes cache-invalidation code from the application; a cache that must front other data stores, or hold application-defined structures, is instead a job for ElastiCache.[^s1][^s4]

## Connections

DAX sits under **Amazon DynamoDB**, the only data store it fronts, and is a concrete, managed form of the read-through and write-through patterns described in **database caching strategies**.[^s2][^s4] It relates to **DynamoDB partition key design** because a cache absorbs reads on hot keys that a skewed key design would otherwise concentrate on one partition.[^s1] It is an **alternative to Amazon ElastiCache** for DynamoDB-only caching, **runs on Amazon VPC** subnets and security groups, is **monitored by Amazon CloudWatch** metrics such as `ThrottledRequestCount`, and is **scoped to AWS Regions** because a cluster can serve only tables in its own Region.[^s2][^s3]

## Sources
[^s1]: Amazon Web Services, "In-memory acceleration with DynamoDB Accelerator (DAX) - Amazon DynamoDB". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DAX.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "DAX: How it works - Amazon DynamoDB". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DAX.concepts.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "DAX cluster components - Amazon DynamoDB". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DAX.concepts.cluster.md (accessed 2026-11-19)
[^s4]: Amazon Web Services, "DAX and DynamoDB consistency models - Amazon DynamoDB". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DAX.consistency.md (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Amazon DynamoDB Accelerator (DAX) Pricing". https://aws.amazon.com/dynamodbaccelerator/pricing/ (accessed 2026-11-19)
[^s6]: Amazon Web Services, "Amazon DynamoDB Accelerator (DAX) is now available in additional Regions" (What's New, 2026-10-01). https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-dynamodb-accelerator/ (accessed 2026-11-19)
