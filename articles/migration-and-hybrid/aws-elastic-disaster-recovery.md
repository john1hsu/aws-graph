---
id: kt:aws-elastic-disaster-recovery
type: Service
title: AWS Elastic Disaster Recovery
aliases: [AWS DRS, Elastic Disaster Recovery, DRS]
domain: migration-and-hybrid
summary: >-
  AWS's managed disaster recovery service, which continuously replicates physical, virtual and
  cloud servers at the block level into a low-cost staging area in an AWS Region and launches
  them as Amazon EC2 instances for drills, recovery and failback, implementing a pilot light
  strategy with RPOs of seconds and RTOs of minutes.
verified: 2026-11-16
status: current
broader: [kt:disaster-recovery-strategies]
related: [kt:aws-transform-mgn, kt:aws-backup, kt:amazon-route-53]
relations:
  - {predicate: dependsOn, object: kt:amazon-ec2}
  - {predicate: dependsOn, object: kt:amazon-ebs}
  - {predicate: replaces, object: kt:cloudendure-disaster-recovery}
  - {predicate: governedBy, object: kt:drs-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
sources:
  - id: s1
    title: "What is Elastic Disaster Recovery? - AWS Elastic Disaster Recovery"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/drs/latest/userguide/what-is-drs.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "AWS Elastic Disaster Recovery FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/disaster-recovery/faqs/
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Replication related - AWS Elastic Disaster Recovery"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/drs/latest/userguide/Replication-Related-FAQ.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Point in time (PIT) policy - AWS Elastic Disaster Recovery"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/drs/latest/userguide/point-in-time.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "AWS Elastic Disaster Recovery endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/drs.html
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "AWS Elastic Disaster Recovery Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/disaster-recovery/pricing/
    type: official-docs
    accessed: 2026-11-16
  - id: s7
    title: "Elastic Disaster Recovery FAQ - AWS Elastic Disaster Recovery"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/drs/latest/userguide/FAQ.html
    type: official-docs
    accessed: 2026-11-16
  - id: s8
    title: "Using Elastic Disaster Recovery for recovery and failback - AWS Elastic Disaster Recovery"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/drs/latest/userguide/failback.html
    type: official-docs
    accessed: 2026-11-16
  - id: s9
    title: "Disaster recovery options in the cloud - Disaster Recovery of Workloads on AWS: Recovery in the Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html
    type: official-docs
    accessed: 2026-11-16
  - id: s10
    title: "AWS Elastic Disaster Recovery introduces Recovery Plans for orchestrated application recovery"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/08/elastic-disaster-recovery-plans/
    type: official-blog
    accessed: 2026-11-16
  - id: s11
    title: "AWS Elastic Disaster Recovery reduces recovery time for AWS-to-AWS workloads"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/07/aws-drs-fast-recovery/
    type: official-blog
    accessed: 2026-11-16
  - id: s12
    title: "AWS Services in Scope by Compliance Program - FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS Elastic Disaster Recovery

AWS Elastic Disaster Recovery (AWS DRS) keeps an up-to-date, cheaply stored copy of your servers in AWS so that you pay for a full recovery site only when you run a drill or actually recover.[^s2] It replicates on-premises or cloud-hosted applications into a staging area subnet in your own AWS account and a Region you choose, and can launch recovery instances from the latest state or an earlier point in time.[^s1] AWS describes it as the next generation of CloudEndure Disaster Recovery and the recommended service for customers moving off it.[^s7] AWS's DR whitepaper classifies it as a **pilot light** implementation: data and switched-off resources sit in a staging VPC, and a full-capacity deployment is built from them on failover.[^s9]

## How it works

You install the AWS Replication Agent on each source server without a reboot; it captures blocks as they are written, compresses and encrypts them, and sends them over TLS 1.2 directly into your VPC, optionally over Direct Connect or VPN.[^s2] Supported sources include physical servers, VMware vSphere, Microsoft Hyper-V, other clouds, and EC2 instances in another Availability Zone or Region.[^s2] Because it works at the operating-system layer, a source can be anything that can run the agent and the DRS Failback Client.[^s7]

In the staging area, DRS launches and recycles **replication servers** automatically (default t3.small, typically 15 volumes per server, refreshed about every 14 days), each source volume gets a same-sized staging EBS volume, and agents send data on TCP 1500 using LZ4 compression, which AWS says typically achieves 60–70%.[^s3] **Point-in-time** snapshots are kept every 10 minutes for the last hour and hourly for the last 24 hours (both fixed), plus daily snapshots whose retention you can set from 1 to 365 days.[^s4] AWS's documentation states RPOs typically in the sub-second range and RTOs in minutes, depending mainly on OS boot time.[^s7]

When you launch for a drill or recovery, DRS converts the servers to boot natively on EC2.[^s1] **Failback** to an on-premises source uses the Failback Client ISO or DRS Failback Automation; for AWS sources you start reverse replication from the protected recovery instance, in the same or a cross-account setup. Redirecting production traffic (for example with Route 53) happens outside DRS.[^s8]

## Quotas, pricing and gotchas

Default quotas per Region (as of 2026-11): 300 replicating source servers per account (not adjustable), 4,000 total source servers per account (adjustable), 100 source servers per job, 500 across all jobs and 20 concurrent jobs (none adjustable), and 100 source networks (adjustable).[^s5] To recover more servers into one account, AWS lets you feed up to 3,000 servers into a single target account from multiple staging accounts.[^s2] The endpoints list includes both AWS GovCloud (US) Regions with FIPS endpoints.[^s5]

Pricing (as of 2026-11): a flat $0.028 per replicating source server per hour, the same whatever Region you replicate to, with no upfront fee.[^s6] On top of that you pay standard rates for staging EBS volumes, EBS snapshots (one base per disk plus incrementals, with a default 7-day retention), replication servers, short-lived conversion servers, and full EC2/EBS only while drill or recovery instances run; AWS's 100-server, 30 TB example comes to about $6,389 per month in US East (N. Virginia).[^s6]

Gotchas: OS-level disk encryption such as BitLocker must be turned off, in-place OS upgrades aren't supported, boot disks mirrored in software aren't supported, and NAS/NFS shares are only protected if the agent runs on the NFS server itself.[^s7] The staging security group opens TCP 1500 to 0.0.0.0/0 by default, so narrow it with NACLs or other controls.[^s3] Running DRS and CloudEndure DR in parallel during a move roughly doubles replication resource costs.[^s7]

## What changed (2026-07 to 2026-08)

In July 2026 AWS added a faster recovery option for EC2-hosted sources, which skips unneeded preparation steps and cuts recovery time by up to 65% on Windows and 40% on Linux, at no extra cost.[^s11] In August 2026 it launched **Recovery Plans**, which launch multi-server applications in ordered steps with wait times, approval steps and a drill mode, in all DRS Regions at no extra cost.[^s10]

## When to use it (and when not)

Use DRS for server-based workloads, on-premises, in another cloud, or on EC2, where you want low RPO without running a warm standby.[^s9] For AWS-hosted workloads AWS limits it to applications and databases on EC2, not Amazon RDS; for managed services, AWS Backup's cross-Region and cross-account copies fit the backup-and-restore strategy, though AWS Backup does not restore automatically.[^s9] If you need near-zero RTO, a warm standby or active/active design is the step up.[^s9]

## Connections

- **Broader: disaster recovery strategies** (`kt:disaster-recovery-strategies`): DRS is AWS's managed pilot light implementation.[^s9]
- **Related to AWS Transform MGN** (`kt:aws-transform-mgn`), its block-replication sibling for migrations; DRS recovery can itself end as a migration when you keep the workload on AWS.[^s2] **Related to AWS Backup** (`kt:aws-backup`) for non-EC2 resources and **Amazon Route 53** (`kt:amazon-route-53`) for traffic failover.[^s8][^s9]
- **Depends on Amazon EC2** (`kt:amazon-ec2`) and **Amazon EBS** (`kt:amazon-ebs`) for replication servers, staging volumes, snapshots and recovery instances.[^s3][^s6]
- **Replaces CloudEndure Disaster Recovery** (`kt:cloudendure-disaster-recovery`).[^s7]
- **Governed by DRS quotas** (`kt:drs-quotas`) and **scoped to AWS Regions** (`kt:aws-regions`).[^s5]
- **In scope for FedRAMP** (`kt:fedramp-on-aws`): AWS lists it under both the East/West and GovCloud authorizations.[^s12]

## Sources
[^s1]: Amazon Web Services, "What is Elastic Disaster Recovery? - AWS Elastic Disaster Recovery". https://docs.aws.amazon.com/drs/latest/userguide/what-is-drs.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "AWS Elastic Disaster Recovery FAQs". https://aws.amazon.com/disaster-recovery/faqs/ (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Replication related - AWS Elastic Disaster Recovery". https://docs.aws.amazon.com/drs/latest/userguide/Replication-Related-FAQ.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Point in time (PIT) policy - AWS Elastic Disaster Recovery". https://docs.aws.amazon.com/drs/latest/userguide/point-in-time.html (accessed 2026-11-16)
[^s5]: Amazon Web Services, "AWS Elastic Disaster Recovery endpoints and quotas - AWS General Reference". https://docs.aws.amazon.com/general/latest/gr/drs.html (accessed 2026-11-16)
[^s6]: Amazon Web Services, "AWS Elastic Disaster Recovery Pricing". https://aws.amazon.com/disaster-recovery/pricing/ (accessed 2026-11-16)
[^s7]: Amazon Web Services, "Elastic Disaster Recovery FAQ - AWS Elastic Disaster Recovery". https://docs.aws.amazon.com/drs/latest/userguide/FAQ.html (accessed 2026-11-16)
[^s8]: Amazon Web Services, "Using Elastic Disaster Recovery for recovery and failback - AWS Elastic Disaster Recovery". https://docs.aws.amazon.com/drs/latest/userguide/failback.html (accessed 2026-11-16)
[^s9]: Amazon Web Services, "Disaster recovery options in the cloud - Disaster Recovery of Workloads on AWS: Recovery in the Cloud". https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html (accessed 2026-11-16)
[^s10]: Amazon Web Services, What's New with AWS, "AWS Elastic Disaster Recovery introduces Recovery Plans for orchestrated application recovery". https://aws.amazon.com/about-aws/whats-new/2026/08/elastic-disaster-recovery-plans/ (accessed 2026-11-16)
[^s11]: Amazon Web Services, What's New with AWS, "AWS Elastic Disaster Recovery reduces recovery time for AWS-to-AWS workloads". https://aws.amazon.com/about-aws/whats-new/2026/07/aws-drs-fast-recovery/ (accessed 2026-11-16)
[^s12]: Amazon Web Services, "AWS Services in Scope by Compliance Program - FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-16)
