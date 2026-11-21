---
id: kt:s3-object-lock
type: Feature
title: Amazon S3 Object Lock
aliases: [S3 Object Lock, S3 WORM, Object Lock]
domain: storage
summary: >-
  An Amazon S3 feature that stores object versions in a write-once-read-many (WORM) model, blocking
  deletion or overwrite through retention periods (governance or compliance mode, fixed or event-driven)
  and legal holds, for regulatory record retention and ransomware protection.
verified: 2026-11-21
status: current
broader: [kt:amazon-s3]
related: [kt:aws-backup, kt:ebs-snapshots, kt:s3-storage-classes, kt:aws-kms]
relations:
  - {predicate: dependsOn, object: kt:s3-versioning}
  - {predicate: configuredVia, object: kt:s3-batch-operations}
  - {predicate: integratesWith, object: kt:s3-lifecycle-policies}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: compliesWith, object: kt:sec-rule-17a-4}
sources:
  - id: s1
    title: "Locking objects with Object Lock - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html
    type: official-docs
    accessed: 2026-11-21
  - id: s2
    title: "Object Lock considerations - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock-managing.html
    type: official-docs
    accessed: 2026-11-21
  - id: s3
    title: "Amazon S3 Object Lock now supports variable retention with event holds"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-s3-object-lock-variable-retention/
    type: official-blog
    accessed: 2026-11-21
  - id: s4
    title: "Amazon S3 now supports enabling S3 Object Lock on existing buckets"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2023/11/amazon-s3-enabling-object-lock-buckets/
    type: official-blog
    accessed: 2026-11-21
  - id: s5
    title: "S3 Object Lock – Amazon S3"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/s3/features/object-lock/
    type: official-docs
    accessed: 2026-11-21
created: 2026-11-21
---

# Amazon S3 Object Lock

S3 Object Lock applies a write-once-read-many (WORM) model to objects in Amazon S3, so that a protected object version cannot be deleted or overwritten for a fixed period, until a future event plus a set duration, or indefinitely.[^s1] AWS positions it both for regulated record retention and as a defence against ransomware, where an attacker with stolen credentials tries to delete or encrypt backups.[^s5]

## How it works

Object Lock protects individual **object versions**, so it only works in buckets with S3 Versioning enabled; the S3 feature page notes that enabling Object Lock turns versioning on automatically.[^s1][^s5] A lock covers only the version named in the request: new versions can still be written over the same key, and a simple DELETE without a version ID succeeds by inserting a delete marker, while a DELETE aimed at a locked version ID is refused with 403 Access Denied.[^s1] Delete markers themselves are not WORM-protected.[^s2]

There are two independent protection mechanisms, and a version can carry either, both or neither:[^s1]

- **Retention period** – a retain-until-date. It can be *fixed* (you set the date) or, since September 2026, *variable*: you place an **event hold** with a duration, the object stays protected while the hold is on, and when the hold is released S3 sets the date to release time plus the duration.[^s1][^s3] Decreasing an event-hold duration never moves the date earlier, and the maximum event-hold duration is 36,500 days.[^s1]
- **Legal hold** – no expiry; it stays until someone with `s3:PutObjectLegalHold` removes it, and it keeps protecting the version even after the retention period has lapsed.[^s1]

Retention periods come in two **modes**.[^s1] In **governance mode**, users with the `s3:BypassGovernanceRetention` permission can shorten or remove retention or delete the version, but only if the request also sends `x-amz-bypass-governance-retention:true`; the S3 console sends that header by default.[^s1][^s2] In **compliance mode**, no user—including the account root user—can overwrite or delete the version, change the mode or shorten the period; AWS notes the only way to remove such data early is to delete the AWS account.[^s1] Bypass permissions never affect legal holds.[^s2]

A bucket can carry a **default retention** (mode plus days or years, optionally with an event hold) that applies to new objects; an explicit retention setting on a PUT overrides it.[^s1] Since November 2023 Object Lock can be enabled on existing buckets, not just at creation, and existing objects can be locked in bulk with S3 Batch Operations.[^s4]

## Quotas, pricing and gotchas

- **It is one-way.** Once Object Lock is enabled on a bucket you cannot disable it or suspend versioning, and Object Lock buckets cannot be server access log destinations (as of 2026-11).[^s2]
- **Integrity headers are required.** Any PutObject that sets a retention period must include `Content-MD5` or `x-amz-sdk-checksum-algorithm`; the console adds Content-MD5 for you.[^s2]
- **Lifecycle keeps running, but can't delete locked data.** Lifecycle rules still transition objects and place delete markers, and the lock survives storage-class transitions, but an expiration action cannot remove a locked version.[^s2]
- **Keys are not locked.** Object Lock does not protect against losing an AWS KMS key; if the key is deleted, locked objects may become unreadable.[^s2]
- **Replication** needs Object Lock on both source and destination buckets; replicated objects carry the source's lock settings, including variable-retention holds.[^s2]
- **Guardrails in policy.** Bucket policies can bound retention with the condition keys `s3:object-lock-remaining-retention-days`, `s3:object-lock-event-hold` and `s3:object-lock-event-hold-duration-days` (years count as 365 days).[^s2]
- **Pricing.** AWS states the event-hold capability carries no additional charge and is available in all AWS Regions including China (as of 2026-09); you still pay normal S3 storage for every retained version.[^s3] The 2023 existing-bucket launch was likewise announced for all Regions, including AWS GovCloud (US).[^s4]

## When to use it (and when not)

Use **compliance mode** for records a regulator says must be immutable: Cohasset Associates assessed Object Lock for SEC Rule 17a-4(f), FINRA Rule 4511 and CFTC Regulation 1.31.[^s3][^s4] Use **governance mode** for ransomware-resistant backups and for testing retention settings, because privileged users can still correct mistakes.[^s1] Use a **legal hold** when the end date is unknown, and **variable retention** when the clock should start at a business event such as a contract closing or an audit finishing—unlike a legal hold, the protection continues for the set duration after the hold is released.[^s1][^s3] Avoid compliance mode on data you may legitimately need to purge early, since nothing short of closing the account removes it.[^s1]

## Connections

Object Lock is a feature of `kt:amazon-s3` and depends on S3 Versioning (`kt:s3-versioning`), because it protects versions rather than keys.[^s1] It is configured at scale through S3 Batch Operations (`kt:s3-batch-operations`) and coexists with S3 Lifecycle rules (`kt:s3-lifecycle-policies`) and `kt:s3-storage-classes` transitions.[^s2][^s4] Hold and retention changes emit `s3:ObjectRetention:Put` events that AWS CloudTrail and Amazon CloudWatch can track.[^s2] Its regulatory role links it to SEC Rule 17a-4.[^s3] Comparable immutability exists elsewhere in the graph: `kt:ebs-snapshots` has snapshot lock and `kt:aws-backup` protects recovery points, while `kt:aws-kms` key management sits outside the lock's protection.[^s2][^s5]

## Sources
[^s1]: Amazon Web Services, "Locking objects with Object Lock - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html (accessed 2026-11-21)
[^s2]: Amazon Web Services, "Object Lock considerations - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock-managing.html (accessed 2026-11-21)
[^s3]: Amazon Web Services, "Amazon S3 Object Lock now supports variable retention with event holds" (What's New, 2026-09-08). https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-s3-object-lock-variable-retention/ (accessed 2026-11-21)
[^s4]: Amazon Web Services, "Amazon S3 now supports enabling S3 Object Lock on existing buckets" (What's New, 2023-11-20). https://aws.amazon.com/about-aws/whats-new/2023/11/amazon-s3-enabling-object-lock-buckets/ (accessed 2026-11-21)
[^s5]: Amazon Web Services, "S3 Object Lock – Amazon S3". https://aws.amazon.com/s3/features/object-lock/ (accessed 2026-11-21)
