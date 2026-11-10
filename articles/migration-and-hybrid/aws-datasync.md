---
id: kt:aws-datasync
type: Service
title: AWS DataSync
aliases: [DataSync]
domain: migration-and-hybrid
summary: >-
  AWS's managed online data-transfer service that copies files and objects between on-premises
  or other-cloud storage (NFS, SMB, HDFS, object storage, Azure Blob) and AWS storage services
  such as Amazon S3, Amazon EFS and Amazon FSx, or between AWS storage services, with scheduling,
  incremental transfers and integrity verification.
verified: 2026-11-10
status: current
broader: [kt:aws-migration-and-transfer-services]
related: [kt:amazon-efs, kt:amazon-fsx, kt:aws-storage-gateway, kt:aws-transform-mgn, kt:aws-govcloud-us]
relations:
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: contrastsWith, object: kt:aws-transfer-family}
  - {predicate: governedBy, object: kt:datasync-quotas}
  - {predicate: securedBy, object: kt:aws-privatelink}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "How AWS DataSync works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/datasync/latest/userguide/how-datasync-transfer-works.html
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Choosing a task mode for your data transfer"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/datasync/latest/userguide/choosing-task-mode.html
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "AWS DataSync quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/datasync/latest/userguide/datasync-limits.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "AWS DataSync pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/datasync/pricing/
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "AWS DataSync FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/datasync/faqs/
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# AWS DataSync

AWS DataSync is a managed service for moving file and object data over the network. It copies data between on-premises or self-managed storage (NFS and SMB shares, HDFS, self-managed object storage), other clouds (for example Google Cloud Storage, Azure Files and Azure Blob Storage) and AWS storage: Amazon S3, Amazon EFS, and the Amazon FSx file systems for Windows File Server, Lustre, OpenZFS and NetApp ONTAP.[^s5] It is an online service: data is encrypted with TLS in transit and is not persisted inside DataSync itself.[^s5]

## How it works

A transfer is built from four pieces. A **location** describes a source or destination; a **task** pairs a source and destination location and records how to copy (metadata, permissions, deleted files); a **task execution** is one run of that task; and an **agent** is a virtual machine appliance that reads or writes storage outside AWS. Agents run on VMware ESXi, KVM, Nutanix AHV or Microsoft Hyper-V, or as an Amazon EC2 instance for storage inside a VPC; transfers between AWS storage services in the same account need no agent at all.[^s1] Agents connect outbound to DataSync endpoints, so no inbound firewall rules or VPN are needed, and traffic can be kept off the public internet with VPC (AWS PrivateLink) endpoints; FIPS endpoints are also offered.[^s5]

Each execution has three phases: prepare (compare source and destination to find differences), transfer, and verify. DataSync always performs integrity checks during transfer and can optionally verify either only the transferred data or the whole dataset at the end, using checksums and metadata. Files written to mid-transfer can fail verification, and locked files are skipped and logged.[^s1] Recurring tasks can be scheduled hourly, daily or weekly, copy only what changed since the last run, and use include/exclude filters or manifests to limit scope.[^s5]

### Basic vs. Enhanced mode

Every task is created in one of two modes, and the mode cannot be changed afterwards; you must create a new task to switch.[^s2] **Basic mode** runs prepare, transfer and verify one after another, so a full scan of both locations happens before any bytes move. **Enhanced mode** runs the phases in parallel, streaming each object through as it is listed, which is faster for most workloads, removes the per-execution file-count limit, and adds richer metrics and structured JSON logs.[^s1][^s2] Enhanced mode supports every location except S3 on Outposts, which still requires Basic mode, and each mode needs its matching agent type.[^s2]

## Quotas, pricing and gotchas

Quotas are per account and Region (as of 2026-11): 100 tasks (adjustable); Basic mode allows 50 million files, objects and directories per execution for transfers with on-premises or other-cloud storage and 25 million between AWS storage services (both adjustable), while Enhanced mode is described as virtually unlimited. Throughput per task is capped at 10 Gbps with an agent and 5 Gbps without one, and up to 120 Enhanced mode executions can run concurrently (not adjustable).[^s3] In Basic mode, S3 prefixes count as directories toward the limit, and tasks over 20 million items need an agent with at least 64 GB of RAM; splitting work with filters is the suggested alternative to a quota increase.[^s3]

Pricing is per gigabyte copied. The pricing page's examples (several set in US East (Ohio), as of 2026-11) use $0.0125/GB for Basic mode, and $0.015/GB plus $0.55 per task execution for Enhanced mode.[^s4] S3 request charges, storage-class retrieval fees, AWS data transfer (such as data transfer out of the source Region) and PrivateLink endpoint charges are billed separately.[^s4] Because Enhanced mode has a per-execution fee, very frequent small runs can cost more than the per-GB comparison suggests.

## When to use it (and when not)

DataSync fits bulk migrations and recurring synchronisation of NAS, HDFS or object data into AWS. AWS positions it alongside, not instead of, related services: migrate with DataSync and then use Storage Gateway's File Gateway for ongoing on-premises access; use AWS Transfer Family for SFTP, FTPS, FTP or AS2 exchanges with partners; and prefer S3 Replication for continuous copying to a single destination bucket.[^s5] For whole-server rehosting or database migration, server- and database-level tools are the better fit, since DataSync moves files and objects only.

For regulated workloads, the FAQ lists FedRAMP Moderate in the US East and US West Regions and FedRAMP High in AWS GovCloud (US), plus HIPAA eligibility, PCI DSS, ISO and SOC programs.[^s5]

## Connections

DataSync sits in AWS's Migration and Transfer category alongside AWS Application Migration Service (now AWS Transform MGN), AWS DMS and AWS Transfer Family; it moves file and object data, while those tools rehost servers or migrate databases.[^s6] Its main AWS endpoints are Amazon S3, Amazon EFS and Amazon FSx; it contrasts with AWS Transfer Family (protocol endpoints for partners) and pairs with AWS Storage Gateway for post-migration hybrid access.[^s5] Traffic can be secured with AWS PrivateLink endpoints, its limits form the DataSync quotas node, and it is scoped to AWS Regions, including GovCloud (US).[^s3][^s5] AWS DataSync is listed under Migration and Transfer in the in-scope services for the AWS Certified Solutions Architect – Associate (SAA-C03) exam.[^s6]

## Sources

[^s1]: Amazon Web Services, "How AWS DataSync works". https://docs.aws.amazon.com/datasync/latest/userguide/how-datasync-transfer-works.html (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Choosing a task mode for your data transfer". https://docs.aws.amazon.com/datasync/latest/userguide/choosing-task-mode.html (accessed 2026-11-10)
[^s3]: Amazon Web Services, "AWS DataSync quotas". https://docs.aws.amazon.com/datasync/latest/userguide/datasync-limits.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "AWS DataSync pricing". https://aws.amazon.com/datasync/pricing/ (accessed 2026-11-10)
[^s5]: Amazon Web Services, "AWS DataSync FAQs". https://aws.amazon.com/datasync/faqs/ (accessed 2026-11-10)
[^s6]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-10)
