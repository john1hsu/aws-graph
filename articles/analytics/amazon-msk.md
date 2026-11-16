---
id: kt:amazon-msk
type: Service
title: Amazon Managed Streaming for Apache Kafka
aliases: [Amazon MSK, MSK, AWS MSK, MSK Serverless, MSK Provisioned]
domain: analytics
summary: >-
  AWS's fully managed service for running open-source Apache Kafka, offered as MSK Provisioned
  (Standard or Express brokers you size) or MSK Serverless (capacity managed by AWS), with MSK
  Connect for connectors and MSK Replicator for cross-cluster replication.
verified: 2026-11-16
status: current
broader: [kt:aws-analytics-services]
related: [kt:amazon-managed-service-for-apache-flink, kt:aws-glue]
relations:
  - {predicate: alternativeTo, object: kt:amazon-kinesis-data-streams}
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: dependsOn, object: kt:amazon-vpc}
  - {predicate: governedBy, object: kt:amazon-msk-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "Welcome to the Amazon MSK Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/msk/latest/developerguide/what-is-msk.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Amazon MSK Express brokers"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/msk/latest/developerguide/msk-broker-types-express.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "What is MSK Serverless? - Amazon Managed Streaming for Apache Kafka"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/msk/latest/developerguide/serverless
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Amazon MSK quota - Amazon Managed Streaming for Apache Kafka"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/msk/latest/developerguide/limits.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "Amazon Managed Streaming for Apache Kafka pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/msk/pricing/
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "Amazon MSK now supports up to 5x more partitions on Express Brokers"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-msk-partitions-express-broker
    type: official-blog
    accessed: 2026-11-16
  - id: s7
    title: "What's the Difference Between Amazon MSK and MSK Serverless?"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compare/msk-and-msk-serverless/
    type: official-docs
    accessed: 2026-11-16
  - id: s8
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-16
created: 2026-11-16
---

# Amazon Managed Streaming for Apache Kafka

Amazon Managed Streaming for Apache Kafka (Amazon MSK) is AWS's fully managed service for building and running applications that use Apache Kafka to process streaming data. It runs open-source Kafka versions, so existing producers, consumers, tooling and community plugins work without application code changes.[^s1] The division of labour is clean: AWS runs the control plane (create, update and delete clusters, change broker count and type through the console, CLI or SDK), while you use ordinary Kafka operations on the data plane to create topics and to produce and consume records.[^s1]

## How it works

MSK comes in two deployment options. With **MSK Provisioned** you create a cluster and choose how many brokers to place in each Availability Zone (at least one per AZ, each AZ in its own VPC subnet), picking either Standard or Express brokers. With **MSK Serverless** you provision only at the cluster level and MSK manages the brokers that carry your traffic.[^s1] Metadata is handled for you: MSK creates the ZooKeeper nodes on older clusters, and on KRaft-mode clusters the controllers are included at no extra cost with nothing to manage. When a broker fails, MSK replaces it, reuses its storage where possible, and keeps the same broker IP addresses so clients reconnect without changes.[^s1]

**Express brokers** are the newer Provisioned option. AWS states they deliver up to 3x the throughput per broker of Standard brokers (an m7g.16xlarge Express broker can safely write up to 500 MBps versus 153.8 MBps for the equivalent Standard broker), scale and move partitions up to 20x faster, and recover up to 90% faster. Storage is elastic and pay-as-you-go with nothing to size, there are no maintenance windows, and best-practice configuration guardrails are pre-applied.[^s2] In July 2025 AWS raised Express partition limits by up to 5x, to 20,000 partitions per m7g.16xlarge broker.[^s6]

**MSK Serverless** scales capacity and manages topic partitions automatically and bills on throughput. It requires IAM access control on every cluster and does not support Kafka ACLs; clients reach it privately through AWS PrivateLink. It integrates with AWS Glue Schema Registry, Amazon Managed Service for Apache Flink and AWS Lambda.[^s3] Two companion features round out the service: **MSK Connect** runs Kafka Connect connectors to move data in and out of clusters, and **MSK Replicator** copies data between Provisioned clusters in the same or different Regions.[^s1]

## Quotas, pricing and gotchas

Quotas as of 2026-11, per the MSK quota page: Provisioned accounts default to 90 brokers and clusters to 30 brokers (ZooKeeper) or 60 (KRaft), both adjustable in Service Quotas; Standard broker storage tops out at 16,384 GiB, while Express storage is unlimited. IAM-authenticated clients are capped at 3,000 TCP connections per broker. Express brokers have per-size throughput throttles and a 15 MB/s per-partition maximum. A Serverless cluster allows 200 MBps ingress, 400 MBps egress, 8 MiB messages and 2,400 non-compacted leader partitions (raisable by support case), and an account gets 10 Serverless clusters by default. MSK Replicator allows 15 replicators per account.[^s4]

Prices from the pricing page as of 2026-11, US East (N. Virginia) examples: a Standard kafka.m7g.large broker is $0.204 per hour plus $0.10 per GB-month of storage; an Express m7g.large broker is $0.408 per hour plus $0.01 per GB ingested and $0.10 per GB-month stored. Serverless, in the page's US East example, is $0.75 per cluster-hour, $0.0015 per partition-hour, $0.10 per GB in and $0.05 per GB out. MSK Connect costs $0.11 per MCU-hour.[^s5] Prices vary by Region, so check the pricing page for yours.

Gotchas: Express brokers do not yet fully support the Kafka Streams API, run only in a three-AZ layout, come in select instance sizes and do not yet support KIP-932 (Queues for Kafka).[^s2] Serverless is limited to IAM auth and is available in a subset of Regions.[^s3]

## When to use it (and when not)

Choose MSK when you need Kafka compatibility: existing Kafka clients, Kafka Connect, Schema Registry or stream-processing tooling. Pick Provisioned for steady or specialised workloads where you want control over brokers, and Serverless for spiky workloads or dev/test where you would rather not size brokers at all.[^s7] AWS itself notes that if you only need to land data in S3 or Redshift and don't need Kafka APIs, a simpler delivery service may suit better.[^s7]

## Connections

MSK sits under `kt:aws-analytics-services` and is an `alternativeTo` `kt:amazon-kinesis-data-streams`, AWS's native streaming service; the choice usually comes down to whether you need Kafka APIs.[^s7] It is `securedBy` `kt:aws-iam` (mandatory for Serverless) and `dependsOn` `kt:amazon-vpc`, since brokers live in per-AZ subnets.[^s1][^s3] It `integratesWith` `kt:aws-lambda` and is related to `kt:amazon-managed-service-for-apache-flink` and `kt:aws-glue` (via Glue Schema Registry).[^s3] Its limits are `governedBy` the `kt:amazon-msk-quotas` node.[^s4] The `coveredBy` edge reflects that Amazon MSK is listed among the in-scope Analytics services of the SAA-C03 exam.[^s8]

## Sources
[^s1]: Amazon Web Services, "Welcome to the Amazon MSK Developer Guide". https://docs.aws.amazon.com/msk/latest/developerguide/what-is-msk.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Amazon MSK Express brokers". https://docs.aws.amazon.com/msk/latest/developerguide/msk-broker-types-express.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "What is MSK Serverless?". https://docs.aws.amazon.com/msk/latest/developerguide/serverless (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Amazon MSK quota". https://docs.aws.amazon.com/msk/latest/developerguide/limits.html (accessed 2026-11-16)
[^s5]: Amazon Web Services, "Amazon Managed Streaming for Apache Kafka pricing". https://aws.amazon.com/msk/pricing/ (accessed 2026-11-16)
[^s6]: Amazon Web Services, "Amazon MSK now supports up to 5x more partitions on Express Brokers" (What's New, 2025-07-21). https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-msk-partitions-express-broker (accessed 2026-11-16)
[^s7]: Amazon Web Services, "What's the Difference Between Amazon MSK and MSK Serverless?". https://aws.amazon.com/compare/msk-and-msk-serverless/ (accessed 2026-11-16)
[^s8]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-16)
