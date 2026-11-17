---
id: kt:amazon-neptune
type: Service
title: Amazon Neptune
aliases: [Neptune, Neptune Database, Amazon Neptune Database, Neptune Serverless]
domain: databases
summary: >-
  AWS's fully managed graph database service, which stores property graphs (queried with Gremlin
  or openCypher) and RDF graphs (queried with SPARQL) on a cluster volume replicated across three
  Availability Zones, with provisioned or serverless instances and an in-memory sibling engine,
  Neptune Analytics, for graph analytics and vector search.
verified: 2026-11-17
status: current
broader: [kt:purpose-built-databases]
related: [kt:amazon-dynamodb, kt:amazon-documentdb, kt:amazon-aurora, kt:neptune-analytics]
relations:
  - {predicate: governedBy, object: kt:neptune-quotas}
  - {predicate: dependsOn, object: kt:amazon-vpc}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: pricedBy, object: kt:database-savings-plans}
sources:
  - id: s1
    title: "What Is Amazon Neptune?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/neptune/latest/userguide/intro.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Amazon Neptune FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/neptune/faqs/
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Amazon Neptune Limits"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/neptune/latest/userguide/limits.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "Capacity scaling in a Neptune Serverless DB cluster"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/neptune/latest/userguide/neptune-serverless-capacity-scaling.html
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "Amazon Neptune Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/neptune/pricing
    type: official-docs
    accessed: 2026-11-17
  - id: s6
    title: "Using Amazon Neptune with a global database"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/neptune/latest/userguide/neptune-global-database.html
    type: official-docs
    accessed: 2026-11-17
  - id: s7
    title: "When to use Neptune Analytics and when to use Neptune Database"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/neptune-analytics/latest/userguide/neptune-analytics-vs-neptune-database.html
    type: official-docs
    accessed: 2026-11-17
  - id: s8
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-17
created: 2026-11-17
---

# Amazon Neptune

Amazon Neptune is AWS's fully managed graph database. It is built for highly connected data, where the relationships between records matter as much as the records themselves. AWS lists recommendation engines, fraud detection, knowledge graphs, drug discovery and network security as typical uses. The engine is designed to store billions of relationships and traverse them with millisecond latency.[^s1]

## How it works

Neptune supports two graph models. A **property graph** can be queried with Apache TinkerPop Gremlin and with openCypher, and both languages work on the same graph. An **RDF** graph is queried with SPARQL, the W3C's RDF query language.[^s1] So choosing a model is a real design decision: property graphs suit application-style traversals, while RDF suits linked-data and ontology work.

A Neptune DB cluster has one **primary** instance that handles reads and writes and makes every change to the cluster volume. It can also have up to **15 Neptune replicas**, which read from the same volume and are placed in other Availability Zones to spread read load and serve as failover targets.[^s1] The **cluster volume** keeps copies of the data across three Availability Zones in one Region. You are billed for the data stored once, not for each replicated copy.[^s2] Storage starts at 10 GiB and grows automatically in 10 GiB steps up to 128 TiB.[^s2][^s3] If the primary fails and a replica exists, failover typically finishes within 30 seconds. Without a replica, recovery typically takes under 15 minutes.[^s2] Automated backups to S3 are always on, and point-in-time restore can reach a point up to 5 minutes in the past.[^s2]

**Neptune Serverless** sizes instances in Neptune Capacity Units (NCUs). Each NCU is 2 GiB of memory plus matching vCPU and networking. You set a minimum of at least 1.0 NCU and a maximum of up to 128.0 NCUs. Readers in promotion tiers 0 and 1 scale with the writer so they're ready to take over, while readers in tiers 2–15 scale independently.[^s4]

**Neptune Global Database** replicates at the storage layer from a primary cluster to as many as five read-only secondary clusters in other Regions. Lag is typically under one second. Managed planned failover can move the primary with no data loss, and an unplanned outage is handled by detaching and promoting a secondary.[^s6]

## Quotas, pricing and gotchas

- Neptune is VPC-only, requires TLS (only HTTPS connections since engine 1.0.4.0), and uses IAM rather than user names and passwords for access control. A DB subnet group must span at least two AZs, and AWS recommends three or more.[^s3]
- Gremlin and SPARQL HTTP requests must be smaller than 150 MB, and a single property or label can't exceed 55 MB. There is no cap on the number of vertices, edges or RDF quads. You can queue at most 64 bulk-load jobs at a time.[^s3]
- Per-Region instance and cluster quotas are shared with Amazon RDS.[^s3]
- You can't encrypt an existing unencrypted cluster in place. You have to create an encrypted one and migrate.[^s2]
- Global databases don't support the db.t3.medium or db.t4g.medium classes, and secondary clusters can't auto scale.[^s6]
- Pricing as of 2026-11 (US East (N. Virginia) examples on the pricing page): a db.r5.large On-Demand instance costs $0.348 per hour. Standard storage is $0.10 per GB-month plus $0.20 per million I/O requests, and I/O-Optimized storage is $0.225 per GB-month. Serverless is billed per second in NCUs.[^s5] New users get a 30-day free trial of 750 hours on db.t3.medium or db.t4g.medium, with 10 million I/Os, 1 GB of storage and 1 GB of backup storage.[^s5]

## When to use it (and when not)

Choose Neptune when your queries are multi-hop traversals, such as "friends of friends who bought X" or "accounts that share a device with a flagged account." Joins in a relational or key-value store get expensive and awkward at that depth. AWS describes **Neptune Database** as the transactional engine for operational graph workloads, designed to scale to 100,000 queries per second with Multi-AZ and multi-Region deployment. **Neptune Analytics** is a separate in-memory engine. It loads large graphs from Neptune or a data lake and runs graph algorithms and low-latency analytic queries over tens of billions of relationships.[^s7] The FAQ adds that Neptune Analytics supports vector search over graph data.[^s2] If your access patterns are simple key lookups, DynamoDB is usually the better fit. For JSON documents, use DocumentDB. Use Aurora or RDS when the data is relational with only shallow joins.[^s1][^s7]

## Connections

Neptune is the graph member of `kt:purpose-built-databases` and sits beside `kt:amazon-dynamodb`, `kt:amazon-documentdb` and `kt:amazon-aurora` as alternatives for different data shapes.[^s7] `kt:neptune-analytics` is its in-memory analytics sibling.[^s7] It `dependsOn` `kt:amazon-vpc` because clusters are VPC-only, and it is `securedBy` `kt:aws-kms` for encryption at rest.[^s1][^s3] It is `scopedTo` `kt:availability-zones` because the cluster volume and replicas span AZs within a Region.[^s1][^s2] It is `governedBy` `kt:neptune-quotas`, the limits summarized above.[^s3] AWS's FedRAMP services-in-scope page lists Neptune under FedRAMP Class C (formerly the Moderate baseline) in US East/West and Class D (formerly High) in GovCloud (US), which supports the `compliesWith` edge to `kt:fedramp-on-aws`.[^s8] The FAQ notes that Database Savings Plans apply to Neptune, hence `pricedBy` `kt:database-savings-plans`.[^s2]

## Sources
[^s1]: Amazon Web Services, "What Is Amazon Neptune?". https://docs.aws.amazon.com/neptune/latest/userguide/intro.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Amazon Neptune FAQs". https://aws.amazon.com/neptune/faqs/ (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Amazon Neptune Limits". https://docs.aws.amazon.com/neptune/latest/userguide/limits.html (accessed 2026-11-17)
[^s4]: Amazon Web Services, "Capacity scaling in a Neptune Serverless DB cluster". https://docs.aws.amazon.com/neptune/latest/userguide/neptune-serverless-capacity-scaling.html (accessed 2026-11-17)
[^s5]: Amazon Web Services, "Amazon Neptune Pricing". https://aws.amazon.com/neptune/pricing (accessed 2026-11-17)
[^s6]: Amazon Web Services, "Using Amazon Neptune with a global database". https://docs.aws.amazon.com/neptune/latest/userguide/neptune-global-database.html (accessed 2026-11-17)
[^s7]: Amazon Web Services, "When to use Neptune Analytics and when to use Neptune Database". https://docs.aws.amazon.com/neptune-analytics/latest/userguide/neptune-analytics-vs-neptune-database.html (accessed 2026-11-17)
[^s8]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-17)
