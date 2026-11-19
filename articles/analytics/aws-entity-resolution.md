---
id: kt:aws-entity-resolution
type: Service
title: AWS Entity Resolution
aliases: [Entity Resolution, Amazon Entity Resolution]
domain: analytics
summary: >-
  AWS's managed record-matching service: it links related records about the same customer,
  product or business across many datasets using rule-based, machine-learning or data-provider
  matching, and stamps each matched group with a shared match ID.
verified: 2026-11-19
status: current
broader: [kt:aws-analytics-services]
related: [kt:aws-clean-rooms, kt:data-lake-on-aws, kt:aws-data-exchange]
relations:
  - {predicate: dependsOn, object: kt:aws-glue}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:aws-clean-rooms}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: governedBy, object: kt:entity-resolution-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is AWS Entity Resolution? - AWS Entity Resolution"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/entityresolution/latest/userguide/what-is-service.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Quotas for AWS Entity Resolution - AWS Entity Resolution"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/entityresolution/latest/userguide/quotas.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "AWS Entity Resolution Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/entity-resolution/pricing
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "AWS Entity Resolution endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/entity-resolution.html
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "AWS Entity Resolution: Match and Link Related Records from Multiple Applications and Data Stores"
    publisher: AWS News Blog
    url: https://aws.amazon.com/blogs/aws/aws-entity-resolution-match-and-link-related-records-from-multiple-applications-and-data-stores
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# AWS Entity Resolution

AWS Entity Resolution is a managed service for matching, linking and enhancing related records that are scattered across applications, channels and data stores, so that, for example, the same customer appearing in a CRM export, a loyalty database and web sign-ups can be recognised as one entity.[^s1] It became generally available on 26 July 2023; the launch post demonstrated deduplicating a loyalty programme and also pointed at advertising and retail product-code reconciliation as typical uses.[^s5]

## How it works

You first define a **schema mapping** that tells the service what each input column means (name, email, phone, address and so on). It can be imported from an AWS Glue table or built in a visual or JSON editor, and input normalisation is on by default.[^s1] A **matching workflow** then reads up to 20 data inputs through AWS Glue, applies one matching technique and writes a consolidated output table to an Amazon S3 bucket in which related records share a common **match ID**.[^s1][^s5]

Three matching techniques are available:[^s1]

- **Rule-based** – ready-made rules you can add, remove or reorder; each match group records the number of the rule that produced it.[^s1]
- **Machine-learning-based** – a pre-configured model that uses name, email, phone, address and date-of-birth fields and gives every match group a confidence score between 0.0 and 1.0.[^s1]
- **Data service provider** – matching against a third-party reference dataset, which requires a licence with that provider.[^s1] The provider subscription is obtained through AWS Data Exchange, either publicly or as a bring-your-own-subscription private offer.[^s3]

Jobs can be run manually in bulk from the console, CLI or API, and rule-based workflows can also process new records automatically and incrementally.[^s1] For near-real-time use, the GetMatchId API looks up the existing match ID for a record, hashing personal data before the lookup.[^s1] Separate **ID mapping** workflows and **ID namespaces** translate identifiers between parties, which is how the service supports multi-party transcoding in collaborations such as AWS Clean Rooms.[^s1][^s2]

## Quotas, pricing and gotchas

Default quotas per Region (as of 2026-11): 10 matching workflows, 10 ID mapping workflows, 10 ID namespaces and 50 schema mappings, all adjustable. Only one matching job and one ID mapping job can run at a time, and those two concurrency limits are not adjustable.[^s2] Workflow size limits vary by technique and Region: a rule-based or provider matching workflow handles 100,000,000 records, while an ML-based workflow handles 600,000,000 records in larger Regions such as us-east-1 but 150,000,000 in af-south-1, ap-northeast-2 and eu-west-2 (adjustable).[^s2] GetMatchId is limited to 50 requests per second and GenerateMatchId to 10, both adjustable.[^s2]

Pricing (as of 2026-11; the page states it does not vary by Region): rule-based or ML matching costs $0.25 per 1,000 records processed, and provider matching $0.10 per 1,000 records plus the provider's own subscription fee.[^s3] Every processed row counts, including records that find no match, and there is no Free Tier for the service.[^s3] Automatic incremental processing bills only the new records but is not available for ML matching, so repeated ML runs reprocess the full input.[^s3] Entity Resolution used inside AWS Clean Rooms is priced separately.[^s3]

The service runs in twelve commercial Regions across North America, Europe, Asia Pacific and Africa; FIPS endpoints exist only in us-east-1, us-east-2, us-west-2 and ca-central-1, and provider workflows only in the three US Regions.[^s4][^s3] The single-job concurrency limit is the main operational gotcha: large backfills across several workflows queue behind each other.[^s2]

## When to use it (and when not)

Use Entity Resolution when you need a durable, shared identifier for people, products or businesses across many sources and do not want to build and tune your own fuzzy-matching pipeline; ML matching suits messy consumer data, while rule-based matching is more explainable and supports incremental updates.[^s1][^s3] If your data already shares a reliable key, a plain join in Athena or AWS Glue is cheaper. When the records belong to different companies that must not see each other's data, run the matching through AWS Clean Rooms rather than exchanging files.[^s1]

## Connections

Entity Resolution is one of the AWS analytics services and **depends on** AWS Glue tables for its inputs and schema imports, writing results to **Amazon S3**.[^s1] It **integrates with** AWS Clean Rooms for ID mapping inside privacy-protected collaborations,[^s1][^s3] relies on AWS Data Exchange for provider subscriptions,[^s3] and is **secured by** encryption keys managed in AWS KMS for its data inputs.[^s5][^s1] API activity is **monitored by** AWS CloudTrail,[^s1] and capacity is **governed by** the per-Region quotas published in the user guide and AWS General Reference.[^s2][^s4] It is a natural enrichment step in a data lake on AWS, linking records held across multiple data stores into one consolidated view.[^s1]

## Sources
[^s1]: Amazon Web Services, "What is AWS Entity Resolution?". https://docs.aws.amazon.com/entityresolution/latest/userguide/what-is-service.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Quotas for AWS Entity Resolution". https://docs.aws.amazon.com/entityresolution/latest/userguide/quotas.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "AWS Entity Resolution Pricing". https://aws.amazon.com/entity-resolution/pricing (accessed 2026-11-19)
[^s4]: Amazon Web Services, "AWS Entity Resolution endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/entity-resolution.html (accessed 2026-11-19)
[^s5]: AWS News Blog, "AWS Entity Resolution: Match and Link Related Records from Multiple Applications and Data Stores" (2023-07-26). https://aws.amazon.com/blogs/aws/aws-entity-resolution-match-and-link-related-records-from-multiple-applications-and-data-stores (accessed 2026-11-19)
