---
id: kt:s3-storage-classes
type: Feature
title: Amazon S3 storage classes
aliases: [S3 storage classes, S3 storage tiers, S3 Glacier storage classes]
domain: storage
summary: >-
  The per-object tiers of Amazon S3 (Standard, Express One Zone, Intelligent-Tiering, Standard-IA,
  One Zone-IA and the three Glacier classes) that trade storage price against availability, Availability
  Zone resilience, access latency, retrieval fees and minimum billing commitments.
verified: 2026-11-15
status: current
broader: [kt:amazon-s3]
related: [kt:amazon-s3-quotas, kt:cost-optimization-pillar, kt:aws-backup]
relations:
  - {predicate: configuredVia, object: kt:s3-lifecycle-policies}
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: pricedBy, object: kt:aws-pricing-fundamentals}
  - {predicate: monitoredBy, object: kt:s3-storage-lens}
  - {predicate: integratesWith, object: kt:local-zones}
sources:
  - id: s1
    title: "Understanding and managing Amazon S3 storage classes - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/us_en/AmazonS3/latest/userguide/storage-class-intro.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Amazon S3 Storage Classes"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/s3/storage-classes/
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Transitioning objects using Amazon S3 Lifecycle - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "Understanding archive retrieval options - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects-retrieval-options.md
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "Managing storage costs with Amazon S3 Intelligent-Tiering - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/us_en/AmazonS3/latest/userguide/intelligent-tiering.html
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "Analyze access patterns and use the most cost-effective Amazon S3 storage class"
    publisher: Amazon Web Services (AWS Storage Blog)
    url: https://aws.amazon.com/blogs/storage/analyze-access-patterns-and-use-the-most-cost-effective-amazon-s3-storage-class
    type: official-blog
    accessed: 2026-11-15
created: 2026-11-15
---

# Amazon S3 storage classes

Every object in Amazon S3 carries a storage class, set per object at upload or changed later by a copy or a Lifecycle transition; if you set nothing, S3 uses `STANDARD`.[^s1] Classes differ in price, availability, AZ placement, first-byte latency and billing minimums, not in API. All current classes are designed for eleven nines (99.999999999%) of durability.[^s1][^s2]

## The classes at a glance

According to the S3 user guide's comparison table (as of 2026-11):[^s1]

- **S3 Standard** — the default; 99.99% designed availability across at least three AZs, millisecond access, no minimum duration or object size and no retrieval fee.
- **S3 Express One Zone** — lives only in directory buckets in a single AZ you choose; 99.95% availability and single-digit-millisecond latency. Directory buckets can also run in Local Zones.[^s1][^s2] The product page lists a one-hour minimum storage duration and notes it does not support Lifecycle transitions.[^s2]
- **S3 Intelligent-Tiering** — 99.9% availability, at least three AZs, no retrieval fees and no minimum duration, in exchange for a small monthly per-object monitoring and automation fee.[^s1][^s5]
- **S3 Standard-IA** and **S3 One Zone-IA** — millisecond access for infrequently read data with a per-GB retrieval fee, a 30-day minimum duration and a 128 KB minimum billable object size. One Zone-IA stores data in one AZ (99.5% availability), so AWS positions it for re-creatable data that can survive the loss of that zone.[^s1][^s2]
- **S3 Glacier Instant Retrieval** — millisecond access for archives read about once a quarter; 90-day minimum, 128 KB minimum object size, per-GB retrievals.[^s1][^s6]
- **S3 Glacier Flexible Retrieval** (formerly S3 Glacier) and **S3 Glacier Deep Archive** — objects must be restored before they can be read; 90-day and 180-day minimums respectively. Each archived object adds about 40 KB of billed metadata overhead.[^s1][^s2]

Legacy Reduced Redundancy (99.99% durability) is not recommended, and `OUTPOSTS` applies only to S3 on Outposts.[^s1]

## How Intelligent-Tiering works

Intelligent-Tiering starts objects in a Frequent Access tier, moves those not accessed for 30 consecutive days to Infrequent Access and those untouched for 90 days to Archive Instant Access; all three return data in milliseconds and an accessed object moves back to Frequent Access with no tiering charge.[^s1][^s5] AWS's product page claims savings of up to 40% and 68% for the two cooler instant tiers.[^s2] Archive Access and Deep Archive Access are opt-in, asynchronous tiers configured per bucket; the user guide gives minimums of 90 and 180 days without access for them.[^s1][^s5] Objects under 128 KB are never monitored or moved and always bill at Frequent Access rates, without the monitoring fee.[^s1][^s2]

## Retrieving archived data

Glacier Flexible Retrieval and Deep Archive objects must first be restored with the `RestoreObject` API. For Flexible Retrieval (and the Intelligent-Tiering Archive Access tier), Expedited restores typically finish in 1–5 minutes, Standard in 3–5 hours and Bulk in 5–12 hours, and Bulk is free. Deep Archive has no Expedited option: Standard finishes within 12 hours and Bulk within 48 hours.[^s4] Restore requests are throttled at 1,000 transactions per second per account and large datasets restore at roughly 1–2 PB per day, so very large recoveries should use S3 Batch Operations and be planned for days, not hours.[^s4] Provisioned capacity units guarantee Expedited capacity.[^s4]

## Gotchas

- **Minimums bite small and short-lived data.** Transitioning an object out of a Glacier class before its minimum duration still bills the remainder of that minimum, and a single Lifecycle rule cannot move an object onwards until that minimum has passed.[^s3]
- **Transitions are a waterfall.** Lifecycle can move objects only "downhill" (for example Standard to Standard-IA to Glacier Flexible Retrieval to Deep Archive); nothing transitions out of Deep Archive, and Flexible Retrieval can only go to Deep Archive. Moving back up requires a restore and copy.[^s3]
- **Small objects.** Since September 2024 Lifecycle by default does not transition objects under 128 KB, unless you add an object-size filter or override the default.[^s3] Each object transitioned to a Glacier class is a billed request, so millions of tiny objects can cost more to move than they save; AWS suggests filtering them out or aggregating them first.[^s3][^s6]
- **Prices are per Region.** Storage, request and retrieval rates vary by Region and over time, so check the S3 pricing page for yours.[^s2]

## Choosing a class

AWS's guidance is to measure access patterns before picking: use S3 Storage Lens to find the large buckets, then Storage Class Analysis (a paid, per-object feature that needs about 30 days of observation and only recommends Standard-IA transitions).[^s6] Predictable patterns suit Lifecycle rules into Standard-IA or a Glacier class sized to how often data is read — roughly quarterly for Instant Retrieval, twice a year for Flexible Retrieval, yearly for Deep Archive — while unknown or changing patterns suit Intelligent-Tiering.[^s6][^s5] Express One Zone fits the most frequently accessed, latency-sensitive data that can tolerate single-AZ placement.[^s1][^s2]

## Connections

Storage classes are a capability of **Amazon S3** (`kt:amazon-s3`), bounded by **Amazon S3 quotas** (`kt:amazon-s3-quotas`).[^s1] They are mostly **configured via** **S3 Lifecycle policies** (`kt:s3-lifecycle-policies`).[^s3] Most classes span three or more **Availability Zones** (`kt:availability-zones`); One Zone-IA and Express One Zone use one, and directory buckets can run in **Local Zones** (`kt:local-zones`).[^s1] Usage is **monitored by** **S3 Storage Lens** (`kt:s3-storage-lens`) and billed under the general model in **AWS pricing fundamentals** (`kt:aws-pricing-fundamentals`).[^s6][^s2] Choosing the cheapest adequate class supports the **cost optimization pillar** (`kt:cost-optimization-pillar`), and archive classes suit backup data, as with **AWS Backup** (`kt:aws-backup`).[^s6][^s2]

## Sources

[^s1]: Amazon Web Services, "Understanding and managing Amazon S3 storage classes - Amazon Simple Storage Service". https://docs.aws.amazon.com/us_en/AmazonS3/latest/userguide/storage-class-intro.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Amazon S3 Storage Classes". https://aws.amazon.com/s3/storage-classes/ (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Transitioning objects using Amazon S3 Lifecycle - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "Understanding archive retrieval options - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects-retrieval-options.md (accessed 2026-11-15)
[^s5]: Amazon Web Services, "Managing storage costs with Amazon S3 Intelligent-Tiering - Amazon Simple Storage Service". https://docs.aws.amazon.com/us_en/AmazonS3/latest/userguide/intelligent-tiering.html (accessed 2026-11-15)
[^s6]: Amazon Web Services (AWS Storage Blog), "Analyze access patterns and use the most cost-effective Amazon S3 storage class". https://aws.amazon.com/blogs/storage/analyze-access-patterns-and-use-the-most-cost-effective-amazon-s3-storage-class (accessed 2026-11-15)
