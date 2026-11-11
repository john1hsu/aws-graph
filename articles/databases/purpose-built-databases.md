---
id: kt:purpose-built-databases
type: Concept
title: Purpose-built databases
aliases: [purpose-built databases on AWS, AWS database services, AWS databases, choosing an AWS database]
domain: databases
summary: >-
  AWS's approach of matching each workload to a database engineered for its data model and access
  pattern (relational, key-value, document, wide-column, graph, in-memory, time-series or vector)
  instead of forcing every workload into one general-purpose database.
verified: 2026-11-11
status: concept
broader: [kt:amazon-web-services, kt:performance-efficiency-pillar]
related: [kt:aws-database-migration-service, kt:database-caching-strategies, kt:amazon-redshift]
relations:
  - {predicate: hasPart, object: kt:amazon-rds}
  - {predicate: hasPart, object: kt:amazon-aurora}
  - {predicate: hasPart, object: kt:amazon-dynamodb}
  - {predicate: hasPart, object: kt:amazon-elasticache}
  - {predicate: hasPart, object: kt:amazon-neptune}
  - {predicate: pricedBy, object: kt:database-savings-plans}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "Choosing an AWS database service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/databases-on-aws-how-to-choose/databases-on-aws-how-to-choose.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "PERF03-BP01 Use a purpose-built data store that best supports your data access and storage requirements"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/performance-efficiency-pillar/perf_data_use_purpose_built_data_store.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Amazon Timestream for LiveAnalytics availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/timestream/latest/developerguide/AmazonTimestreamForLiveAnalytics-availability-change.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Services in Full Shutdown - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/qldb.html
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Announcing Database Savings Plans with up to 35% savings"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/database-savings-plans-savings/
    type: official-blog
    accessed: 2026-11-11
  - id: s6
    title: "Content Domain 3: Design High-Performing Architectures - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03-domain3.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# Purpose-built databases

"Purpose-built databases" is AWS's shorthand for picking a data store by its data model and access pattern rather than defaulting to one general-purpose relational engine for everything. AWS's database decision guide (last updated June 2026) covers more than 15 options across relational, key-value, document, wide-column, graph, in-memory and time-series models, plus a separate section on vector search for generative AI. It recommends mixing them inside one application: for example, DocumentDB for a product catalog, DynamoDB for low-latency browsing and Aurora for inventory and orders.[^s1] The Well-Architected Performance Efficiency pillar makes this a best practice (PERF03-BP01). It rates the risk of skipping it as high and names two anti-patterns: staying with one store because the team knows it, and assuming every workload has the same storage and access needs.[^s2]

## The categories and their AWS services

As of 2026-11, the decision guide maps categories to services like this:[^s1]

- **Relational:** Amazon RDS (PostgreSQL, MySQL, MariaDB, SQL Server, Oracle, Db2), Amazon Aurora, Aurora DSQL and Aurora PostgreSQL Limitless Database. Use these for joins, referential integrity and ACID transactions.[^s1]
- **Key-value:** Amazon DynamoDB, for high-throughput access with single-digit millisecond latency (session stores, carts).[^s1]
- **Document:** Amazon DocumentDB (with MongoDB compatibility), for JSON-like documents queried across fields.[^s1]
- **Wide-column:** Amazon Keyspaces (for Apache Cassandra), for Cassandra migrations and heavy read/write scale.[^s1]
- **Graph:** Amazon Neptune, for highly connected data such as fraud rings, social graphs and recommendations.[^s1]
- **In-memory:** Amazon ElastiCache for ephemeral caching, and Amazon MemoryDB as a durable primary in-memory database.[^s1]
- **Time series:** Amazon Timestream, for IoT readings, metrics and asset tracking.[^s1]
- **Vector:** pgvector on Aurora PostgreSQL or RDS for PostgreSQL, OpenSearch Service, Neptune Analytics, MemoryDB, DocumentDB, DynamoDB and S3 Vectors.[^s1]

The guide is scoped to OLTP and points analytics (OLAP) workloads to Amazon Redshift.[^s1] The Well-Architected table also lists Redshift under relational, and it sends analytics queries to Redshift or to S3 with Athena.[^s2]

## How to choose

The Well-Architected guidance starts by listing each dataset's traits: structure, volume and growth, durability, ACID needs, read/write ratio, latency and throughput, and retention. It then asks targeted questions.[^s2] Do you need foreign keys? Use RDS or Aurora. Do you need simple, very high-throughput transactions? Use DynamoDB. Is the workload read-heavy? Add ElastiCache, DAX or read replicas. It also weighs licensing (open-source engines), operational overhead (managed services over self-hosting on EC2), and tools that need a specific engine. The last step is benchmarking candidates in a non-production environment.[^s2]

AWS's decision guide adds the migration path to that list. You can rehost on EC2, re-platform to RDS, or refactor to cloud-native stores such as Aurora, DynamoDB or Neptune. It also asks whether serverless or provisioned capacity fits better, and lists resiliency features such as replication, failover and point-in-time recovery. For vector search, it advises extending an engine the team already runs (for example, pgvector) before adopting a new one.[^s1]

## Lifecycle gotchas and pricing

Older category maps are out of date. The Well-Architected table still names Amazon QLDB for ledger workloads,[^s2] but QLDB reached end of support on July 31, 2025, and AWS lists it as a service in full shutdown.[^s4] The current decision guide has no ledger category.[^s1] Timestream for LiveAnalytics closed to new customers on June 20, 2025. Existing workloads keep running, and AWS points new customers to Timestream for InfluxDB.[^s3]

On cost, Database Savings Plans were announced on December 2, 2025. You commit to a consistent $/hour of spend for one year, with no upfront payment, and save up to 35% on eligible serverless and provisioned usage. The plans cover Aurora, RDS, DynamoDB, ElastiCache, DocumentDB, Neptune, Keyspaces, Timestream and DMS in every Region except China. The discount applies regardless of engine, instance family, size or Region, so it doesn't lock you into one database choice.[^s5]

## When it matters (and when not)

Running several engines adds operational surface, so AWS's guidance treats this as matching tools to needs, not collecting databases.[^s2] A modest CRUD application can live happily on one relational database. Separate stores pay off when access patterns diverge sharply, such as microsecond caching beside transactional orders, or relationship traversal beside key lookups.[^s1][^s2]

## Connections

This concept groups the database services in the graph. It `hasPart` `kt:amazon-rds`, `kt:amazon-aurora`, `kt:amazon-dynamodb`, `kt:amazon-elasticache` and `kt:amazon-neptune`, which the decision guide lists as the relational, key-value, in-memory and graph options.[^s1] Its `broader` parents are `kt:amazon-web-services` and `kt:performance-efficiency-pillar`, because PERF03-BP01 makes the purpose-built choice a Well-Architected best practice.[^s2] It relates to `kt:database-caching-strategies` (the ElastiCache/DAX guidance), `kt:aws-database-migration-service` (the re-platform and refactor path) and `kt:amazon-redshift` (where OLAP work goes instead).[^s1][^s2] It is `pricedBy` `kt:database-savings-plans`.[^s5] SAA-C03 Task 3.3 asks candidates to choose database types and services, including relational versus non-relational and in-memory, and to determine an appropriate type such as Aurora or DynamoDB. That is the basis for the `coveredBy` edge.[^s6]

## Sources
[^s1]: Amazon Web Services, "Choosing an AWS database service". https://docs.aws.amazon.com/decision-guides/latest/databases-on-aws-how-to-choose/databases-on-aws-how-to-choose.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "PERF03-BP01 Use a purpose-built data store that best supports your data access and storage requirements". https://docs.aws.amazon.com/wellarchitected/latest/performance-efficiency-pillar/perf_data_use_purpose_built_data_store.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Amazon Timestream for LiveAnalytics availability change". https://docs.aws.amazon.com/timestream/latest/developerguide/AmazonTimestreamForLiveAnalytics-availability-change.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Services in Full Shutdown - AWS General Reference". https://docs.aws.amazon.com/general/latest/gr/qldb.html (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Announcing Database Savings Plans with up to 35% savings". https://aws.amazon.com/about-aws/whats-new/2025/12/database-savings-plans-savings/ (accessed 2026-11-11)
[^s6]: Amazon Web Services, "Content Domain 3: Design High-Performing Architectures - AWS Certified Solutions Architect - Associate (SAA-C03)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03-domain3.html (accessed 2026-11-11)
