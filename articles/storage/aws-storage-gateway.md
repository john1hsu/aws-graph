---
id: kt:aws-storage-gateway
type: Service
title: AWS Storage Gateway
aliases: [Storage Gateway, Amazon S3 File Gateway, S3 File Gateway, Volume Gateway, Tape Gateway, Virtual Tape Library gateway]
domain: storage
summary: >-
  AWS's hybrid cloud storage service: a gateway appliance (VM, or EC2 instance) that runs near on-premises
  applications and presents Amazon S3-backed storage over standard protocols, as NFS/SMB file shares
  (S3 File Gateway), iSCSI block volumes (Volume Gateway) or an iSCSI virtual tape library (Tape Gateway).
verified: 2026-11-13
status: current
broader: [kt:aws-storage-services]
related: [kt:aws-datasync, kt:amazon-fsx, kt:aws-backup, kt:disaster-recovery-strategies]
relations:
  - {predicate: dependsOn, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:amazon-ebs}
  - {predicate: runsOn, object: kt:amazon-ec2}
  - {predicate: securedBy, object: kt:aws-privatelink}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: governedBy, object: kt:storage-gateway-quotas}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: compliesWith, object: kt:hipaa-eligible-services}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is Volume Gateway? - AWS Storage Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/storagegateway/latest/vgw/WhatIsStorageGateway.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "How Tape Gateway works - AWS Storage Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/storagegateway/latest/tgw/StorageGatewayConcepts.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "How Amazon S3 File Gateway works - AWS Storage Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/filegateway/latest/files3/file-gateway-concepts.html
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "Limits and quotas for Amazon S3 File Gateway - AWS Storage Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/filegateway/latest/files3/fgw-quotas.html
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "AWS Storage Gateway quotas (Volume Gateway) - AWS Storage Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/storagegateway/latest/vgw/resource-gateway-limits.html
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "AWS Storage Gateway FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/storagegateway/faqs
    type: official-docs
    accessed: 2026-11-13
  - id: s7
    title: "AWS Storage Gateway pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/storagegateway/pricing/
    type: official-docs
    accessed: 2026-11-13
  - id: s8
    title: "What is Amazon FSx File Gateway - AWS Storage Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/filegateway/latest/filefsxw/what-is-file-fsxw.html
    type: official-docs
    accessed: 2026-11-13
  - id: s9
    title: "Using the AWS Storage Gateway Hardware Appliance - AWS Storage Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/filegateway/latest/files3/hardware-appliance.html
    type: official-docs
    accessed: 2026-11-13
  - id: s10
    title: "AWS Storage Gateway now supports FIPS-compliant private connectivity for Amazon S3 File Gateway"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/storage-gateway-fips-privatelink-s3/
    type: official-blog
    accessed: 2026-11-13
  - id: s11
    title: "AWS Storage Gateway adds console support for copying file shares across gateways"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/07/aws-storage-gateway-console-copy-file-shares/
    type: official-blog
    accessed: 2026-11-13
  - id: s12
    title: "In-scope AWS services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-13
created: 2026-11-13
---

# AWS Storage Gateway

AWS Storage Gateway is a hybrid storage service: you run a gateway appliance close to your on-premises applications, and it presents cloud-backed storage to them over familiar protocols while keeping the data in AWS. AWS groups the offerings into file-based (Amazon S3 File Gateway, Amazon FSx File Gateway), volume-based (Volume Gateway) and tape-based (Tape Gateway) gateways. The appliance runs as a VM on VMware ESXi, KVM, Microsoft Hyper-V or Nutanix AHV, or as an Amazon EC2 instance (for example, for disaster recovery).[^s1]

## How it works

**S3 File Gateway** exposes NFS or SMB file shares, each linked to an S3 bucket. Every file maps one-to-one to an S3 object whose key is the file path, so other AWS services can read the data natively. A local cache serves recently used data at low latency, and changes are uploaded asynchronously with multipart parallel uploads over HTTPS; objects are encrypted with SSE-S3 keys.[^s3] Supported protocol versions are NFS v3 and v4.1 and SMB v2 and v3, and file shares can write to S3 Standard, S3 Intelligent-Tiering, S3 Standard-IA or S3 One Zone-IA.[^s6]

**Volume Gateway** presents iSCSI block volumes in two modes. Cached volumes keep the primary copy in Amazon S3 and hold frequently accessed data locally. Stored volumes keep the full dataset on premises and asynchronously back up point-in-time snapshots to AWS.[^s1] Those snapshots are stored as Amazon EBS snapshots, so you can restore them as gateway volumes or as EBS volumes for EC2. They are not reachable through S3 APIs.[^s6]

**Tape Gateway** gives existing backup software a virtual tape library (VTL): one media changer and 10 tape drives, all presented as iSCSI devices. Writes land in the local cache and upload buffer and are acknowledged before an asynchronous upload to S3-backed virtual tapes. When the backup application ejects a tape, the gateway archives it to a virtual tape shelf in S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive. Archived tapes must be retrieved to the library before they can be read, which typically takes 3–5 hours from Flexible Retrieval and 12 hours from Deep Archive.[^s2]

## Quotas, pricing and gotchas

Quotas as of 2026-11, per the user guides:

- **S3 File Gateway:** 50 file shares per gateway; files up to 5 TiB; paths up to 1,024 bytes; local cache from 150 GiB to 64 TiB.[^s4]
- **Volume Gateway:** up to 32 volumes per gateway. Cached volumes can be up to 32 TiB each (1,024 TiB per gateway); stored volumes up to 16 TiB each (512 TiB per gateway). A snapshot of a cached volume larger than 16 TiB can't be restored to EBS.[^s5]
- **Tape Gateway:** virtual tapes from 100 GiB to 15 TiB, and up to 1,500 tapes or 1 PiB per gateway.[^s2]

Pricing combines storage in the backing AWS service, requests and data transfer. For S3 File Gateway, the per-gateway charge for data written is capped at $125 per gateway per month, and the first 100 GB written to AWS per account is free. Gateways hosted on EC2 are also billed at EC2 rates. Per-GB rates vary by Region; check the pricing page for yours.[^s7]

Gotchas: on S3 File Gateway, renames, permission changes and appends replace whole objects. With versioning that multiplies object versions, and in infrequent-access classes it can trigger early-deletion and retrieval fees.[^s3] Don't resize cache or upload-buffer disks that are already allocated; add new disks instead.[^s4][^s5]

## Lifecycle notes

Amazon FSx File Gateway, which caches FSx for Windows File Server shares on premises, is no longer available to new customers, although existing customers can keep using it.[^s8] AWS stopped offering the Storage Gateway Hardware Appliance as of May 12, 2025. Existing appliances stay supported until May 2028.[^s9] The service itself is current. Recent launches include FIPS 140-3 endpoints over AWS PrivateLink for S3 File Gateway (September 2026, in US, Canada and GovCloud (US) Regions)[^s10] and console copying of file shares between gateways (July 2026).[^s11]

## When to use it (and when not)

Use Storage Gateway when on-premises applications need **ongoing** low-latency access to cloud storage through standard protocols. Typical cases are replacing physical tape, tiering file shares into S3, or getting off-site volume backups that can be recovered in EC2.[^s1][^s2] For one-time or scheduled bulk copies into S3, EFS or FSx, AWS DataSync is the better fit. For fully cloud-native Windows shares, use FSx directly rather than a cached gateway.[^s8]

## Compliance

According to AWS's FAQ, Storage Gateway is HIPAA eligible for all gateway types under a BAA. It holds FedRAMP Moderate authorization in the US commercial Regions and FedRAMP High in the AWS GovCloud (US) Regions. Gateways support FIPS endpoints, but the hardware appliance is not FIPS 140-2 compliant.[^s6]

## Connections

This node is part of `kt:aws-storage-services`. It `dependsOn` `kt:amazon-s3`, the backing store for file shares, cached volumes and virtual tapes, and `integratesWith` `kt:amazon-ebs` through Volume Gateway snapshots.[^s3][^s6] It `runsOn` `kt:amazon-ec2` when hosted in AWS[^s1], is `securedBy` `kt:aws-privatelink` for private (including FIPS) endpoints[^s10], is `monitoredBy` `kt:amazon-cloudwatch`[^s3], and is `governedBy` the frontier node `kt:storage-gateway-quotas`.[^s4][^s5] The `compliesWith` edges to `kt:fedramp-on-aws` and `kt:hipaa-eligible-services` reflect the FAQ.[^s6] It is related to `kt:aws-datasync` and `kt:amazon-fsx` as alternatives for moving and serving files, and to `kt:aws-backup` and `kt:disaster-recovery-strategies` because it is used for backup and recovery.[^s1][^s8] The SAA-C03 exam guide lists AWS Storage Gateway among its in-scope storage services, which is the basis for the `coveredBy` edge.[^s12]

## Sources
[^s1]: Amazon Web Services, "What is Volume Gateway? - AWS Storage Gateway". https://docs.aws.amazon.com/storagegateway/latest/vgw/WhatIsStorageGateway.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "How Tape Gateway works - AWS Storage Gateway". https://docs.aws.amazon.com/storagegateway/latest/tgw/StorageGatewayConcepts.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "How Amazon S3 File Gateway works - AWS Storage Gateway". https://docs.aws.amazon.com/filegateway/latest/files3/file-gateway-concepts.html (accessed 2026-11-13)
[^s4]: Amazon Web Services, "Limits and quotas for Amazon S3 File Gateway". https://docs.aws.amazon.com/filegateway/latest/files3/fgw-quotas.html (accessed 2026-11-13)
[^s5]: Amazon Web Services, "AWS Storage Gateway quotas (Volume Gateway)". https://docs.aws.amazon.com/storagegateway/latest/vgw/resource-gateway-limits.html (accessed 2026-11-13)
[^s6]: Amazon Web Services, "AWS Storage Gateway FAQs". https://aws.amazon.com/storagegateway/faqs (accessed 2026-11-13)
[^s7]: Amazon Web Services, "AWS Storage Gateway pricing". https://aws.amazon.com/storagegateway/pricing/ (accessed 2026-11-13)
[^s8]: Amazon Web Services, "What is Amazon FSx File Gateway". https://docs.aws.amazon.com/filegateway/latest/filefsxw/what-is-file-fsxw.html (accessed 2026-11-13)
[^s9]: Amazon Web Services, "Using the AWS Storage Gateway Hardware Appliance". https://docs.aws.amazon.com/filegateway/latest/files3/hardware-appliance.html (accessed 2026-11-13)
[^s10]: Amazon Web Services, "AWS Storage Gateway now supports FIPS-compliant private connectivity for Amazon S3 File Gateway" (What's New, 2026-09-10). https://aws.amazon.com/about-aws/whats-new/2026/09/storage-gateway-fips-privatelink-s3/ (accessed 2026-11-13)
[^s11]: Amazon Web Services, "AWS Storage Gateway adds console support for copying file shares across gateways" (What's New, 2026-07-14). https://aws.amazon.com/about-aws/whats-new/2026/07/aws-storage-gateway-console-copy-file-shares/ (accessed 2026-11-13)
[^s12]: Amazon Web Services, "In-scope AWS services - SAA-C03". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-13)
