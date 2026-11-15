---
id: kt:amazon-opensearch-service
type: Service
title: Amazon OpenSearch Service
aliases: [Amazon Elasticsearch Service, Amazon ES, OpenSearch Service, AWS OpenSearch, Amazon OpenSearch]
domain: analytics
summary: >-
  AWS's managed search and analytics service for running OpenSearch (and legacy Elasticsearch OSS up
  to 7.10) as provisioned domains or as OpenSearch Serverless collections, used for log analytics,
  observability, full-text search and vector search.
verified: 2026-11-15
status: current
broader: [kt:aws-analytics-services]
related: [kt:amazon-bedrock-knowledge-bases, kt:amazon-dynamodb, kt:amazon-kinesis-data-streams]
relations:
  - {predicate: integratesWith, object: kt:amazon-data-firehose}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: runsOn, object: kt:amazon-vpc}
  - {predicate: governedBy, object: kt:opensearch-service-quotas}
sources:
  - id: s1
    title: "What is Amazon OpenSearch Service?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/opensearch-service/latest/developerguide/what-is.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Amazon OpenSearch Service FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/opensearch-service/faqs/
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Amazon OpenSearch Service Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/opensearch-service/pricing
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "Amazon OpenSearch Service quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/opensearch-service/latest/developerguide/limits.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "Amazon OpenSearch Service rename - Summary of changes"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/opensearch-service/latest/developerguide/rename.html
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "Introducing the next generation of Amazon OpenSearch Serverless for building your agentic AI applications"
    publisher: AWS News Blog
    url: https://aws.amazon.com/blogs/aws/introducing-the-next-generation-of-amazon-opensearch-serverless-for-building-your-agentic-ai-applications/
    type: official-blog
    accessed: 2026-11-15
created: 2026-11-15
---

# Amazon OpenSearch Service

Amazon OpenSearch Service is AWS's managed offering for OpenSearch, the open-source search and analytics suite derived from Elasticsearch. It provisions cluster resources, detects and replaces failed nodes, and lets you resize a cluster with a single API call; typical workloads are log analytics, real-time application monitoring, clickstream analysis and website search.[^s1][^s2] The service was called Amazon Elasticsearch Service until it was renamed on September 8, 2021; it still runs legacy Elasticsearch OSS versions up to 7.10 alongside current OpenSearch releases (3.x, 2.x and 1.x).[^s5][^s1]

## How it works

The unit of deployment is a **domain**, which is equivalent to an OpenSearch cluster: you choose instance types, instance counts and storage, and each EC2 instance in the domain is one node.[^s1] Domains can span one, two or three Availability Zones (three is recommended), and dedicated master nodes offload cluster management from data nodes.[^s2][^s1] The **Multi-AZ with Standby** option keeps standby nodes that can take over in under a minute for some infrastructure failures; it requires OpenSearch 1.3 or later and data-node counts in multiples of three.[^s2]

Storage is tiered. Hot data sits on EBS volumes or instance storage; **UltraWarm** and **cold storage** keep read-only, less-accessed indexes on Amazon S3-backed managed storage at lower cost.[^s1][^s3] Data arrives through Amazon Data Firehose, Logstash, the native APIs, or OpenSearch Ingestion; a DynamoDB zero-ETL integration replicates table data within seconds, and Direct Query lets you query data in S3 in place.[^s2] OpenSearch Dashboards (and Kibana for Elasticsearch domains) provide visualisation.[^s2]

**OpenSearch Serverless** removes cluster sizing: capacity is measured in OpenSearch Compute Units (OCUs), roughly 6 GB of RAM plus matching vCPU each.[^s3] A next-generation Serverless architecture reached general availability in May 2026; it scales to zero when idle, creates collections in seconds, and AWS says it scales up to 20 times faster than the previous generation. At launch it supported search and vector-search collections, with the older design still reachable as "Classic".[^s6]

Security layers include IAM access control, VPC placement with security groups, encryption at rest and node-to-node, index-, document- and field-level security, and Dashboards sign-in via Amazon Cognito, HTTP basic auth or SAML.[^s1]

## Quotas, pricing and gotchas

- **Scale:** a domain supports up to 1,002 data nodes (334 hot nodes per AZ in a three-AZ layout) and up to 25 PB of attached storage; default per-family node limits (for example 80 for most current families, 10 for T2/T3) can be raised through Service Quotas (as of 2026-11).[^s4][^s1]
- **Payload size:** HTTP request bodies are capped at 10 MiB on smaller instance sizes and 100 MiB on xlarge and above.[^s4]
- **Shards:** on OpenSearch 2.17 and later the per-node shard limit is 1,000 per 16 GB of heap, up to 4,000, and is not changeable.[^s4]
- **Provisioned pricing:** you pay per instance-hour (partial hours bill as full), per GB-month of EBS storage and standard data transfer; cross-AZ replication traffic inside a domain and UltraWarm/cold-to-S3 traffic are free.[^s1][^s3] Example US East (N. Virginia) rates on the pricing page, as of 2026-11: r6g.xlarge.search at $0.335/hour and UltraWarm managed storage at $0.024/GB-month.[^s3]
- **Reserved Instances:** 1- or 3-year terms discount up to 35% and 52% respectively (All Upfront); they cannot be cancelled, transferred or resold.[^s3]
- **Serverless:** Classic collections bill at least 2 OCUs for the first collection (0.5 + 0.5 with the dev-test option), while next-generation collections have no OCU minimum.[^s3]
- **Extended Support:** domains left on versions past standard support are charged an extra fee automatically; upgrading avoids it.[^s1]
- **Rename leftovers:** IAM actions still use the `es:` prefix, and service control policies must list both old and new action names (for example `es:CreateElasticsearchDomain` and `es:CreateDomain`) because SCPs don't get the automatic permission mapping that identity policies do.[^s5]

## When to use it (and when not)

Choose OpenSearch Service for full-text relevance search, log and trace analytics with near-real-time dashboards, and vector search for AI and agentic applications.[^s2][^s6] Prefer Serverless for spiky or intermittent workloads and provisioned domains when you need specific instance families, cross-cluster replication, or tight control over shards.[^s2][^s3] For ad hoc SQL over a data lake, Amazon Athena or Amazon Redshift are usually cheaper than indexing everything; OpenSearch's Direct Query to S3 bridges the two.[^s2]

## Connections

OpenSearch Service sits in the AWS analytics portfolio. Amazon Data Firehose and Amazon Kinesis Data Streams are the usual streaming feeds into domains, and the DynamoDB zero-ETL integration indexes table data for search.[^s2] Amazon S3 holds snapshots, UltraWarm and cold data, and is the target of Direct Query.[^s2][^s3] Access is secured by IAM and VPC placement, and CloudWatch and CloudTrail provide metrics, logs and configuration auditing.[^s1] The vector-search collections of Serverless link it to generative-AI retrieval such as Amazon Bedrock Knowledge Bases, and node, shard and storage limits are collected under OpenSearch Service quotas.[^s6][^s4]

## Sources
[^s1]: Amazon Web Services, "What is Amazon OpenSearch Service?". https://docs.aws.amazon.com/opensearch-service/latest/developerguide/what-is.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Amazon OpenSearch Service FAQs". https://aws.amazon.com/opensearch-service/faqs/ (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Amazon OpenSearch Service Pricing". https://aws.amazon.com/opensearch-service/pricing (accessed 2026-11-15)
[^s4]: Amazon Web Services, "Amazon OpenSearch Service quotas". https://docs.aws.amazon.com/opensearch-service/latest/developerguide/limits.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "Amazon OpenSearch Service rename - Summary of changes". https://docs.aws.amazon.com/opensearch-service/latest/developerguide/rename.html (accessed 2026-11-15)
[^s6]: AWS News Blog, "Introducing the next generation of Amazon OpenSearch Serverless for building your agentic AI applications". https://aws.amazon.com/blogs/aws/introducing-the-next-generation-of-amazon-opensearch-serverless-for-building-your-agentic-ai-applications/ (accessed 2026-11-15)
