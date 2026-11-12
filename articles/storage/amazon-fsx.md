---
id: kt:amazon-fsx
type: Service
title: Amazon FSx
aliases: [FSx, Amazon FSx family, AWS FSx]
domain: storage
summary: >-
  AWS's family of fully managed file systems built on established file system software — FSx for
  NetApp ONTAP, FSx for OpenZFS, FSx for Windows File Server and FSx for Lustre — for lifting existing
  NAS, Windows and Linux file workloads to AWS or running high-performance parallel file storage.
verified: 2026-11-12
status: current
broader: [kt:aws-storage-services]
related: [kt:amazon-s3, kt:aws-datasync, kt:amazon-ec2]
relations:
  - {predicate: alternativeTo, object: kt:amazon-efs}
  - {predicate: integratesWith, object: kt:amazon-sagemaker-ai}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: governedBy, object: kt:fsx-quotas}
  - {predicate: scopedTo, object: kt:availability-zones}
sources:
  - id: s1
    title: "Choosing an Amazon FSx File System"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/fsx/when-to-choose-fsx/
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "What is Amazon FSx for Lustre? - FSx for Lustre"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fsx/latest/LustreGuide/what-is.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "What is FSx for Windows File Server? - Amazon FSx for Windows File Server"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fsx/latest/WindowsGuide/what-is.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "What is Amazon FSx for NetApp ONTAP? - FSx for ONTAP"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/what-is-fsx-ontap.md
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "What is Amazon FSx for OpenZFS? - FSx for OpenZFS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/es_es/fsx/latest/OpenZFSGuide/what-is-fsx.md
    type: official-docs
    accessed: 2026-11-12
  - id: s6
    title: "Quotas - Amazon FSx for Windows File Server"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fsx/latest/WindowsGuide/limits.html
    type: official-docs
    accessed: 2026-11-12
  - id: s7
    title: "Amazon FSx for NetApp ONTAP now supports Amazon S3 access"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/amazon-fsx-netapp-ontap-s3-access
    type: official-blog
    accessed: 2026-11-12
created: 2026-11-12
---

# Amazon FSx

Amazon FSx is not one file system but a family of four fully managed ones, each built on a well-known storage technology: NetApp ONTAP, OpenZFS, Windows File Server and Lustre. AWS's own selection guidance is to pick the member that matches what you run today — ONTAP or other NAS appliances map to FSx for NetApp ONTAP, ZFS or Linux file servers to FSx for OpenZFS, Windows Server to FSx for Windows File Server, and Lustre or other parallel file systems to FSx for Lustre.[^s1] AWS handles provisioning, patching, hardware failure detection and daily backups for all of them.[^s3][^s4]

## How it works

Each file system lives in your VPC and is reached over standard file protocols. FSx for NetApp ONTAP is the multi-protocol option, serving NFS, SMB and iSCSI block storage (its user guide also lists NVMe), with SnapMirror replication, FlexCache caching and automatic tiering of cold data to a lower-cost capacity pool.[^s1][^s4] FSx for OpenZFS serves NFS v3 through v4.2 and brings ZFS snapshots, instant clones and compression; it offers an SSD storage class and an elastic Intelligent-Tiering class billed on data stored.[^s5] FSx for Windows File Server serves SMB 2.0 to 3.1.1 from a native Windows file system and is joined to a Microsoft Active Directory domain for authentication; you set storage, SSD IOPS and throughput independently and can change them later.[^s3] FSx for Lustre is a POSIX-compliant parallel file system for Linux clients; it can link to an S3 bucket, presenting objects as files and writing results back.[^s1][^s2]

Availability is chosen per file system. ONTAP, OpenZFS and Windows File Server offer Single-AZ and Multi-AZ deployments, where Multi-AZ keeps a standby in a second Availability Zone in the same Region.[^s1][^s3] OpenZFS adds a Single-AZ (non-HA) option whose recovery typically completes within 30 minutes, versus about 60 seconds for its HA failovers.[^s5] Lustre is Single-AZ only, with a scratch type that does not replicate data and a persistent type that does.[^s1][^s2]

## Performance, quotas and gotchas

AWS's comparison page lists, as of 2026-11, a maximum per-file-system throughput of 1,000 GB/s for Lustre, 72–80 GB/s for ONTAP, 12–20 GB/s for Windows File Server and 10–21 GB/s for OpenZFS (the higher figures include caching, bursting and compression). Maximum sizes are given as multiple PBs for Lustre, tens of PBs for ONTAP, 512 TiB for OpenZFS and 64 TiB for Windows File Server.[^s1] For Windows File Server, the default per-Region account quotas are 100 file systems and 10,240 MBps of total throughput capacity, both adjustable through Service Quotas; a single file system is limited to 64 TiB and 12,288 MBps.[^s6]

Billing is per resource, not per request: for ONTAP, for example, you pay for SSD capacity, SSD IOPS above three per GB, throughput capacity, capacity-pool storage and requests, and backups.[^s4] Prices vary by Region and file system; check each member's pricing page. Gotchas: Lustre needs a Lustre client on Linux, so Windows hosts cannot mount it;[^s1][^s2] Windows File Server requires Active Directory;[^s3] and only ONTAP and Windows File Server support Active Directory authentication and data deduplication.[^s1]

## When to use it (and when not)

Choose FSx when an application expects a specific file system's semantics or features — SMB with NTFS ACLs, NetApp tooling, ZFS snapshots, or Lustre's throughput for machine learning, HPC, video and financial modeling.[^s1][^s2] ONTAP is also the main hybrid choice, supporting caching on premises and SnapMirror-based backup and disaster recovery from on-premises NetApp systems.[^s1] For simple, elastic NFS shared by Linux clients, where you don't want to size throughput or storage, Amazon EFS is usually the simpler fit; for data processed as objects, Amazon S3 remains the default. Since December 2025, S3 Access Points can be attached to FSx for ONTAP volumes, letting S3-aware services such as Bedrock, SageMaker and Glue read file data in place.[^s7]

## Connections

- **Broader:** `kt:aws-storage-services`, where FSx sits beside S3, EBS and EFS as the managed file family.[^s1]
- **Alternative to** `kt:amazon-efs`, AWS's native elastic NFS service; both serve NFS, but FSx offers more protocols and specialised engines.[^s1]
- **Integrates with** `kt:amazon-sagemaker-ai`: Lustre file systems can be training data sources, and ONTAP volumes are reachable through S3 Access Points.[^s2][^s7]
- **Related to** `kt:amazon-s3` (Lustre data repositories, ONTAP S3 access), `kt:amazon-ec2` (the main clients) and `kt:aws-datasync` (moving data in).[^s2][^s7]
- **Secured by** `kt:aws-kms` (encryption at rest of data and backups) and **monitored by** `kt:amazon-cloudwatch`.[^s4][^s5]
- **Governed by** `kt:fsx-quotas` and **scoped to** `kt:availability-zones`, since every deployment is Single-AZ or Multi-AZ.[^s1][^s6]

## Sources
[^s1]: Amazon Web Services, "Choosing an Amazon FSx File System". https://aws.amazon.com/fsx/when-to-choose-fsx/ (accessed 2026-11-12)
[^s2]: Amazon Web Services, "What is Amazon FSx for Lustre?". https://docs.aws.amazon.com/fsx/latest/LustreGuide/what-is.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "What is FSx for Windows File Server?". https://docs.aws.amazon.com/fsx/latest/WindowsGuide/what-is.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, "What is Amazon FSx for NetApp ONTAP?". https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/what-is-fsx-ontap.md (accessed 2026-11-12)
[^s5]: Amazon Web Services, "What is Amazon FSx for OpenZFS?". https://docs.aws.amazon.com/es_es/fsx/latest/OpenZFSGuide/what-is-fsx.md (accessed 2026-11-12)
[^s6]: Amazon Web Services, "Quotas - Amazon FSx for Windows File Server". https://docs.aws.amazon.com/fsx/latest/WindowsGuide/limits.html (accessed 2026-11-12)
[^s7]: Amazon Web Services, "Amazon FSx for NetApp ONTAP now supports Amazon S3 access" (2025-12-02). https://aws.amazon.com/about-aws/whats-new/2025/12/amazon-fsx-netapp-ontap-s3-access (accessed 2026-11-12)
