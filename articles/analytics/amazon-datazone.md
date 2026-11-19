---
id: kt:amazon-datazone
type: Service
title: Amazon DataZone
aliases: [DataZone, AWS DataZone]
domain: analytics
summary: >-
  AWS's data management service for cataloging, discovering, sharing and governing data across
  AWS, on-premises and third-party sources, organized around domains, projects, a business data
  catalog with glossaries, and a publish/subscribe access workflow fulfilled through Lake Formation
  and Amazon Redshift grants.
verified: 2026-11-19
status: current
broader: [kt:aws-analytics-services]
related: [kt:data-lake-on-aws, kt:amazon-sagemaker-unified-studio, kt:amazon-athena]
relations:
  - {predicate: dependsOn, object: kt:aws-lake-formation}
  - {predicate: integratesWith, object: kt:aws-glue}
  - {predicate: integratesWith, object: kt:amazon-redshift}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: securedBy, object: kt:iam-identity-center}
sources:
  - id: s1
    title: "Amazon DataZone terminology and concepts - Amazon DataZone"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/datazone/latest/userguide/datazone-concepts.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Amazon DataZone FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/datazone/faqs/
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Amazon DataZone Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/datazone/pricing/
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "Amazon DataZone updates pricing and removes the user-level subscription fee"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2024/11/amazon-datazone-pricing-removes-user-subscription-fee
    type: official-blog
    accessed: 2026-11-19
  - id: s5
    title: "Upgrade Amazon DataZone domains to Amazon SageMaker unified domains - Amazon DataZone"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/datazone/latest/userguide/upgrade-domain.html
    type: official-docs
    accessed: 2026-11-19
  - id: s6
    title: "Amazon SageMaker Data and AI Governance FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/sagemaker/catalog/faqs/
    type: official-docs
    accessed: 2026-11-19
  - id: s7
    title: "Amazon DataZone Now Generally Available – Collaborate on Data Projects across Organizational Boundaries"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/amazon-datazone-now-generally-available-collaborate-on-data-projects-across-organizational-boundaries/
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# Amazon DataZone

Amazon DataZone is a data management service for cataloging, discovering, sharing and governing data held in AWS, on premises and in third-party sources. It adds a business layer on top of technical catalogs: a searchable business data catalog with glossaries, projects that group people and tools around a use case, and built-in workflows for requesting and approving access to data.[^s2] AWS announced general availability on October 4, 2023, initially in eleven Regions; the launch post describes a self-service data portal that sits outside the AWS Management Console.[^s7]

## How it works

Everything hangs off a **domain**, the organizing entity that ties together assets, users and projects. An enterprise can run a single domain or one per business unit, and **domain units** subdivide a domain so that authority over resources such as environment profiles can be delegated to unit owners.[^s1] **Projects** are business use-case groupings of people, assets and tools, with member roles such as owner, contributor, consumer, viewer and steward. Projects get infrastructure through **environments**: sets of configured resources (an S3 bucket, a Glue database, an Athena workgroup) that IAM principals can use, created from environment profiles built on **blueprints**. The default blueprints are Data Lake, Data Warehouse and Amazon SageMaker.[^s1]

Metadata enters through **data sources** that connect to the AWS Glue Data Catalog or Amazon Redshift and import technical metadata such as table and column names and types, on a manual, scheduled or automated run. Imported objects become **assets** in a project's inventory; each edit creates a new version, and the latest version can be published to the catalog. Publishers enrich assets with **business glossary** terms and administrator-defined **metadata forms**.[^s1] At GA, AWS described machine-learning-generated business names for tables and columns as part of catalog curation.[^s7] Beyond Glue and Redshift, the FAQ lists Glue connectors, the Amazon AppFlow integration and custom asset types as ways to bring in other sources.[^s2]

Access follows a **publish/subscribe** model. A consuming project subscribes to a published asset; the owner approves or rejects; and approval triggers fulfillment, which creates grants in AWS Lake Formation or Amazon Redshift so project members can query the data. For unmanaged assets, DataZone emits an Amazon EventBridge event so you can fulfill the grant yourself and then report the subscription status back through the API.[^s1] Users reach all of this through the **data portal**, which authenticates with IAM credentials or IAM Identity Center; consumers can then query subscribed data from Athena or the Redshift Query Editor, and the service can also be driven by API, CLI, CloudFormation and the CDK.[^s1][^s2]

## Pricing and gotchas

Since November 1, 2024 there is no per-user monthly subscription fee; DataZone is billed on usage, and the same change cut metadata storage from $0.417 to $0.40 per GB and made core domain, blueprint and project APIs free.[^s4] As listed on the pricing page (as of 2026-11; the page does not name a Region), metadata storage is $0.40 per GB with 20 MB free per month, requests are $10 per 100,000 with 4,000 free, compute is $1.776 per compute unit with 0.2 units free, and AI recommendations are charged per 1,000 input and output tokens. Core calls such as CreateDomain, CreateProject and Search are never charged, and the underlying Glue, Athena, Redshift, S3 and KMS usage is billed separately by those services.[^s3]

Gotchas: DataZone governs and brokers access but does not store or query data itself, so the Lake Formation and Redshift permission models underneath still matter. Subscription fulfillment is automatic only for managed sources; anything else needs your own EventBridge-driven automation.[^s1]

## DataZone and Amazon SageMaker

AWS's newer governance experience, Amazon SageMaker Catalog in SageMaker Unified Studio, is built on DataZone and offers the same governance capabilities, while the standalone DataZone experience remains available for customers who prefer it.[^s6] An existing DataZone domain can be upgraded in the console to a SageMaker unified domain: domain and project IDs, assets, glossaries, metadata forms and subscriptions carry over, and the DataZone portal stays usable until an administrator turns it off. Environments are not carried into Unified Studio (project profiles replace them), CloudFormation logic may need to distinguish `domainVersion` V1 from V2, and a rollback is blocked once any project has been created in Unified Studio.[^s5]

## When to use it (and when not)

Choose DataZone when the problem is organizational: many producer and consumer teams, a need for business-friendly search and glossaries, and auditable request-and-approve access across accounts. If you only need fine-grained table permissions within a data lake, AWS Lake Formation alone may suffice; if teams also need notebooks, SQL and ML in one workspace, the upgrade path to SageMaker Unified Studio is the more integrated option.[^s1][^s5][^s6]

## Connections

DataZone sits in the `kt:aws-analytics-services` portfolio and is the governance and sharing layer for a `kt:data-lake-on-aws`. It **depends on** `kt:aws-lake-formation` to fulfill subscriptions and **integrates with** `kt:aws-glue` and `kt:amazon-redshift` as metadata sources and grant targets, and with `kt:amazon-eventbridge` for custom fulfillment.[^s1] Portal sign-in is **secured by** `kt:iam-identity-center` or IAM.[^s1] Its successor experience, `kt:amazon-sagemaker-unified-studio`, is built on the same foundation, and consumers typically query subscribed data with `kt:amazon-athena`.[^s2][^s6]

## Sources
[^s1]: Amazon Web Services, "Amazon DataZone terminology and concepts". https://docs.aws.amazon.com/datazone/latest/userguide/datazone-concepts.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Amazon DataZone FAQs". https://aws.amazon.com/datazone/faqs/ (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Amazon DataZone Pricing". https://aws.amazon.com/datazone/pricing/ (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Amazon DataZone updates pricing and removes the user-level subscription fee" (What's New, 2024-11-08). https://aws.amazon.com/about-aws/whats-new/2024/11/amazon-datazone-pricing-removes-user-subscription-fee (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Upgrade Amazon DataZone domains to Amazon SageMaker unified domains". https://docs.aws.amazon.com/datazone/latest/userguide/upgrade-domain.html (accessed 2026-11-19)
[^s6]: Amazon Web Services, "Amazon SageMaker Data and AI Governance FAQs". https://aws.amazon.com/sagemaker/catalog/faqs/ (accessed 2026-11-19)
[^s7]: Amazon Web Services, "Amazon DataZone Now Generally Available – Collaborate on Data Projects across Organizational Boundaries" (AWS News Blog, 2023-10-04). https://aws.amazon.com/blogs/aws/amazon-datazone-now-generally-available-collaborate-on-data-projects-across-organizational-boundaries/ (accessed 2026-11-19)
