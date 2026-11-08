---
id: kt:amazon-s3
type: Service
title: Amazon S3
aliases: [Amazon Simple Storage Service, S3, AWS S3]
domain: storage
summary: >-
  AWS's regional object storage service for storing and retrieving any amount of data as objects in
  buckets, with strong read-after-write consistency, multiple storage classes, and specialised bucket
  types for low-latency, tabular (Apache Iceberg) and vector data.
verified: 2026-11-08
status: current
broader: [kt:aws-storage-services]
related: [kt:s3-storage-classes, kt:data-lake-on-aws, kt:amazon-athena]
relations:
  - {predicate: alternativeTo, object: kt:amazon-efs}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: governedBy, object: kt:amazon-s3-quotas}
  - {predicate: integratesWith, object: kt:amazon-cloudfront}
sources:
  - id: s1
    title: "Amazon S3 FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/s3/faqs/
    type: official-docs
    accessed: 2026-11-08
  - id: s2
    title: "What is Amazon S3? - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html
    type: official-docs
    accessed: 2026-11-08
  - id: s3
    title: "Amazon Simple Storage Service endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/s3.html
    type: official-docs
    accessed: 2026-11-08
  - id: s4
    title: "Amazon S3 Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/s3/pricing/
    type: official-docs
    accessed: 2026-11-08
  - id: s5
    title: "Amazon S3 increases the maximum object size to 50 TB"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/amazon-s3-maximum-object-size-50-tb/
    type: official-blog
    accessed: 2026-11-08
  - id: s6
    title: "Best practices design patterns: optimizing Amazon S3 performance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/optimizing-performance.html
    type: official-docs
    accessed: 2026-11-08
  - id: s7
    title: "Top announcements of AWS re:Invent 2025"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/top-announcements-of-aws-reinvent-2025/
    type: official-blog
    accessed: 2026-11-08
created: 2026-11-08
---

# Amazon S3

Amazon Simple Storage Service (S3) is AWS's object storage service, built to store and retrieve any amount of data from anywhere; total data volume and object count are unlimited.[^s1] It is the default landing zone for backups, logs, media, static web assets and data lakes, and many other AWS services read from or write to it.

## How it works

An **object** is a file plus metadata describing it; a **bucket** is the container for objects; and a **key** uniquely identifies an object within its bucket. Bucket, key and (optional) version ID together address one object. You choose a bucket's name and AWS Region at creation and cannot change either afterwards, and objects never leave that Region unless you explicitly transfer or replicate them.[^s2]

S3 now has four bucket types. **General purpose** buckets are the original type and suit most workloads. **Directory** buckets target low-latency and data-residency cases and hold S3 Express One Zone data in a single Availability Zone. **Table** buckets store tabular data in Apache Iceberg format, and **vector** buckets store and query vector embeddings through dedicated vector APIs.[^s2][^s1] The FAQ also describes **S3 Files**, a shared file system that lets AWS compute work with S3 data as files while it stays in S3.[^s1]

Consistency is strong: after a successful PUT or DELETE, subsequent reads and list operations reflect the change, in all Regions, at no extra cost. Updates are atomic per key but there are no multi-key transactions, simultaneous writes to the same key resolve to the latest timestamp, and *bucket configuration* changes are eventually consistent (AWS suggests waiting 15 minutes after first enabling versioning before writing).[^s2][^s1]

Storage classes trade price against access pattern: S3 Standard, Intelligent-Tiering, Standard-IA, One Zone-IA, Glacier Instant Retrieval, Glacier Flexible Retrieval, Glacier Deep Archive and Express One Zone. S3 Standard is designed for 99.99% availability and 11 nines of annual durability across multiple Availability Zones; One Zone-IA keeps its data in one AZ and is not resilient to losing that AZ.[^s1][^s2]

Security defaults have tightened over time: buckets and objects are private by default, Block Public Access is on for new buckets, Object Ownership defaults to "bucket owner enforced" with ACLs disabled (so access is managed purely by policies), and SSE-S3 encryption applies to all new uploads since January 5, 2023.[^s2][^s1] Access points give shared datasets named endpoints with their own policies and can be restricted to a VPC.[^s2]

## Quotas, pricing and gotchas

Default quotas (AWS General Reference, as of 2026-11): 10,000 general purpose buckets and 100 directory buckets per account, both adjustable; 10,000 access points (adjustable); multipart uploads of up to 10,000 parts of 5 MB to 5 GB each; 1,000 lifecycle rules; and a 20 KB bucket policy, none of those last four adjustable.[^s3] The maximum object size rose from 5 TB to 50 TB on 2025-12-02, in all Regions and all storage classes (the quota table expresses it as 48.828125 "terabytes", i.e. binary units); a single PUT is still capped at 5 GB, and AWS recommends multipart upload above 100 MB.[^s5][^s3][^s1] Console uploads are limited to 160 GB.[^s3]

Performance scales per prefix: at least 3,500 PUT/COPY/POST/DELETE or 5,500 GET/HEAD requests per second per partitioned prefix, with no limit on prefixes. Scaling is gradual, so a sudden ramp can return temporary 503 Slow Down errors that clients should retry with backoff.[^s6] SSE-KMS workloads may hit AWS KMS request quotas before S3 limits.[^s6]

Pricing has six components: storage, requests and retrievals, data transfer (and Transfer Acceleration), management and insights features, replication, and transform/query features. Data transfer in from the internet is free; the first 100 GB per month out to the internet is free, aggregated across services and Regions; and in a US East (N. Virginia) example, S3 Standard GET requests cost $0.0004 per 1,000 (as of 2026-11).[^s4] Transfers between S3 and other AWS services in the same Region are free, while cross-Region transfers are charged to the source bucket owner.[^s1] Infrequent-access and archive classes carry minimum storage durations (30 days for Standard-IA, 90 for Glacier Instant and Flexible Retrieval, 180 for Deep Archive) and Standard-IA and Glacier Instant Retrieval bill a 128 KB minimum object size, so lifecycle transitions of many tiny or short-lived objects can cost more than they save.[^s1]

## When to use it (and when not)

Use S3 for durable, scalable storage accessed over HTTP APIs: data lakes queried in place, backups and archives, static content behind a CDN, and ML datasets.[^s1][^s6] It is not a POSIX file system or a block device: workloads needing shared file semantics fit a file service (or S3 Files), and latency-critical small-object access may need Express One Zone, Amazon CloudFront or Amazon ElastiCache in front of it.[^s1][^s6]

## What changed (2025-12)

Around re:Invent 2025 AWS raised the object size limit to 50 TB, made S3 Vectors generally available (up to 2 billion vectors per index), and added Intelligent-Tiering and cross-Region/cross-account replication for S3 Tables, plus Storage Lens performance metrics.[^s5][^s7]

## Connections

- **Broader: AWS storage services** (`kt:aws-storage-services`): S3 is AWS's object store alongside block and file services.[^s2]
- **Related to S3 storage classes** (`kt:s3-storage-classes`), **data lakes on AWS** (`kt:data-lake-on-aws`) and **Amazon Athena** (`kt:amazon-athena`), which queries S3 data in place.[^s1]
- **Alternative to Amazon EFS** (`kt:amazon-efs`) when file-system semantics are not required.[^s1]
- **Secured by AWS IAM** (`kt:aws-iam`) policies, since ACLs are disabled by default; **scoped to AWS Regions** (`kt:aws-regions`); **governed by S3 quotas** (`kt:amazon-s3-quotas`).[^s2][^s3]
- **Integrates with Amazon CloudFront** (`kt:amazon-cloudfront`) for edge caching and Transfer Acceleration.[^s6]

## Sources
[^s1]: Amazon Web Services, "Amazon S3 FAQs". https://aws.amazon.com/s3/faqs/ (accessed 2026-11-08)
[^s2]: Amazon Web Services, "What is Amazon S3?". https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html (accessed 2026-11-08)
[^s3]: Amazon Web Services, "Amazon Simple Storage Service endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/s3.html (accessed 2026-11-08)
[^s4]: Amazon Web Services, "Amazon S3 Pricing". https://aws.amazon.com/s3/pricing/ (accessed 2026-11-08)
[^s5]: Amazon Web Services, What's New, "Amazon S3 increases the maximum object size to 50 TB" (2025-12-02). https://aws.amazon.com/about-aws/whats-new/2025/12/amazon-s3-maximum-object-size-50-tb/ (accessed 2026-11-08)
[^s6]: Amazon Web Services, "Best practices design patterns: optimizing Amazon S3 performance". https://docs.aws.amazon.com/AmazonS3/latest/userguide/optimizing-performance.html (accessed 2026-11-08)
[^s7]: Amazon Web Services, AWS News Blog, "Top announcements of AWS re:Invent 2025". https://aws.amazon.com/blogs/aws/top-announcements-of-aws-reinvent-2025/ (accessed 2026-11-08)
