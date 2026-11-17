---
id: kt:amazon-managed-service-for-apache-flink
type: Service
title: Amazon Managed Service for Apache Flink
aliases: [Amazon Kinesis Data Analytics, Kinesis Data Analytics, Kinesis Data Analytics for Apache Flink, KDA, Amazon Managed Service for Apache Flink Studio, Managed Flink, MSF]
domain: analytics
summary: >-
  AWS's serverless service for running Apache Flink stream-processing applications (Java, Scala,
  Python or SQL), billed per Kinesis Processing Unit; it was called Amazon Kinesis Data Analytics
  until August 2023.
verified: 2026-11-17
status: current
broader: [kt:aws-analytics-services]
related: [kt:amazon-kinesis-data-streams, kt:amazon-data-firehose]
relations:
  - {predicate: implements, object: kt:apache-flink}
  - {predicate: integratesWith, object: kt:amazon-msk}
  - {predicate: alternativeTo, object: kt:amazon-emr}
  - {predicate: dependsOn, object: kt:amazon-s3}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: governedBy, object: kt:managed-flink-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-data-engineer-associate}
sources:
  - id: s1
    title: "Get started with Amazon Managed Service for Apache Flink (DataStream API)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/managed-flink/latest/java/getting-started.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Implement application scaling in Managed Service for Apache Flink"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/managed-flink/latest/java/how-scaling.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Amazon Managed Service for Apache Flink endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/ka.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "Amazon Managed Service for Apache Flink pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/kinesis/analytics/pricing
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "Amazon Managed Service for Apache Flink FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/kinesis/data-analytics/faqs/
    type: official-docs
    accessed: 2026-11-17
  - id: s6
    title: "Announcing Amazon Managed Service for Apache Flink, renamed from Amazon Kinesis Data Analytics"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/announcing-amazon-managed-service-for-apache-flink-renamed-from-amazon-kinesis-data-analytics
    type: official-blog
    accessed: 2026-11-17
  - id: s7
    title: "Document history - Amazon Kinesis Data Analytics for SQL Applications Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kinesisanalytics/latest/dev/doc-history.html
    type: official-docs
    accessed: 2026-11-17
  - id: s8
    title: "Amazon Managed Service for Apache Flink 2.3"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/managed-flink/latest/java/flink-2-3.md
    type: official-docs
    accessed: 2026-11-17
  - id: s9
    title: "Amazon Managed Service for Apache Flink now supports Apache Flink 2.3 (What's New, Portuguese edition)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/pt/about-aws/whats-new/2026/07/amazon-managed-service-flink-2-3/
    type: official-blog
    accessed: 2026-11-17
  - id: s10
    title: "AWS Certified Data Engineer - Associate (DEA-C01) Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/data-engineer-associate-01/data-engineer-associate-01.pdf
    type: official-learning
    accessed: 2026-11-17
created: 2026-11-17
---

# Amazon Managed Service for Apache Flink

Amazon Managed Service for Apache Flink runs Apache Flink applications that process streaming data in real time, without you provisioning or patching Flink clusters.[^s1] AWS renamed it from Amazon Kinesis Data Analytics on 30 August 2023, and Kinesis Data Analytics Studio became Managed Service for Apache Flink Studio; existing applications kept running unchanged.[^s6] The old name survives in the API: the regional endpoint is still `kinesisanalytics.<region>.amazonaws.com`, with FIPS endpoints in US, Canada and GovCloud (US) Regions.[^s3]

## How it works

You build a Flink job, package it (for Java, a fat JAR), upload it to Amazon S3 and create an application that points at it. A job is made of runtime properties, sources (for example Kinesis Data Streams or Kafka), operators that transform, enrich or aggregate, and sinks such as Kinesis, Kafka, S3 or relational databases.[^s1] Applications can be written in Java, Scala or Python with the Flink libraries, or in Java via Apache Beam; Studio notebooks give an Apache Zeppelin environment for interactive SQL, Python and Scala that can be promoted to a long-running application.[^s5]

State is protected two ways: checkpoints use running application storage for automatic recovery, while snapshots are point-in-time backups, roughly equivalent to Flink savepoints, kept in durable application backups. Enhanced snapshots, which can be exported and restored across applications and Regions, need Flink 1.20 or later.[^s5] Applications can reach resources in a VPC (subnets must share one VPC), but a VPC-attached application has no internet access by default.[^s5]

Capacity is measured in Kinesis Processing Units (KPUs): one KPU is 1 vCPU and 4 GB of memory plus 50 GB of running application storage. Allocated KPUs equal `Parallelism / ParallelismPerKPU`; `ParallelismPerKPU` defaults to 1 and can go up to 8, which helps I/O-bound jobs. Automatic scaling adds capacity quickly on throughput spikes and removes it gradually, but never beyond the job's `maxParallelism`; changing `maxParallelism` later blocks restoring from earlier snapshots.[^s2]

## Versions and lifecycle

The service is generally available and supports Apache Flink 2.3 as of July 2026, alongside earlier versions; existing applications can move with in-place version upgrades that keep the same ARN, snapshots, logs and metrics.[^s9][^s8] Going from Flink 1.x to 2.x is a major upgrade with breaking changes and state-compatibility checks. Flink 2.3 recommends Java 17, does not support Java 21, Materialized Tables or the ForSt state backend, and cannot be used for Studio applications.[^s8]

The separate legacy product, Kinesis Data Analytics for SQL applications, is discontinued: new SQL applications could not be created after 15 October 2025, and AWS began deleting existing ones on 27 January 2026.[^s7]

## Quotas, pricing and gotchas

Default quotas per account per Region (as of 2026-11): 64 KPUs per Flink application and 100 applications, both adjustable through Service Quotas.[^s3][^s2] Raising `ParallelismPerKPU` lets a 64-KPU application reach a parallelism of up to 512.[^s2]

Pricing in US East (N. Virginia), as of 2026-11: $0.11 per KPU-hour, billed per second, plus one extra KPU per streaming application for orchestration and $0.10 per GB-month of running application storage.[^s4] Studio notebooks add two KPUs (orchestration and the development environment), and the FAQ gives a minimum of 3 KPUs and 50 GB of storage for a running notebook.[^s4][^s5] The smallest always-on streaming application therefore pays for two KPUs, which matters for many small jobs. Durable backups are billed separately per GB-month.[^s4] The service SLA is a 99.9% monthly uptime percentage.[^s5]

## When to use it (and when not)

Choose it for stateful, low-latency stream processing such as windowed aggregations, joins across streams, anomaly detection or change-data-capture pipelines, when you want Flink without operating JobManagers and TaskManagers.[^s1][^s8] If you only need to land a stream in a destination without stateful logic, a delivery service such as Amazon Data Firehose (itself a common sink of Flink jobs) is simpler.[^s5] Features the managed runtime leaves out, such as Java 21 or the ForSt state backend in Flink 2.3, are a reason to run Flink yourself, for example on Amazon EMR, at the cost of operating the cluster.[^s8]

## Connections

It **implements** open-source Apache Flink and sits under AWS analytics services. Its most common sources are Kinesis Data Streams and Amazon MSK, and common sinks include Data Firehose, DynamoDB and S3, so it is **related** to and **integrates with** those streaming services.[^s5] Application code and snapshots live in Amazon S3 (**dependsOn**), metrics and logs flow to CloudWatch (**monitoredBy**), and its KPU and application limits are **governed by** the service quotas page.[^s1][^s3] Amazon EMR is the self-managed **alternative**. The DEA-C01 exam guide lists Amazon Managed Service for Apache Flink among in-scope analytics services, hence the **coveredBy** edge to the Data Engineer Associate certification.[^s10]

## Sources
[^s1]: Amazon Web Services, "Get started with Amazon Managed Service for Apache Flink (DataStream API)". https://docs.aws.amazon.com/managed-flink/latest/java/getting-started.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Implement application scaling in Managed Service for Apache Flink". https://docs.aws.amazon.com/managed-flink/latest/java/how-scaling.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Amazon Managed Service for Apache Flink endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/ka.html (accessed 2026-11-17)
[^s4]: Amazon Web Services, "Amazon Managed Service for Apache Flink pricing". https://aws.amazon.com/kinesis/analytics/pricing (accessed 2026-11-17)
[^s5]: Amazon Web Services, "Amazon Managed Service for Apache Flink FAQs". https://aws.amazon.com/kinesis/data-analytics/faqs/ (accessed 2026-11-17)
[^s6]: Amazon Web Services, "Announcing Amazon Managed Service for Apache Flink, renamed from Amazon Kinesis Data Analytics", AWS News Blog. https://aws.amazon.com/blogs/aws/announcing-amazon-managed-service-for-apache-flink-renamed-from-amazon-kinesis-data-analytics (accessed 2026-11-17)
[^s7]: Amazon Web Services, "Document history - Amazon Kinesis Data Analytics for SQL Applications Developer Guide". https://docs.aws.amazon.com/kinesisanalytics/latest/dev/doc-history.html (accessed 2026-11-17)
[^s8]: Amazon Web Services, "Amazon Managed Service for Apache Flink 2.3". https://docs.aws.amazon.com/managed-flink/latest/java/flink-2-3.md (accessed 2026-11-17)
[^s9]: Amazon Web Services, "Amazon Managed Service for Apache Flink now supports Apache Flink 2.3", What's New with AWS (Portuguese edition). https://aws.amazon.com/pt/about-aws/whats-new/2026/07/amazon-managed-service-flink-2-3/ (accessed 2026-11-17)
[^s10]: Amazon Web Services, "AWS Certified Data Engineer - Associate (DEA-C01) Exam Guide". https://docs.aws.amazon.com/pdfs/aws-certification/latest/data-engineer-associate-01/data-engineer-associate-01.pdf (accessed 2026-11-17)
