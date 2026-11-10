---
id: kt:amazon-redshift
type: Service
title: Amazon Redshift
aliases: [Redshift, AWS Redshift, Redshift Serverless, Amazon Redshift Serverless]
domain: analytics
summary: >-
  AWS's fully managed, massively parallel cloud data warehouse for SQL analytics, offered as
  provisioned clusters (RG and RA3 nodes on Redshift Managed Storage) or as Redshift Serverless
  billed per RPU-hour.
verified: 2026-11-10
status: current
broader: [kt:aws-analytics-services, kt:data-lake-on-aws]
related: [kt:aws-glue, kt:amazon-aurora, kt:amazon-dynamodb]
relations:
  - {predicate: alternativeTo, object: kt:amazon-athena}
  - {predicate: alternativeTo, object: kt:amazon-emr}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: governedBy, object: kt:redshift-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Amazon Redshift FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/redshift/faqs/
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Amazon Redshift Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/redshift/pricing/
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Compute capacity for Amazon Redshift Serverless"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/redshift/latest/mgmt/serverless-capacity.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Quotas and limits in Amazon Redshift"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/redshift/latest/mgmt/amazon-redshift-limits.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Amazon Redshift launches RG instances powered by AWS Graviton"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/05/amazon-redshift-rg-instances-powered-by-graviton/
    type: official-blog
    accessed: 2026-11-10
  - id: s6
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# Amazon Redshift

Amazon Redshift is AWS's managed cloud data warehouse: a SQL engine built for analytical queries over large, structured datasets, used as the central store behind BI dashboards and reporting. It comes in two deployment models, provisioned clusters that you size yourself and Redshift Serverless, which provisions and scales capacity automatically and bills only for what runs.[^s1] Where Amazon Athena queries files in place, Redshift is the system you load curated data into when queries are heavy, frequent and concurrent.

## How it works

A provisioned cluster is a set of nodes working as a massively parallel processing (MPP) system; AWS positions this architecture for complex BI and analytics at scale.[^s1] Current node families separate compute from storage: RA3 nodes and Serverless both use **Redshift Managed Storage (RMS)**, which keeps hot data on local high-performance SSDs and moves colder data to Amazon S3 automatically, so storage and compute are sized and billed independently.[^s1]

**RG instances**, generally available since 12 May 2026, are a newer Graviton-based provisioned family (launched as rg.xlarge and rg.4xlarge). AWS claims up to 2.4x faster performance than RA3 at 30% lower price per vCPU. RG nodes include a vectorized engine that reads Apache Iceberg and Parquet data directly on the cluster, removing the need for Redshift Spectrum's separate scan fleet and its per-TB charge. RA3 clusters can move to RG by snapshot-and-restore, elastic resize or classic resize.[^s5][^s1]

**Redshift Serverless** measures capacity in Redshift Processing Units (RPUs); one RPU provides 16 GB of memory. A workgroup's base capacity defaults to 128 RPUs and can be set from 4 up to 512 RPUs in most Regions, or 1,024 RPUs in five large Regions including us-east-1. A max-capacity setting and an RPU-hour cap bound how far it scales.[^s3] The 4-RPU option is limited to 32 TB of managed storage, is only offered in some Regions, and cannot drop back to 4 RPUs after being scaled above it.[^s3]

Beyond core SQL, Redshift offers **data sharing** (live, read-only access to data across clusters and accounts, e.g. separating ETL from BI), **Concurrency Scaling** (temporary extra capacity when queries queue), **zero-ETL integrations** that replicate Aurora MySQL/PostgreSQL, RDS for MySQL and DynamoDB data into Redshift without pipelines, **Redshift Spectrum** for querying S3 data, and **Multi-AZ** deployments that AWS states have an RPO of zero.[^s1]

## Quotas, pricing and gotchas

Pricing (as of 2026-11, US East (N. Virginia) examples on the pricing page; other Regions differ):[^s2]

- **Serverless:** $0.375 per RPU-hour, billed per second with a 60-second minimum; no charge for start-up time. Serverless Reservations give up to 24% (1-year) or 45% (3-year) savings and cannot be cancelled.[^s2][^s1]
- **Provisioned:** ra3.4xlarge on-demand at $3.26 per hour (pricing example); reserved instances are also offered.[^s2]
- **Managed storage:** $0.024 per GB-month, billed hourly on data stored.[^s2]
- **Spectrum:** $5.00 per TB scanned, rounded up to the next MB with a 10 MB minimum per query.[^s2]
- **Concurrency Scaling:** each cluster earns up to one hour of free credit per day, accruing to 30 hours; usage beyond that bills per second with a one-minute minimum.[^s2]

Default quotas (as of 2026-11): 128 nodes per RA3 or RG cluster and 200 nodes per account per Region (both adjustable); 10 Concurrency Scaling clusters (adjustable). Hard limits that cannot be raised include 2,000 concurrent connections per cluster or Serverless workgroup, 60 databases per cluster, 9,900 schemas per database, and 50 total slots across manual WLM queues. Table limits depend on node size: 9,900 on single small nodes, 20,000 on small multi-node clusters, and 200,000 on 4xlarge and larger nodes.[^s4]

Gotchas: Serverless charges per second whenever queries run, with a 60-second minimum each time, so tools that poll frequently can keep compute billing almost continuously; cap spend with a max capacity and max RPU-hours limit on the workgroup.[^s2][^s3] The table quota counts temporary tables, datashare tables and materialized views, which surprises teams with many staging tables.[^s4]

## When to use it (and when not)

AWS frames the choice by workload: Redshift for complex BI and analytics at scale as the central warehouse, Athena for interactive, ad-hoc exploration of data in S3 and other sources without loading data first.[^s1] Steady, high-concurrency reporting favours provisioned RG/RA3 with reservations; spiky or unpredictable workloads suit Serverless.[^s1][^s2] Amazon EMR remains the choice when you need open-source frameworks and control of the cluster rather than a SQL warehouse.

## Connections

- **Broader:** part of AWS analytics services (`kt:aws-analytics-services`) and the warehouse layer of a data lake on AWS (`kt:data-lake-on-aws`); it appears in the Analytics section of the Cloud Practitioner exam's in-scope services (`kt:aws-certified-cloud-practitioner`).[^s6]
- **Alternative to Amazon Athena and Amazon EMR** (`kt:amazon-athena`, `kt:amazon-emr`) depending on workload.[^s1]
- **Integrates with Amazon S3** (`kt:amazon-s3`): RMS tiers data to S3 and Spectrum/RG query lake data there.[^s1][^s5]
- **Related to Amazon Aurora and Amazon DynamoDB** (`kt:amazon-aurora`, `kt:amazon-dynamodb`) via zero-ETL, and **AWS Glue** (`kt:aws-glue`) for ETL and cataloguing.[^s1]
- **Governed by Redshift quotas** (`kt:redshift-quotas`) and **scoped to AWS Regions** (`kt:aws-regions`), where RPU limits and features vary.[^s4][^s3]

## Sources
[^s1]: Amazon Web Services, "Amazon Redshift FAQs". https://aws.amazon.com/redshift/faqs/ (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Amazon Redshift Pricing". https://aws.amazon.com/redshift/pricing/ (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Compute capacity for Amazon Redshift Serverless". https://docs.aws.amazon.com/redshift/latest/mgmt/serverless-capacity.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Quotas and limits in Amazon Redshift". https://docs.aws.amazon.com/redshift/latest/mgmt/amazon-redshift-limits.html (accessed 2026-11-10)
[^s5]: Amazon Web Services, What's New with AWS, "Amazon Redshift launches RG instances powered by AWS Graviton" (2026-05-12). https://aws.amazon.com/about-aws/whats-new/2026/05/amazon-redshift-rg-instances-powered-by-graviton/ (accessed 2026-11-10)
[^s6]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html (accessed 2026-11-10)
