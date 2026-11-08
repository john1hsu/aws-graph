---
id: kt:amazon-athena
type: Service
title: Amazon Athena
aliases: [Athena, AWS Athena, Athena for SQL]
domain: analytics
summary: >-
  AWS's serverless, interactive query service that runs standard SQL (on a Trino-based engine) and
  Apache Spark directly against data in Amazon S3 and other sources, billed per terabyte scanned or
  by reserved DPU capacity.
verified: 2026-11-08
status: current
broader: [kt:data-lake-on-aws]
related: [kt:aws-lake-formation, kt:aws-lambda]
relations:
  - {predicate: dependsOn, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:aws-glue}
  - {predicate: alternativeTo, object: kt:amazon-redshift}
  - {predicate: alternativeTo, object: kt:amazon-emr}
sources:
  - id: s1
    title: "Amazon Athena FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/athena/faqs/
    type: official-docs
    accessed: 2026-11-08
  - id: s2
    title: "Amazon Athena Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/athena/pricing/?p=ps
    type: official-docs
    accessed: 2026-11-08
  - id: s3
    title: "Service Quotas - Amazon Athena"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/athena/latest/ug/service-limits.html
    type: official-docs
    accessed: 2026-11-08
  - id: s4
    title: "Amazon Athena endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/athena.html
    type: official-docs
    accessed: 2026-11-08
  - id: s5
    title: "Configure per-query and per-workgroup data usage controls - Amazon Athena"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/athena/latest/ug/workgroups-setting-control-limits-cloudwatch.html
    type: official-docs
    accessed: 2026-11-08
  - id: s6
    title: "Top 10 performance tuning tips for Amazon Athena"
    publisher: Amazon Web Services (AWS Big Data Blog)
    url: https://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena
    type: official-blog
    accessed: 2026-11-08
created: 2026-11-08
---

# Amazon Athena

Amazon Athena is a serverless query service for analysing data where it already lives, chiefly in Amazon S3, without loading it into a database or managing any cluster. You define tables over files in S3, write SQL, and Athena scans the files in parallel and returns results; it also offers a managed Apache Spark experience for notebook-style analytics.[^s1] It is the usual "query the data lake" service on AWS and one of the cheapest ways to get SQL over log, event and export data.

## How it works

Athena for SQL runs on a Trino-based engine: engine version 3 is built on Trino, while the older version 2 was built on Presto. Table definitions use Apache Hive DDL, but Hive is used only for DDL; the queries themselves run on the Trino/Presto engine.[^s1] Table and schema metadata live in the AWS Glue Data Catalog wherever Glue is available (an internal catalog is used elsewhere), so Glue crawlers can infer schemas and partitions and other services can share the same tables.[^s1]

Athena reads delimited text (CSV, TSV), JSON, Avro and the columnar formats Parquet and ORC, including data compressed with Snappy, Zlib, LZO or GZIP. It can also work with Apache Iceberg tables in S3, which add reliable write transactions on top of plain files.[^s1] For data outside S3, **Athena Federated Query** uses connectors that run as AWS Lambda functions and translate between Athena and the source; AWS cites 30 built-in connectors plus an SDK for writing your own.[^s1]

Workgroups separate users, teams or applications and carry their own settings. A workgroup can enforce a single **per-query data scanned limit** (minimum 10 MB) that cancels any query exceeding it, and any number of **per-workgroup** hourly or daily thresholds that raise an alarm and send an Amazon SNS notification without cancelling queries.[^s5]

## Quotas, pricing and gotchas

Pricing (as of 2026-11; the pricing page's worked examples do not name a Region, so treat these as us-east-1-style list prices and check your Region):[^s2]

- **SQL, per-query billing:** $5 per TB scanned. S3 storage, requests and data transfer are billed separately at normal S3 rates.[^s2] Failed queries are not charged; cancelled queries are charged for data scanned up to the point of cancellation.[^s1]
- **Federated queries:** billed per TB scanned, rounded up to the nearest megabyte with a 10 MB minimum per query, plus standard Lambda charges for the connector.[^s2]
- **Provisioned capacity:** $0.30 per DPU-hour for reserved capacity instead of per-scan billing.[^s2]
- **Apache Spark:** $0.35 per DPU-hour for worker and driver DPUs; one DPU is 4 vCPUs and 16 GB of memory.[^s2][^s1]

Default quotas (as of 2026-11), all from the General Reference: active DML queries (SELECT, CTAS, INSERT INTO, counting running and queued) default to 200 in us-east-1, 150 in several large Regions such as us-west-2 and eu-west-1, and 20 in most other Regions; active DDL queries default to 20 per Region. Both are adjustable. The DML timeout defaults to 30 minutes and is adjustable; the DDL timeout is 600 minutes and is not adjustable.[^s4] The DML timeout can be raised to at most 240 minutes; a query string is capped at 262,144 bytes (not adjustable); and an account can have 1,000 workgroups per Region. Quotas are shared across all workgroups, and exceeding the active-query limit returns `TooManyRequestsException`.[^s3] Athena can query Glue tables with up to 10 million partitions but reads no more than 1 million partitions in a single scan.[^s3]

Gotchas: because cost tracks bytes scanned, file layout matters more than SQL cleverness. Partition on columns you filter by (often date), consider partition projection for highly partitioned tables, convert to compressed Parquet or ORC so queries read only the columns they reference, and aim for files around 128 MB, since many small files slow planning and can trigger S3 request throttling.[^s6] Query result reuse can return a recent identical result without rescanning data, which helps dashboards.[^s6] Cancelled or failed queries can leave partial result files in S3 that Athena does not delete, so put a lifecycle rule on the results bucket.[^s5]

## When to use it (and when not)

AWS positions Athena for serverless, interactive SQL over data in S3 with no infrastructure to manage. Amazon Redshift fits enterprise reporting and BI with heavy, complex SQL, joins and subqueries over a data warehouse, while Amazon EMR fits custom, large-scale processing frameworks where you want full control of the cluster.[^s1] In practice Athena is the default for ad-hoc analysis of logs (CloudTrail, VPC Flow Logs, ALB logs), exploration of a data lake and lightweight scheduled reports; steady, high-concurrency BI workloads usually justify a warehouse or provisioned capacity instead of per-scan billing.[^s1][^s2]

## Connections

- **Depends on Amazon S3** (`kt:amazon-s3`): S3 is Athena's primary storage and also where query results are written, billed at S3 rates.[^s2][^s3]
- **Integrates with AWS Glue** (`kt:aws-glue`): the Glue Data Catalog holds Athena's table metadata and its quotas bound databases, tables and partitions.[^s1][^s3]
- **Alternative to Amazon Redshift and Amazon EMR** (`kt:amazon-redshift`, `kt:amazon-emr`): the AWS FAQ contrasts the three by workload.[^s1]
- **Related to AWS Lambda** (`kt:aws-lambda`), which runs federated-query connectors, and **AWS Lake Formation** (`kt:aws-lake-formation`), whose row-level filters combine with Iceberg tables queried by Athena.[^s1]
- **Broader: data lake on AWS** (`kt:data-lake-on-aws`): Athena is the serverless SQL layer over an S3-based lake.[^s1]

## Sources
[^s1]: Amazon Web Services, "Amazon Athena FAQs". https://aws.amazon.com/athena/faqs/ (accessed 2026-11-08)
[^s2]: Amazon Web Services, "Amazon Athena Pricing". https://aws.amazon.com/athena/pricing/?p=ps (accessed 2026-11-08)
[^s3]: Amazon Web Services, "Service Quotas - Amazon Athena". https://docs.aws.amazon.com/athena/latest/ug/service-limits.html (accessed 2026-11-08)
[^s4]: Amazon Web Services, "Amazon Athena endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/athena.html (accessed 2026-11-08)
[^s5]: Amazon Web Services, "Configure per-query and per-workgroup data usage controls". https://docs.aws.amazon.com/athena/latest/ug/workgroups-setting-control-limits-cloudwatch.html (accessed 2026-11-08)
[^s6]: Amazon Web Services, AWS Big Data Blog, "Top 10 performance tuning tips for Amazon Athena" (updated 2024). https://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena (accessed 2026-11-08)
