---
id: kt:ebs-quotas
type: Limit
title: Amazon EBS quotas
aliases: [EBS quotas, EBS service quotas, EBS limits, Amazon Elastic Block Store quotas, Amazon EBS service limits]
domain: storage
summary: >-
  The Regional limits that constrain Amazon EBS: adjustable aggregate storage (TiB) and IOPS per volume
  type, fixed concurrent-snapshot and EBS direct API request rates, snapshot and fast snapshot restore
  counts, and per-volume size ceilings such as 64 TiB for gp3 and io2 Block Express.
verified: 2026-11-17
status: current
broader: [kt:amazon-ebs]
related: [kt:ebs-snapshots, kt:amazon-ec2, kt:amazon-s3-quotas, kt:aws-backup-quotas, kt:amazon-emr]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:amazon-s3-quotas}
sources:
  - id: s1
    title: "Amazon Elastic Block Store endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/ebs-service.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Quotas for Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/ebs-resource-quotas.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Amazon EBS volume constraints"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/volume_constraints.md
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "Amazon EBS increases the maximum size and provisioned performance of General Purpose (gp3) volumes"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/09/amazon-ebs-size-provisioned-performance-gp3-volumes
    type: official-blog
    accessed: 2026-11-17
  - id: s5
    title: "Improve your application resiliency with larger and faster gp3 volumes"
    publisher: Amazon Web Services (AWS Storage Blog)
    url: https://aws.amazon.com/blogs/storage/improve-your-application-resiliency-with-larger-and-faster-gp3-volumes
    type: official-blog
    accessed: 2026-11-17
created: 2026-11-17
---

# Amazon EBS quotas

Amazon EBS quotas are the default limits AWS applies to block storage in each account. Almost all of them are per Region, some can be raised and some are fixed, and they fall into a few families: how much storage and provisioned IOPS you can hold per volume type, how many snapshots you can keep and run at once, how fast you can call the EBS direct APIs, and how large any single volume can be.[^s2][^s1] EBS also watches usage in each Region and may raise some quotas on its own as you grow, although AWS still recommends requesting increases ahead of a planned deployment such as a large gp3 rollout.[^s2]

## Storage and IOPS quotas (adjustable)

The largest family caps the **aggregate** capacity provisioned across all volumes of one type in a Region. As of 2026-11, the default is 50 TiB per type for gp2, io1, st1, sc1 and magnetic (standard) volumes in sixteen long-established Regions such as us-east-1, eu-west-1 and ap-northeast-1, and 300 TiB in the other supported Regions. gp3 is the reverse: 300 TiB in fifteen newer Regions (for example af-south-1, eu-south-1 and il-central-1) and 50 TiB everywhere else, including us-east-1. io2 defaults to 20 TiB in every Region.[^s1] Provisioned IOPS are capped separately at 300,000 aggregate IOPS for io1 and 100,000 for io2 per Region.[^s1] All of these are adjustable through Service Quotas.[^s2]

A second set limits **modifications in flight** (Elastic Volumes changes): 500 TiB of volumes being resized at once for most types, 20 TiB for io2, and 500,000 io1 or 100,000 io2 IOPS. These count the combined *current* size or IOPS of the volumes being changed concurrently, not the number of requests, so a few large volumes can exhaust the quota while smaller ones would not.[^s2][^s1]

## Snapshot quotas

As of 2026-11, an account may keep 100,000 snapshots per Region, enable fast snapshot restore on 5 snapshots per Region, hold 25 archived snapshots per volume, and run 25 snapshot archives and 5 restores from archive at a time; all of these are adjustable.[^s1][^s2] Fixed limits are the number of snapshots a single volume can take concurrently (5 for gp2, gp3, io1, io2 and standard volumes; 1 for st1 and sc1) and 20 concurrent snapshot copies to one destination Region. That copy quota is not adjustable in Service Quotas, so increases go through AWS Support.[^s2] Time-based snapshot copies have an adjustable throughput ceiling of 2,000 MiB/s per destination Region.[^s1]

## EBS direct API throttles

Backup and replication tools that read or write snapshot blocks directly hit per-second API quotas. GetSnapshotBlock and PutSnapshotBlock allow 5,000 requests per second per account in us-east-1, us-east-2, us-west-2, ap-southeast-1 and eu-west-1 and 1,000 in other Regions (adjustable), but only 1,000 per second per snapshot (fixed). ListChangedBlocks and ListSnapshotBlocks are fixed at 50 per second, StartSnapshot and CompleteSnapshot at 10 per second, and an account can have at most 100 pending StartSnapshot snapshots.[^s1][^s2]

## Per-volume and per-launch limits

EBS supports volumes up to 64 TiB. Since 2025-09-26 that includes gp3, whose ceilings rose from 16 TiB, 16,000 IOPS and 1,000 MiB/s to 64 TiB, 80,000 IOPS and 2,000 MiB/s in all commercial Regions and AWS GovCloud (US), with prices unchanged.[^s4][^s3] Whether the operating system can use that space is a separate limit: MBR partitioning tops out at 2 TiB, so larger volumes need GPT, and boot volumes beyond 2 TiB need GPT with GRUB2 or UEFI boot.[^s3] A single instance launch request is also capped at 2,500 volumes in us-east-1, us-west-2, eu-west-1 and ap-northeast-1 and 500 elsewhere; this limit is fixed and applies to services such as Amazon EMR that launch on your behalf.[^s2]

## Gotchas

Per-volume limits grew faster than the account defaults: one maximum-size 64 TiB gp3 volume is larger than the 50 TiB default gp3 quota in us-east-1, so check the Regional quota before provisioning large volumes.[^s4][^s1] AWS's storage team notes that the larger gp3 sizes let many workloads drop RAID-0 striping, which reduces management overhead and the blast radius of a single volume failure, but growing a volume still requires extending the file system inside the OS.[^s5]

## Connections

These quotas are **broader** under Amazon EBS (`kt:amazon-ebs`) and are **configured via** Service Quotas (`kt:service-quotas`), except the snapshot-copy limit that needs AWS Support.[^s2] They are **scoped to** AWS Regions (`kt:aws-regions`).[^s1] Snapshot limits shape EBS snapshots (`kt:ebs-snapshots`) and backup plans whose own limits are in AWS Backup quotas (`kt:aws-backup-quotas`).[^s2] The launch-request cap relates to Amazon EC2 (`kt:amazon-ec2`) and Amazon EMR (`kt:amazon-emr`).[^s2] They **contrast with** Amazon S3 quotas (`kt:amazon-s3-quotas`): S3 caps object count and request rates per prefix, while EBS caps provisioned capacity and IOPS per volume type.[^s1]

## Sources

[^s1]: Amazon Web Services, "Amazon Elastic Block Store endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/ebs-service.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Quotas for Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/ebs-resource-quotas.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Amazon EBS volume constraints". https://docs.aws.amazon.com/ebs/latest/userguide/volume_constraints.md (accessed 2026-11-17)
[^s4]: Amazon Web Services, "Amazon EBS increases the maximum size and provisioned performance of General Purpose (gp3) volumes" (What's New, 2025-09-26). https://aws.amazon.com/about-aws/whats-new/2025/09/amazon-ebs-size-provisioned-performance-gp3-volumes (accessed 2026-11-17)
[^s5]: K. Davydychev and T. Luong, "Improve your application resiliency with larger and faster gp3 volumes", AWS Storage Blog (2026-02-03). https://aws.amazon.com/blogs/storage/improve-your-application-resiliency-with-larger-and-faster-gp3-volumes (accessed 2026-11-17)
