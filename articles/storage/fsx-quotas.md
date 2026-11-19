---
id: kt:fsx-quotas
type: Limit
title: Amazon FSx quotas
aliases: [FSx quotas, FSx limits, FSx service quotas, Amazon FSx service limits, Amazon FSx endpoints and quotas]
domain: storage
summary: >-
  The limits that constrain Amazon FSx, set separately for each file system type: adjustable per-account,
  per-Region defaults (typically 100 file systems per type and 10,240 MBps of aggregate throughput) and
  fixed per-file-system ceilings such as 64 TiB for FSx for Windows File Server, 512 TiB for FSx for
  OpenZFS and up to 1 PiB for a scale-out FSx for NetApp ONTAP file system.
verified: 2026-11-19
status: current
broader: [kt:amazon-fsx]
related: [kt:efs-quotas, kt:ebs-quotas, kt:aws-backup-quotas, kt:kms-quotas]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: configuredVia, object: kt:aws-support-plans}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:efs-quotas}
sources:
  - id: s1
    title: "Amazon FSx endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/fsxn.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Service quotas for Amazon FSx for Lustre"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fsx/latest/LustreGuide/limits.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Quotas - FSx for ONTAP"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/limits.html
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "Amazon FSx for OpenZFS service quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fsx/latest/OpenZFSGuide/limits.html
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Quotas - Amazon FSx for Windows File Server"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fsx/latest/WindowsGuide/limits.html
    type: official-docs
    accessed: 2026-11-19
  - id: s6
    title: "Amazon FSx for Windows File Server increases maximum throughput to 12 GB/s"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2023/08/amazon-fsx-windows-file-server-increases-throughput-12-gbs
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# Amazon FSx quotas

Amazon FSx quotas are the limits AWS places on its four managed file system types (Lustre, NetApp ONTAP, OpenZFS and Windows File Server) plus Amazon File Cache. They come in two layers: account quotas, which apply per AWS account per Region, are all adjustable and cap the count and aggregate capacity of each file system type; and per-file-system resource quotas, which are mostly fixed design ceilings on a single file system's size, throughput, IOPS, volumes or snapshots. Storage figures are in GiB and throughput in MBps.[^s1]

## Account quotas (adjustable)

As of 2026-11, every type defaults to 100 file systems per Region, and the Windows, ONTAP and OpenZFS types each default to 10,240 MBps of total throughput capacity.[^s1] Type-specific defaults differ:

- **Lustre**: separate pools for Scratch, Persistent_1 and Persistent_2, each with 100 file systems and 100,800 GiB of storage; 500 user-initiated backups; Intelligent-Tiering file systems get 100,000 MBps of throughput and 100,800 GiB of SSD read cache. A per-file-system Persistent HDD capacity quota defaults to 102,000 GiB. Amazon File Cache (Lustre Cache_1) has its own 100-cache and 100,800 GiB quotas.[^s1][^s2]
- **ONTAP**: 10,000 backups, plus aggregate SSD storage and SSD IOPS quotas (see the gotcha below on conflicting defaults).[^s1][^s3]
- **OpenZFS**: 65,536 GiB of SSD storage and the same amount of Intelligent-Tiering read cache, raised to 262,144 GiB in us-east-1, us-east-2 and us-west-2; 400,000 disk IOPS; 10,000 backups.[^s1][^s4]
- **Windows**: 524,288 GiB each of SSD and HDD storage, 500,000 total SSD IOPS and 500 backups.[^s1][^s5]

Lustre, ONTAP and Windows account quotas are raised in the Service Quotas console.[^s2][^s5] OpenZFS also accepts AWS Support cases, which is the only route for raising its adjustable per-file-system quotas: volumes, snapshots, user and group quotas per volume, and Intelligent-Tiering capacity.[^s4]

## Per-file-system ceilings

**FSx for Windows File Server**: SSD file systems start at 32 GiB and HDD at 2,000 GiB, both top out at 64 TiB; throughput runs from 8 to 12,288 MBps, SSD IOPS from 96 to 400,000, and one file system holds up to 100,000 file shares.[^s5] The 12 GB/s ceiling arrived in August 2023, up from 2 GB/s, initially in six Regions.[^s6]

**FSx for NetApp ONTAP**: a second-generation Single-AZ file system scales to 12 HA pairs at 512 TiB of SSD each, capped at 1 PiB, and up to 73,728 MBps of throughput; second-generation Multi-AZ caps at 512 TiB and 6,144 MBps, and first-generation file systems at 192 TiB. SSD IOPS max out at 200,000 per HA pair. Volumes are capped at 500 (one HA pair) or 1,000 (two or more), snapshots at 1,023 per volume, backups at 4,091 per volume and client connections at 100,000 per file server. The SVM limit, from 6 to 24, depends on generation, throughput tier and network type.[^s3]

**FSx for OpenZFS**: 64 GiB to 512 TiB and 64 to 10,240 MBps, with 100 volumes, 700 snapshots, 50 tags and 32,768 client connections per file server. For Single-AZ 1 and Multi-AZ deployments, the maximum storage and the number of access points both scale with provisioned throughput and Region group. In some Regions capacity stops at 128 TiB whatever throughput you buy.[^s4]

**FSx for Lustre**: SSD file systems start at 1,200 GiB and HDD at 6,000 GiB. Persistent_2 tops out at 1,200,000 GiB, Persistent_1 at 604,800 GiB (SSD) or 1,002,000 GiB (HDD). Per-TiB throughput is 50–1,000 MBps on SSD and 12–40 MBps on HDD, and a linked S3 bucket can push at most 10 million file updates a month into a file system.[^s2]

Across types, file systems carry 50 tags and keep automated backups for at most 90 days. Lustre and Windows limit each account to 5 in-progress backup copy requests per destination Region and let one AWS KMS key encrypt at most 125 file systems.[^s2][^s5]

## Gotchas

Per-file-system ceilings can exceed the account defaults. A single Windows file system at 12,288 MBps, or a 12-pair ONTAP file system at 73,728 MBps, needs more than the 10,240 MBps aggregate throughput quota, so request the increase before you size up.[^s1][^s3][^s5] As of 2026-11 the two ONTAP pages disagree: the General Reference lists 1,048,576 GiB of SSD storage and 2,000,000 SSD IOPS as defaults, while the ONTAP User Guide still shows 524,288 GiB and 1,000,000. Check your own Service Quotas console.[^s1][^s3] Every Lustre deployment type draws on its own capacity pool, so Scratch usage doesn't eat into Persistent_2 headroom.[^s1] On ONTAP, attaching S3 access points lowers the volume limit (to 491 on one HA pair). Routes are capped at 50 per file system, computed as (1 + SVMs) × associated route tables, which limits SVM growth in VPCs with many route tables.[^s3]

## Connections

These quotas are **broader** under Amazon FSx (`kt:amazon-fsx`) and **scoped to** AWS Regions (`kt:aws-regions`), since account defaults apply per Region and some (OpenZFS storage, ONTAP first-generation throughput) vary by Region.[^s1][^s3][^s4] They are **configured via** Service Quotas (`kt:service-quotas`) for most account quotas and via AWS Support (`kt:aws-support-plans`) for OpenZFS per-file-system increases.[^s2][^s4] They **contrast with** Amazon EFS quotas (`kt:efs-quotas`): EFS caps shared elastic throughput and connections, while FSx caps provisioned capacity and throughput that you size up front.[^s1] Related nodes: Amazon EBS quotas (`kt:ebs-quotas`), AWS Backup quotas (`kt:aws-backup-quotas`), which govern the backup copies FSx relies on, and AWS KMS quotas (`kt:kms-quotas`), relevant to the 125-file-systems-per-key limit.[^s2][^s5]

## Sources

[^s1]: Amazon Web Services, "Amazon FSx endpoints and quotas" (AWS General Reference). https://docs.aws.amazon.com/general/latest/gr/fsxn.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Service quotas for Amazon FSx for Lustre". https://docs.aws.amazon.com/fsx/latest/LustreGuide/limits.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Quotas - FSx for ONTAP". https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/limits.html (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Amazon FSx for OpenZFS service quotas". https://docs.aws.amazon.com/fsx/latest/OpenZFSGuide/limits.html (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Quotas - Amazon FSx for Windows File Server". https://docs.aws.amazon.com/fsx/latest/WindowsGuide/limits.html (accessed 2026-11-19)
[^s6]: Amazon Web Services, "Amazon FSx for Windows File Server increases maximum throughput to 12 GB/s" (What's New, 2023-08-09). https://aws.amazon.com/about-aws/whats-new/2023/08/amazon-fsx-windows-file-server-increases-throughput-12-gbs (accessed 2026-11-19)
