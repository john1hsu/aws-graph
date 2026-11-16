---
id: kt:aws-backup-quotas
type: Limit
title: AWS Backup quotas
aliases: [AWS Backup limits, Backup quotas, AWS Backup service quotas, AWS Backup API rate limits]
domain: storage
summary: >-
  The per-account, per-Region limits on AWS Backup: counts of vaults, plans and recovery points,
  fixed policy limits on rules, assignments and copy actions, concurrency caps on backup, copy,
  indexing and restore-testing jobs, and API request rates. Only some are adjustable through Service Quotas.
verified: 2026-11-16
status: current
broader: [kt:aws-backup]
related: [kt:amazon-s3-quotas, kt:organizations-quotas, kt:disaster-recovery-strategies]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:amazon-s3-quotas}
sources:
  - id: s1
    title: "AWS Backup quotas - AWS Backup"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-backup/latest/devguide/aws-backup-limits.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "AWS Backup endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/bk.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "AWS Backup now supports protecting more than 1,000 Amazon S3 buckets per account"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/aws-backup-more-than-1000-s3-buckets/
    type: official-blog
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS Backup quotas

AWS Backup applies its own set of quotas, most of them per account and per Region. They cover how many containers you can create (vaults, plans, recovery points), how a backup plan may be shaped (rules, resource assignments, copy actions), how many jobs can run at once, and how fast you can call the API.[^s1][^s2] A minority are adjustable; most of the policy and concurrency limits are fixed, so they shape how you design plans rather than something you raise with a ticket.[^s1] The quotas of the protected services (EBS, RDS, S3 and so on) still apply on top.[^s1]

## Containers and policy shape (as of 2026-11)

- **Vaults:** 300 per Region per account, counting standard backup vaults and logically air-gapped vaults together; adjustable.[^s1]
- **Recovery points:** 1,000,000 per vault; adjustable.[^s1][^s2]
- **Backup plans:** 300 per Region per account, with up to 2,000 versions per plan; both adjustable.[^s1][^s2]
- **Plan structure (not adjustable):** 10 rules per plan, 100 resource assignments per plan, 10 tag-based resource selections per plan, 30 tags or 30 conditions in a resource selection, 5 copy actions per rule, and 10 tags added to a recovery point.[^s1]
- **Other fixed limits:** 50 legal holds per account, 20 hypervisors, and 10 nested layers for application-stack backups.[^s1]

The limits on a single assignment apply per rule, so AWS suggests spreading resources across multiple rules or plans when one assignment is not enough.[^s1] Organization-wide backup policies also count against AWS Organizations quotas.[^s1]

## Job concurrency

Only **one** backup or copy job can run against a given resource at a time.[^s1][^s2] Copy jobs into a destination Region are capped at 100 concurrent per account for resource types fully managed by AWS Backup; once that cap is hit, a specific destination vault running fewer than 5 copies can still start new ones, up to 5.[^s1] Up to 30 concurrent cross-account copies of the same resource can target one destination Region.[^s1] Copies into a logically air-gapped vault have tighter, fixed per-type ceilings per source account and destination Region: 20 for EBS, Aurora, DocumentDB, Neptune and Storage Gateway, and 5 for FSx.[^s1]

Newer features carry their own caps. Backup indexing allows 40 concurrent indexing jobs per account in most Regions but 10 in Asia Pacific (Malaysia), Canada (Central), Asia Pacific (Thailand) and Mexico (Central); search allows 10 concurrent search jobs and 20 recovery points per search job.[^s1] GuardDuty malware scanning of backups is limited to 150 running scan jobs per account (adjustable).[^s1] Restore testing allows 100 plans, and caps concurrent restore jobs created by those plans per resource type, for example 100 for EBS and EC2 and 30 for EFS, Redshift and S3; these caps do not apply to on-demand restores.[^s1] An EKS cluster backup can include up to 10,000 namespaces, and an AWS Backup gateway for VMware runs 4 backup or restore jobs at once.[^s1]

## API rate limits

The General Reference lists default request rates for the AWS Backup API: 5 calls per second for mutating calls such as `StartBackupJob`, `StartRestoreJob` and `CreateBackupPlan`; 10 for `DeleteRecoveryPoint`; 15 for `Describe*` and `Get*` calls; 20 for `List*` calls; and 50 calls per second across all APIs combined.[^s2] AWS recommends a client-side rate limiter if you see regular throttling.[^s2]

## Gotchas

- **The two AWS pages disagree on Backup Audit Manager.** The developer guide gives 15 frameworks per account per Region, while the General Reference table lists 10; both say 50 controls and 20 report plans, adjustable. Check the applied value in Service Quotas.[^s1][^s2]
- **Restore-testing failures look like timing problems.** A scheduled restore that cannot start because of the per-type cap fails with a start-window message; AWS's advice is to widen the start window.[^s1]
- **Gateway throughput scales by adding gateways**, not by raising the 4-job limit, which is fixed.[^s1]
- **S3 bucket coverage is no longer capped at 1,000.** Since 2026-09-01 AWS Backup can protect every general purpose bucket up to the account's S3 bucket quota, in all commercial and AWS GovCloud (US) Regions; custom IAM policies may need new permissions.[^s3]

## Connections

This node is the limits side of `kt:aws-backup` (broader); every value is per account and, for most, per Region, hence `scopedTo` `kt:aws-regions`.[^s1][^s2] It is `configuredVia` `kt:service-quotas`, where adjustable values are viewed and raised.[^s2] It is related to and `contrastsWith` `kt:amazon-s3-quotas`: the S3 bucket quota now sets how many buckets AWS Backup can protect, while AWS Backup's own limits govern the jobs.[^s3] It is related to `kt:organizations-quotas` because cross-account backup policies are bounded by both sets of limits, and to `kt:disaster-recovery-strategies`, since copy-job concurrency limits how quickly a backup-and-restore DR design can replicate recovery points between Regions.[^s1]

## Sources
[^s1]: Amazon Web Services, "AWS Backup quotas - AWS Backup". https://docs.aws.amazon.com/aws-backup/latest/devguide/aws-backup-limits.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "AWS Backup endpoints and quotas - AWS General Reference". https://docs.aws.amazon.com/general/latest/gr/bk.html (accessed 2026-11-16)
[^s3]: Amazon Web Services (What's New with AWS), "AWS Backup now supports protecting more than 1,000 Amazon S3 buckets per account", 2026-09-01. https://aws.amazon.com/about-aws/whats-new/2026/09/aws-backup-more-than-1000-s3-buckets/ (accessed 2026-11-16)
