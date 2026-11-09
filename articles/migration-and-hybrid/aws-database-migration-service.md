---
id: kt:aws-database-migration-service
type: Service
title: AWS Database Migration Service
aliases: [AWS DMS, DMS, Database Migration Service]
domain: migration-and-hybrid
summary: >-
  AWS's managed service for migrating and continuously replicating data between databases and
  other data stores, using a full load followed by change data capture (CDC), for same-engine
  and cross-engine moves with minimal downtime.
verified: 2026-11-09
status: current
broader: [kt:aws-migration-and-transfer-services]
related: [kt:seven-rs-migration-strategies, kt:aws-schema-conversion-tool, kt:amazon-rds]
relations:
  - {predicate: alternativeTo, object: kt:aws-transform-mgn}
  - {predicate: governedBy, object: kt:dms-quotas}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "High-level view of AWS DMS - AWS Database Migration Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Introduction.HighLevelView.html
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "Quotas for AWS Database Migration Service - AWS Database Migration Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Limits.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "AWS DMS Serverless components - AWS Database Migration Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Serverless.Components.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Selecting the best size for a replication instance - AWS Database Migration Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/dms/latest/userguide/CHAP_BestPractices.SizingReplicationInstance.html
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "AWS Database Migration Service FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/dms/faqs/
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "AWS DMS Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/dms/pricing/
    type: official-docs
    accessed: 2026-11-09
  - id: s7
    title: "AWS Transform MGN and AWS Database Migration Service (DMS) - AWS Product Comparison"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compare/dms-and-transform-mgn/
    type: official-docs
    accessed: 2026-11-09
  - id: s8
    title: "Cloud Database Migration - AWS Database Migration Service (AWS DMS)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/dms/
    type: official-docs
    accessed: 2026-11-09
  - id: s9
    title: "FedRAMP - Services in Scope"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-09
  - id: s10
    title: "AWS Certified Cloud Practitioner - Exam Guide (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02.pdf
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS Database Migration Service

AWS Database Migration Service (AWS DMS) is a managed service that moves data from a source data store to a target data store, reading the source, reshaping the data for the target and loading it, while the source stays fully operational until cutover.[^s1][^s8] It handles both homogeneous (same-engine) and heterogeneous (cross-engine) migrations across engines such as Oracle, SQL Server, PostgreSQL, MySQL, MariaDB and MongoDB, and it is also used for ongoing replication and for loading data warehouses and data lakes.[^s7][^s8] AWS's own FAQ states that at least one side of the migration, source or target, must live in Amazon RDS or on Amazon EC2; on-premises-to-on-premises replication is not supported.[^s5]

## How it works

A classic DMS deployment has three parts: a **replication instance** (server) that does the processing, **endpoints** holding connection details for the source and target, and **tasks** that move data between them.[^s1] A task runs in three phases. First a **full load** copies existing tables while changes to those tables are cached on the replication server; then the **cached changes** for each table are applied once its load finishes; finally **ongoing replication (CDC)** applies new source changes as transactions until lag settles to a steady state, at which point you stop the application, drain the last changes and repoint it at the target.[^s1]

By default DMS creates only what it needs to move data (tables, primary keys and sometimes unique indexes). Converting views, stored procedures, functions and other code objects is the job of **DMS Schema Conversion**, which flags anything it cannot convert for manual work; the downloadable AWS Schema Conversion Tool (SCT) remains the option AWS points to for large data warehouse migrations.[^s1][^s5]

There are now three ways to run the data movement. **Homogeneous data migrations** use native database tooling for supported engines such as PostgreSQL and MySQL, need no replication instance, and bill only for the hours a migration runs.[^s5][^s6] **DMS Serverless** provisions capacity in DMS Capacity Units (1 DCU = 2 GB of RAM) between a minimum and maximum you set, scales up and down with load (never down during a full load), starts with 100 GB of storage that grows when 90% full, and supports Multi-AZ, which AWS recommends for CDC.[^s3] **Provisioned replication instances** suit steady, predictable transfers you can rightsize.[^s5]

## Quotas, pricing and gotchas

Default quotas per account per Region (as of 2026-11, DMS User Guide): 60 replication instances, 30,000 GB total replication-instance storage, 1,000 endpoints, 600 tasks (200 per instance), 100 serverless replications, 50 migration projects, and 600 homogeneous data migrations with 100 running at once. The API is throttled by a token bucket of 200 tokens refilling at 8 per second, which cannot be raised.[^s2] The guide does not say which of the other quotas are adjustable.[^s2]

Pricing (as of 2026-11): provisioned instances and serverless DCUs are billed per hour; T2/T3 instances include 50 GB of gp2 storage and C4/R4/R5 include 100 GB; DMS Schema Conversion is free apart from the Amazon S3 storage it uses; data transfer into DMS, and between DMS and RDS or EC2 in the same Availability Zone, is free, while cross-AZ, cross-Region and internet transfer is charged.[^s6] Accounts created before 15 July 2025 get 750 hours a month of single-AZ dms.t3.micro for a year; newer accounts use the credit-based Free Tier, which is not offered in AWS GovCloud (US).[^s6]

Gotchas: memory is the main sizing constraint, since changes are cached until applied; LOB columns are processed in memory and written with an extra update per row; tables without primary keys force a slower transactional apply; and EBS storage on an instance cannot be shrunk once increased.[^s4] Serverless replications that do not start within 48 hours are deprovisioned, and under-provisioned maximum capacity can end in an out-of-memory failure.[^s3] AWS ended support for DMS Fleet Advisor on 20 May 2026.[^s8]

## When to use it (and when not)

Use DMS when the unit of migration is a database: cross-engine moves, near-zero-downtime cutovers and continuous replication. When the unit is a whole server (lift-and-shift, data center exits), AWS points to AWS Transform MGN instead, which replicates at the server rather than the database level.[^s7] For same-engine moves to supported engines, prefer the serverless homogeneous migrations; for heterogeneous moves or variable volumes, AWS recommends DMS Serverless.[^s5]

## Connections

- **Broader: AWS migration and transfer services** (`kt:aws-migration-and-transfer-services`); the CLF-C02 exam guide lists AWS DMS in its Migration and Transfer category, so it is **covered by AWS Certified Cloud Practitioner** (`kt:aws-certified-cloud-practitioner`).[^s10]
- **Related to the 7 Rs migration strategies** (`kt:seven-rs-migration-strategies`), **AWS Schema Conversion Tool** (`kt:aws-schema-conversion-tool`) and **Amazon RDS** (`kt:amazon-rds`), a common source or target.[^s5]
- **Alternative to AWS Transform MGN** (`kt:aws-transform-mgn`) for database workloads.[^s7]
- **Governed by DMS quotas** (`kt:dms-quotas`).[^s2]
- **Monitored by Amazon CloudWatch** (`kt:amazon-cloudwatch`) through metrics such as CapacityUtilization and CDC latency, and logs; **secured by AWS KMS** (`kt:aws-kms`), whose key encrypts serverless replication storage.[^s3]
- **Complies with FedRAMP** (`kt:fedramp-on-aws`): AWS lists DMS in scope for FedRAMP Class C (formerly Moderate) in US East/West and Class D (formerly High) in AWS GovCloud (US).[^s9]

## Sources
[^s1]: Amazon Web Services, "High-level view of AWS DMS - AWS Database Migration Service". https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Introduction.HighLevelView.html (accessed 2026-11-09)
[^s2]: Amazon Web Services, "Quotas for AWS Database Migration Service". https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Limits.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "AWS DMS Serverless components". https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Serverless.Components.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Selecting the best size for a replication instance". https://docs.aws.amazon.com/dms/latest/userguide/CHAP_BestPractices.SizingReplicationInstance.html (accessed 2026-11-09)
[^s5]: Amazon Web Services, "AWS Database Migration Service FAQs". https://aws.amazon.com/dms/faqs/ (accessed 2026-11-09)
[^s6]: Amazon Web Services, "AWS DMS Pricing". https://aws.amazon.com/dms/pricing/ (accessed 2026-11-09)
[^s7]: Amazon Web Services, "AWS Transform MGN and AWS Database Migration Service (DMS) - AWS Product Comparison". https://aws.amazon.com/compare/dms-and-transform-mgn/ (accessed 2026-11-09)
[^s8]: Amazon Web Services, "Cloud Database Migration - AWS Database Migration Service (AWS DMS)". https://aws.amazon.com/dms/ (accessed 2026-11-09)
[^s9]: Amazon Web Services, "FedRAMP - Services in Scope". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-09)
[^s10]: Amazon Web Services, "AWS Certified Cloud Practitioner - Exam Guide (CLF-C02)". https://docs.aws.amazon.com/pdfs/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02.pdf (accessed 2026-11-09)
