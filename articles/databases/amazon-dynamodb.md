---
id: kt:amazon-dynamodb
type: Service
title: Amazon DynamoDB
aliases: [DynamoDB, DDB]
domain: databases
summary: >-
  AWS's serverless, fully managed, distributed NoSQL key-value and document database, designed for
  single-digit millisecond performance at any scale, with on-demand or provisioned capacity, ACID
  transactions, change streams and multi-Region global tables.
verified: 2026-11-09
status: current
broader: [kt:purpose-built-databases, kt:aws-serverless-services]
related: [kt:dynamodb-global-tables, kt:dynamodb-partition-key-design, kt:aws-lambda]
relations:
  - {predicate: governedBy, object: kt:dynamodb-quotas}
  - {predicate: alternativeTo, object: kt:amazon-rds}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is Amazon DynamoDB?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.md
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "Constraints in Amazon DynamoDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Constraints.md
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Quotas in Amazon DynamoDB (Simplified Chinese edition of the developer guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/zh_cn/amazondynamodb/latest/developerguide/ServiceQuotas.md
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Amazon DynamoDB pricing for on-demand capacity"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/dynamodb/pricing/on-demand
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "Amazon DynamoDB global tables with multi-Region strong consistency is now generally available"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/06/amazon-dynamo-db-global-tables-multi-region-strong-consistency-generally-available/
    type: official-blog
    accessed: 2026-11-09
  - id: s6
    title: "DynamoDB overview - Modernizing your application by migrating from an RDBMS to Amazon DynamoDB"
    publisher: Amazon Web Services (AWS Prescriptive Guidance)
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-rdbms-dynamodb/overview.html
    type: official-docs
    accessed: 2026-11-09
created: 2026-11-09
---

# Amazon DynamoDB

Amazon DynamoDB is a serverless, fully managed, distributed NoSQL database that AWS describes as delivering single-digit millisecond performance at any scale. There are no servers to provision or patch, no engine versions and no maintenance windows. It supports both key-value and document data models and AWS launched it in 2012.[^s1] It is the default choice in AWS's portfolio for operational workloads whose access patterns are known in advance and must stay fast as traffic grows.[^s1][^s6]

## How it works

A table is schemaless apart from its primary key: a required partition key and an optional sort key. Every other attribute can differ from item to item.[^s6] DynamoDB spreads items across partitions by the partition key, so AWS advises a high-cardinality key to avoid hot partitions.[^s6] Data is stored on SSDs and replicated automatically across three Availability Zones in a Region, which backs a 99.99% availability SLA.[^s1][^s6] There is no JOIN operator; AWS recommends denormalizing the data model instead. Reads can be eventually or strongly consistent, and ACID transactions can span multiple tables in one account and Region.[^s1][^s2]

Alternate access paths come from secondary indexes. Global secondary indexes (GSIs) can use a different partition and sort key from the table; local secondary indexes (LSIs) share the table's partition key. Every write to the table also updates its indexes.[^s1][^s6]

Capacity comes in two modes. **On-demand** bills per request, scales instantly and scales to zero with no cold start. **Provisioned** reserves read capacity units (RCUs) and write capacity units (WCUs), optionally adjusted by auto scaling.[^s1][^s6] One read unit covers one strongly consistent read, or two eventually consistent reads, of up to 4 KB. One write unit covers one write of up to 1 KB. Transactional reads and writes cost double.[^s2]

Around the core table, DynamoDB offers DynamoDB Streams and Kinesis Data Streams for item-level change data capture, Lambda triggers, export to and import from Amazon S3, zero-ETL integrations with Amazon Redshift and OpenSearch, and the DynamoDB Accelerator (DAX) in-memory cache.[^s1] Point-in-time recovery restores to any second in a configurable window of 1 to 35 days, and on-demand backups can be managed through AWS Backup.[^s1] **Global tables** replicate a table across chosen Regions in a multi-active setup with a 99.999% availability SLA.[^s1] Since 2025-06-30, global tables also support **multi-Region strong consistency (MRSC)**, which AWS says gives a recovery point objective of zero. At launch it was available in ten Regions.[^s5]

## Quotas, pricing and gotchas

Fixed constraints (as of 2026-11): items are capped at 400 KB, and that limit counts attribute names as well as values. Partition key values can be up to 2,048 bytes and sort key values up to 1,024 bytes. Nesting goes 32 levels deep. Query and Scan return at most 1 MB per call, so you paginate with `LastEvaluatedKey`. `BatchWriteItem` takes up to 25 put or delete requests and `BatchGetItem` up to 100 items, each capped at 16 MB in total. A transaction can include up to 100 unique items and 4 MB of data. On tables with any LSI, an item collection cannot exceed 10 GB.[^s2] You can switch a table from provisioned to on-demand up to four times in a rolling 24 hours.[^s2]

Default quotas per Region (as of 2026-11, all adjustable): 40,000 read and 40,000 write units per table in either mode, and 80,000 RCUs and 80,000 WCUs per account for provisioned tables. AWS says these are starting defaults, not maximums. Each account starts with 2,500 tables per Region, which can be raised to 10,000. Tables can have up to 20 GSIs by default and up to 5 LSIs. Table size has no practical limit.[^s3] The quotas page also lists limits for vector indexes, a newer table feature (for example, 5 per table by default).[^s3]

Pricing in US East (N. Virginia), as of 2026-11, from AWS's on-demand pricing page: $0.625 per million write request units and $0.125 per million read request units for the Standard table class. The Standard-IA class costs $0.780 and $0.155 per million. Storage is $0.25 per GB-month for Standard and $0.10 for Standard-IA. PITR costs $0.20 per GB-month and on-demand backup $0.10 per GB-month.[^s4] The free tier gives 25 GB of storage plus 25 provisioned WCUs and 25 RCUs per Region.[^s4][^s1]

Gotchas: GSIs and replicas multiply write cost, because each one is written too. Items larger than 400 KB belong in Amazon S3, with a pointer kept in DynamoDB.[^s6][^s4] Transactions are not atomic across global-table Regions, so other replicas can briefly see partial results.[^s2]

## When to use it (and when not)

DynamoDB fits high-scale operational workloads that need consistent latency and little operational effort. AWS's examples include shopping carts, gaming state and leaderboards, financial ledgers and streaming-media metadata.[^s1] It is a poor fit when you need ad hoc queries, joins or relational reporting. Those workloads usually belong in a relational engine such as Amazon RDS, or the data can be exported to an analytics service.[^s1][^s6]

## Connections

- **Broader:** purpose-built databases, and AWS's serverless services, since it has no servers and on-demand mode scales to zero.[^s1]
- **Related:** global tables for multi-Region replication, partition-key design for avoiding hot partitions, and AWS Lambda, which consumes DynamoDB Streams through triggers.[^s1][^s5][^s6]
- **Governed by DynamoDB quotas** (`kt:dynamodb-quotas`).[^s3]
- **Alternative to Amazon RDS** when access patterns are known and joins are not needed.[^s1]
- **Secured by AWS IAM** (no database user names or passwords) and **by AWS KMS** for encryption at rest, which is on by default.[^s1]
- **Integrates with Amazon S3** for import, export and storing large objects.[^s1][^s6]
- **Scoped to AWS Regions**: a table lives in one Region unless it is a global table.[^s1]

## Sources
[^s1]: Amazon Web Services, "What is Amazon DynamoDB?". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.md (accessed 2026-11-09)
[^s2]: Amazon Web Services, "Constraints in Amazon DynamoDB". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Constraints.md (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Quotas in Amazon DynamoDB" (Simplified Chinese edition of the developer guide). https://docs.aws.amazon.com/zh_cn/amazondynamodb/latest/developerguide/ServiceQuotas.md (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Amazon DynamoDB pricing for on-demand capacity". https://aws.amazon.com/dynamodb/pricing/on-demand (accessed 2026-11-09)
[^s5]: Amazon Web Services, What's New, "Amazon DynamoDB global tables with multi-Region strong consistency is now generally available" (2025-06-30). https://aws.amazon.com/about-aws/whats-new/2025/06/amazon-dynamo-db-global-tables-multi-region-strong-consistency-generally-available/ (accessed 2026-11-09)
[^s6]: Amazon Web Services, AWS Prescriptive Guidance, "DynamoDB overview". https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-rdbms-dynamodb/overview.html (accessed 2026-11-09)
