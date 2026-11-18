---
id: kt:aws-clean-rooms
type: Service
title: AWS Clean Rooms
aliases: [Clean Rooms, Amazon Clean Rooms]
domain: analytics
summary: >-
  AWS's data clean room service: members of a collaboration jointly analyze their combined
  datasets with SQL, PySpark or ML while each owner's analysis rules, optional client-side
  encryption and differential privacy keep the underlying records from being revealed or copied.
verified: 2026-11-18
status: current
broader: [kt:aws-analytics-services]
related: [kt:amazon-athena, kt:aws-lake-formation, kt:data-lake-on-aws]
relations:
  - {predicate: dependsOn, object: kt:aws-glue}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:aws-entity-resolution}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: governedBy, object: kt:clean-rooms-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is AWS Clean Rooms? - AWS Clean Rooms"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/clean-rooms/latest/userguide/what-is.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Analysis rules in AWS Clean Rooms - AWS Clean Rooms"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/clean-rooms/latest/userguide/analysis-rules.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "AWS Clean Rooms Glossary - AWS Clean Rooms"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/clean-rooms/latest/userguide/glossary.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "AWS Clean Rooms pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/clean-rooms/pricing/
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "AWS Clean Rooms endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/clean-rooms.html
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "AWS Clean Rooms Now Generally Available"
    publisher: AWS News Blog
    url: https://aws.amazon.com/es/blogs/aws/aws-clean-rooms-now-generally-available/
    type: official-blog
    accessed: 2026-11-18
created: 2026-11-18
---

# AWS Clean Rooms

AWS Clean Rooms is a managed "data clean room": it lets two or more organizations analyze the overlap of their datasets without either side handing its raw records to the other. A member creates a **collaboration**, invites other AWS accounts, links data and runs approved analyses inside that boundary; data stays where it lives and is read in place when a query or job runs.[^s1] The service became generally available in March 2023, aimed at use cases such as advertising measurement, audience enrichment and cross-organization research.[^s6]

## How it works

Each member registers a **configured table**, a reference to an existing AWS Glue Data Catalog table, typically over Amazon S3, which can be reused across several collaborations.[^s3] Sources now also include Glue Data Catalog views queried through Athena, Snowflake tables (credentials held in AWS Secrets Manager) and Databricks tables shared via Delta Sharing.[^s1] Analyses run either as SQL queries or PySpark jobs.[^s1]

Roles are fixed per collaboration: exactly one member can run queries, exactly one can receive results (to an S3 location, as CSV or Parquet), and one or more payer candidates can be named to cover compute.[^s3]

Every configured table needs an **analysis rule** before it can be queried, and the most restrictive rule wins when tables are joined:[^s2]

- **Aggregation** – only COUNT, SUM and AVG style results with inner joins and a minimum aggregation threshold, so no row-level output.[^s2]
- **List** – row-level lists of the overlap between tables, for enrichment or segment building.[^s2]
- **Custom** – broader SQL (subqueries, CTEs) but only through analysis templates or creators that the data owner has reviewed and approved; optional controls include aggregation thresholds, disallowed output columns and **differential privacy**.[^s2]

If two owners set different thresholds, the higher one applies and rows below it are silently dropped from results.[^s2] For stricter protection, the **Cryptographic Computing for Clean Rooms (C3R)** client encrypts data before upload, with "fingerprint" columns usable for joins and "sealed" columns usable only in SELECT output.[^s3] Add-ons include Clean Rooms ML lookalike modelling and ID mapping tables that use AWS Entity Resolution (rule-based or LiveRamp provider matching).[^s1][^s3] Queries and jobs are recorded in analysis logs for audit.[^s1]

## Quotas, pricing and gotchas

Default quotas per Region (as of 2026-11): 10 invited members per collaboration, 500 collaborations created per account, 250 configured tables per account and 15 configured tables per protected query, all adjustable. Concurrent SQL queries default to 5 in us-east-1 and 2 elsewhere (adjustable), while concurrent ongoing queries per membership are fixed at 5. SQL query text is capped at 90 KB and differential-privacy queries at 8 KB, neither adjustable.[^s5] The service has endpoints in 11 commercial Regions across the US, Europe and Asia Pacific, with FIPS endpoints only in the three US Regions.[^s5]

Pricing (us-east-1 example, as of 2026-11): Spark SQL compute costs $2.00 per CRPU-hour, billed per second with a 60-second minimum; PySpark costs $4.00 per CRPU-hour with a 10-minute minimum per job; differential privacy adds $2.00 per CRPU-hour. Rule-based entity matching costs $0.50 per 1,000 records matched plus a one-time $100 fee per collaboration.[^s4] Compute is billed to whichever member runs the query or is designated as payer.[^s4] Snowflake-hosted data also incurs that provider's own egress and compute charges.[^s1]

Gotchas: the querying and result-receiving roles cannot be changed after creation, so plan them up front;[^s3] the glossary notes that S3 locations registered with Lake Formation are not supported as configured-table sources;[^s3] and silent row suppression from thresholds can make small segments look empty.[^s2]

## When to use it (and when not)

Use Clean Rooms when the other party must not see your records but you both need joint answers, such as campaign reach, overlap counts or shared model training.[^s1] If the parties may see each other's data and only need governed access, cross-account sharing through Lake Formation and querying with Athena is simpler and cheaper; Clean Rooms adds value specifically through output controls and privacy techniques.[^s2][^s6]

## Connections

Clean Rooms is part of the AWS analytics services family and **depends on** the AWS Glue Data Catalog for table metadata, reading data in place from Amazon S3 and, optionally, Athena views.[^s1][^s3] It **integrates with** AWS Entity Resolution for ID mapping,[^s1] is **monitored by** CloudTrail for API activity alongside its own analysis logs, and is **governed by** the per-Region quotas listed in the AWS General Reference.[^s5] It is a complement to, not a replacement for, Lake Formation governance inside a data lake on AWS.[^s3]

## Sources
[^s1]: Amazon Web Services, "What is AWS Clean Rooms?". https://docs.aws.amazon.com/clean-rooms/latest/userguide/what-is.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Analysis rules in AWS Clean Rooms". https://docs.aws.amazon.com/clean-rooms/latest/userguide/analysis-rules.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "AWS Clean Rooms Glossary". https://docs.aws.amazon.com/clean-rooms/latest/userguide/glossary.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "AWS Clean Rooms pricing". https://aws.amazon.com/clean-rooms/pricing/ (accessed 2026-11-18)
[^s5]: Amazon Web Services, "AWS Clean Rooms endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/clean-rooms.html (accessed 2026-11-18)
[^s6]: AWS News Blog, "AWS Clean Rooms Now Generally Available" (2023-03-21). https://aws.amazon.com/es/blogs/aws/aws-clean-rooms-now-generally-available/ (accessed 2026-11-18)
