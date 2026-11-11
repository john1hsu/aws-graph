---
id: kt:aws-storage-services
type: Concept
title: AWS storage services
aliases: [AWS storage, storage on AWS, AWS storage portfolio, AWS storage category, AWS cloud storage services]
domain: storage
summary: >-
  The AWS service category for keeping data: object storage (Amazon S3), block storage (Amazon EBS, instance
  store), managed file systems (Amazon EFS, the Amazon FSx family), caching and hybrid access (Amazon File Cache,
  AWS Storage Gateway) and data protection (AWS Backup, AWS Elastic Disaster Recovery).
verified: 2026-11-11
status: concept
broader: [kt:amazon-web-services]
related: [kt:aws-compute-services, kt:purpose-built-databases, kt:aws-migration-and-transfer-services]
relations:
  - {predicate: hasPart, object: kt:amazon-s3}
  - {predicate: hasPart, object: kt:amazon-ebs}
  - {predicate: hasPart, object: kt:amazon-efs}
  - {predicate: hasPart, object: kt:amazon-fsx}
  - {predicate: hasPart, object: kt:aws-storage-gateway}
  - {predicate: hasPart, object: kt:aws-backup}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Storage - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/storage-services.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Choosing an AWS storage service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/storage-on-aws-how-to-choose/choosing-aws-storage-service.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Data management - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/wellarchitected/latest/framework/perf-data.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "AWS Snowball Edge availability change - AWS Snowball Edge Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/snowball/latest/developer-guide/snowball-edge-availability-change.html
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Amazon FSx File Gateway availability - Amazon FSx File Gateway User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/filegateway/latest/filefsxw/available-regions-intro.html
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# AWS storage services

"Storage" is the AWS service category for holding data durably and serving it to applications, users and other services. The AWS overview whitepaper describes it as a broad portfolio for storing, accessing, protecting and analyzing data, and as of 2026-11 it lists 11 services: AWS Backup, Amazon EBS, AWS Elastic Disaster Recovery, Amazon EFS, Amazon File Cache, the four Amazon FSx file systems (Lustre, NetApp ONTAP, OpenZFS, Windows File Server), Amazon S3 and AWS Storage Gateway.[^s1] This node is not something you provision. It is the map that explains how those services divide the work, and which one to reach for.

## The storage types

The first question AWS asks is how the workload reaches its data. Its storage decision guide (last updated 2026-10-05) groups the portfolio into seven categories:[^s2]

- **Block** – Amazon EBS volumes and EC2 instance store. These give low-latency storage attached to one instance or container, the usual home for a self-managed database. For scale, the guide notes a gp3 volume reaches up to 64 TiB, 80,000 IOPS and 2,000 MB/s.[^s2]
- **File** – Amazon EFS, the FSx family, S3 File Gateway and the newer S3 Files. They provide shared NFS or SMB access for many instances, containers or on-premises servers at once.[^s2]
- **Object** – Amazon S3, plus S3 Tables for Apache Iceberg data, S3 Vectors for embeddings and S3 Express One Zone for latency-sensitive reads. Objects are reached through an API, which suits read-heavy content, data lakes and ML.[^s2] S3 is designed for 11 nines (99.999999999%) of durability.[^s1][^s3]
- **Cache** – Amazon File Cache, which fronts on-premises NFS, FSx and S3 data with a high-speed cache.[^s2]
- **Hybrid and edge** – Storage Gateway's S3 File, Tape and Volume gateways give on-premises applications local access to cloud-backed storage. AWS Outposts serves data that must stay on premises.[^s2]
- **Data protection** – AWS Backup for scheduled and cross-Region backups, Elastic Disaster Recovery for recovery, S3 Object Lock for write-once immutability, and Amazon Macie for finding sensitive data in S3.[^s2]
- **Data transfer** – DataSync, Transfer Family, FSx for ONTAP SnapMirror and Storage Gateway move data online. Offline moves use AWS Data Transfer Terminal.[^s2]

## How to choose

The Well-Architected performance efficiency pillar asks how you store, manage and access data (PERF 3). It ties the answer to six traits: block, file or object access; random or sequential patterns; throughput; how often data is read (online, offline, archival); how often it changes (write-once or dynamic); and availability and durability needs.[^s3] It recommends purpose-built stores and using more than one storage service where the workload calls for it.[^s3]

The decision guide adds practical tie-breakers. Client operating system matters: Windows points to FSx for Windows File Server, Linux to EFS or FSx for Lustre, and mixed fleets to FSx for NetApp ONTAP, which serves NFS and SMB at the same time.[^s2] Cost depends on capacity, transfer and availability choices, so the guide recommends tiering that follows access patterns. Examples are S3 Intelligent-Tiering and EFS Lifecycle Management, which by default moves files to EFS Archive after 90 days without access.[^s2] For AI and containers, it suggests S3 Vectors for retrieval-augmented generation, S3 Express One Zone or FSx for Lustre for training data, and the EBS and EFS CSI drivers for Amazon EKS.[^s2]

## Gotchas

Category membership drifts, so check each service's lifecycle before designing around it. The AWS Snow Family no longer takes new customers or new device orders. Existing customers keep their devices, and AWS points new projects to DataSync, Data Transfer Terminal, partner devices or Outposts.[^s4] The decision guide adds that Snowball support in commercial Regions ends on 2026-12-31, and that Data Transfer Terminal currently requires AWS Enterprise Support.[^s2] Amazon FSx File Gateway is also closed to new customers; AWS suggests mapping file shares straight to FSx for Windows File Server.[^s5] Finally, the guide notes S3 has dropped its 30-day minimum storage duration for transitions to Standard-IA and One Zone-IA, so older lifecycle advice may overstate that cost.[^s2]

## Connections

This node is the storage branch of `kt:amazon-web-services`. Its `hasPart` edges point to the core services: `kt:amazon-s3`, `kt:amazon-ebs`, `kt:amazon-efs`, `kt:amazon-fsx`, `kt:aws-storage-gateway` and `kt:aws-backup`.[^s1] It is related to `kt:aws-compute-services` because block storage and instance store exist to serve EC2, and to `kt:purpose-built-databases` because both follow the Well-Architected advice to match the data store to the access pattern.[^s2][^s3] It is related to `kt:aws-migration-and-transfer-services` through the transfer tools the decision guide lists alongside storage.[^s2] The `coveredBy` edge reflects CLF-C02 Task Statement 3.6, "Identify AWS storage services". It covers object storage uses, S3 storage classes, block storage (EBS, instance store), file services (EFS, FSx), cached file systems (Storage Gateway), lifecycle policies and AWS Backup.[^s6]

## Sources
[^s1]: Amazon Web Services, "Storage - Overview of Amazon Web Services". https://docs.aws.amazon.com/whitepapers/latest/aws-overview/storage-services.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Choosing an AWS storage service". https://docs.aws.amazon.com/decision-guides/latest/storage-on-aws-how-to-choose/choosing-aws-storage-service.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Data management - AWS Well-Architected Framework". https://docs.aws.amazon.com/en_us/wellarchitected/latest/framework/perf-data.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "AWS Snowball Edge availability change - AWS Snowball Edge Developer Guide". https://docs.aws.amazon.com/snowball/latest/developer-guide/snowball-edge-availability-change.html (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Amazon FSx File Gateway availability - Amazon FSx File Gateway User Guide". https://docs.aws.amazon.com/filegateway/latest/filefsxw/available-regions-intro.html (accessed 2026-11-11)
[^s6]: Amazon Web Services, "Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html (accessed 2026-11-11)
