---
id: kt:amazon-kinesis-data-streams
type: Service
title: Amazon Kinesis Data Streams
aliases: [Kinesis Data Streams, KDS, Amazon Kinesis Streams]
domain: analytics
summary: >-
  AWS's managed, serverless service for capturing and storing real-time data streams in ordered,
  replayable shards, with on-demand (Standard or Advantage) and provisioned capacity modes and
  retention from 24 hours up to 365 days.
verified: 2026-11-10
status: current
broader: [kt:aws-analytics-services]
related: [kt:amazon-data-firehose, kt:amazon-managed-service-for-apache-flink, kt:amazon-s3]
relations:
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: dependsOn, object: kt:amazon-dynamodb}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: governedBy, object: kt:kinesis-data-streams-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is Amazon Kinesis Data Streams? - Amazon Kinesis Data Streams"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/streams/latest/dev/introduction.html
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Amazon Kinesis Data Streams Terminology and concepts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/streams/latest/dev/key-concepts.md
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Choose the data stream capacity mode - Amazon Kinesis Data Streams"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/streams/latest/dev/how-do-i-size-a-stream.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Quotas and limits - Amazon Kinesis Data Streams"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/streams/latest/dev/service-sizes-and-limits.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Amazon Kinesis Data Streams pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/kinesis/data-streams/pricing/
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "Amazon Kinesis Data Streams launches On-demand Advantage mode"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/amazon-kinesis-data-streams-ondemand-advantage
    type: official-blog
    accessed: 2026-11-10
created: 2026-11-10
---

# Amazon Kinesis Data Streams

Amazon Kinesis Data Streams (KDS) collects and stores large streams of data records in real time so that applications can process them as they arrive. Typical uses are log and data-feed intake, real-time metrics, clickstream analytics and chained stream-processing applications. The put-to-get delay is typically under one second, and several applications can read the same stream independently, for example one keeping running aggregates in DynamoDB while another archives to Amazon S3.[^s1]

## How it works

A data stream is a set of **shards**; each shard is an ordered sequence of **data records**, and each record holds a sequence number assigned by the service, a partition key and an opaque data blob that Kinesis never inspects.[^s2] Producers must supply a partition key (a Unicode string of up to 256 characters); Kinesis hashes it with MD5 to a 128-bit value and routes the record to the shard whose hash-key range contains it, so records with the same key land on the same shard.[^s2] Sequence numbers are unique per partition key within a shard and generally increase over time, but AWS warns they are not an index into the data.[^s2]

Consumers come in two flavours: shared fan-out consumers that poll with GetRecords, and enhanced fan-out consumers registered on the stream.[^s2] The Kinesis Client Library (KCL) runs a record processor per shard, handles failover and stores its checkpoints in DynamoDB tables, which it creates per consuming application.[^s2] Records stay readable for the **retention period**: 24 hours by default, adjustable from 24 hours up to 8,760 hours (365 days), with extra charges beyond 24 hours.[^s2] Server-side encryption uses AWS KMS keys, so producers and consumers also need permission on the key.[^s2] A newer managed **data delivery** capability (on-demand streams only) can write records to Apache Iceberg streaming tables on Amazon S3 Tables or to general-purpose S3 buckets without a consumer application.[^s2][^s4]

## Capacity modes

- **Provisioned** — you choose the shard count and pay per shard-hour. Each shard accepts up to 1 MB/s or 1,000 records/s of writes and serves up to 2 MB/s of reads and five read transactions per second.[^s2] You scale with UpdateShardCount or by splitting and merging shards.[^s3]
- **On-demand Standard** — Kinesis manages shards per stream; a stream absorbs up to double the peak write throughput seen in the previous 30 days, but traffic that more than doubles within 15 minutes can be throttled.[^s3] New streams start at 4 MB/s write and 8 MB/s read.[^s4]
- **On-demand Advantage** — launched on 2025-11-04 as an account-level, per-Region setting. It removes the per-stream charge, cuts data rates, allows pre-set **warm throughput** at no extra cost, and raises the enhanced fan-out limit to 50 consumers per stream; in exchange the account is billed for at least 25 MB/s of ingest and of retrieval across its on-demand streams.[^s6][^s3][^s4]

## Quotas, pricing and gotchas

Quotas (per the quotas page as of 2026-11): records can carry up to 10 MiB of payload before base64 encoding, with large records absorbed by burst capacity; PutRecords accepts up to 500 records and 10 MiB per request; GetRecords returns up to 10,000 records or 10 MB per call.[^s4] On-demand streams scale to 10 GB/s write and 20 GB/s read in US East (N. Virginia), US West (Oregon) and Europe (Ireland), but 200 MB/s write elsewhere unless raised by a support ticket; accounts default to 50 on-demand streams (raisable by ticket).[^s4] The provisioned shard quota defaults to 20,000 per account in those three Regions and 1,000 or 6,000 elsewhere, adjustable through Service Quotas.[^s4] A stream can switch capacity mode only twice per 24 hours.[^s4]

Pricing in US East (N. Virginia), as of 2026-11: provisioned shards cost $0.015 per shard-hour plus $0.014 per million 25 KB PUT payload units; On-demand Standard charges $0.08 per GB written and $0.040 per GB read plus a per-stream hourly fee; On-demand Advantage charges $0.032 per GB written and $0.016 per GB read.[^s5][^s6] Kinesis Data Streams is not in the AWS Free Tier.[^s5]

Gotchas: on-demand mode does not isolate hot keys, so one partition key exceeding 1 MB/s or 1,000 records/s still throws exceptions — use provisioned mode for heavily skewed keys; provisioned streams never auto-scale; and leaving Advantage requires removing warm throughput first and waiting at least 24 hours after enabling it.[^s3]

## When to use it (and when not)

AWS suggests On-demand Standard for unpredictable traffic and few streams or consumers, On-demand Advantage for steady throughput around 25 MiB/s or more, many fan-out consumers or hundreds of streams, and provisioned mode for forecastable traffic or when you need control over shard distribution.[^s3] If you only need to land data in S3 or another destination with no custom processing, a delivery service such as Amazon Data Firehose (part of the same Kinesis platform) or KDS's own data delivery may be simpler than writing consumers.[^s1][^s2]

## Connections

Kinesis Data Streams sits in AWS's analytics portfolio as the streaming store of the Kinesis platform, alongside Amazon Data Firehose, Kinesis Video Streams and Amazon Managed Service for Apache Flink.[^s1] It integrates with AWS Lambda, Amazon EMR and Amazon Redshift as processing or loading targets, and its data delivery writes into Amazon S3.[^s1][^s2] The KCL depends on Amazon DynamoDB for checkpoint tables and uses Amazon CloudWatch metric namespaces named after the application; encryption is secured by AWS KMS.[^s2] Streams are Regional, with limits that differ by Region.[^s4]

## Sources

[^s1]: Amazon Web Services, "What is Amazon Kinesis Data Streams?". https://docs.aws.amazon.com/streams/latest/dev/introduction.html (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Amazon Kinesis Data Streams Terminology and concepts". https://docs.aws.amazon.com/streams/latest/dev/key-concepts.md (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Choose the data stream capacity mode". https://docs.aws.amazon.com/streams/latest/dev/how-do-i-size-a-stream.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Quotas and limits - Amazon Kinesis Data Streams". https://docs.aws.amazon.com/streams/latest/dev/service-sizes-and-limits.html (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Amazon Kinesis Data Streams pricing". https://aws.amazon.com/kinesis/data-streams/pricing/ (accessed 2026-11-10)
[^s6]: Amazon Web Services, "Amazon Kinesis Data Streams launches On-demand Advantage mode" (What's New, 2025-11-04). https://aws.amazon.com/about-aws/whats-new/2025/11/amazon-kinesis-data-streams-ondemand-advantage (accessed 2026-11-10)
