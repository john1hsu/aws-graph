---
id: kt:aws-glue
type: Service
title: AWS Glue
aliases: [Glue, Amazon Glue, AWS Glue ETL]
domain: analytics
summary: >-
  AWS's serverless data integration service: a central Data Catalog plus crawlers, connections and
  Apache Spark-based ETL jobs for discovering, preparing and moving data into data lakes and
  warehouses, billed per DPU-hour.
verified: 2026-11-09
status: current
broader: [kt:data-lake-on-aws]
related: [kt:aws-lake-formation, kt:amazon-redshift, kt:amazon-eks]
relations:
  - {predicate: integratesWith, object: kt:amazon-athena}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: alternativeTo, object: kt:amazon-emr}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: governedBy, object: kt:glue-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is AWS Glue? - AWS Glue"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "AWS Glue Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/glue/pricing
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "AWS Glue endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/glue.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "AWS Glue versions - AWS Glue"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/glue/latest/dg/release-notes.html
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "AWS Glue for Ray end of support - AWS Glue"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/glue/latest/dg/awsglue-ray-jobs-availability-change.html
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "Amazon EMR and AWS Glue - AWS Product Comparison"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compare/emr-and-glue/
    type: official-docs
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS Glue

AWS Glue is a serverless data integration service for discovering, preparing, moving and combining data from many sources for analytics, machine learning and application development. AWS says it can connect to more than 70 data sources on premises and on AWS, and it supports ETL, ELT and streaming workloads without any cluster for you to run.[^s1] In most AWS data lakes Glue plays two roles at once: it is the metadata layer (the Data Catalog) that query engines share, and it is the managed Spark engine that cleans and reshapes the data.[^s1][^s6]

## How it works

Glue's capabilities fall into three groups.[^s1]

- **Discover and organize.** The **Data Catalog** is a central index of databases, tables, schemas and partitions across your sources and sinks. **Crawlers** scan data stores, infer schemas and partitions, and write table definitions into the catalog. **Connections** hold the details needed to reach JDBC databases and other sources.[^s1]
- **Transform and prepare.** **AWS Glue Studio** is a visual editor that generates job code for the serverless Apache Spark engine and lets you edit the script directly. Built-in transforms include FindMatches (machine-learning deduplication) and sensitive data detection; interactive sessions and notebooks support exploratory work. **AWS Glue DataBrew** adds no-code visual data preparation.[^s1]
- **Build and monitor pipelines.** Jobs run on demand, on a schedule or in response to events; workflows chain crawlers, jobs and triggers; workers can scale automatically. Monitoring uses CloudTrail, job run insights and the Spark UI.[^s1]

Because the catalog is shared, a table a crawler defines once can be queried from Amazon Athena, Amazon EMR and Amazon Redshift Spectrum, and AWS Lake Formation layers fine-grained permissions on top of it.[^s1][^s6]

**Versions.** A Glue version pins the runtime for Spark jobs. As of 2026-11 the latest is AWS Glue 6.0 (Spark 4.1.1, Python 3.13, Java 17), while Glue 5.1 (Spark 3.5.6, Python 3.11) is the default for jobs created without a version. Glue 0.9, 1.0 and 2.0 reached end of life on April 1, 2026, so jobs pinned to them need upgrading.[^s4]

## Quotas, pricing and gotchas

Pricing (as of 2026-11; the pricing page notes rates can vary by Region and its examples don't name one):[^s2]

- **Spark ETL jobs, crawlers and interactive sessions:** $0.44 per DPU-hour, billed per second. Interactive sessions default to 5 DPUs. **Flex** execution, for jobs without strict timing needs, is shown at $0.29 per DPU-hour.[^s2]
- **Data Catalog:** the first million metadata objects (tables, partitions, versions, databases, catalogs) and the first million requests each month are free; storage above that is $1.00 per 100,000 objects per month.[^s2]
- **Data quality tasks, Iceberg table optimization, column statistics and materialized view refresh:** $0.44 per DPU-hour with a 1-minute minimum. **DataBrew:** $1.00 per 30-minute interactive session and $0.48 per node-hour for jobs.[^s2]

Default quotas (per Region, as of 2026-11, all adjustable unless noted): 2,000 jobs per account; 2,000 concurrent job runs per account; 1,000 crawlers, of which 150 can run concurrently; 10,000 databases; 200,000 tables per database; 10 million partitions per table and 20 million per account. **Max task DPUs per account**, the concurrent compute ceiling, defaults to 1,000 in us-east-1, us-east-2, us-west-2, ap-northeast-1, ap-southeast-2 and eu-west-1, and 500 elsewhere. Ray capacity is capped at 50 M-DPUs and is not adjustable.[^s3] Glue has endpoints in both AWS GovCloud (US) Regions as well as commercial Regions.[^s3]

Gotchas:

- **Glue for Ray is closed to new customers** as of April 30, 2026. Existing users keep running and get security and availability fixes but no new features; AWS points new Ray workloads to KubeRay on Amazon EKS.[^s5]
- Catalog costs come from object counts, so a heavily partitioned table with many versions can leave the free tier quickly, and the 10-million-partitions-per-table quota bounds over-partitioned designs.[^s2][^s3]
- The DPU quota is account-wide, so one large backfill can block scheduled jobs; ask for an increase or move non-urgent work to Flex.[^s2][^s3]

## When to use it (and when not)

AWS positions Glue for ETL, cataloging, data preparation, data lake management and event-driven ETL, billed per DPU-hour with no infrastructure to size. Amazon EMR suits teams already invested in Spark, Hive or Presto who want framework control, and is billed per instance-hour. For SQL-only teams without Spark skills, AWS suggests Athena or Redshift instead.[^s6] A common split is to use the Glue Data Catalog everywhere, including from EMR, while choosing between Glue jobs and EMR per workload.[^s6]

## Connections

- **Broader: data lake on AWS** (`kt:data-lake-on-aws`): Glue loads S3 data lakes and catalogs their contents.[^s1]
- **Integrates with Amazon Athena and Amazon S3** (`kt:amazon-athena`, `kt:amazon-s3`): crawlers catalog S3 data that Athena then queries.[^s1][^s6]
- **Alternative to Amazon EMR** (`kt:amazon-emr`): serverless Spark per DPU-hour versus managed clusters per instance-hour.[^s6]
- **Related to AWS Lake Formation, Amazon Redshift and Amazon EKS** (`kt:aws-lake-formation`, `kt:amazon-redshift`, `kt:amazon-eks`): Lake Formation secures the catalog, Redshift Spectrum reads it, and EKS is AWS's path off Glue for Ray.[^s1][^s5]
- **Monitored by AWS CloudTrail** (`kt:aws-cloudtrail`), **governed by Glue quotas** (`kt:glue-quotas`) and **scoped to AWS Regions** (`kt:aws-regions`): quotas apply per account per Region.[^s1][^s3]

## Sources
[^s1]: Amazon Web Services, "What is AWS Glue?". https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html (accessed 2026-11-09)
[^s2]: Amazon Web Services, "AWS Glue Pricing". https://aws.amazon.com/glue/pricing (accessed 2026-11-09)
[^s3]: Amazon Web Services, "AWS Glue endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/glue.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "AWS Glue versions". https://docs.aws.amazon.com/glue/latest/dg/release-notes.html (accessed 2026-11-09)
[^s5]: Amazon Web Services, "AWS Glue for Ray end of support". https://docs.aws.amazon.com/glue/latest/dg/awsglue-ray-jobs-availability-change.html (accessed 2026-11-09)
[^s6]: Amazon Web Services, "Amazon EMR and AWS Glue — AWS Product Comparison". https://aws.amazon.com/compare/emr-and-glue/ (accessed 2026-11-09)
