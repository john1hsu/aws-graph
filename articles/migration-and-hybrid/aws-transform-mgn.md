---
id: kt:aws-transform-mgn
type: Service
title: AWS Transform MGN
aliases: [AWS Application Migration Service, Application Migration Service, AWS MGN, MGN]
domain: migration-and-hybrid
summary: >-
  AWS's rehosting (lift-and-shift) service, renamed from AWS Application Migration Service in
  June 2026, which continuously replicates physical, virtual and cloud servers at the block level
  into a staging area in your AWS account and launches them as Amazon EC2 instances for testing
  and a short cutover.
verified: 2026-11-09
status: current
broader: [kt:aws-migration-and-transfer-services]
related: [kt:seven-rs-migration-strategies, kt:aws-transform, kt:aws-elastic-disaster-recovery]
relations:
  - {predicate: dependsOn, object: kt:amazon-ec2}
  - {predicate: alternativeTo, object: kt:aws-database-migration-service}
  - {predicate: governedBy, object: kt:mgn-quotas}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "AWS Application Migration Service is now AWS Transform MGN"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/aws-transform-mgn-rebrand/
    type: official-blog
    accessed: 2026-11-09
  - id: s2
    title: "AWS Transform MGN FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/application-migration-service/faqs/
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "General questions - AWS Transform MGN"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/mgn/latest/ug/General-Questions-FAQ.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Network requirements for MGN - AWS Transform MGN"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/mgn/latest/ug/preparing-environments.html
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "AWS Transform MGN endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/mgn.html
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "Migration with AWS Application Migration Service - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/migration-database-rehost-tools/mgn.html
    type: official-docs
    accessed: 2026-11-09
  - id: s7
    title: "AWS Transform MGN and AWS Database Migration Service (DMS) - AWS Product Comparison"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compare/dms-and-transform-mgn/
    type: official-docs
    accessed: 2026-11-09
  - id: s8
    title: "AWS Transform MGN - Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/application-migration-service/pricing/
    type: official-docs
    accessed: 2026-11-09
  - id: s9
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS Transform MGN

AWS Transform MGN is AWS's server rehosting service: it moves servers from physical, virtual or cloud environments onto AWS without changing the applications or their architecture, and AWS positions it as a recommended service for migrations to AWS.[^s2] Until 8 June 2026 it was called AWS Application Migration Service (abbreviated MGN); AWS renamed it to reflect its role as the replication engine behind AWS Transform, the agentic migration service, and kept its existing compliance certifications, which AWS lists as including FedRAMP High, HIPAA, PCI DSS, ISO and SOC 1/2/3.[^s1][^s3] It is available in all commercial AWS Regions and both AWS GovCloud (US) Regions.[^s1]

You can drive it directly from the MGN console, or through the AWS Transform workflow, where an agent handles discovery, wave planning, landing zone and network setup, and rehosting or containerization; at the rehosting stage you can switch between the two.[^s1][^s3]

## How it works

You install the AWS Replication Agent on each source server. It works at the operating-system level, capturing blocks as they are written to directly attached disks and continuously replicating them to matching Amazon EBS volumes in AWS, which AWS Prescriptive Guidance describes as continuous data protection with an RPO of near seconds and an RTO of minutes.[^s3][^s6] Sources can be physical servers, VMware vSphere, Microsoft Hyper-V, other clouds, or EC2 instances moving between Regions or accounts; VMware Cloud on AWS also supports agentless replication through an appliance.[^s2][^s3]

Replicated data lands in a **staging area subnet** you choose in the replication settings template (a dedicated subnet is the recommended practice). Lightweight **replication servers**, based on Amazon Linux 2023, receive data there, and **conversion servers** prepare the boot volumes when you launch.[^s4] Source servers talk to the MGN API over TCP 443 and send data to the replication servers over TCP 1500, encrypted with TLS 1.2 end to end; replicated data stays in your VPC and is encrypted at rest with EBS encryption.[^s2][^s4] Replication can run over private connectivity such as VPN, AWS Direct Connect or VPC peering.[^s2] From the staging area you launch non-disruptive **test** instances and then **cutover** instances, typically within a cutover window of minutes.[^s2][^s3]

## Quotas, pricing and gotchas

Default quotas per Region (as of 2026-11, AWS General Reference): 150 active source servers (adjustable), 4,000 non-archived source servers (adjustable), 50,000 total source servers per account (not adjustable), 200 source servers in a single job (not adjustable) and 20 concurrent jobs in progress (not adjustable).[^s5]

Pricing (as of 2026-11, the same in all supported Regions): each source server gets a free period of 2,160 hours, which is 90 days of continuous replication starting when the agent is installed; after that, replication is billed per server-hour.[^s2][^s8] Even during the free period you pay standard rates for the infrastructure MGN provisions for replication and for the EC2 and EBS resources of test and cutover instances.[^s2][^s8]

Gotchas from AWS guidance: NAS and shared file systems are not replicated like local disks, most clustered databases need special handling, high change-rate OLTP databases can outrun the network, there is no offline shipping option, and non-x86 platforms are out of scope.[^s6] Volumes restored from snapshots can initially perform worse, which can stretch a database cutover.[^s6] Do not apply SSL interception between agents or replication servers and the MGN endpoint, and the MGN and AWS Elastic Disaster Recovery agents cannot run on the same server at once.[^s3][^s4]

## When to use it (and when not)

Choose it for whole-server moves: lift-and-shift, data center exits with hard deadlines and cross-Region moves.[^s7] Prescriptive Guidance recommends migrating database servers with it in each wave unless the database is clustered, changes faster than the network can carry, or needs near-zero downtime.[^s6] In those cases use AWS Database Migration Service or native database tools, which keep source and target databases in sync, and move only the application servers with MGN.[^s6][^s7]

## Connections

- **Broader: AWS migration and transfer services** (`kt:aws-migration-and-transfer-services`); the CLF-C02 in-scope list places AWS Application Migration Service in the Migration and Transfer category.[^s9]
- **Related to the 7 Rs migration strategies** (`kt:seven-rs-migration-strategies`) as the rehost tool, to **AWS Transform** (`kt:aws-transform`), which uses it as its replication engine, and to **AWS Elastic Disaster Recovery** (`kt:aws-elastic-disaster-recovery`), whose agent cannot coexist with it.[^s1][^s3][^s6]
- **Depends on Amazon EC2** (`kt:amazon-ec2`): replicated servers are launched as EC2 instances with EBS volumes.[^s2][^s7]
- **Alternative to AWS Database Migration Service** (`kt:aws-database-migration-service`) for database workloads.[^s7]
- **Governed by MGN quotas** (`kt:mgn-quotas`) and **scoped to AWS Regions** (`kt:aws-regions`), with quotas set per Region.[^s5]
- **Monitored by Amazon CloudWatch** (`kt:amazon-cloudwatch`) and **integrates with Amazon EventBridge** (`kt:amazon-eventbridge`), which receive its metrics and replication and launch events.[^s3]
- **Covered by AWS Certified Cloud Practitioner** (`kt:aws-certified-cloud-practitioner`), listed under its former name.[^s9]

## Sources
[^s1]: Amazon Web Services, What's New with AWS, "AWS Application Migration Service is now AWS Transform MGN". https://aws.amazon.com/about-aws/whats-new/2026/06/aws-transform-mgn-rebrand/ (accessed 2026-11-09)
[^s2]: Amazon Web Services, "AWS Transform MGN FAQs". https://aws.amazon.com/application-migration-service/faqs/ (accessed 2026-11-09)
[^s3]: Amazon Web Services, "General questions - AWS Transform MGN". https://docs.aws.amazon.com/mgn/latest/ug/General-Questions-FAQ.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Network requirements for MGN - AWS Transform MGN". https://docs.aws.amazon.com/mgn/latest/ug/preparing-environments.html (accessed 2026-11-09)
[^s5]: Amazon Web Services, "AWS Transform MGN endpoints and quotas - AWS General Reference". https://docs.aws.amazon.com/general/latest/gr/mgn.html (accessed 2026-11-09)
[^s6]: Amazon Web Services, AWS Prescriptive Guidance, "Migration with AWS Application Migration Service". https://docs.aws.amazon.com/prescriptive-guidance/latest/migration-database-rehost-tools/mgn.html (accessed 2026-11-09)
[^s7]: Amazon Web Services, "AWS Transform MGN and AWS Database Migration Service (DMS) - AWS Product Comparison". https://aws.amazon.com/compare/dms-and-transform-mgn/ (accessed 2026-11-09)
[^s8]: Amazon Web Services, "AWS Transform MGN - Pricing". https://aws.amazon.com/application-migration-service/pricing/ (accessed 2026-11-09)
[^s9]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html (accessed 2026-11-09)
