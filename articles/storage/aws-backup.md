---
id: kt:aws-backup
type: Service
title: AWS Backup
aliases: [Backup, AWS Backup service]
domain: storage
summary: >-
  AWS's fully managed, policy-based backup service: backup plans schedule and retain recovery points
  for EBS, EC2, S3, EFS, FSx, RDS, Aurora, DynamoDB and other resources in encrypted backup vaults, with
  cross-Region and cross-account copies, Vault Lock immutability and organization-wide backup policies.
verified: 2026-11-11
status: current
broader: [kt:aws-storage-services]
related: [kt:amazon-ebs, kt:amazon-efs, kt:amazon-s3, kt:disaster-recovery-strategies, kt:amazon-eventbridge]
relations:
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: governedBy, object: kt:aws-backup-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is AWS Backup? - AWS Backup"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "AWS Backup quotas - AWS Backup"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-backup/latest/devguide/aws-backup-limits.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "AWS Backup Vault Lock - AWS Backup"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-backup/latest/devguide/vault-lock.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Logically air-gapped vault - AWS Backup"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-backup/latest/devguide/logicallyairgappedvault.html
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "AWS Backup FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/backup/faqs/
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "AWS Backup pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/backup/pricing
    type: official-docs
    accessed: 2026-11-11
  - id: s7
    title: "AWS Backup now supports protecting more than 1,000 Amazon S3 buckets per account"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/aws-backup-more-than-1000-s3-buckets/
    type: official-blog
    accessed: 2026-11-11
  - id: s8
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-11
  - id: s9
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# AWS Backup

AWS Backup is a fully managed service that centralizes and automates data protection across AWS services, in the cloud and on premises. Instead of scheduling snapshots service by service or maintaining custom scripts, you define backup policies once and monitor every job from one console.[^s1] Supported resources include EC2 instances, EBS volumes, S3 buckets, EFS, all four FSx file systems, RDS and Aurora (including Aurora DSQL), DynamoDB, DocumentDB, Neptune, Redshift, Timestream for LiveAnalytics, Storage Gateway volumes, VMware Cloud on AWS, CloudFormation stacks, SAP HANA on EC2 and EKS clusters; not every resource type supports every feature, such as incremental backups or cold storage.[^s1]

## How it works

**Backup plans and assignments.** A backup plan is a policy of rules: schedule, backup window, lifecycle and copy actions. Resources join a plan through resource assignments, commonly by tag, so anything tagged later is protected automatically.[^s1] Each run produces a backup (a recovery point). For supported types the first backup is full and later ones store only changes; types without incremental support are stored as full copies, which raises storage cost.[^s1] Lifecycle rules can move backups from warm storage to a cheaper cold tier on a schedule you set.[^s1] For S3, continuous backups allow restore to any point within the last 35 days, while periodic backups can run as often as hourly.[^s5]

**Vaults.** Backups live in backup vaults, separate from their sources, so an EBS or EC2 backup is retained per its lifecycle even after the source instance and volumes are deleted.[^s1] Vaults are encrypted with AWS KMS keys and carry resource-based access policies.[^s1] For eligible resource types under full AWS Backup management, backups take the vault's KMS key and their own backup ARNs, which lets you write access policies independent of the source service.[^s1]

**Copies across Regions and accounts.** Plans can copy backups to other Regions and, through AWS Organizations, to other accounts, for example fanning in to a central backup account.[^s1] Organizations backup policies push plans to every member account, and a delegated administrator can manage them without management-account access.[^s1][^s5]

**Immutability.** Vault Lock enforces write-once-read-many retention. *Governance mode* can be removed by suitably authorized IAM users; *compliance mode* has a grace time of 3 to 36,500 days, after which neither users nor AWS can change or delete the lock or the protected backups before retention ends.[^s3] Vault Lock has been assessed by Cohasset Associates for SEC 17a-4, CFTC and FINRA environments.[^s3] A *logically air-gapped vault* is always locked in compliance mode, uses an AWS-owned key by default, and can be shared to specific accounts through AWS RAM so they can restore even if the owning account is compromised; minimum retention is 7 days and the encryption key cannot be changed after creation.[^s4]

**Audit and testing.** Backup Audit Manager evaluates resources against controls and reports each as compliant or non-compliant, rolling up into framework-level compliance and cross-account reports.[^s1][^s5] Restore testing plans restore selected recovery points on a schedule and clean up the test resources after a retention period.[^s5]

## Quotas, pricing and gotchas

Default quotas (as of 2026-11, per account per Region): 300 backup vaults and 300 backup plans (adjustable); 10 rules per plan, 100 resource assignments per plan, 5 copy actions per rule and 50 legal holds per account (not adjustable); 1,000,000 recovery points per vault (adjustable).[^s2] Only one backup or copy job runs at a time per resource, and concurrent cross-Region copy jobs into a destination Region are capped at 100 per account.[^s2] Since 2026-09, AWS Backup can protect more than 1,000 S3 buckets per account, up to the account's bucket quota, in commercial and GovCloud (US) Regions.[^s7]

You pay per GB-month of backup storage, plus restores, cross-Region transfer and restore-testing evaluations.[^s6] AWS's US East (N. Virginia) pricing examples (as of 2026-11) use $0.05 per GB-month of warm storage for EBS snapshots, EFS and S3 backups, $0.02 per GB restored for EFS and S3, and $1.50 per recovery point evaluated by restore testing; check the live rate table for your Region and resource type.[^s6]

Gotchas: compliance-mode locks are irreversible after grace time, so test retention settings in governance mode first.[^s3] Logically air-gapped vaults reject copies whose retention falls outside the vault's minimum and maximum range.[^s4] VMware backups moved to cold storage carry a 90-day minimum with prorated early-deletion charges.[^s5]

## When to use it (and when not)

Use AWS Backup when you need one policy, audit trail and retention model across many services and accounts, especially for compliance-driven retention or ransomware recovery with locked, cross-account copies.[^s1][^s4] It implements the backup-and-restore tier of disaster recovery; tighter RPO/RTO targets need continuous replication on top. Native features such as EFS replication or S3 versioning remain useful but do not replace point-in-time, isolated backups.[^s1][^s5]

## Connections

- **Broader:** one of AWS's storage services (`kt:aws-storage-services`); **related** to the resources it protects most often, `kt:amazon-ebs`, `kt:amazon-efs` and `kt:amazon-s3`, and to `kt:disaster-recovery-strategies`.[^s1]
- **Integrates with** `kt:aws-organizations` for backup policies and cross-account copies; **secured by** `kt:aws-kms` vault encryption.[^s1]
- **Monitored by** `kt:amazon-cloudwatch` and `kt:aws-cloudtrail`, with job events sent to `kt:amazon-eventbridge` and Amazon SNS.[^s1]
- **Governed by** `kt:aws-backup-quotas`; **scoped to** `kt:aws-regions`, since vaults and plans are Regional.[^s2]
- **Complies with** `kt:fedramp-on-aws`: AWS lists it in the East/West and GovCloud FedRAMP boundaries as of 2026-11.[^s8]
- **Covered by** `kt:aws-certified-solutions-architect-associate`, whose SAA-C03 guide lists AWS Backup as an in-scope storage service.[^s9]

## Sources
[^s1]: Amazon Web Services, "What is AWS Backup? - AWS Backup". https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "AWS Backup quotas - AWS Backup". https://docs.aws.amazon.com/aws-backup/latest/devguide/aws-backup-limits.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "AWS Backup Vault Lock - AWS Backup". https://docs.aws.amazon.com/aws-backup/latest/devguide/vault-lock.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Logically air-gapped vault - AWS Backup". https://docs.aws.amazon.com/aws-backup/latest/devguide/logicallyairgappedvault.html (accessed 2026-11-11)
[^s5]: Amazon Web Services, "AWS Backup FAQs". https://aws.amazon.com/backup/faqs/ (accessed 2026-11-11)
[^s6]: Amazon Web Services, "AWS Backup pricing". https://aws.amazon.com/backup/pricing (accessed 2026-11-11)
[^s7]: Amazon Web Services (What's New with AWS), "AWS Backup now supports protecting more than 1,000 Amazon S3 buckets per account", 2026-09-01. https://aws.amazon.com/about-aws/whats-new/2026/09/aws-backup-more-than-1000-s3-buckets/ (accessed 2026-11-11)
[^s8]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-11)
[^s9]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-11)
