---
id: kt:ebs-snapshots
type: Feature
title: Amazon EBS snapshots
aliases: [EBS snapshots, Amazon Elastic Block Store snapshots, EBS snapshot, EBS Snapshots Archive]
domain: storage
summary: >-
  Point-in-time, block-level backups of Amazon EBS volumes: the first is full and later ones store only
  changed blocks, they can be copied across Regions and accounts, archived to a cheaper full-copy tier,
  locked for WORM retention, and pre-warmed with fast snapshot restore.
verified: 2026-11-18
status: current
broader: [kt:amazon-ebs]
related: [kt:aws-backup, kt:amazon-machine-images, kt:recycle-bin, kt:aws-elastic-disaster-recovery]
relations:
  - {predicate: governedBy, object: kt:ebs-quotas}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: enables, object: kt:disaster-recovery-strategies}
sources:
  - id: s1
    title: "How Amazon EBS snapshots work - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/how_snapshots_work.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Copy an Amazon EBS snapshot - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/ebs-copy-snapshot.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Guidelines and best practices for archiving Amazon EBS snapshots - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/archiving-guidelines.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Pricing and billing for archiving Amazon EBS snapshots - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/snapshot-archive-pricing.html
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Amazon EBS fast snapshot restore - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/ebs-fast-snapshot-restore.html
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "Amazon EBS snapshot lock concepts - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/snapshot-lock-concepts.html
    type: official-docs
    accessed: 2026-11-18
  - id: s7
    title: "Amazon EBS Snapshots FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ebs/snapshots/faqs/
    type: official-docs
    accessed: 2026-11-18
  - id: s8
    title: "Amazon EBS now supports associating additional KMS keys with snapshots"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/11/ebs-snapshots-associate-kms-keys/
    type: official-blog
    accessed: 2026-11-18
  - id: s9
    title: "Archive Amazon EBS snapshots - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/snapshot-archive.html
    type: official-docs
    accessed: 2026-11-18
created: 2026-11-18
---

# Amazon EBS snapshots

An EBS snapshot is a point-in-time copy of the data on an Amazon EBS volume, used to back up volumes, create new volumes and move data between Availability Zones, Regions and accounts. A volume's first snapshot is full and captures every block written so far; each later snapshot stores only the blocks that changed or were added since the previous one and points back to older snapshots for the rest.[^s1] Size and cost depend on data written, not provisioned size.[^s1]

## How it works

**Incremental chains.** Three snapshots of one volume holding 10 GiB, then 4 GiB changed, then 2 GiB added, consume 16 GiB in total, because each new snapshot references unchanged blocks in its predecessors.[^s1] A snapshot of a volume restored from another snapshot is incremental to it, unless the source came from another account or the KMS key differs.[^s1] The EBS direct APIs read and write snapshot data in 512 KiB blocks and can list the blocks changed between two snapshots.[^s7]

**Copying.** A completed snapshot can be copied within its Region or to another Region (including associated Local Zones and Outposts), and you can copy snapshots shared with you; snapshots that live on an Outpost cannot be copied.[^s2] A same-account, same-Region, same-key copy is always incremental. A cross-Region or cross-account copy is incremental only if an earlier, unarchived copy still exists at the destination with the same encryption; changing the KMS key always produces a full copy.[^s2] Copying can encrypt or re-encrypt a snapshot; tags and fast snapshot restore don't carry over.[^s2] Time-based copy lets you request a completion duration in 15-minute increments instead of best-effort completion.[^s2]

**Snapshots Archive.** Archiving converts an incremental snapshot into a full one and moves it to a cheaper archive tier; you must restore it to the standard tier before using it.[^s1][^s3] Retrieval typically takes 24 to 72 hours.[^s7]

**Snapshot lock.** Locking blocks deletion. Governance mode can be unlocked or changed by users with IAM permissions. Compliance mode gives WORM retention: after an optional 1–72 hour cooling-off period the lock can only be extended, and before it expires the only way to delete the snapshot is to close the AWS account. Lock durations run from 1 day to 36,500 days.[^s6] Deleted snapshots can also be recovered from Recycle Bin under retention rules you define.[^s7]

**Fast snapshot restore (FSR).** Volumes restored from a snapshot normally pay a latency penalty the first time each block is read. FSR, enabled per snapshot-and-AZ pair, delivers fully initialized volumes. It allows up to 5 FSR-enabled snapshots per Region, snapshots of 16 TiB or less, and gives full benefit up to 64,000 IOPS and 1,000 MiB/s per volume. It is not supported in Outposts, Local Zones or Wavelength Zones.[^s5] How many volumes you can create at full performance is limited by per-AZ volume-creation credits that scale inversely with snapshot size.[^s5][^s7]

## Quotas, pricing and gotchas

Example prices from AWS's documentation (as of 2026-11; they vary by Region): standard snapshot storage $0.05 per GB-month; archive tier $0.0125 per GB-month with a 90-day minimum and a one-time $0.03 per GB restore fee; FSR $0.75 per snapshot per AZ per hour (US East (N. Virginia)), billed per minute with a one-hour minimum, so one snapshot in one AZ for 30 days costs about $540.[^s4][^s5] Deleting or permanently restoring an archived snapshot before 90 days incurs a pro-rated charge for the remaining days.[^s4] Shared-snapshot FSR is billed to the consumer account.[^s5] Cross-Region copies also incur EC2 data transfer charges.[^s2]

Quotas (as of 2026-11): 20 concurrent copy requests per destination; 25 archived snapshots per volume, 25 concurrent archives and 5 concurrent archive restores per account by default (increases available on request).[^s2][^s9] Per-Region snapshot counts are listed under `kt:ebs-quotas`.

Gotchas: archiving incremental snapshots rarely saves money, because the archived copy is full and later snapshots still reference the standard-tier blocks. Archive a volume's only snapshot or standalone monthly or yearly compliance copies, and never the most recent snapshot you'd restore from.[^s3] Losing access to a shared snapshot's KMS key breaks restores, so re-encrypt shared copies with a key you own.[^s2]

## What changed (2026-11)

On 2026-11-16 AWS added the ability to associate additional KMS keys with a snapshot. You can, for example, attach a customer managed key to a snapshot encrypted with the AWS managed key and then share or copy it across accounts without re-encrypting it first. The feature is available in all commercial, China and AWS GovCloud (US) Regions, the European Sovereign Cloud and on Local Zones and Outposts.[^s8]

## When to use it (and when not)

Use snapshots for volume-level backup, cross-AZ and cross-Region recovery, cloning environments and long-term compliance copies (archive plus compliance-mode lock).[^s2][^s3][^s6] Use FSR only for the few images that must launch at full performance immediately, since it bills per AZ per hour.[^s5]

## Connections

- **Broader:** a feature of `kt:amazon-ebs`; snapshots are what let AZ-bound volumes be restored elsewhere.[^s1]
- **Governed by** `kt:ebs-quotas` (copy, archive and FSR limits) and **scoped to** `kt:aws-regions`, the unit for copying and FSR quotas.[^s2][^s5]
- **Secured by** `kt:aws-kms`: encryption keys decide whether copies are incremental and who can restore them.[^s1][^s2][^s8]
- **Enables** `kt:disaster-recovery-strategies` such as backup-and-restore through cross-Region and cross-account copies.[^s2]
- **Related:** `kt:aws-backup` for centralized scheduling, `kt:recycle-bin` for accidental-deletion recovery,[^s7] `kt:amazon-machine-images` built from snapshots, and `kt:aws-elastic-disaster-recovery` for continuous replication instead of periodic snapshots.

## Sources
[^s1]: Amazon Web Services, "How Amazon EBS snapshots work - Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/how_snapshots_work.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Copy an Amazon EBS snapshot - Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/ebs-copy-snapshot.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Guidelines and best practices for archiving Amazon EBS snapshots - Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/archiving-guidelines.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Pricing and billing for archiving Amazon EBS snapshots - Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/snapshot-archive-pricing.html (accessed 2026-11-18)
[^s5]: Amazon Web Services, "Amazon EBS fast snapshot restore - Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/ebs-fast-snapshot-restore.html (accessed 2026-11-18)
[^s6]: Amazon Web Services, "Amazon EBS snapshot lock concepts - Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/snapshot-lock-concepts.html (accessed 2026-11-18)
[^s7]: Amazon Web Services, "Amazon EBS Snapshots FAQs". https://aws.amazon.com/ebs/snapshots/faqs/ (accessed 2026-11-18)
[^s8]: Amazon Web Services (What's New with AWS), "Amazon EBS now supports associating additional KMS keys with snapshots", 2026-11-16. https://aws.amazon.com/about-aws/whats-new/2026/11/ebs-snapshots-associate-kms-keys/ (accessed 2026-11-18)
[^s9]: Amazon Web Services, "Archive Amazon EBS snapshots - Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/snapshot-archive.html (accessed 2026-11-18)
