---
id: kt:dynamodb-partition-key-design
type: Pattern
title: DynamoDB partition key design
aliases: [DynamoDB partition key best practices, DynamoDB hash key design, DynamoDB write sharding]
domain: databases
summary: >-
  The practice of choosing an Amazon DynamoDB partition key (and, where needed, sharding it) so
  that reads and writes spread evenly across physical partitions, staying under the per-partition
  ceiling of 3,000 read units and 1,000 write units per second and avoiding hot-partition throttling.
verified: 2026-11-17
status: concept
broader: [kt:amazon-dynamodb]
related: [kt:purpose-built-databases, kt:dynamodb-global-tables]
relations:
  - {predicate: governedBy, object: kt:dynamodb-quotas}
  - {predicate: mitigates, object: kt:dynamodb-hot-partitions}
  - {predicate: integratesWith, object: kt:amazon-dynamodb-accelerator}
sources:
  - id: s1
    title: "Best practices for designing and using partition keys effectively in DynamoDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-design.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Designing partition keys to distribute your workload in DynamoDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-uniform-load.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Using write sharding to distribute workloads evenly in your DynamoDB table"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-sharding.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "DynamoDB burst and adaptive capacity"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/burst-adaptive-capacity.html
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "Choosing the Right DynamoDB Partition Key"
    publisher: AWS Database Blog
    url: https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key
    type: official-blog
    accessed: 2026-11-17
  - id: s6
    title: "Amazon DynamoDB: A Scalable, Predictably Performant, and Fully Managed NoSQL Database Service (USENIX ATC 2022)"
    publisher: USENIX
    url: https://usenix.org/system/files/atc22-elhemali.pdf
    type: peer-reviewed
    accessed: 2026-11-17
created: 2026-11-17
---

# DynamoDB partition key design

Every DynamoDB item is identified by a primary key that is either simple (a partition key alone) or composite (a partition key plus a sort key), and the partition key value decides which logical partition, and therefore which physical partition, stores the item.[^s1][^s2] Partition key design is the discipline of picking that attribute so traffic is spread evenly across the table and its secondary indexes. A poor choice concentrates requests on a "hot" partition, which throttles requests and wastes the capacity paid for elsewhere.[^s2]

## How it works

AWS documents that each partition can serve at most 3,000 read units and 1,000 write units per second (as of 2026-11). A read unit covers one strongly consistent read per second, or two eventually consistent reads, of an item up to 4 KB. A write unit covers one write per second of an item up to 1 KB. Item size therefore matters: a 20 KB item costs 5 read units per strongly consistent read, so one key can sustain about 600 such reads per second.[^s1] Above the partition level, total throughput is bounded by provisioned capacity or, in on-demand mode, by the table-level throughput limit.[^s1]

The DynamoDB paper explains why this matters. A table is split into partitions that each own a contiguous key range, replicated across Availability Zones. Concentrated traffic on a few items can overload one partition and cause throttling even when the table as a whole has spare capacity.[^s6]

## What a good key looks like

AWS's guidance is about the share of distinct key values the workload actually touches: the higher that ratio, the more evenly capacity is used.[^s2] High-cardinality identifiers such as user IDs, or device IDs where devices report at similar rates, distribute well. Status codes with only a few values, creation dates rounded to a day, hour or minute, or a device ID dominated by one popular device do not.[^s2] The AWS Database Blog adds composite keys built from several attributes (for example customer, product and country) when that matches the access pattern. It also warns against copying a relational schema's sequence or database-generated IDs as the partition key.[^s5]

## Write sharding

When a natural key has too few values, write sharding spreads one logical key over many partition key values by appending a suffix.[^s3] A *random* suffix (for example 1–200 appended to a date) distributes writes evenly, but reading a single item then means querying every suffix and merging results in the application.[^s3] A *calculated* suffix derives the number from an attribute you query on, such as a hash of an order ID, so point reads can recompute the key and use `GetItem`. Reading everything for a date still fans out across all suffixes.[^s3] The blog suggests sizing the suffix range from the expected peak. For example, 5,000 writes per second of 1 KB items needs at least five shards. Plan headroom up front, because changing the range later requires application changes; the same approach applies to global secondary index keys.[^s5]

## Burst, adaptive capacity and gotchas

DynamoDB softens some imbalance automatically. It keeps up to 300 seconds of unused read and write capacity for bursts, though AWS says these details may change.[^s4] Adaptive capacity raises throughput for busy partitions as long as traffic stays within table capacity and the partition maximum. It is always on, at no extra charge.[^s4] It can also isolate a consistently hot item on its own partition, or split an item collection by sort key. Neither helps when access moves monotonically through the sort key, and item collections are not split when the table has a local secondary index.[^s4] The DynamoDB paper describes how adaptive capacity later gave way to global admission control and split-for-consumption. Even so, no automatic mechanism lifts a single key above the partition ceiling.[^s6]

Other gotchas: a single `Query` returns at most 1 MB before pagination, global secondary indexes are eventually consistent, and read-heavy hot items are often better served by caching in DynamoDB Accelerator (DAX) than by resharding.[^s5]

## When to apply it

Design the key from access patterns before creating the table. AWS notes that a table that fits in one partition (allowing for growth) and stays under one partition's throughput will not see partition-driven throttling. For a small, light table, simplicity can beat elaborate sharding.[^s2] For high-volume time-series data, AWS points to its separate time-series best practice rather than date-plus-random-suffix keys.[^s3]

## Connections

This pattern is a design practice *within* `kt:amazon-dynamodb` (broader), and it is *governed by* DynamoDB's per-partition and table throughput limits (`kt:dynamodb-quotas`).[^s1] Its main purpose is to *mitigate* hot partitions (`kt:dynamodb-hot-partitions`).[^s2][^s4] It *integrates with* DynamoDB Accelerator, which absorbs read-heavy hot keys.[^s5] It relates to `kt:dynamodb-global-tables`, whose replicas keep the same key schema, and to `kt:purpose-built-databases`, where key-value stores reward access-pattern-first modelling.[^s6]

## Sources

[^s1]: Amazon Web Services, "Best practices for designing and using partition keys effectively in DynamoDB". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-design.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Designing partition keys to distribute your workload in DynamoDB". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-uniform-load.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Using write sharding to distribute workloads evenly in your DynamoDB table". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-sharding.html (accessed 2026-11-17)
[^s4]: Amazon Web Services, "DynamoDB burst and adaptive capacity". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/burst-adaptive-capacity.html (accessed 2026-11-17)
[^s5]: G. Balasubramanian and S. Shriver, "Choosing the Right DynamoDB Partition Key", AWS Database Blog (2017, reviewed 2022). https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key (accessed 2026-11-17)
[^s6]: M. Elhemali et al., "Amazon DynamoDB: A Scalable, Predictably Performant, and Fully Managed NoSQL Database Service", USENIX ATC 2022. https://usenix.org/system/files/atc22-elhemali.pdf (accessed 2026-11-17)
