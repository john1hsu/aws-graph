---
id: kt:aws-data-exports
type: Service
title: AWS Data Exports
aliases: [Data Exports, BCM Data Exports, Billing and Cost Management Data Exports]
domain: cost-management
summary: >-
  The Billing and Cost Management feature that delivers recurring exports of billing, cost-optimization
  and carbon-emissions tables (CUR 2.0, FOCUS 1.0 and 1.2, Cost Optimization Hub recommendations, carbon
  emissions) to Amazon S3, shaped by a basic SQL query, and that can also deploy a prebuilt QuickSight
  cost and usage dashboard.
verified: 2026-11-20
status: current
broader: [kt:aws-billing-and-cost-management, kt:aws-cost-management-services]
related: [kt:cost-optimization-hub, kt:aws-organizations, kt:sustainability-pillar]
relations:
  - {predicate: hasPart, object: kt:aws-cost-and-usage-report}
  - {predicate: implements, object: kt:finops-open-cost-and-usage-specification}
  - {predicate: dependsOn, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:amazon-athena}
  - {predicate: integratesWith, object: kt:amazon-redshift}
  - {predicate: integratesWith, object: kt:amazon-quick}
sources:
  - id: s1
    title: "What is AWS Data Exports? - AWS Data Exports"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cur/latest/userguide/what-is-data-exports.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Creating data exports - AWS Data Exports"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cur/latest/userguide/dataexports-create.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Creating a standard export - AWS Data Exports"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cur/latest/userguide/dataexports-create-standard.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "Setting up an Amazon S3 bucket for data exports - AWS Data Exports"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cur/latest/userguide/dataexports-s3-bucket.html
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "Data query–SQL query and table configurations - AWS Data Exports"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cur/latest/userguide/dataexports-data-query.html
    type: official-docs
    accessed: 2026-11-20
  - id: s6
    title: "AWS Data Exports for FOCUS 1.2 is now generally available"
    publisher: What's New with AWS
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/aws-data-exports-focus-1-2-available/
    type: official-blog
    accessed: 2026-11-20
  - id: s7
    title: "AWS Data Exports now supports cross-account delivery"
    publisher: What's New with AWS
    url: https://aws.amazon.com/about-aws/whats-new/2026/03/aws-data-exports-cross-account-delivery-cost/
    type: official-blog
    accessed: 2026-11-20
  - id: s8
    title: "FOCUS - FinOps Open Cost & Usage Specification"
    publisher: FinOps Foundation
    url: https://focus.finops.org/
    type: standards-body
    accessed: 2026-11-20
created: 2026-11-20
---

# AWS Data Exports

AWS Data Exports is the part of the Billing and Cost Management console that produces recurring, file-based exports of billing and sustainability data. You pick a table, shape it with basic SQL (column selection, row filters, column renaming), and AWS delivers the result to an Amazon S3 bucket on a schedule. The console offers a column picker; the CLI and SDKs accept the SQL directly. Trimming columns and rows keeps files smaller and lets you strip sensitive cost fields or charges for particular linked accounts before the data leaves billing.[^s1]

## What it exports

Data Exports has three export types.[^s1][^s2]

- **Standard data export**, built from one table: Cost and Usage Report 2.0 (CUR 2.0, the recommended detailed cost dataset), Cost Optimization Hub recommendations, FOCUS 1.2 with AWS columns, FOCUS 1.0 with AWS columns, or carbon emissions.[^s1]
- **Cost and usage dashboard**, which exports data and deploys a prebuilt dashboard in QuickSight. It needs QuickSight Enterprise Edition.[^s1][^s2]
- **Legacy data export**, the original Cost and Usage Report. It is managed through the separate CUR API actions, not the Data Exports API.[^s1]

FOCUS is the FinOps Foundation's open specification for normalizing billing data across cloud, SaaS and other vendors. As of 2026-11 the specification site lists FOCUS 1.4 as its newest release and AWS as a v1.2 data generator.[^s8] AWS made FOCUS 1.2 exports generally available on 2025-11-19. The export keeps FOCUS's four cost columns (ListCost, ContractedCost, BilledCost, EffectiveCost) and adds invoice reconciliation, capacity reservation status and virtual-currency support. It runs from us-east-1 and covers usage in all Regions except GovCloud (US) and China.[^s6]

## How an export is defined and delivered

Each export is a **SQL query** plus **table configurations**. Configurations change the table before the query runs. For CUR 2.0 they set hourly, daily or monthly granularity and can add resource-level rows or split cost allocation data. The query supports SELECT, AS, FROM, WHERE and LIMIT, with dot notation to pull attributes out of MAP and STRUCT columns. A statement can be up to 36,000 characters. You can edit the query later, but you cannot change the table an export reads from.[^s5]

Delivery options:[^s3]

- **Cadence:** billing and cost exports refresh at most once a day. Carbon exports refresh monthly, each delivering the previous month's data.[^s3]
- **File versioning:** overwrite each partition, which saves S3 storage, or write a new version every time to track how billing data changes. Recommendations exports cannot use overwrite.[^s3]
- **Format:** Parquet, gzip CSV or zip CSV. The Athena integration preset picks Parquet with overwrite, and the Redshift preset picks gzip CSV. Both presets deliver a setup script.[^s3]

The first delivery can take up to 24 hours to arrive.[^s2] The recommendations table needs a service-linked role. The carbon table needs the `sustainability:GetCarbonFootprintSummary` IAM permission.[^s3]

## S3 bucket, accounts and quotas

The destination bucket must carry a policy that lets the `bcm-data-exports.amazonaws.com` service principal call `s3:PutObject`. Conditions on `aws:SourceArn` and `aws:SourceAccount` restrict it to the exporting account. The policy gives AWS no permission to read or delete objects. Changing the bucket policy or the bucket's owner after setup can break delivery and shows up as an "Invalid bucket" error. Exported files are billed at standard S3 storage rates.[^s4] Since 2026-04-14, standard exports can go straight to a bucket in another authorized account, such as a central FinOps analytics account. The destination's bucket policy decides which source accounts may write. This works in commercial Regions except GovCloud (US) and China.[^s7]

As of 2026-11 the documented per-table limits are 5 CUR 2.0 exports and 2 each for recommendations, FOCUS 1.0, FOCUS 1.2, the dashboard and carbon emissions. The documentation does not say whether these limits can be raised.[^s2]

## When to use it

Use Data Exports when cost data has to reach your own analytics stack: Athena or Redshift queries, a data warehouse, or third-party FinOps tools. Choose CUR 2.0 for full AWS line-item detail. Choose FOCUS when you need one schema across several clouds or SaaS vendors.[^s1][^s6][^s8] For interactive charts without a pipeline, Cost Explorer or the prebuilt dashboard is simpler. Pick cross-account delivery over S3 replication when the management account should not host analytics workloads.[^s7]

## Connections

Data Exports sits **under** AWS Billing and Cost Management and the wider cost management services family.[^s1] It **has part** the Cost and Usage Report, since CUR 2.0 and legacy CUR are both created here, and it **implements** the FinOps Open Cost and Usage Specification through its FOCUS tables.[^s1][^s6] It **depends on** Amazon S3 for delivery and **integrates with** Amazon Athena and Amazon Redshift through delivery presets, and with AWS Quick (formerly QuickSight) for the prebuilt dashboard.[^s3][^s4][^s2] It is related to Cost Optimization Hub, whose recommendations it exports, and to AWS Organizations, because which accounts' data an export covers depends on whether a management or member account created it.[^s1][^s7] The carbon emissions table links it to the sustainability pillar.[^s3]

## Sources

[^s1]: Amazon Web Services, "What is AWS Data Exports?". https://docs.aws.amazon.com/cur/latest/userguide/what-is-data-exports.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "Creating data exports". https://docs.aws.amazon.com/cur/latest/userguide/dataexports-create.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "Creating a standard export". https://docs.aws.amazon.com/cur/latest/userguide/dataexports-create-standard.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "Setting up an Amazon S3 bucket for data exports". https://docs.aws.amazon.com/cur/latest/userguide/dataexports-s3-bucket.html (accessed 2026-11-20)
[^s5]: Amazon Web Services, "Data query–SQL query and table configurations". https://docs.aws.amazon.com/cur/latest/userguide/dataexports-data-query.html (accessed 2026-11-20)
[^s6]: What's New with AWS, "AWS Data Exports for FOCUS 1.2 is now generally available" (2025-11-19). https://aws.amazon.com/about-aws/whats-new/2025/11/aws-data-exports-focus-1-2-available/ (accessed 2026-11-20)
[^s7]: What's New with AWS, "AWS Data Exports now supports cross-account delivery" (2026-04-14). https://aws.amazon.com/about-aws/whats-new/2026/03/aws-data-exports-cross-account-delivery-cost/ (accessed 2026-11-20)
[^s8]: FinOps Foundation, "FOCUS - FinOps Open Cost & Usage Specification". https://focus.finops.org/ (accessed 2026-11-20)
