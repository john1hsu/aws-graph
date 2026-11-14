---
id: kt:aws-analytics-services
type: Concept
title: AWS analytics services
aliases: [AWS analytics, analytics on AWS, AWS analytics portfolio, AWS analytics category, AWS data analytics services]
domain: analytics
summary: >-
  The AWS service category for turning raw data into answers: interactive and big-data processing (Athena, EMR,
  Glue), streaming (Kinesis, Data Firehose, Managed Service for Apache Flink, MSK), warehousing (Redshift),
  search analytics (OpenSearch Service), business intelligence (Quick), and governance and collaboration
  (Lake Formation, DataZone, Clean Rooms), increasingly fronted by the next generation of Amazon SageMaker.
verified: 2026-11-14
status: concept
broader: [kt:amazon-web-services]
related: [kt:data-lake-on-aws, kt:amazon-sagemaker-ai, kt:aws-storage-services]
relations:
  - {predicate: hasPart, object: kt:amazon-athena}
  - {predicate: hasPart, object: kt:amazon-redshift}
  - {predicate: hasPart, object: kt:amazon-emr}
  - {predicate: hasPart, object: kt:aws-glue}
  - {predicate: hasPart, object: kt:amazon-kinesis-data-streams}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Analytics - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/analytics.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "Choosing an AWS analytics service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/analytics-on-aws-how-to-choose/analytics-on-aws-how-to-choose.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Best practice 8.1 - Identify analytics solutions that best suit your technical challenges (Data Analytics Lens)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/analytics-lens/best-practice-8.1-identify-analytics-solutions-that-best-suit-your-technical-challenges..html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Reimagine business intelligence: Amazon QuickSight evolves to Amazon Quick Suite"
    publisher: AWS Business Intelligence Blog
    url: https://aws.amazon.com/blogs/business-intelligence/reimagine-business-intelligence-amazon-quicksight-evolves-to-amazon-quick-suite
    type: official-blog
    accessed: 2026-11-14
  - id: s5
    title: "What Is Amazon CloudSearch? - Amazon CloudSearch Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cloudsearch/latest/developerguide/what-is-cloudsearch.html
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "Document History - AWS Data Pipeline Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/DocHistory.html
    type: official-docs
    accessed: 2026-11-14
  - id: s7
    title: "Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html
    type: official-learning
    accessed: 2026-11-14
created: 2026-11-14
---

# AWS analytics services

"Analytics" is the AWS service category for collecting, processing, storing, querying and visualizing data so that people and applications can act on it. It is a map, not a thing you provision. As of 2026-11 the AWS overview whitepaper lists about two dozen entries under Analytics. They include Amazon Athena, Amazon EMR, AWS Glue, AWS Lake Formation, Amazon Redshift (and Redshift Serverless), Amazon OpenSearch Service (and OpenSearch Serverless), the Kinesis family, Amazon Data Firehose, Amazon Managed Service for Apache Flink, Amazon MSK, Quick, Amazon DataZone, AWS Clean Rooms, AWS Data Exchange, AWS Entity Resolution and Amazon FinSpace.[^s1]

## How the portfolio divides the work

AWS's analytics decision guide (last updated 2025-09-24) sorts the portfolio by job rather than by product family:[^s2]

- **Unified analytics and AI** – the next generation of Amazon SageMaker. Its Unified Studio brings data processing, SQL analytics, model development and generative AI into one workspace. Its lakehouse is Apache Iceberg-compatible and spans S3 data lakes and Redshift warehouses, with zero-ETL integrations, federated queries and fine-grained permissions.[^s2]
- **Data processing** – Athena for interactive SQL on data in S3, EMR for big-data frameworks such as Spark and Hive, Glue for serverless ETL and the Data Catalog, and Amazon MWAA for Airflow workflow orchestration.[^s1][^s2]
- **Streaming** – Kinesis Data Streams for real-time ingestion, Data Firehose for managed delivery into stores and tools, Managed Service for Apache Flink for stream-processing applications, and MSK for Apache Kafka. With MSK, AWS runs the cluster control plane, and you use Kafka's own APIs to produce and consume.[^s1][^s2]
- **Data lake and warehouse** – Amazon S3 as lake storage and Amazon Redshift as the warehouse, both reachable through the SageMaker lakehouse.[^s2]
- **Search analytics** – OpenSearch Service for log analytics, application monitoring and clickstream analysis, including direct queries over data in S3.[^s2]
- **Business intelligence** – Quick for dashboards, plus natural-language questions about data.[^s2]
- **Governance and collaboration** – DataZone for cataloguing and granting data access, Lake Formation for securing data lakes, and Clean Rooms for joint analysis without sharing raw data.[^s1][^s2]

## How to choose

The Well-Architected Data Analytics Lens tells you to pick a tool for each stage of the analytics process rather than forcing one tool to do everything. It starts from the business metrics you need and warns that a poorly matched service tends to cause performance problems at scale.[^s3] The decision guide lists the questions to answer. What are the sources and data types (transactional, file, event)? How much processing, at what frequency and latency? What volume, retention and access patterns? Do you want self-managed or serverless? Is the workload batch, interactive or streaming? Is the analysis descriptive, diagnostic, predictive or prescriptive?[^s2] The operational-model question matters because several services come in both forms: Redshift alongside Redshift Serverless, and OpenSearch Service alongside OpenSearch Serverless, which runs workloads without cluster management.[^s1][^s2]

## Gotchas

**Names move.** In October 2025 AWS announced that QuickSight was evolving into a broader product. The BI blog now calls it AWS Quick, with "Quick Sight" naming its BI component, and existing dashboards, datasets and analyses carry over unchanged.[^s4] The overview whitepaper lists the entry simply as "Quick" while its text still says QuickSight.[^s1] Older material uses all of these names.

**Lifecycle drifts.** Two services still in the whitepaper's list are closed to new customers. Amazon CloudSearch is no longer available to new customers; existing customers can keep using it, and AWS's linked guidance points to OpenSearch Service.[^s5] AWS Data Pipeline is also closed to new customers. AWS recommends moving typical workloads to AWS Glue, AWS Step Functions or Amazon MWAA.[^s6] New designs should start from the decision guide's categories, not the whitepaper's longer list.[^s1][^s2]

**Pricing is per service.** There is no single analytics price. The decision guide treats cost optimization as one of its selection criteria, alongside scalability, governance and integration. Check each service's own pricing page for the Region you use.[^s2]

## Connections

This node is the analytics branch of `kt:amazon-web-services`. Its `hasPart` edges point to the most-used members that have articles: `kt:amazon-athena`, `kt:amazon-redshift`, `kt:amazon-emr`, `kt:aws-glue` and `kt:amazon-kinesis-data-streams`.[^s1][^s2] It is related to `kt:data-lake-on-aws`, the architecture most of these services read from and write to, and to `kt:aws-storage-services`, because S3 is the lake's storage layer.[^s2] It is related to `kt:amazon-sagemaker-ai` because the next-generation SageMaker now frames analytics and ML as one environment.[^s2] The `coveredBy` edge reflects CLF-C02 Task Statement 3.7. It asks candidates to identify data analytics services, naming Athena, Kinesis, Glue and Quick as examples.[^s7]

## Sources
[^s1]: Amazon Web Services, "Analytics - Overview of Amazon Web Services". https://docs.aws.amazon.com/whitepapers/latest/aws-overview/analytics.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "Choosing an AWS analytics service". https://docs.aws.amazon.com/decision-guides/latest/analytics-on-aws-how-to-choose/analytics-on-aws-how-to-choose.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, "Best practice 8.1 - Identify analytics solutions that best suit your technical challenges (Data Analytics Lens)". https://docs.aws.amazon.com/wellarchitected/latest/analytics-lens/best-practice-8.1-identify-analytics-solutions-that-best-suit-your-technical-challenges..html (accessed 2026-11-14)
[^s4]: AWS Business Intelligence Blog, "Reimagine business intelligence: Amazon QuickSight evolves to Amazon Quick Suite". https://aws.amazon.com/blogs/business-intelligence/reimagine-business-intelligence-amazon-quicksight-evolves-to-amazon-quick-suite (accessed 2026-11-14)
[^s5]: Amazon Web Services, "What Is Amazon CloudSearch? - Amazon CloudSearch Developer Guide". https://docs.aws.amazon.com/cloudsearch/latest/developerguide/what-is-cloudsearch.html (accessed 2026-11-14)
[^s6]: Amazon Web Services, "Document History - AWS Data Pipeline Developer Guide". https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/DocHistory.html (accessed 2026-11-14)
[^s7]: Amazon Web Services, "Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html (accessed 2026-11-14)
