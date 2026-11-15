---
id: kt:amazon-s3-quotas
type: Limit
title: Amazon S3 quotas
aliases: [S3 quotas, S3 service quotas, S3 limits, Amazon S3 service limits, Amazon Simple Storage Service quotas]
domain: storage
summary: >-
  The account-, bucket- and object-level limits that constrain Amazon S3: adjustable bucket, access
  point and replication quotas; fixed object, multipart, policy, tag and configuration limits; per-prefix
  request-rate baselines; and separate limit sets for table buckets and vector buckets.
verified: 2026-11-15
status: current
broader: [kt:amazon-s3]
related: [kt:s3-storage-classes, kt:amazon-s3-files, kt:aws-kms, kt:amazon-cloudfront]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:kms-quotas}
sources:
  - id: s1
    title: "Amazon Simple Storage Service endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/s3.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "General purpose bucket quotas, limitations, and restrictions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/BucketRestrictions.html
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Best practices design patterns: optimizing Amazon S3 performance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/optimizing-performance.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "Amazon S3 increases the maximum object size to 50 TB"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/amazon-s3-maximum-object-size-50-tb/
    type: official-blog
    accessed: 2026-11-15
  - id: s5
    title: "S3 Tables AWS Regions, endpoints, and service quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-tables-regions-quotas.html
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "S3 Vectors limitations and restrictions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-vectors-limitations.html
    type: official-docs
    accessed: 2026-11-15
created: 2026-11-15
---

# Amazon S3 quotas

Amazon S3 is often described as "unlimited", and for capacity that is true: a general purpose bucket has no maximum size and no cap on how many objects it holds.[^s2] What S3 does limit is the number of containers you create, the size of individual objects and parts, the size of configuration documents attached to a bucket, and how fast a single key prefix can absorb requests before S3 scales out. Knowing which of these limits can be raised, and which are hard, saves redesigns later.[^s1][^s3]

## How the quotas are organised

AWS publishes S3's quotas in the General Reference and through the Service Quotas console. The table labels every default as applying to "each account", but in practice the scope differs per row: some are per account, some per Region, some per bucket, and some per object or upload.[^s1] Commercial-partition S3 quotas are viewed and managed only from US East (N. Virginia), and AWS GovCloud (US) quotas only from AWS GovCloud (US-West), whichever Region the buckets live in.[^s2]

**Adjustable (request through Service Quotas), defaults as of 2026-11:** 10,000 general purpose buckets per account; 100 directory buckets per account; 10,000 access points per Region; 28 replication destination buckets per source bucket; and a 1 Gbps Replication Time Control transfer rate from a source Region.[^s1]

**Fixed, as of 2026-11:** a 20 KB bucket policy; 50 tags per bucket and 10 per object; 100 event notification configurations per bucket; 1,000 lifecycle rules and 1,000 replication rules per configuration; 100 Multi-Region Access Points per account, each spanning at most 20 Regions; and, for archived data, 35 random restore requests per PiB stored per day, 1,000 retrieval requests per second and at most 2 purchased provisioned capacity units.[^s1] S3 on Outposts has its own fixed limits of 100 buckets per account per Outpost and 10 access points per bucket.[^s1]

## Object and upload limits

A single object can be up to 50 TB, a tenfold increase from 5 TB that AWS announced on 2025-12-02 for all Regions and storage classes; the quota table writes it as 48.828125 TB because it counts in binary units.[^s4][^s1] Multipart uploads use parts of 5 MB to 5 GB (the final part may be smaller) and at most 10,000 parts, and console uploads stop at 160 GB, so very large objects need the SDKs or CLI.[^s1] AWS recommends the current AWS Common Runtime and S3 Transfer Manager for moving objects at this scale.[^s4]

## Request-rate behaviour

Throughput is not a Service Quotas entry but behaves like one. Each partitioned prefix supports at least 3,500 PUT/COPY/POST/DELETE and 5,500 GET/HEAD requests per second, there is no limit on the number of prefixes, and spreading load across ten prefixes could reach 55,000 reads per second.[^s3] S3 scales gradually, so a sudden spike can produce HTTP 503 Slow Down responses until partitioning catches up; clients should rely on SDK retries with backoff. Workloads encrypted with SSE-KMS should also check AWS KMS request quotas, which can bind first.[^s3]

## Newer bucket types have their own limits

Table buckets (S3 Tables, for Apache Iceberg data) default to 100 per Region per account, with 10,000 namespaces and 10,000 tables per table bucket; all three are raised through AWS Support.[^s5] Vector buckets (S3 Vectors) allow 10,000 buckets per Region, 10,000 indexes per bucket and up to 2 billion vectors per index, with 1–4,096 dimensions, up to 40 KB of metadata per vector, up to 1,000 combined PutVectors/DeleteVectors requests per second per index, and top-K of at most 10,000 per query.[^s6] The documentation does not say which vector limits are adjustable.[^s6]

## Gotchas

Accounts approved for more than 10,000 general purpose buckets must paginate ListBuckets; unpaginated calls are rejected for them, which breaks older scripts.[^s2] A bucket's name and Region are fixed at creation and ownership cannot be moved to another account, so "one bucket per tenant" designs should be sized against the bucket quota early; shared buckets with access points or prefixes often scale better.[^s2][^s1] Tag and lifecycle-rule limits are hard, so cost-allocation and retention schemes must fit within them.[^s1]

## Connections

These quotas are **broader** under Amazon S3 (`kt:amazon-s3`) and are **configured via** Service Quotas (`kt:service-quotas`) for the adjustable rows.[^s1][^s2] Most are **scoped to** AWS Regions (`kt:aws-regions`) or accounts, with management pinned to one Region per partition.[^s2] They **contrast with** AWS KMS quotas (`kt:kms-quotas`), which can throttle SSE-KMS traffic before S3's prefix rates do, and relate to AWS KMS (`kt:aws-kms`) itself.[^s3] Amazon CloudFront (`kt:amazon-cloudfront`) is AWS's suggested front for single-connection throughput or very low latency beyond S3's baseline.[^s3] Storage classes (`kt:s3-storage-classes`) all accept 50 TB objects, and S3 Files (`kt:amazon-s3-files`) is a related way of working with bucket data.[^s4]

## Sources

[^s1]: Amazon Web Services, "Amazon Simple Storage Service endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/s3.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "General purpose bucket quotas, limitations, and restrictions". https://docs.aws.amazon.com/AmazonS3/latest/userguide/BucketRestrictions.html (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Best practices design patterns: optimizing Amazon S3 performance". https://docs.aws.amazon.com/AmazonS3/latest/userguide/optimizing-performance.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "Amazon S3 increases the maximum object size to 50 TB" (What's New, 2025-12-02). https://aws.amazon.com/about-aws/whats-new/2025/12/amazon-s3-maximum-object-size-50-tb/ (accessed 2026-11-15)
[^s5]: Amazon Web Services, "S3 Tables AWS Regions, endpoints, and service quotas". https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-tables-regions-quotas.html (accessed 2026-11-15)
[^s6]: Amazon Web Services, "S3 Vectors limitations and restrictions". https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-vectors-limitations.html (accessed 2026-11-15)
