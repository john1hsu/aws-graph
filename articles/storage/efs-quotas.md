---
id: kt:efs-quotas
type: Limit
title: Amazon EFS quotas
aliases: [EFS quotas, EFS limits, EFS service quotas, Amazon Elastic File System quotas, Amazon EFS service limits]
domain: storage
summary: >-
  The limits that constrain Amazon EFS: a few adjustable account and performance quotas (1,000 file
  systems per Region, Elastic throughput and IOPS ceilings), many fixed ones (25,000 connections, one
  mount target per AZ, one VPC per file system), per-NFS-client caps such as 1,500 MiBps and 65,536 open
  files, and file-level limits such as a 47.9 TiB maximum file size.
verified: 2026-11-19
status: current
broader: [kt:amazon-efs]
related: [kt:ebs-quotas, kt:amazon-s3-quotas, kt:amazon-fsx, kt:aws-datasync, kt:amazon-eks]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: configuredVia, object: kt:aws-support-plans}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: contrastsWith, object: kt:ebs-quotas}
sources:
  - id: s1
    title: "Amazon EFS quotas - Amazon Elastic File System"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/efs/latest/ug/limits.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Amazon Elastic File System endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/general/latest/gr/elasticfilesystem.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Amazon EFS performance specifications"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/efs/latest/ug//performance.html
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "Amazon EFS now supports up to 2.5 million IOPS per file system"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2024/11/amazon-efs-up-to-2-5-million-iops-per-file-system
    type: official-blog
    accessed: 2026-11-19
  - id: s5
    title: "Amazon EFS now supports up to 60 GiB/s (a 2x increase) of read throughput"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2024/10/amazon-efs-60-gib-s-2x-increase-read-throughput
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# Amazon EFS quotas

Amazon EFS quotas are the limits AWS places on Elastic File System at five levels: the account (file systems per Region), each file system (throughput, IOPS, connections, mount targets, access points), each NFS client (throughput, open files, locks), the files themselves (size, name length, links, directory depth), and the NFS protocol features EFS supports. Only a handful are adjustable; most are fixed design limits you have to architect around.[^s1][^s2]

## Adjustable quotas

As of 2026-11 the adjustable defaults are 1,000 file systems per account per Region, 10,000 access points per file system, and, for file systems using Elastic throughput, 250,000 read IOPS for frequently accessed data, 90,000 read IOPS for infrequently accessed data and 50,000 write IOPS.[^s1] You can ask for up to ten times the frequently accessed read IOPS default, which raises the other two IOPS figures along with it; AWS introduced this headroom in November 2024, taking the ceiling to 2.5 million read and 500,000 write IOPS for General Purpose file systems on Elastic throughput.[^s1][^s4] Only the file-system count and the frequently accessed read IOPS can be raised in the Service Quotas console; the others (access points, Elastic and Provisioned throughput ceilings) go through an AWS Support service-limit case, which the EFS team reviews one by one.[^s1]

The General Reference table is less current than the EFS User Guide: as of 2026-11 it still marks access points per file system as not adjustable and lists older Provisioned and Bursting throughput figures in MB/s, so treat the User Guide as authoritative and confirm in your own Service Quotas console.[^s2][^s1]

## Throughput ceilings by Region

Aggregate throughput for all clients of one Regional file system depends on throughput mode and Region. With Elastic throughput, eleven large Regions (including us-east-1, us-east-2, us-west-2, eu-west-1, eu-central-1, eu-west-2 and ap-northeast-1) allow 60 GiBps read and 5 GiBps write, and every other Region allows 20 GiBps read and 1 GiBps write.[^s1] The 60 GiBps figure dates from October 2024, when AWS doubled Elastic read throughput in seven Regions; the list of Regions at that tier has since grown.[^s5][^s1] Provisioned throughput tops out at 10 GiBps read and 3.33 GiBps write in us-east-1, us-east-2, us-west-2 and eu-west-1, and 3 GiBps / 1 GiBps elsewhere.[^s1]

Fixed throughput limits apply to the other configurations: One Zone file systems are capped at 3 GiBps read and 1 GiBps write in every Region, and Bursting throughput at 5 GiBps read and 3 GiBps write in five Regions (us-east-1, us-east-2, us-west-2, ap-southeast-2, eu-west-1) and 3 GiBps / 1 GiBps elsewhere.[^s1] Bursting also has a size-based baseline of 50 KiBps per GiB of Standard storage, and changing throughput mode or lowering provisioned throughput locks further changes for 24 hours.[^s3][^s2]

## Fixed resource and client limits

Each file system accepts at most 25,000 connections, one mount target per Availability Zone, five security groups per mount target, 50 tags and a single VPC; each VPC can hold 1,400 EFS mount targets. None of these can be raised.[^s1][^s2] General Purpose mode supports 250,000 file operations per second per file system, where a read costs one operation and a write costs five.[^s2]

A single Linux NFSv4.1 client can push 1,500 MiBps combined read and write when the file system uses Elastic throughput and is mounted with amazon-efs-utils 2.0 or later or the EFS CSI driver; otherwise the per-client limit is 500 MiBps.[^s1][^s3] Each client can have 65,536 open files and 65,536 active users, each mount gets up to 65,536 locks per connection, a user carries at most 16 group IDs (extra GIDs are dropped), and full per-client throughput needs about 1 ms or less of network latency.[^s1]

## File-level limits and protocol gaps

As of 2026-11 a single file can be at most 52,673,613,135,872 bytes (47.9 TiB); names are limited to 255 bytes, symlinks to 4,080 bytes, hard links to 177 per file, directories to 1,000 levels deep, and a single file to 512 locks across all clients; file system policies max out at 20,000 characters.[^s1] EFS speaks only NFSv4.0 and 4.1 and omits several protocol features, among them pNFS, extended attributes, delegations, mandatory locking, NFS ACLs and Kerberos. It also does not support the `nconnect` mount option or Windows clients.[^s1]

## Gotchas

The per-client cap is usually hit long before the file-system ceiling, so high aggregate throughput needs many clients in parallel, and clients mounting without efs-utils 2.0 or the CSI driver silently get a third of the Elastic per-client rate.[^s1][^s3] One VPC per file system and one mount target per AZ mean multi-VPC access goes through peering or a transit network rather than extra mount targets.[^s1] On-premises or cross-Region clients see lower throughput because of latency; AWS points to DataSync for moving large datasets in.[^s1]

## Connections

These quotas are **broader** under Amazon EFS (`kt:amazon-efs`). They are **configured via** Service Quotas (`kt:service-quotas`) for file-system count and read IOPS, and via AWS Support (`kt:aws-support-plans`) for the remaining adjustable limits.[^s1] They are **scoped to** AWS Regions (`kt:aws-regions`), since throughput ceilings vary by Region, and to Availability Zones (`kt:availability-zones`) through the one-mount-target-per-AZ rule.[^s1] They **contrast with** Amazon EBS quotas (`kt:ebs-quotas`), which cap aggregate provisioned capacity per volume type rather than shared throughput and connections.[^s1] Related nodes: Amazon S3 quotas (`kt:amazon-s3-quotas`), Amazon FSx (`kt:amazon-fsx`) for workloads needing Windows/SMB or features EFS omits, AWS DataSync (`kt:aws-datasync`) for bulk loads, and Amazon EKS (`kt:amazon-eks`) whose EFS CSI driver unlocks the higher per-client rate.[^s1]

## Sources

[^s1]: Amazon Web Services, "Amazon EFS quotas - Amazon Elastic File System". https://docs.aws.amazon.com/efs/latest/ug/limits.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Amazon Elastic File System endpoints and quotas" (AWS General Reference). https://docs.aws.amazon.com/en_us/general/latest/gr/elasticfilesystem.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Amazon EFS performance specifications". https://docs.aws.amazon.com/efs/latest/ug//performance.html (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Amazon EFS now supports up to 2.5 million IOPS per file system" (What's New, 2024-11-26). https://aws.amazon.com/about-aws/whats-new/2024/11/amazon-efs-up-to-2-5-million-iops-per-file-system (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Amazon EFS now supports up to 60 GiB/s (a 2x increase) of read throughput" (What's New, 2024-10-15). https://aws.amazon.com/about-aws/whats-new/2024/10/amazon-efs-60-gib-s-2x-increase-read-throughput (accessed 2026-11-19)
