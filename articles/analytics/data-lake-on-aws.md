---
id: kt:data-lake-on-aws
type: Pattern
title: Data lake on AWS
aliases: [AWS data lake, data lakes on AWS, S3 data lake, Amazon S3 data lake]
domain: analytics
summary: >-
  The architecture pattern of storing structured, semi-structured and unstructured data in its
  native form on Amazon S3, cataloguing it in the AWS Glue Data Catalog, governing it with Lake
  Formation and querying it in place with decoupled engines such as Athena, EMR and Redshift.
verified: 2026-11-13
status: concept
broader: [kt:aws-analytics-services]
related: [kt:amazon-redshift, kt:amazon-emr, kt:amazon-data-firehose, kt:apache-iceberg]
relations:
  - {predicate: dependsOn, object: kt:amazon-s3}
  - {predicate: hasPart, object: kt:aws-glue}
  - {predicate: hasPart, object: kt:aws-lake-formation}
  - {predicate: hasPart, object: kt:amazon-athena}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: contrastsWith, object: kt:data-warehouse}
sources:
  - id: s1
    title: "What's the Difference Between a Data Warehouse, Data Lake, and Data Mart?"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compare/the-difference-between-a-data-warehouse-data-lake-and-data-mart
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Data lake foundation - Storage Best Practices for Data and Analytics Applications"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/building-data-lakes/data-lake-foundation.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "Securing, protecting, and managing data - Storage Best Practices for Data and Analytics Applications"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/building-data-lakes/securing-protecting-managing-data.html
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "Modern data lakes - AWS Prescriptive Guidance (Using Apache Iceberg on AWS)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/apache-iceberg-on-aws/data-lakes.html
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "Working with Amazon S3 Tables and table buckets - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-tables.html
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "S3 Tables AWS Regions, endpoints, and service quotas - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-tables-regions-quotas.html
    type: official-docs
    accessed: 2026-11-13
  - id: s7
    title: "AWS modern data architecture - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-aws-data/aws-architecture.html
    type: official-docs
    accessed: 2026-11-13
  - id: s8
    title: "Amazon S3 Tables now support 10,000 tables per table bucket"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/01/amazon-s3-tables-10000-tables-bucket
    type: official-blog
    accessed: 2026-11-13
created: 2026-11-13
---

# Data lake on AWS

A data lake is a central store that accepts structured, semi-structured and unstructured data (logs, clickstreams, sensor feeds, relational extracts) and applies a schema only when the data is read. AWS contrasts this schema-on-read approach with a data warehouse, where the schema is usually designed before loading. The lake trades some query speed for much cheaper storage, because compute and storage are decoupled; AWS points to Amazon S3 (or Lake Formation on top of it) for lakes and to Amazon Redshift for warehouses and data marts.[^s1] On AWS the pattern is not a single service but a composition: S3 for storage, the AWS Glue Data Catalog for metadata, Lake Formation for permissions, and engines that query the data in place.[^s2][^s7]

## How it works

**Storage and zones.** AWS's data lake storage whitepaper (now marked for historical reference) builds the lake on S3 with separate buckets per zone. A *raw* zone keeps immutable copies of source data in its original format, restricted to service principals. A *transformed* zone holds normalized data, typically columnar. A *curated* zone holds enriched, blended data tuned for Athena, Redshift Spectrum and Redshift. A *logs* zone collects S3 access, CloudWatch and CloudTrail logs.[^s2] It recommends Apache Parquet with Snappy compression for transformed and curated data, files of about 128 MB for data queried by Athena or Spectrum, and partitioning by processing date in raw but by business date downstream.[^s2]

**Catalog, governance and engines.** AWS's modern data architecture guidance maps the layers to services: S3 as the shared storage layer; Lake Formation for governance and sharing; Glue for the catalog; Athena for ad-hoc SQL and Redshift for warehousing; EMR and Glue for processing; Kinesis and MSK for streaming; and SageMaker for ML.[^s7] Lake Formation adds a grant/revoke model over both catalog metadata and the S3 locations behind it. A user needs IAM permissions and Lake Formation permissions to query through integrated services, and AWS recommends LF-tag-based access control because it scales better than named-resource grants.[^s3]

**Open table formats.** Plain Hive-style tables on S3 lack ACID transactions, make record-level updates and deletes slow, degrade as tables grow to millions of files, and need rewrites for schema changes. Those gaps hurt CDC, privacy deletions and streaming ingestion most.[^s4] AWS Prescriptive Guidance positions Apache Iceberg as the fix. It brings atomic commits with serializable isolation, SQL `MERGE`/`UPDATE`/`DELETE`, schema and partition evolution, and time travel and rollback. Athena, EMR, Glue, Redshift Spectrum, Data Firehose and Managed Service for Apache Flink all read or write Iceberg tables.[^s4]

## S3 Tables, quotas and gotchas

Amazon S3 Tables stores Iceberg tables in a dedicated *table bucket* type. S3 runs compaction, snapshot management and unreferenced-file removal for you. AWS says table buckets give higher transactions per second and query throughput than self-managed Iceberg tables in general purpose buckets. Block Public Access is always on and cannot be disabled.[^s5] As of 2026-11 the default quotas are 100 table buckets per account per Region, and 10,000 namespaces and 10,000 tables per table bucket. All three are adjustable through AWS Support.[^s6] Older material is out of date. The January 2025 announcement that raised the tables quota to 10,000 still cites a limit of 10 table buckets per Region, so check the quotas page rather than launch posts.[^s8][^s6]

Other gotchas from AWS's guidance:

- **Bucket names are unique within a partition.** A deleted name can't be reused right away, so don't delete buckets whose names you need to keep.[^s2]
- **Keep production separate.** Put production lake buckets in their own account, apart from non-production.[^s2]
- **Protect against deletes.** Turn on versioning, with MFA Delete for extra protection. Use lifecycle rules to tier old versions.[^s3]
- **Encrypt and lock where required.** Encrypt every zone with KMS-managed keys. Use S3 Object Lock where records must be write-once.[^s3][^s2]

## When to use it (and when not)

Choose a lake when you need to keep large volumes of diverse or not-yet-modelled data cheaply, serve several engines (SQL, Spark, ML) from one copy, or keep raw data for later reprocessing.[^s1][^s7] A warehouse such as Redshift remains the better fit for the fastest repeated BI queries over well-modelled relational data, and most AWS designs combine the two, with Redshift Spectrum querying the curated zone.[^s1][^s2] A lake without a catalog and permissions model quickly becomes hard to find data in; AWS's guidance treats a unique catalog and governed sharing as mandatory tenets, not add-ons.[^s7]

## Connections

The pattern `dependsOn` `kt:amazon-s3`, the storage layer every zone lives in.[^s2] It `hasPart` `kt:aws-glue` (catalog and ETL), `kt:aws-lake-formation` (fine-grained permissions and sharing) and `kt:amazon-athena` (serverless query in place).[^s7][^s3] It is `securedBy` `kt:aws-kms`, which encrypts the zones and logs key use to CloudTrail.[^s3] It `contrastsWith` `kt:data-warehouse` on schema timing, cost and speed.[^s1] Related nodes are `kt:amazon-redshift` and `kt:amazon-emr` (query and processing engines), `kt:amazon-data-firehose` (streaming ingestion into Iceberg tables) and `kt:apache-iceberg` (the open table format behind S3 Tables).[^s4][^s5] Its `broader` parent is `kt:aws-analytics-services`.[^s7]

## Sources
[^s1]: Amazon Web Services, "What's the Difference Between a Data Warehouse, Data Lake, and Data Mart?". https://aws.amazon.com/compare/the-difference-between-a-data-warehouse-data-lake-and-data-mart (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Data lake foundation - Storage Best Practices for Data and Analytics Applications". https://docs.aws.amazon.com/whitepapers/latest/building-data-lakes/data-lake-foundation.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Securing, protecting, and managing data - Storage Best Practices for Data and Analytics Applications". https://docs.aws.amazon.com/whitepapers/latest/building-data-lakes/securing-protecting-managing-data.html (accessed 2026-11-13)
[^s4]: Amazon Web Services, "Modern data lakes - AWS Prescriptive Guidance (Using Apache Iceberg on AWS)". https://docs.aws.amazon.com/prescriptive-guidance/latest/apache-iceberg-on-aws/data-lakes.html (accessed 2026-11-13)
[^s5]: Amazon Web Services, "Working with Amazon S3 Tables and table buckets - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-tables.html (accessed 2026-11-13)
[^s6]: Amazon Web Services, "S3 Tables AWS Regions, endpoints, and service quotas - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-tables-regions-quotas.html (accessed 2026-11-13)
[^s7]: Amazon Web Services, "AWS modern data architecture - AWS Prescriptive Guidance". https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-aws-data/aws-architecture.html (accessed 2026-11-13)
[^s8]: Amazon Web Services, "Amazon S3 Tables now support 10,000 tables per table bucket". https://aws.amazon.com/about-aws/whats-new/2025/01/amazon-s3-tables-10000-tables-bucket (accessed 2026-11-13)
