---
id: kt:amazon-appflow
type: Service
title: Amazon AppFlow
aliases: [AppFlow, AWS AppFlow]
domain: serverless-and-integration
summary: >-
  AWS's fully managed, no-code integration service that moves data between SaaS applications such as
  Salesforce, SAP, ServiceNow and Slack and AWS services such as Amazon S3 and Amazon Redshift, running
  flows on demand, on a schedule or on a SaaS event, with mapping, filtering and validation along the way.
verified: 2026-11-18
status: current
broader: [kt:aws-application-integration-services]
related: [kt:amazon-s3, kt:amazon-redshift, kt:amazon-eventbridge, kt:aws-step-functions, kt:data-lake-on-aws]
relations:
  - {predicate: alternativeTo, object: kt:aws-glue}
  - {predicate: contrastsWith, object: kt:aws-datasync}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: securedBy, object: kt:aws-privatelink}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: governedBy, object: kt:appflow-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
  - {predicate: coveredBy, object: kt:aws-certified-data-engineer-associate}
sources:
  - id: s1
    title: "What is Amazon AppFlow?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/appflow/latest/userguide/what-is-appflow.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Amazon AppFlow endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/appflow.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Amazon AppFlow pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/appflow/pricing/
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Amazon AppFlow FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/appflow/faqs/
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Amazon AppFlow features"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/appflow/features/
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "Private Amazon AppFlow flows"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ru_ru/appflow/latest/userguide/private-flows.html
    type: official-docs
    accessed: 2026-11-18
  - id: s7
    title: "Amazon AppFlow now supports AWS Glue Data Catalog integration and provides enhanced data preparation"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2022/11/amazon-appflow-aws-glue-data-catalog-integration-provides-enhanced-data-preparation
    type: official-blog
    accessed: 2026-11-18
  - id: s8
    title: "Combining Amazon AppFlow with AWS Step Functions to maximize application integration benefits"
    publisher: Amazon Web Services (AWS Compute Blog)
    url: https://aws.amazon.com/blogs/compute/combining-amazon-appflow-with-aws-step-functions-to-maximize-application-integration-benefits
    type: official-blog
    accessed: 2026-11-18
  - id: s9
    title: "In-Scope AWS Services (AWS Certified Solutions Architect – Associate SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-18
  - id: s10
    title: "In-Scope AWS Services (AWS Certified Data Engineer – Associate DEA-C01)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/data-engineer-associate-01/dea-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-18
created: 2026-11-18
---

# Amazon AppFlow

Amazon AppFlow is a fully managed integration service for moving data between software-as-a-service (SaaS) applications, such as Salesforce, Slack, Zendesk and Marketo, and AWS services such as Amazon S3 and Amazon Redshift. You build these data flows in the console without writing code.[^s1] AWS pitches it as a replacement for the custom API connectors teams would otherwise write and maintain, and as a tool business users can drive themselves.[^s4]

## How it works

A **flow** connects a source to a destination through a connector. AWS's features page cites over 50 SaaS connectors, including native integrations with Salesforce, SAP, Google Analytics and Facebook Ads.[^s5] Where no connector exists, the open-source Custom Connector SDKs for Python and Java let you build one for private APIs, on-premises systems or other clouds.[^s1] Transfers can go both ways. For example, a flow can push records or churn scores from S3 back into Salesforce.[^s4]

Flows run **on demand**, on a **schedule**, or in response to a **business event** such as a new sales opportunity or a change in a support ticket's status.[^s5] As data moves, you can map fields (in bulk, one at a time or from a CSV file) and apply merging, masking, filtering and validation.[^s5] For S3 destinations, AppFlow can split output into folders by partition and combine records into files of a target size. It can also register the result in the AWS Glue Data Catalog so Athena, Glue and QuickSight can query it. AWS added these data-preparation features in November 2022.[^s7]

**Security.** Data is encrypted at rest and in transit. AppFlow uses an AWS managed key by default, and you can supply a customer managed key from AWS KMS.[^s4] **Private flows** use AWS PrivateLink, so data never crosses the public internet. They are supported for Salesforce, Singular, Snowflake and Trend Micro, and the SaaS account must be enabled for PrivateLink. AppFlow creates and removes the VPC endpoint service configuration for you. The console still makes metadata calls, such as listing objects and fields, over public endpoints.[^s6]

## Quotas, pricing and gotchas

Quotas apply per account per Region (as of 2026-11).[^s2]

- **Adjustable:** 1,000 flows in total, 100 connector profiles, 1,000 concurrent flow runs and 10,000,000 flow runs per month.
- **Fixed:** a single flow run can move at most 100 GB. Many connectors have their own limits. Salesforce, ServiceNow and Slack flows can run at most once per minute, Marketo once per hour, and Google Analytics and Amplitude once per day. A Salesforce import can be at most 15 GB and an export at most 500 MB.[^s2]

AppFlow is listed in 16 commercial Regions. FIPS endpoints exist only in the four US Regions, and no AWS GovCloud (US) Region is listed. AppFlow writes into S3 through a VPC endpoint, so a bucket policy that allows only certain IP addresses and denies all others will block it.[^s2]

As of 2026-11, AppFlow costs $0.001 per successful flow run plus $0.02 per GB of data processed. The pricing page uses these rates in both its US East (N. Virginia) and Europe (Frankfurt) examples. There are no upfront fees. S3 requests and storage and KMS key usage are billed separately. Watch for two things. A scheduled run that finds no new data still counts as a billable run. Each extra concurrent process in a run, as used by SAP parallel transfers, is billed as another flow run.[^s3]

## When to use it (and when not)

Choose AppFlow when the source or destination is a SaaS application reached through its API, and you want a managed, low-code pipeline. AWS's FAQ draws the boundaries this way:[^s4]

- **AWS Glue** is the ETL service for data that already lives in AWS, mostly from JDBC-compatible databases.[^s4]
- **AWS DataSync** handles bulk transfers of tens or hundreds of terabytes from on premises.[^s4]
- **Amazon EventBridge** routes SaaS events to compute targets for event-driven applications, rather than copying datasets.[^s4]

A common pattern pairs the two: AppFlow lands SaaS data in an S3 data lake, and Glue catalogs it for querying in Athena or QuickSight.[^s8]

## Connections

AppFlow is one of the **application integration services**, and SAA-C03 and DEA-C01 both list it in that category.[^s9][^s10] Its usual destinations are **Amazon S3** for a **data lake** and **Amazon Redshift**.[^s1] It **integrates with EventBridge** in both directions. It can take in Salesforce events, and every completed flow run publishes an event. AWS's Compute Blog shows that run event starting an **AWS Step Functions** workflow, which creates or updates Glue tables and partitions.[^s1][^s8] Private flows are **secured by AWS PrivateLink**, and data is **secured by AWS KMS** keys.[^s4][^s6] Its quotas are a separate node (`kt:appflow-quotas`), and the service is **scoped to** individual AWS Regions.[^s2] It is an **alternative to** AWS Glue for SaaS ingestion and **contrasts with** AWS DataSync for bulk file transfers.[^s4]

## Sources
[^s1]: Amazon Web Services, "What is Amazon AppFlow?". https://docs.aws.amazon.com/appflow/latest/userguide/what-is-appflow.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Amazon AppFlow endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/appflow.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Amazon AppFlow pricing". https://aws.amazon.com/appflow/pricing/ (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Amazon AppFlow FAQs". https://aws.amazon.com/appflow/faqs/ (accessed 2026-11-18)
[^s5]: Amazon Web Services, "Amazon AppFlow features". https://aws.amazon.com/appflow/features/ (accessed 2026-11-18)
[^s6]: Amazon Web Services, "Private Amazon AppFlow flows". https://docs.aws.amazon.com/ru_ru/appflow/latest/userguide/private-flows.html (accessed 2026-11-18)
[^s7]: Amazon Web Services, "Amazon AppFlow now supports AWS Glue Data Catalog integration and provides enhanced data preparation". https://aws.amazon.com/about-aws/whats-new/2022/11/amazon-appflow-aws-glue-data-catalog-integration-provides-enhanced-data-preparation (accessed 2026-11-18)
[^s8]: Amazon Web Services, "Combining Amazon AppFlow with AWS Step Functions to maximize application integration benefits". https://aws.amazon.com/blogs/compute/combining-amazon-appflow-with-aws-step-functions-to-maximize-application-integration-benefits (accessed 2026-11-18)
[^s9]: Amazon Web Services, "In-Scope AWS Services (SAA-C03)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-18)
[^s10]: Amazon Web Services, "In-Scope AWS Services (DEA-C01)". https://docs.aws.amazon.com/aws-certification/latest/data-engineer-associate-01/dea-01-in-scope-services.html (accessed 2026-11-18)
