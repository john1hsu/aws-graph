---
id: kt:recycle-bin
type: Feature
title: Recycle Bin for Amazon EBS
aliases: [Recycle Bin, Amazon EBS Recycle Bin, EC2 Recycle Bin, Recycle Bin rule lock, rbin]
domain: storage
summary: >-
  A Region-scoped Amazon EBS capability that retains deleted EBS volumes, EBS snapshots and EBS-backed AMIs
  for a period set by retention rules, so they can be restored after accidental or malicious deletion;
  rules can be locked against changes.
verified: 2026-11-20
status: current
broader: [kt:amazon-ebs]
related: [kt:ebs-snapshots, kt:amazon-machine-images, kt:aws-backup]
relations:
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: enables, object: kt:disaster-recovery-strategies}
sources:
  - id: s1
    title: "Recover deleted EBS volumes, EBS snapshots, and EBS-backed AMIs with Recycle Bin - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recycle-bin.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "How does Recycle Bin work? - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/recycle-bin-concepts.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Considerations for Recycle Bin - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/recycle-bin-factors.md
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "Lock a Recycle Bin retention rule to prevent it from being updated or deleted - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/recycle-bin-lock.html
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "CreateRule - Recycle Bin API Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/recyclebin/latest/APIReference/API_CreateRule.html
    type: official-docs
    accessed: 2026-11-20
  - id: s6
    title: "Amazon EBS launches Rule Lock for Recycle Bin to prevent unintended changes to Region-level retention rules for Snapshots and AMIs"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2022/11/amazon-ebs-rule-lock-recycle-bin-unintended-changes-region-level-snapshots-amis
    type: official-blog
    accessed: 2026-11-20
  - id: s7
    title: "Protect your resources from unintended deletions through Rule Lock for Recycle Bin"
    publisher: Amazon Web Services (AWS Storage Blog)
    url: https://aws.amazon.com/blogs/storage/protect-your-resources-from-deletions-through-rule-lock-for-recycle-bin
    type: official-blog
    accessed: 2026-11-20
created: 2026-11-20
---

# Recycle Bin for Amazon EBS

Recycle Bin is a safety net for deletions in Amazon EBS. When a supported resource is deleted and matches a retention rule, it is kept for the rule's retention period instead of being destroyed immediately; you can restore it at any time before that period ends, after which it is permanently deleted.[^s1][^s2] It protects three resource types: EBS volumes, EBS snapshots (including archived snapshots) and EBS-backed AMIs, including disabled AMIs.[^s1] AWS assesses Recycle Bin as part of Amazon EBS, so it falls under the same compliance programs.[^s1]

## How it works

**Retention rules.** A rule names a resource type, a rule type and a retention period. Volumes can be retained for 1–7 days; snapshots and AMIs for 1–365 days.[^s2] Rules are Regional: you must create them in every Region you want covered, and a rule only acts on deletions in its own Region.[^s2][^s5]

- **Tag-level rules** retain only deleted resources that carry at least one of the rule's tag key/value pairs (up to 50 per rule).[^s1][^s5]
- **Region-level rules** retain every deleted resource of that type in the Region, optionally minus resources carrying one of up to five exclusion tags.[^s5]

When a resource matches several rules, the longest retention period wins, regardless of whether the rules are Region-level or tag-level.[^s3][^s7] The first rule for a resource type in a Region is eventually consistent and may take a short time to start retaining; later rules take effect almost immediately.[^s3] A new rule's status must reach `available` before it retains anything.[^s5]

**While in the bin.** You can view, restore or retag a resource, but you cannot delete it manually; it leaves only when restored or when retention expires.[^s2][^s3] AWS adds the system tag `aws:recycle-bin:resource-in-bin = true` on entry and removes it on restore.[^s3] Shared snapshots and AMIs are unshared on entry and re-shared on restore, and fast snapshot restore is disabled shortly after a snapshot enters the bin and must be re-enabled manually.[^s3]

**Rule lock.** Launched on 2022-11-23, rule lock makes a Region-level rule immune to modification or deletion, even by users who hold the IAM permissions to manage it.[^s4][^s6] Locking sets an unlock delay of 7–30 days that cannot be changed later. Unlocking puts the rule into a pending state for the full delay, during which it still cannot be changed; re-locking during the delay resets it.[^s4] This means a compromised administrator account cannot quietly shorten retention and then mass-delete snapshots: defenders get at least a week to notice. Tag-level rules and Region-level rules with exclusion tags cannot be locked.[^s4] AWS recommends EventBridge notifications on lock-state changes, and rule actions are recorded by CloudTrail.[^s4][^s7]

## Quotas, pricing and gotchas

Quotas (as of 2026-11): 250 retention rules per Region and 50 tag key/value pairs per rule.[^s1] Recycle Bin and its rules carry no extra charge; volumes and snapshots in the bin are billed at their normal rates, EBS-backed AMIs add nothing beyond their snapshots, and billing stops when retention expires even if the resource is briefly still visible.[^s1] Archived snapshots in the bin keep the archive tier's rate and its 90-day minimum.[^s1] Rule lock is also free.[^s6]

Gotchas:

- **AMIs depend on their snapshots.** Restore all of an AMI's snapshots first; if those snapshots expire out of the bin, the AMI can no longer be recovered. Set snapshot retention at least as long as AMI retention.[^s3]
- **Automated deletions are retained too.** Snapshots that AWS Backup or Amazon Data Lifecycle Manager deletes as part of normal lifecycle are kept (and billed) if they match a rule. Exclude them with tags if you don't want that. A snapshot restored from the bin is no longer managed by AWS Backup or Data Lifecycle Manager and must be cleaned up manually.[^s3]
- **Volumes still count.** Volumes in the bin count toward EBS volume quotas, and in-progress volume initialization keeps billing.[^s3]
- **Not every volume is caught.** Volumes from failed creations or failed instance launches are not protected; volumes deleted by instance termination or root volume replacement are.[^s3]

## When to use it (and when not)

Use a locked Region-level snapshot and AMI rule as a low-cost guard against fat-finger deletions and ransomware-style destruction of backups, and a short volume rule (up to 7 days) to undo accidental terminations.[^s2][^s4][^s7] It is not a backup tool: it only preserves what already existed at deletion time and never creates copies. Use snapshots and AWS Backup (including vault lock and cross-account copies) for scheduled, isolated backups, with Recycle Bin as the last line of defense behind them.[^s3]

## Connections

- **Broader:** a capability of `kt:amazon-ebs`, assessed and documented as part of that service.[^s1]
- **Related:** protects `kt:ebs-snapshots` and `kt:amazon-machine-images`; interacts with `kt:aws-backup`, whose lifecycle deletions it can retain.[^s3]
- **Scoped to** `kt:aws-regions`: rules exist and act per Region.[^s2]
- **Monitored by** `kt:aws-cloudtrail` and **integrates with** `kt:amazon-eventbridge` for rule and lock-state events.[^s4][^s7]
- **Enables** part of `kt:disaster-recovery-strategies` by making deletions of recovery points reversible.[^s2]

## Sources
[^s1]: Amazon Web Services, "Recover deleted EBS volumes, EBS snapshots, and EBS-backed AMIs with Recycle Bin - Amazon EBS". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recycle-bin.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "How does Recycle Bin work? - Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/recycle-bin-concepts.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "Considerations for Recycle Bin - Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/recycle-bin-factors.md (accessed 2026-11-20)
[^s4]: Amazon Web Services, "Lock a Recycle Bin retention rule to prevent it from being updated or deleted - Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/recycle-bin-lock.html (accessed 2026-11-20)
[^s5]: Amazon Web Services, "CreateRule - Recycle Bin API Reference". https://docs.aws.amazon.com/recyclebin/latest/APIReference/API_CreateRule.html (accessed 2026-11-20)
[^s6]: Amazon Web Services (What's New with AWS), "Amazon EBS launches Rule Lock for Recycle Bin to prevent unintended changes to Region-level retention rules for Snapshots and AMIs", 2022-11-23. https://aws.amazon.com/about-aws/whats-new/2022/11/amazon-ebs-rule-lock-recycle-bin-unintended-changes-region-level-snapshots-amis (accessed 2026-11-20)
[^s7]: Amazon Web Services (AWS Storage Blog), "Protect your resources from unintended deletions through Rule Lock for Recycle Bin", 2022-11-23. https://aws.amazon.com/blogs/storage/protect-your-resources-from-deletions-through-rule-lock-for-recycle-bin (accessed 2026-11-20)
