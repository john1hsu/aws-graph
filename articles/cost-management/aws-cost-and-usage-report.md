---
id: kt:aws-cost-and-usage-report
type: Feature
title: AWS Cost and Usage Report
aliases: [Cost and Usage Report, CUR, CUR 2.0, Cost and Usage Report 2.0, Legacy CUR]
domain: cost-management
summary: >-
  AWS's most detailed billing dataset: line-item cost and usage data, at hourly, daily or
  monthly granularity, delivered on a recurring basis to an Amazon S3 bucket. The current
  version, CUR 2.0, is created as an export in AWS Data Exports with a fixed schema and
  SQL-based column and row selection; the original "legacy CUR" is still available.
verified: 2026-11-18
status: current
broader: [kt:aws-billing-and-cost-management, kt:aws-cost-management-services]
related: [kt:aws-cost-explorer, kt:cost-allocation-tags]
relations:
  - {predicate: configuredVia, object: kt:aws-data-exports}
  - {predicate: dependsOn, object: kt:amazon-s3}
  - {predicate: queriedWith, object: kt:amazon-athena}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is AWS Data Exports? - AWS Data Exports"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cur/latest/userguide/what-is-data-exports.md
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Migrating from CUR to Data Exports CUR 2.0 - AWS Data Exports"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cur/latest/userguide/dataexports-migrate.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Cost and Usage Report (CUR) 2.0 - AWS Data Exports"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cur/latest/userguide/table-dictionary-cur2.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Understanding export delivery - AWS Data Exports"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cur/latest/userguide/dataexports-export-delivery.html
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Quotas and restrictions (Data Exports) - AWS Data Exports"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cur/latest/userguide/dataexports-quotas.html
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "Quotas and restrictions (Cost and Usage Reports) - AWS Data Exports"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cur/latest/userguide/billing-cur-limits.html
    type: official-docs
    accessed: 2026-11-18
  - id: s7
    title: "COST03-BP01 Configure detailed information sources - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/cost_monitor_usage_detailed_source.html
    type: official-docs
    accessed: 2026-11-18
  - id: s8
    title: "AWS Certified Solutions Architect - Associate (SAA-C03) Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.pdf
    type: official-learning
    accessed: 2026-11-18
created: 2026-11-18
---

# AWS Cost and Usage Report

The AWS Cost and Usage Report (CUR) is the most granular cost and usage data AWS publishes about an account or organization. Each row is a line item for a product, usage type and operation over a time slice, with rates, costs and attributes such as tags, location, resource attributes and account IDs.[^s7] Rather than being viewed in a console, the report is delivered as files to an Amazon S3 bucket you own and then analyzed with your own tools.[^s1] AWS now calls the current version **CUR 2.0** and describes it as the recommended way to receive detailed cost and usage data. It is created as a standard export in **AWS Data Exports**, which also produces FOCUS, Cost Optimization Hub recommendation and carbon-emissions tables. The original report is still offered there as a "legacy data export", managed through a separate set of API actions.[^s1]

## How it works

You create a CUR 2.0 export from the Data Exports page of the Billing and Cost Management console, or with the AWS CLI or SDKs. The console offers column picking; the CLI and SDKs accept basic SQL to select columns, filter rows and rename columns, which lets you drop sensitive cost fields or charges for particular linked accounts.[^s1] Table configuration options set the time granularity (hourly, daily or monthly) and optionally add a line item per resource ID, split cost allocation data for containers, capacity reservation columns (populated from 1 November 2025) and an IAM principal column for Amazon Bedrock inference costs (populated from 8 April 2026). Resource IDs in particular can greatly increase row counts and file sizes.[^s3] An export created in the management account of AWS Organizations covers the management account and every member account. One created in a member account covers only that account, and only for the period it has belonged to the organization.[^s3]

Compared with legacy CUR, CUR 2.0 keeps the same schema from month to month instead of adding columns as your tags and cost categories change. It nests tags, cost categories, product attributes and discounts into key-value columns to cut sparsity, and it adds account-name and IAM principal columns. Both versions deliver ZIP, GZIP or Parquet files to S3, and both integrate with Athena, Redshift and QuickSight. Only CUR 2.0 supports the SQL customization, so legacy CUR users build Athena or QuickSight views instead. Migrating has two paths: a SQL query that reproduces the old CUR schema for existing pipelines, or adopting the new schema.[^s2]

## Delivery, quotas and pricing

A CUR 2.0 export refreshes whenever its source data changes, at least once a day, until the billing period closes. AWS may still update the previous period during the first two weeks after it ends.[^s4] Files land under a `BILLING_PERIOD=YYYY-MM` partition. In *overwrite* mode only the latest refresh is kept, which saves storage. In *create new* mode every refresh is kept in its own timestamped folder. A `Manifest.json` lists the current data files and is written only after all of them arrive, so ingest jobs should read it rather than list the bucket.[^s4]

As of 2026-11, Data Exports allows 5 CUR 2.0 exports and 2 each of the FOCUS, recommendations, dashboard and carbon tables; the documentation does not say whether these are adjustable.[^s5] Legacy CUR allows 10 reports per account. AWS states that Cost and Usage Reports are free, but standard Amazon S3 storage rates apply to the delivered files.[^s6]

## When to use it (and when not)

The Well-Architected cost optimization pillar rates configuring detailed cost information sources as high risk if you skip it. It recommends at least one hourly export with resource IDs, plus coarser exports for summary views, and split cost allocation data for Amazon ECS and EKS chargeback.[^s7] Use CUR when you need line-item detail, chargeback by team or tag, or billing data joined with business data in a warehouse. For interactive charts and forecasts, Cost Explorer is simpler. For a ready-made view, Data Exports can deploy a prebuilt cost and usage dashboard in QuickSight instead.[^s1][^s7] New pipelines should start on CUR 2.0 rather than legacy CUR.[^s1][^s2]

## Connections

CUR is part of **AWS Billing and Cost Management** and the wider **cost management services** family, and it is **configured via AWS Data Exports**.[^s1] It **depends on Amazon S3** for delivery and is typically **queried with Amazon Athena** in standard mode; the Well-Architected guidance names Athena for SQL analysis of the exported data.[^s4][^s7] It complements **AWS Cost Explorer**, the console tool for interactive analysis. **Cost allocation tags** and cost categories appear in its nested columns, which makes it the usual chargeback source.[^s2][^s3] The SAA-C03 exam guide lists the AWS Cost and Usage Report among in-scope cost management services and in its cost-optimized architecture tasks, so it is **covered by the AWS Certified Solutions Architect – Associate**.[^s8]

## Sources
[^s1]: Amazon Web Services, "What is AWS Data Exports?". https://docs.aws.amazon.com/cur/latest/userguide/what-is-data-exports.md (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Migrating from CUR to Data Exports CUR 2.0". https://docs.aws.amazon.com/cur/latest/userguide/dataexports-migrate.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Cost and Usage Report (CUR) 2.0". https://docs.aws.amazon.com/cur/latest/userguide/table-dictionary-cur2.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Understanding export delivery". https://docs.aws.amazon.com/cur/latest/userguide/dataexports-export-delivery.html (accessed 2026-11-18)
[^s5]: Amazon Web Services, "Quotas and restrictions" (Data Exports). https://docs.aws.amazon.com/cur/latest/userguide/dataexports-quotas.html (accessed 2026-11-18)
[^s6]: Amazon Web Services, "Quotas and restrictions" (Cost and Usage Reports). https://docs.aws.amazon.com/cur/latest/userguide/billing-cur-limits.html (accessed 2026-11-18)
[^s7]: Amazon Web Services, "COST03-BP01 Configure detailed information sources", AWS Well-Architected Framework. https://docs.aws.amazon.com/wellarchitected/latest/framework/cost_monitor_usage_detailed_source.html (accessed 2026-11-18)
[^s8]: Amazon Web Services, "AWS Certified Solutions Architect - Associate (SAA-C03) Exam Guide". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.pdf (accessed 2026-11-18)
