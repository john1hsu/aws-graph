---
id: kt:amazon-ebs
type: Service
title: Amazon EBS
aliases: [Amazon Elastic Block Store, EBS, AWS EBS, Elastic Block Store]
domain: storage
summary: >-
  AWS's network-attached block storage for Amazon EC2: persistent volumes that live in one Availability
  Zone, come in SSD and HDD volume types (gp3, gp2, io2 Block Express, io1, st1, sc1), and are backed up
  with incremental, Region-wide snapshots.
verified: 2026-11-09
status: current
broader: [kt:aws-storage-services]
related: [kt:amazon-ec2, kt:ebs-snapshots, kt:aws-backup]
relations:
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: governedBy, object: kt:ebs-quotas}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: alternativeTo, object: kt:amazon-efs}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Features and benefits of Amazon EBS volumes - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/EBSFeatures.html
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "Amazon EBS Volume Types"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ebs/volume-types/
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Amazon EBS General Purpose SSD volumes - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/general-purpose.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Amazon EBS increases the maximum size and provisioned performance of General Purpose (gp3) volumes"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/09/amazon-ebs-size-provisioned-performance-gp3-volumes
    type: official-blog
    accessed: 2026-11-09
  - id: s5
    title: "Amazon EBS snapshots - Amazon EBS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSSnapshots.html
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "Amazon Elastic Block Store endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/ebs-service.html
    type: official-docs
    accessed: 2026-11-09
  - id: s7
    title: "Amazon EBS Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ebs/pricing/
    type: official-docs
    accessed: 2026-11-09
  - id: s8
    title: "AWS Certified Cloud Practitioner (CLF-C02) exam guide: Domain 3"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# Amazon EBS

Amazon Elastic Block Store (EBS) provides block-level volumes that EC2 instances use like local disks, but that live off-instance on the network. A volume persists independently of the instance it is attached to and keeps its data until you delete it explicitly, and you pay for it for as long as it exists.[^s1] Each volume is automatically replicated within its own Availability Zone to survive the failure of a single hardware component, and it can be attached only to instances in that same Availability Zone.[^s1]

## How it works

**Volume types.** EBS offers SSD types for transactional work and HDD types for sequential throughput (figures as of 2026-11):[^s2]

- **gp3** (General Purpose SSD): 1 GiB–64 TiB, a 3,000 IOPS and 125 MiB/s baseline at any size, provisionable up to 80,000 IOPS and 2,000 MiB/s, independently of capacity.[^s2][^s3]
- **gp2** (previous General Purpose SSD): 1 GiB–16 TiB; performance is tied to size at 3 IOPS per GiB (minimum 100, maximum 16,000), and volumes under 1 TiB burst to 3,000 IOPS using an I/O credit bucket.[^s3]
- **io2 Block Express**: up to 64 TiB, 256,000 IOPS and 4,000 MB/s per volume, with 99.999% durability versus 99.8–99.9% for the other types; aimed at demanding relational and NoSQL databases.[^s2]
- **io1**: up to 16 TiB and 64,000 IOPS (the top figures only on Nitro-based instances).[^s2]
- **st1** (Throughput Optimized HDD) and **sc1** (Cold HDD): 125 GiB–16 TiB, built for big data, log processing and rarely scanned cold data.[^s2]

The gp3 ceilings were raised on 2025-09-26 from 16 TiB, 16,000 IOPS and 1,000 MiB/s, in all commercial and AWS GovCloud (US) Regions that offer gp3.[^s4] On Outposts, gp3 keeps the older 16 TiB / 16,000 IOPS / 1,000 MiB/s limits.[^s3]

**Elastic Volumes and Multi-Attach.** You can change a volume's type, size and IOPS without interrupting service.[^s1] io1 and io2 volumes can be attached to up to 16 Nitro-based instances at once; other types attach to a single instance.[^s1]

**Encryption.** Every volume type supports AES-256 encryption with AWS KMS keys. The first encrypted volume in a Region creates an AWS managed default key, or you can choose a customer managed key for control over rotation, access and auditing. Snapshots of encrypted volumes are encrypted too.[^s1]

**Snapshots.** A snapshot is an incremental, point-in-time backup: only blocks changed since the previous snapshot are saved, yet you need to keep only the latest snapshot to restore. Snapshots are stored in AWS-managed S3 storage replicated across all Availability Zones in the Region, so a volume can be restored into any AZ there.[^s1][^s5] AWS does not snapshot your volumes automatically; you schedule them with Amazon Data Lifecycle Manager or AWS Backup.[^s5]

## Quotas, pricing and gotchas

Default per-Region quotas (as of 2026-11, all adjustable): 50 TiB of gp3 storage in most Regions (300 TiB in some newer Regions), 20 TiB of io2 storage and 100,000 io2 IOPS, and 100,000 snapshots. Concurrent snapshots per gp2/gp3/io1/io2 volume are capped at 5 and are not adjustable.[^s6] EBS can raise some quotas automatically based on usage.[^s6]

AWS's pricing page uses these example rates (as of 2026-11; check your Region): gp3 at $0.08 per GB-month plus $0.005 per provisioned IOPS-month above 3,000 and $0.06 per MB/s-month above 125; gp2 at $0.10 per GB-month; st1 at $0.045 and sc1 at $0.015 per GB-month; snapshots at $0.05 per GB-month standard or $0.0125 archived, the archive tier carrying a 90-day minimum.[^s7] Provisioned storage, IOPS and throughput bill per second with a 60-second minimum.[^s7]

Gotchas: the root volume is deleted on termination by default but additional volumes are not, so orphaned volumes keep billing.[^s1] A gp2 volume under 1 TiB that exhausts its burst credits drops to baseline; watch the `BurstBalance` CloudWatch metric, or move to gp3, whose performance doesn't depend on size or credits.[^s3] Because volumes are AZ-bound, cross-AZ recovery depends on snapshots.[^s1][^s5]

## When to use it (and when not)

Use EBS for EC2 boot volumes, self-managed databases and any workload that needs low-latency block storage for one instance (or a small cluster with io2 Multi-Attach).[^s1][^s2] For a file system shared by many instances, look to Amazon EFS. For durable object storage reached over an API, use Amazon S3. The Cloud Practitioner exam asks candidates to identify block storage options such as EBS and instance store.[^s8]

## Connections

- **Broader:** part of AWS's storage services (`kt:aws-storage-services`).
- **Related:** attached to `kt:amazon-ec2` instances; backed up through `kt:ebs-snapshots` and `kt:aws-backup`.[^s1][^s5]
- **Scoped to** `kt:availability-zones`: a volume lives in, and attaches within, one AZ.[^s1]
- **Secured by** `kt:aws-kms` for encryption at rest, and **monitored by** `kt:amazon-cloudwatch`, which publishes volume metrics at no extra charge.[^s1]
- **Governed by** `kt:ebs-quotas`, the per-Region storage, IOPS and snapshot limits.[^s6]
- **Alternative to** `kt:amazon-efs` when storage must be shared across instances.
- **Covered by** `kt:aws-certified-cloud-practitioner` (CLF-C02 Task Statement 3.6).[^s8]

## Sources
[^s1]: Amazon Web Services, "Features and benefits of Amazon EBS volumes - Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/EBSFeatures.html (accessed 2026-11-09)
[^s2]: Amazon Web Services, "Amazon EBS Volume Types". https://aws.amazon.com/ebs/volume-types/ (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Amazon EBS General Purpose SSD volumes - Amazon EBS". https://docs.aws.amazon.com/ebs/latest/userguide/general-purpose.html (accessed 2026-11-09)
[^s4]: Amazon Web Services (What's New with AWS), "Amazon EBS increases the maximum size and provisioned performance of General Purpose (gp3) volumes", 2025-09-26. https://aws.amazon.com/about-aws/whats-new/2025/09/amazon-ebs-size-provisioned-performance-gp3-volumes (accessed 2026-11-09)
[^s5]: Amazon Web Services, "Amazon EBS snapshots - Amazon EBS". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSSnapshots.html (accessed 2026-11-09)
[^s6]: Amazon Web Services, "Amazon Elastic Block Store endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/ebs-service.html (accessed 2026-11-09)
[^s7]: Amazon Web Services, "Amazon EBS Pricing". https://aws.amazon.com/ebs/pricing/ (accessed 2026-11-09)
[^s8]: Amazon Web Services, "AWS Certified Cloud Practitioner (CLF-C02) exam guide: Domain 3". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html (accessed 2026-11-09)
