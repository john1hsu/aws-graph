---
id: kt:amazon-s3-files
type: Feature
title: Amazon S3 Files
aliases: [S3 Files, S3 file system]
domain: storage
summary: >-
  An Amazon S3 capability, generally available since 2026-04, that exposes a general purpose S3
  bucket (or prefix) as a shared NFS v4.1/4.2 file system for EC2, Lambda, ECS and EKS. Built on
  Amazon EFS, it caches the active working set on low-latency storage and keeps the file system
  and bucket synchronized in both directions, so the data never leaves S3.
verified: 2026-11-14
status: current
broader: [kt:amazon-s3, kt:aws-storage-services]
related: [kt:amazon-ec2, kt:aws-lambda, kt:amazon-vpc]
relations:
  - {predicate: dependsOn, object: kt:amazon-efs}
  - {predicate: alternativeTo, object: kt:aws-storage-gateway}
  - {predicate: alternativeTo, object: kt:amazon-fsx}
  - {predicate: integratesWith, object: kt:amazon-eks}
  - {predicate: integratesWith, object: kt:amazon-ecs}
  - {predicate: securedBy, object: kt:aws-kms}
sources:
  - id: s1
    title: "Working with Amazon S3 Files - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-files.md
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "Unsupported features, limits, and quotas - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-files-quotas.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Prerequisites for S3 Files - Amazon Simple Storage Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/it_it/AmazonS3/latest/userguide/s3-files-prereq-policies.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Announcing Amazon S3 Files, making S3 buckets accessible as file systems"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/04/amazon-s3-files
    type: official-blog
    accessed: 2026-11-14
  - id: s5
    title: "Launching S3 Files, making S3 buckets accessible as file systems"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/launching-s3-files-making-s3-buckets-accessible-as-file-systems
    type: official-blog
    accessed: 2026-11-14
created: 2026-11-14
---

# Amazon S3 Files

Amazon S3 Files is a shared file system layered directly on an S3 bucket. You link a file system to a bucket, or to a prefix inside one, mount it on AWS compute, and then read, write and organize the bucket's objects with ordinary file and directory operations while the authoritative copy stays in S3.[^s1] AWS announced it on 2026-04-07 as generally available in 34 Regions; the launch post describes it as available in all commercial Regions.[^s4][^s5] It is built using Amazon EFS, and AWS positions it as a way to give file-based applications, ML pipelines and AI agents access to S3 data without migrating or duplicating it.[^s1][^s5]

## How it works

When first mounted, S3 Files presents a browsable view of the bucket's objects as files. As clients open directories and files, metadata and contents are pulled onto the file system's **high-performance storage** on demand, rather than copying the whole dataset.[^s1] Small files (under 128 KiB by default, configurable) are imported asynchronously for low-latency repeat reads, while reads of data not on the file system and large reads of 1 MiB or more are streamed straight from the bucket. Data not read within a configurable window of 1 to 365 days (default 30) expires from the high-performance tier.[^s1] AWS quotes sub-millisecond to single-digit millisecond latency for hot small files and up to terabytes per second of aggregate throughput for streamed reads.[^s1][^s4]

Writes land on the high-performance storage and are exported back to S3 as new objects or object versions, typically within minutes; changes made directly in the bucket show up in the file system usually within seconds, occasionally a minute or more.[^s5] The file system speaks NFS 4.1 and 4.2 and provides read-after-write consistency, advisory file locking and POSIX permissions; the launch post describes close-to-open consistency for concurrent clients.[^s1][^s5] Data on the high-performance tier is stored redundantly across multiple Availability Zones, matching S3's durability.[^s1]

Clients reach a file system through **mount targets** in one VPC (at most one per Availability Zone) and can use **access points** to enforce a user identity and root directory per application. Supported compute is Amazon EC2, AWS Lambda, Amazon ECS and Amazon EKS (including Fargate); EC2 hosts mount with `amazon-efs-utils` 3.0.0 or later.[^s1][^s3][^s5]

## Prerequisites, quotas and gotchas

The bucket must be a **general purpose** bucket in the same Region with **S3 Versioning enabled**, encrypted with SSE-S3 or SSE-KMS. You supply an IAM role that the service assumes to read and write the bucket and to manage the EventBridge rules that drive synchronization; compute roles need mount and client permissions plus direct S3 read access. Mount target and client security groups must allow NFS on TCP 2049.[^s3] Data is encrypted in transit with TLS and at rest with AWS KMS keys (AWS owned by default, or customer managed).[^s1]

Quotas as of 2026-11 (account- and file-system-level, not Region-specific in the docs): 1,000 file systems per account (adjustable through AWS Support); not adjustable: 25,000 access points and 25,000 connections per file system, one mount target per AZ, five security groups per mount target, 50 tags and one VPC per file system. Per-file limits include a 48 TiB maximum file size, 1,000 directory levels, 255-byte names and a 1,024-byte full path.[^s2]

Gotchas: objects in Glacier Flexible Retrieval, Glacier Deep Archive or the Intelligent-Tiering archive tiers are unreadable until restored via the S3 API; S3 ACLs are not preserved after file-system changes; hard links are unsupported; keys with empty or relative path components (`a//b`, `a/../b`) are not representable; and pNFS, Kerberos, NFS ACLs, mandatory locking and the `nconnect` mount option are not supported.[^s2]

Billing has three parts: a storage rate for data resident on the high-performance tier, access charges for writes and small-file reads on that tier, and charges for synchronization (imports bill as writes, exports as reads). Large reads streamed from S3 carry only normal S3 GET request costs. Rates are on the Amazon S3 pricing page.[^s1][^s5]

## When to use it (and when not)

Use S3 Files when data already lives in, or should live in, S3 but applications, notebooks, agents or training jobs expect a POSIX file system, and you want both file and S3 API access to the same data at once.[^s4][^s5] AWS steers on-premises NAS migrations and feature-specific file systems (NetApp ONTAP, OpenZFS, Windows File Server) to Amazon FSx, and HPC or GPU-cluster scratch storage to FSx for Lustre.[^s5] For a general shared Linux file system that is not tied to a bucket, Amazon EFS remains the fit; for on-premises clients needing S3-backed shares, AWS Storage Gateway's S3 File Gateway runs in your data center.[^s1][^s5]

## Connections

- **Broader:** `kt:amazon-s3`, since S3 Files is documented and priced as part of S3, and `kt:aws-storage-services`.[^s1]
- **dependsOn `kt:amazon-efs`:** the file system is built on EFS, and synchronization runs under the EFS service principal.[^s1][^s3]
- **alternativeTo `kt:amazon-fsx` and `kt:aws-storage-gateway`:** other ways to put file semantics in front of data, for NAS migrations and on-premises access respectively.[^s5]
- **integratesWith `kt:amazon-ecs`, `kt:amazon-eks`; related `kt:amazon-ec2`, `kt:aws-lambda`:** the supported mounting compute.[^s1]
- **Related `kt:amazon-vpc`; securedBy `kt:aws-kms`:** mount targets live in one VPC, and data at rest is KMS-encrypted.[^s1][^s2]

## Sources
[^s1]: Amazon Web Services, "Working with Amazon S3 Files - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-files.md (accessed 2026-11-14)
[^s2]: Amazon Web Services, "Unsupported features, limits, and quotas - Amazon Simple Storage Service". https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-files-quotas.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, "Prerequisites for S3 Files - Amazon Simple Storage Service". https://docs.aws.amazon.com/it_it/AmazonS3/latest/userguide/s3-files-prereq-policies.html (accessed 2026-11-14)
[^s4]: Amazon Web Services (What's New with AWS), "Announcing Amazon S3 Files, making S3 buckets accessible as file systems", 2026-04-07. https://aws.amazon.com/about-aws/whats-new/2026/04/amazon-s3-files (accessed 2026-11-14)
[^s5]: Amazon Web Services (AWS News Blog), "Launching S3 Files, making S3 buckets accessible as file systems", 2026-04-07. https://aws.amazon.com/blogs/aws/launching-s3-files-making-s3-buckets-accessible-as-file-systems (accessed 2026-11-14)
