---
id: kt:s3-lifecycle-policies
type: Feature
title: Amazon S3 Lifecycle
aliases: [S3 Lifecycle, S3 Lifecycle policies, S3 lifecycle rules, S3 Lifecycle configuration, S3 object lifecycle management]
domain: storage
summary: >-
  A bucket-level Amazon S3 feature in which up to 1,000 rules, each scoped by prefix, tags or object
  size, automatically transition objects to cheaper storage classes or expire them (including
  noncurrent versions, expired delete markers and incomplete multipart uploads) on a schedule.
verified: 2026-11-26
status: current
broader: [kt:amazon-s3]
related: [kt:s3-object-lock, kt:s3-versioning, kt:s3-storage-lens]
relations:
  - {predicate: integratesWith, object: kt:s3-storage-classes}
  - {predicate: governedBy, object: kt:amazon-s3-quotas}
  - {predicate: monitoredBy, object: kt:s3-event-notifications}
  - {predicate: implements, object: kt:cost-optimization-pillar}
sources:
  - id: s1
    title: "Managing the lifecycle of objects - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.md
    type: official-docs
    accessed: 2026-11-26
  - id: s2
    title: "Lifecycle configuration elements - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/us_en/AmazonS3/latest/userguide/intro-lifecycle-rules.html
    type: official-docs
    accessed: 2026-11-26
  - id: s3
    title: "Expiring objects - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-expire-general-considerations.html
    type: official-docs
    accessed: 2026-11-26
  - id: s4
    title: "Transitioning objects using Amazon S3 Lifecycle - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html
    type: official-docs
    accessed: 2026-11-26
  - id: s5
    title: "Troubleshooting Amazon S3 Lifecycle issues - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/troubleshoot-lifecycle.html
    type: official-docs
    accessed: 2026-11-26
  - id: s6
    title: "Examples of S3 Lifecycle configurations - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/dev/lifecycle-configuration-examples.html
    type: official-docs
    accessed: 2026-11-26
created: 2026-11-26
---

# Amazon S3 Lifecycle

S3 Lifecycle is a configuration attached to a bucket: a set of rules that tell Amazon S3 what to do with groups of objects as they age. It supports two kinds of action. **Transition** actions change an object's storage class, for example moving it to Standard-IA 30 days after creation. **Expiration** actions delete objects, whatever storage class they are in. S3 runs the actions itself, so no scheduler, Lambda function or batch job is needed, and rules apply to objects that already exist as well as to new ones. A new 30-day expiration rule queues every existing object older than 30 days.[^s1]

## How a rule is built

Each rule has an `ID` (unique in the bucket, up to 255 characters), a `Status` of Enabled or Disabled, a `Filter`, and one or more actions. The filter can name a key prefix, object tags, or a prefix plus tags joined with `And`. It can also bound object size with `ObjectSizeGreaterThan` and `ObjectSizeLessThan`, in bytes. An empty filter selects every object in the bucket.[^s2]

The actions are:

- **Transition** and **Expiration** for current objects, timed by `Days` after creation or by a fixed `Date` (ISO 8601, always midnight UTC). A date-based action keeps applying to matching objects for as long as the rule stays enabled.[^s2]
- **NoncurrentVersionTransition** and **NoncurrentVersionExpiration** for versioned buckets. `NoncurrentDays` counts from when the object became noncurrent, which is when its successor was written. The optional `NewerNoncurrentVersions` (1 to 100) keeps that many recent versions; both thresholds must be passed before S3 acts.[^s2]
- **ExpiredObjectDeleteMarker**, which removes delete markers that no longer have any versions behind them.[^s2][^s6]
- **AbortIncompleteMultipartUpload**, which limits how many days a multipart upload may stay in progress and clears its orphaned parts. It cannot be used in a rule that filters on tags.[^s2]

S3 computes a due date by adding the day count to the object's creation time and rounding up to the next midnight UTC.[^s2] In a versioned bucket, expiring the current version does not delete data. It adds a delete marker, so you need a noncurrent-version rule to reclaim the storage.[^s2] AWS's examples page shows typical combinations: tier to Standard-IA at 30 days and Glacier at 90, expire at 365; keep five newer noncurrent versions; and abort uploads after 7 days.[^s6]

## Limits, timing and billing

As of 2026-11, a bucket can hold up to 1,000 Lifecycle rules, and AWS says this limit cannot be raised.[^s2] Actions run asynchronously, so an object can wait some time after it becomes eligible. Billing changes as soon as the rule is satisfied, not when the action runs. Transitions to Intelligent-Tiering are the exception: they change billing only after the move.[^s1][^s5] Expired objects stop incurring storage charges, and the expiration itself is free.[^s3] Transitions are not free. Each object moved into a storage class is billed as a request, and each move into Glacier Flexible Retrieval or Deep Archive counts as one transition request. Lifecycle transitions have no retrieval charge.[^s1][^s4]

When rules overlap, an object gets only one action per day. Permanent deletion wins over transition, and transition wins over creating a delete marker. Between two transitions, the move to Glacier Flexible Retrieval wins over Standard-IA or One Zone-IA.[^s3][^s6] Changes to a rule can take up to 15 minutes to take effect, and tag filters are checked again when the action runs.[^s3]

## Gotchas

- **Bucket policies don't stop it.** On general purpose buckets, a bucket policy cannot block Lifecycle deletes or transitions, even a policy that denies every action to every principal. To stop deletions, disable or edit the rule. Expired data can be recovered only through versioning.[^s1][^s5]
- **Some objects are skipped.** Noncurrent versions protected by Object Lock are not expired. Objects whose replication status is Pending or Failed are not acted on.[^s3]
- **Small objects stay put.** Since September 2024, new configurations do not transition objects under 128 KB unless a size filter says otherwise. Older configurations keep the earlier behavior until they are edited.[^s2][^s4]
- **Minimum durations still bill.** Deleting or moving data early from Standard-IA or One Zone-IA (30 days), Glacier Flexible Retrieval (90 days) or Deep Archive (180 days) charges the rest of the minimum duration.[^s3][^s4]
- **Delete markers count.** In versioned buckets, delete markers still count as objects. Without an `ExpiredObjectDeleteMarker` rule, object counts can stay high after the data is gone.[^s5]

## When to use it (and when not)

Use Lifecycle when you know in advance how data ages: logs that go cold after a month, backups kept for a compliance period, or version histories with a cap. It is also cheap housekeeping on any bucket, through multipart-abort and delete-marker rules.[^s6] Lifecycle can also move Standard or Standard-IA objects into S3 Intelligent-Tiering, which is one of the allowed transition targets.[^s4] To check that rules are working, AWS points to S3 Event Notifications for Lifecycle transition and expiration events, server access logs, S3 Inventory's Lifecycle expiration date field, S3 Metadata journal tables, and S3 Storage Lens.[^s5]

## Connections

Lifecycle is a feature of **Amazon S3** (`kt:amazon-s3`).[^s1] Its main job is moving objects between **S3 storage classes** (`kt:s3-storage-classes`) along the downhill path AWS allows, so the two nodes integrate.[^s4] It is **governed by** the non-adjustable 1,000-rule limit recorded under **Amazon S3 quotas** (`kt:amazon-s3-quotas`).[^s2] Its noncurrent-version actions only apply with **S3 Versioning** (`kt:s3-versioning`), and **S3 Object Lock** (`kt:s3-object-lock`) protects versions from its expirations.[^s2][^s3] Its actions are **monitored by** **S3 Event Notifications** (`kt:s3-event-notifications`) and observed through **S3 Storage Lens** (`kt:s3-storage-lens`).[^s5] Automatically deleting or tiering data that is no longer needed **implements** the **cost optimization pillar** (`kt:cost-optimization-pillar`), since it stops you paying for storage you don't use.[^s1][^s3]

## Sources
[^s1]: Amazon Web Services, "Managing the lifecycle of objects - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.md (accessed 2026-11-26)
[^s2]: Amazon Web Services, "Lifecycle configuration elements - Amazon Simple Storage Service". https://docs.aws.amazon.com/us_en/AmazonS3/latest/userguide/intro-lifecycle-rules.html (accessed 2026-11-26)
[^s3]: Amazon Web Services, "Expiring objects - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-expire-general-considerations.html (accessed 2026-11-26)
[^s4]: Amazon Web Services, "Transitioning objects using Amazon S3 Lifecycle - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html (accessed 2026-11-26)
[^s5]: Amazon Web Services, "Troubleshooting Amazon S3 Lifecycle issues - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/userguide/troubleshoot-lifecycle.html (accessed 2026-11-26)
[^s6]: Amazon Web Services, "Examples of S3 Lifecycle configurations - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/dev/lifecycle-configuration-examples.html (accessed 2026-11-26)
