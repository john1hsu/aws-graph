---
id: kt:amazon-efs
type: Service
title: Amazon EFS
aliases: [Amazon Elastic File System, EFS, AWS EFS, Elastic File System]
domain: storage
summary: >-
  AWS's serverless, fully elastic shared file storage: NFSv4 file systems that grow and shrink
  automatically, mount concurrently from EC2, containers, Lambda and on-premises servers, and tier
  data across Standard, Infrequent Access and Archive storage classes.
verified: 2026-11-10
status: current
broader: [kt:aws-storage-services]
related: [kt:amazon-ec2, kt:aws-lambda, kt:aws-datasync, kt:aws-backup, kt:amazon-s3-files]
relations:
  - {predicate: alternativeTo, object: kt:amazon-ebs}
  - {predicate: alternativeTo, object: kt:amazon-fsx}
  - {predicate: integratesWith, object: kt:amazon-ecs}
  - {predicate: integratesWith, object: kt:amazon-eks}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: governedBy, object: kt:efs-quotas}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: scopedTo, object: kt:availability-zones}
sources:
  - id: s1
    title: "What is Amazon Elastic File System? - Amazon Elastic File System"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/efs/latest/ug/whatisefs.html
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Amazon EFS performance specifications - Amazon Elastic File System"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/efs/latest/ug/performance.html
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Amazon EFS quotas - Amazon Elastic File System"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/efs/latest/ug/limits.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Amazon Elastic File System (EFS) FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/efs/faq/
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Amazon EFS Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/efs/pricing/
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "Amazon EFS now supports cross-account Replication in AWS GovCloud (US)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/07/amazon-efs-cross-account-replication-aws-gov-cloud-us/
    type: official-blog
    accessed: 2026-11-10
  - id: s7
    title: "Announcing Amazon S3 Files, making S3 buckets accessible as file systems"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/04/amazon-s3-files/
    type: official-blog
    accessed: 2026-11-10
created: 2026-11-10
---

# Amazon EFS

Amazon Elastic File System (EFS) is serverless, fully elastic shared file storage. You create a file system without provisioning capacity or performance; it grows and shrinks as files are added and removed, scales to petabytes, and AWS runs the underlying infrastructure.[^s1] Clients mount it over NFS version 4 (NFSv4.1 and NFSv4.0), so existing Linux tools work unchanged, with strong consistency, file locking and POSIX permissions.[^s1] EFS is for Linux workloads: mounting from Windows-based EC2 instances is not supported.[^s1]

## How it works

**File system types.** A *Regional* file system (AWS's recommendation) stores data redundantly across several Availability Zones in a Region and stays available if one or more AZs fail. A *One Zone* file system keeps data in a single AZ; if that AZ is damaged, data can be lost.[^s1] AWS states 11 nines of designed annual durability, and turns on EFS backups by default for One Zone file systems.[^s4]

**Access.** Clients connect through VPC mount targets, one per AZ at most.[^s3] EC2, ECS (on EC2 and Fargate), EKS (through the EFS CSI driver) and Lambda can all mount it, as do SageMaker training jobs.[^s1][^s4] On-premises servers can mount over AWS Direct Connect or a VPN using NFSv4.1; AWS suggests parallel transfers or AWS DataSync for bulk copies, given tens-of-milliseconds latency.[^s4]

**Storage classes and lifecycle.** *Standard* is SSD-backed, with first-byte latency as low as about 1 ms for reads and 2.7 ms for writes; *Infrequent Access (IA)* and *Archive* have latencies of tens of milliseconds.[^s2] The default lifecycle policy moves files to IA after 30 days without access and to Archive after 90 days, and Intelligent-Tiering can move a file back to Standard when it is read again. Archive is available only on Regional file systems using Elastic throughput and has a 90-day minimum storage duration, and files under 128 KiB in IA or Archive are billed as 128 KiB.[^s4]

**Performance and throughput modes.** General Purpose is the default performance mode and the one AWS now recommends for every file system; Max I/O is a previous-generation mode unsupported with One Zone or Elastic throughput.[^s2] For throughput, *Elastic* (the default and recommended mode) scales automatically and bills per GB read or written; *Provisioned* fixes throughput independently of size; *Bursting* scales with Standard storage at a baseline of 50 MiBps per TiB and earns credits to burst higher.[^s2] After changing provisioned throughput you must wait 24 hours before lowering it or switching modes.[^s2]

**Security and data protection.** Encryption at rest uses AWS KMS and must be chosen when you create the file system; encryption in transit (TLS) is enabled at mount time with the EFS mount helper.[^s1][^s4] Access is governed by security groups on mount targets, IAM file system policies, EFS access points and POSIX permissions.[^s1][^s4] EFS Replication copies a file system to another Region incrementally, aiming for RPO and RTO measured in minutes, while AWS Backup provides incremental backups.[^s4] Since 2026-07, GovCloud (US) replication can also target another account.[^s6]

## Quotas, pricing and gotchas

Default quotas (as of 2026-11): 1,000 file systems per account per Region and 10,000 access points per file system (both adjustable); 25,000 connections per file system, 1 mount target per AZ, 1 VPC per file system and a 47.9 TiB maximum file size (not adjustable).[^s3] Elastic throughput defaults to 60 GiBps read and 5 GiBps write per Regional file system in large Regions such as us-east-1, and 20 GiBps / 1 GiBps elsewhere; One Zone file systems top out at 3 GiBps read and 1 GiBps write.[^s3] Elastic IOPS defaults to 250,000 frequent-access reads, 90,000 infrequent-access reads and 50,000 writes, adjustable up to ten times.[^s2][^s3] A single client gets up to 1,500 MiBps with efs-utils 2.0+ or the CSI driver, otherwise 500 MiBps.[^s2]

You pay for storage per GB-month by class, plus throughput (per GB with Elastic, per MB/s-month above the baseline with Provisioned), and replication at both ends' rates.[^s5] The FAQ's US East (N. Virginia) examples (as of 2026-11) use $0.30 per GB-month for Standard, $0.0165 for IA and $0.008 for Archive, and Elastic throughput at $0.03 per GB read and $0.06 per GB written; confirm current rates before relying on them.[^s4] Mounting a target in a different AZ adds $0.01/GB cross-AZ transfer, and the Free Tier includes 5 GB of Standard storage per month for 12 months.[^s5]

Gotchas: replicas are not point-in-time consistent, so watch `TimeSinceLastSync`.[^s4] On Bursting, exhausted credits throttle throughput; on Elastic, set alerts on `MeteredIOBytes` to keep costs predictable.[^s2]

## When to use it (and when not)

Choose EFS when many Linux clients must share one POSIX file system: content management, home directories, web serving and ML training data.[^s1][^s4] Use Amazon EBS for the lowest-latency block storage attached to one instance, and Amazon S3 for object storage reached through an API.[^s4] Windows (SMB) shares or specialised file systems point to Amazon FSx. Amazon S3 Files, launched in 2026-04, is built on EFS and exposes S3 buckets as file systems when the data should stay in S3.[^s7]

## Connections

- **Broader:** one of AWS's storage services (`kt:aws-storage-services`).
- **Alternative to** `kt:amazon-ebs` (single-instance block storage) and `kt:amazon-fsx` (other file system families).[^s4]
- **Integrates with** `kt:amazon-ecs` and `kt:amazon-eks`; also mounted by `kt:amazon-ec2` and `kt:aws-lambda`.[^s4]
- **Related:** `kt:aws-datasync` (migration), `kt:aws-backup` (backups) and `kt:amazon-s3-files` (built on EFS).[^s4][^s7]
- **Secured by** `kt:aws-kms`; **monitored by** `kt:amazon-cloudwatch` metrics such as `PercentIOLimit`.[^s1][^s2]
- **Governed by** `kt:efs-quotas`; **scoped to** `kt:availability-zones` via Regional versus One Zone placement and per-AZ mount targets.[^s1][^s3]

## Sources
[^s1]: Amazon Web Services, "What is Amazon Elastic File System? - Amazon Elastic File System". https://docs.aws.amazon.com/efs/latest/ug/whatisefs.html (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Amazon EFS performance specifications - Amazon Elastic File System". https://docs.aws.amazon.com/efs/latest/ug/performance.html (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Amazon EFS quotas - Amazon Elastic File System". https://docs.aws.amazon.com/efs/latest/ug/limits.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Amazon Elastic File System (EFS) FAQs". https://aws.amazon.com/efs/faq/ (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Amazon EFS Pricing". https://aws.amazon.com/efs/pricing/ (accessed 2026-11-10)
[^s6]: Amazon Web Services (What's New with AWS), "Amazon EFS now supports cross-account Replication in AWS GovCloud (US)", 2026-07-29. https://aws.amazon.com/about-aws/whats-new/2026/07/amazon-efs-cross-account-replication-aws-gov-cloud-us/ (accessed 2026-11-10)
[^s7]: Amazon Web Services (What's New with AWS), "Announcing Amazon S3 Files, making S3 buckets accessible as file systems", 2026-04-07. https://aws.amazon.com/about-aws/whats-new/2026/04/amazon-s3-files/ (accessed 2026-11-10)
