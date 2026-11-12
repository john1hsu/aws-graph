---
id: kt:amazon-data-firehose
type: Service
title: Amazon Data Firehose
aliases: [Amazon Kinesis Data Firehose, Kinesis Data Firehose, Kinesis Firehose, Firehose]
domain: analytics
summary: >-
  AWS's fully managed streaming-delivery (streaming ETL) service: producers send records to a
  Firehose stream, which buffers, optionally transforms or converts them, and delivers them to
  destinations such as Amazon S3, Amazon Redshift, OpenSearch, Apache Iceberg tables, Splunk,
  Snowflake or HTTP endpoints, with no servers or shards to manage.
verified: 2026-11-12
status: current
broader: [kt:aws-analytics-services]
related: [kt:amazon-kinesis-data-streams]
relations:
  - {predicate: dependsOn, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: integratesWith, object: kt:amazon-redshift}
  - {predicate: governedBy, object: kt:amazon-data-firehose-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is Amazon Data Firehose? - Amazon Data Firehose"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Amazon Data Firehose Quota - Amazon Data Firehose"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/firehose/latest/dev/limits.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Amazon Data Firehose FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/firehose/faqs/
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "Amazon Data Firehose pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/firehose/pricing/
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "Introducing Amazon Data Firehose, formerly known as Amazon Kinesis Data Firehose"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2024/02/amazon-data-firehose-formerly-kinesis-data-firehose/
    type: official-blog
    accessed: 2026-11-12
  - id: s6
    title: "Amazon Data Firehose and Kinesis Data Streams — AWS Product Comparison"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compare/data-firehose-and-kinesis-data-streams/
    type: official-docs
    accessed: 2026-11-12
created: 2026-11-12
---

# Amazon Data Firehose

Amazon Data Firehose is a fully managed service that delivers real-time streaming data to a destination you choose, optionally transforming it on the way, without you writing consumer applications or managing capacity.[^s1] AWS positions it as a streaming ETL service that can also batch, compress and encrypt data before delivery and that scales automatically.[^s3] Until February 2024 it was called Amazon Kinesis Data Firehose; the rename changed the console, documentation and web pages only, so endpoints, APIs, the CLI, IAM policies and CloudWatch metrics stayed the same.[^s5] That is why the API still uses "delivery stream" names such as `CreateDeliveryStream` while newer docs say **Firehose stream**.[^s1][^s2]

## How it works

You create a **Firehose stream** and **data producers** send **records** (up to 1,000 KB each) to it; a stream can also read automatically from an existing Kinesis data stream.[^s1] Supported sources include the Direct PUT API and SDKs, Kinesis Data Streams, Amazon MSK, AWS services such as CloudWatch, EventBridge and AWS IoT, the Kinesis Agent, Fluent Bit and AWS Lambda.[^s3] Firehose **buffers** incoming data until a size (MB) or time (seconds) threshold is reached, whichever comes first, then delivers the batch.[^s1][^s3] For S3 the buffer size is 1–128 MB and the interval 0–900 seconds; Parquet conversion or dynamic partitioning raises the size range to 64–128 MB.[^s3]

Destinations include Amazon S3, Amazon Redshift, Amazon OpenSearch Service and OpenSearch Serverless, Splunk, Apache Iceberg tables, and custom or partner HTTP endpoints (for example Datadog, Dynatrace, New Relic, MongoDB and Elastic); the FAQ also lists Snowflake.[^s1][^s3] Along the way you can invoke a Lambda function to transform records (each returned record must keep its `recordId` and report `Ok`, `Dropped` or `ProcessingFailed`), convert JSON to Apache Parquet or ORC, and use **dynamic partitioning** to write records to S3 prefixes keyed on fields such as a customer ID, which makes Athena, EMR and Redshift Spectrum queries cheaper.[^s3]

Redshift delivery is indirect: Firehose first lands data in an S3 bucket and then issues a Redshift `COPY`.[^s3] Delivery is at-least-once, so retries can produce occasional duplicates.[^s3]

## Quotas, pricing and gotchas

As of 2026-11, quotas from the Firehose quota page:[^s2]

- **Firehose streams per Region:** 5,000 in US East (N. Virginia), US East (Ohio), US West (Oregon), Europe (Ireland) and Asia Pacific (Tokyo); lower (2,000, 500 or 100) elsewhere. Increases can be requested through Service Quotas where available.[^s2]
- **Direct PUT throughput per stream:** 500,000 records/s, 2,000 requests/s and 5 MiB/s in US East (N. Virginia), US West (Oregon) and Europe (Ireland); 100,000 records/s, 1,000 requests/s and 1 MiB/s in other Regions. The three scale together, and Firehose raises them automatically on throttling, though not instantly. With a Kinesis Data Streams source this throughput quota does not apply.[^s2]
- **PutRecordBatch:** up to 500 records or 4 MiB per call (not adjustable).[^s2]
- **Dynamic partitioning:** 500 active partitions per stream by default.[^s2]
- **Control-plane APIs** such as `CreateDeliveryStream`: 5 calls/s each, a hard limit.[^s2]

Pricing is per GB processed with no minimum fee.[^s5] The pricing page's examples (as of 2026-11; no Region stated, so check your Region) show $0.029 per GB for the first 500 TB/month of Direct PUT or Kinesis Data Streams ingestion, $0.13 per GB for Vended Logs, $0.018 per GB for Parquet/ORC conversion, and $0.020 per GB plus $0.005 per 1,000 S3 objects for dynamic partitioning.[^s4] The big gotcha: each Direct PUT/KDS record is **rounded up to 5 KB** for billing, so many tiny records cost far more than the same bytes in fewer, larger records.[^s2][^s4] Over-raising the throughput quota also hurts, because it produces small batches and higher destination costs.[^s2]

If a destination is unavailable, Direct PUT data is kept and retried for up to 24 hours before being discarded. With a Kinesis source, it is kept for the stream's retention period.[^s2][^s3] Failed Redshift loads are retried for up to 120 minutes and then written to an errors manifest for manual loading.[^s3]

## When to use it (and when not)

Choose Firehose when the job is "get this stream into S3, a warehouse, a search cluster or an observability tool" with little operational work: log delivery, streaming ETL, IoT data delivery and near-real-time analytics.[^s6][^s3] Choose Amazon Kinesis Data Streams when you need custom consumers, multiple independent readers, replay, or retention up to 365 days; the two are often combined, with Data Streams as the source and Firehose as the loader.[^s6][^s1] Because data is buffered for seconds to minutes, Firehose is not for per-event, millisecond-latency processing.[^s1][^s3]

## Connections

Firehose belongs to AWS's analytics services and is the delivery half of the Kinesis family; its most common partner is **Amazon Kinesis Data Streams**, which it reads from directly.[^s1][^s5] It **depends on Amazon S3** as its primary destination and as the staging and error bucket, including for Redshift loads.[^s3] It **integrates with AWS Lambda** for record transformation and with **Amazon Redshift** through S3 plus `COPY`.[^s3] Its stream counts and throughput are **governed by** per-Region Firehose quotas, and streams are **scoped to** an AWS Region, with quotas that differ by Region (including AWS GovCloud (US)).[^s2]

## Sources

[^s1]: Amazon Web Services, "What is Amazon Data Firehose?". https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Amazon Data Firehose Quota". https://docs.aws.amazon.com/firehose/latest/dev/limits.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "Amazon Data Firehose FAQs". https://aws.amazon.com/firehose/faqs/ (accessed 2026-11-12)
[^s4]: Amazon Web Services, "Amazon Data Firehose pricing". https://aws.amazon.com/firehose/pricing/ (accessed 2026-11-12)
[^s5]: Amazon Web Services, "Introducing Amazon Data Firehose, formerly known as Amazon Kinesis Data Firehose" (What's New, 2024-02-09). https://aws.amazon.com/about-aws/whats-new/2024/02/amazon-data-firehose-formerly-kinesis-data-firehose/ (accessed 2026-11-12)
[^s6]: Amazon Web Services, "Amazon Data Firehose and Kinesis Data Streams — AWS Product Comparison". https://aws.amazon.com/compare/data-firehose-and-kinesis-data-streams/ (accessed 2026-11-12)
