---
id: kt:amazon-rds
type: Service
title: Amazon Relational Database Service
aliases: [Amazon RDS, RDS]
domain: databases
summary: >-
  AWS's managed service for running relational database engines (Db2, MariaDB, SQL Server, MySQL,
  Oracle and PostgreSQL) on DB instances, with AWS handling provisioning, patching, backups,
  Multi-AZ high availability and read replicas.
verified: 2026-11-09
status: current
broader: [kt:purpose-built-databases]
related: [kt:amazon-aurora, kt:rds-proxy]
relations:
  - {predicate: governedBy, object: kt:rds-quotas}
  - {predicate: alternativeTo, object: kt:amazon-dynamodb}
  - {predicate: pricedBy, object: kt:database-savings-plans}
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: dependsOn, object: kt:amazon-vpc}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is Amazon Relational Database Service (Amazon RDS)?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "Quotas and constraints for Amazon RDS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Working with DB instance read replicas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Amazon RDS FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/rds/faqs/
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "Announcing Database Savings Plans with up to 35% savings"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/database-savings-plans-savings/
    type: official-blog
    accessed: 2026-11-09
  - id: s6
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services (AWS Certification)
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# Amazon Relational Database Service

Amazon Relational Database Service (Amazon RDS) is AWS's managed service for setting up, operating and scaling relational databases. It supports six engines: IBM Db2, MariaDB, Microsoft SQL Server, MySQL, Oracle Database and PostgreSQL. Amazon Aurora is part of the same RDS family but is documented in its own user guide.[^s1] AWS takes on scaling, high availability, backups, database and operating-system patching, server maintenance and the hardware lifecycle. You remain responsible for schema design, query tuning and application optimization.[^s1]

## How it works

The basic building block is the **DB instance**: an isolated database environment that can hold one or more databases and runs inside an Amazon VPC at no extra charge.[^s1] Its **instance class** sets compute and memory. Families include general purpose (`db.m*`), memory optimized (`db.r*`, `db.x*`, `db.z*`), compute optimized (`db.c*`) and burstable (`db.t*`), and you can resize later, for example from `db.m7g.2xlarge` to `db.m7g.4xlarge`.[^s1] Storage is General Purpose SSD or Provisioned IOPS SSD. Magnetic storage is deprecated: it is no longer offered for new instances, and from 2026-07-01 snapshots can't be restored to it.[^s1] You can grow allocated storage without losing availability.[^s4]

High availability comes in two shapes. A **Multi-AZ DB instance** keeps a synchronous standby in another Availability Zone that does not serve reads. A **Multi-AZ DB cluster** runs one writer and two readers across three Availability Zones, and all three can serve reads.[^s1] AWS says Multi-AZ failovers typically finish within one to two minutes.[^s4]

For read scaling, **read replicas** use each engine's built-in *asynchronous* replication, so replicas can lag the source. All six engines support them, including across Regions.[^s3] MariaDB, MySQL and some PostgreSQL versions allow a replica of a replica; Db2, Oracle and SQL Server do not. Oracle's mounted replicas and Db2's standby replicas accept no user connections and are meant for cross-Region disaster recovery.[^s3] A replica can be promoted to a standalone instance, and deleting a source without its same-Region replicas promotes them automatically.[^s3]

**Backups:** automated backups are kept for 7 days by default and up to 35 days. Setting retention to 0 turns them off. Manual snapshots are kept until you delete them.[^s1][^s4]

## Quotas, pricing and gotchas

Default quotas per account per Region (as of 2026-11, adjustable unless noted): 40 DB instances (20 in ap-south-1), and that count is shared with Aurora, Neptune and DocumentDB instances. Other defaults are 100 manual DB instance snapshots, 15 read replicas per primary, 40 reserved DB instances, 50 parameter groups, 20 proxies and 100,000 GB total storage across all DB instances.[^s2] License-included SQL Server (per edition) and license-included Oracle are each limited to 10 instances within that quota. AWS suggests keeping Oracle to 5 replicas per source to limit lag. Subnets per DB subnet group (20) cannot be raised.[^s2]

Billing is per DB instance hour, charged in one-second increments with a 10-minute minimum after a billable status change, plus storage, I/O where it applies, and backup storage.[^s4] Backup storage up to your total provisioned database storage in a Region is free; beyond that you pay a daily prorated rate.[^s4] **Reserved DB instances** give a lower rate for a one- or three-year term. They must match engine, class, Multi-AZ setting, license model and Region, and they can't be cancelled.[^s4] **Database Savings Plans** launched on 2025-12-02. They offer up to 35% off for a one-year, no-upfront $/hour commitment. The discount applies across engines, instance families, sizes, deployment options and Regions, including RDS, Aurora, DynamoDB and other AWS databases, in every Region except China.[^s5] The free tier covers `db.t3.micro` or `db.t4g.micro` Single-AZ instances of MariaDB, MySQL, PostgreSQL or SQL Server Express.[^s1]

Gotchas: a Multi-AZ standby adds cost but no read capacity, so add replicas if you need reads.[^s1][^s3] Replica lag means reads after writes may be stale.[^s3] Scaling compute and engine upgrades can take an instance offline, and you get a 30-minute weekly maintenance window by default.[^s4]

## When to use it (and when not)

Choose RDS when an application needs a standard relational engine, SQL, joins and transactions, and you don't want to run the servers. It also suits lifting existing commercial databases (Oracle, SQL Server, Db2) into a managed service.[^s1] Aurora, documented separately, is the other relational option in the family. Consider DynamoDB when access patterns are known, the scale is very large, and joins aren't needed.[^s1][^s5] For Lambda functions, AWS recommends putting RDS Proxy in front of the database to pool connections.[^s4]

## Connections

- **Broader:** purpose-built databases. RDS is AWS's managed relational option in that portfolio.[^s1]
- **Related:** Amazon Aurora (same service family, separate guide) and RDS Proxy (connection pooling, recommended with Lambda).[^s1][^s4]
- **Governed by RDS quotas** (`kt:rds-quotas`).[^s2]
- **Alternative to Amazon DynamoDB** for operational data.[^s1]
- **Priced by Database Savings Plans** as well as reserved DB instances.[^s4][^s5]
- **Scoped to Availability Zones**: an instance lives in one AZ, and Multi-AZ deployments span two or three.[^s1]
- **Depends on Amazon VPC** for networking.[^s1]
- **Covered by AWS Certified Cloud Practitioner**: Amazon RDS is in the CLF-C02 in-scope Database services.[^s6]

## Sources
[^s1]: Amazon Web Services, "What is Amazon Relational Database Service (Amazon RDS)?". https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html (accessed 2026-11-09)
[^s2]: Amazon Web Services, "Quotas and constraints for Amazon RDS". https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Working with DB instance read replicas". https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Amazon RDS FAQs". https://aws.amazon.com/rds/faqs/ (accessed 2026-11-09)
[^s5]: Amazon Web Services, What's New, "Announcing Database Savings Plans with up to 35% savings" (2025-12-02). https://aws.amazon.com/about-aws/whats-new/2025/12/database-savings-plans-savings/ (accessed 2026-11-09)
[^s6]: Amazon Web Services, AWS Certification, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html (accessed 2026-11-09)
