---
id: kt:aws-regions
type: InfrastructureConcept
title: AWS Regions
aliases: [AWS Region, Region, Regions, AWS Regions and Availability Zones]
domain: global-infrastructure
summary: >-
  A Region is a separate geographic area where AWS clusters data centers into at least three
  isolated Availability Zones; Regions are independent fault-isolation boundaries, belong to one
  partition, and newer (opt-in) Regions must be enabled per account before use.
verified: 2026-11-08
status: concept
broader: [kt:aws-global-infrastructure]
related: [kt:edge-locations, kt:disaster-recovery-strategies]
relations:
  - {predicate: hasPart, object: kt:availability-zones}
  - {predicate: partOf, object: kt:aws-partitions}
  - {predicate: configuredVia, object: kt:aws-account-management}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "AWS Regions - AWS Regions and Availability Zones"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ja_jp/global-infrastructure/latest/regions/aws-regions.html
    type: official-docs
    accessed: 2026-11-08
  - id: s2
    title: "Enable or disable AWS Regions in your account - AWS Account Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/accounts/latest/reference/manage-acct-regions.html
    type: official-docs
    accessed: 2026-11-08
  - id: s3
    title: "AWS Global Infrastructure - Regions and Availability Zones"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/global-infrastructure/regions%5Faz/
    type: official-docs
    accessed: 2026-11-08
  - id: s4
    title: "Regions - AWS Fault Isolation Boundaries (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/regions.html
    type: official-docs
    accessed: 2026-11-08
  - id: s5
    title: "REL10-BP01 Deploy the workload to multiple locations - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_fault_isolation_multiaz_region_system
    type: official-docs
    accessed: 2026-11-08
  - id: s6
    title: "Opening the AWS European Sovereign Cloud"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/opening-the-aws-european-sovereign-cloud
    type: official-blog
    accessed: 2026-11-08
  - id: s7
    title: "Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html
    type: official-learning
    accessed: 2026-11-08
created: 2026-11-08
---

# AWS Regions

An AWS Region is a physical location where AWS clusters its data centers. Every Region is made up of a minimum of three isolated, physically separate Availability Zones (AZs), each being one or more discrete data centers with redundant power, networking and connectivity.[^s3] AZs in a Region sit many kilometers apart but within 100 km (60 miles) of each other, close enough for low-latency synchronous replication.[^s3][^s5] Choosing a Region is usually the first deployment decision: it determines which services and features are available, the latency your users see, and where your data physically lives for regulatory purposes.[^s1]

## How it works

Regions are designed as **fault-isolation boundaries**. Their physical infrastructure and software services are independent of other Regions (with a few documented exceptions), so a service failure is contained to one Region. Resources and data you create in one Region do not exist in any other unless you use a service's replication or copy feature or replicate them yourself.[^s4] The Regions are linked by the AWS Global Network, which AWS describes as more than 9 million kilometers of terrestrial and subsea fiber.[^s4]

Every Region belongs to exactly one **partition**: `aws` for commercial Regions, `aws-cn` for Beijing and Ningxia, and `aws-us-gov` for AWS GovCloud (US-West) and (US-East). Partitions run separate IAM instances and form a hard boundary, so a standard account cannot reach GovCloud or China Regions.[^s2][^s1] The AWS European Sovereign Cloud added another partition, `aws-eusc`; its first Region, `eusc-de-east-1` in Brandenburg, Germany, became generally available on 14 January 2026 and runs physically and logically separate from other AWS Regions.[^s6]

As of 2026-11, AWS's global infrastructure page counts 39 geographic Regions and 124 Availability Zones, with two more Regions (Saudi Arabia and Chile) and seven more AZs announced.[^s3] The Regions documentation table lists the 34 Regions open to standard commercial accounts; most have three AZs, while US East (N. Virginia) has six and Oregon, Seoul and Tokyo have four.[^s1]

## Opt-in Regions, quotas and gotchas

Regions launched before 20 March 2019 are **enabled by default** and cannot be disabled (17 of them, including `us-east-1`, `eu-west-1` and `ap-northeast-1`). Regions launched after that date are **opt-in**: they are disabled until you enable them and do not appear in the console's Region selector (17 as of 2026-11, including `af-south-1`, `ap-southeast-6` New Zealand, `ca-west-1` and `mx-central-1`).[^s2][^s1] Enabling or disabling a Region is free.[^s2]

Enabling propagates IAM data and credentials into the Region and usually takes minutes but can take several hours; an `ENABLED` status does not guarantee every service is ready, and an in-progress request cannot be cancelled.[^s2] One account can have six Region-opt requests in flight at once.[^s2] **Disabling** deactivates IAM access to the Region's resources but does not delete them, so they keep billing; remove them first.[^s2]

Other gotchas:[^s2]
- STS session tokens from the global endpoint are valid only in enabled Regions; regional STS endpoints issue tokens valid everywhere.
- Organizations can enable Regions for member accounts from the management or a delegated admin account, which requires all features and trusted access for AWS Account Management.
- The `aws:RequestedRegion` condition key can restrict which Regions principals may use.
- Amazon Bedrock and Amazon Q Developer cross-Region inference can route requests to any Region in the chosen geography, even ones you have not enabled.

New accounts can access only two AZs in US West (N. California).[^s1]

## When to use one Region or several

The Well-Architected reliability pillar sets running production across at least two AZs in one Region as the baseline, and treats multi-Region active deployments as justified only for extreme resilience needs where data-residency rules allow; using multiple Regions when multi-AZ suffices is listed as an anti-pattern.[^s5] Multi-Region designs must duplicate every component, avoid cross-Region dependencies, and replicate data (for example DynamoDB global tables or read replicas), while pilot-light or warm-standby DR in a second Region is a cheaper middle ground.[^s5] The Cloud Practitioner exam frames the reasons for multiple Regions as disaster recovery, business continuity, low latency for end users and data sovereignty.[^s7]

## Connections

- **Broader: AWS global infrastructure** (`kt:aws-global-infrastructure`), alongside **edge locations** (`kt:edge-locations`), which the CLF-C02 guide groups with Regions and AZs.[^s7][^s3]
- **Has part: Availability Zones** (`kt:availability-zones`), at least three per Region.[^s3][^s4]
- **Part of an AWS partition** (`kt:aws-partitions`), the hard boundary for IAM and account access.[^s2]
- **Configured via AWS Account Management** (`kt:aws-account-management`), whose Region-opt APIs enable and disable opt-in Regions.[^s2]
- **Related to disaster recovery strategies** (`kt:disaster-recovery-strategies`) such as pilot light and warm standby across Regions.[^s5]
- **Covered by the AWS Certified Cloud Practitioner** (`kt:aws-certified-cloud-practitioner`) under task 3.2, "Define the AWS global infrastructure".[^s7]
- Many services are **scoped to a Region**, among them Amazon EC2, Amazon S3 and Amazon CloudWatch in this graph.[^s4]

## Sources
[^s1]: Amazon Web Services, "AWS Regions - AWS Regions and Availability Zones". https://docs.aws.amazon.com/ja_jp/global-infrastructure/latest/regions/aws-regions.html (accessed 2026-11-08)
[^s2]: Amazon Web Services, "Enable or disable AWS Regions in your account". https://docs.aws.amazon.com/accounts/latest/reference/manage-acct-regions.html (accessed 2026-11-08)
[^s3]: Amazon Web Services, "AWS Global Infrastructure - Regions and Availability Zones". https://aws.amazon.com/about-aws/global-infrastructure/regions%5Faz/ (accessed 2026-11-08)
[^s4]: Amazon Web Services, "Regions - AWS Fault Isolation Boundaries". https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/regions.html (accessed 2026-11-08)
[^s5]: Amazon Web Services, "REL10-BP01 Deploy the workload to multiple locations". https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_fault_isolation_multiaz_region_system (accessed 2026-11-08)
[^s6]: Amazon Web Services, AWS News Blog, "Opening the AWS European Sovereign Cloud". https://aws.amazon.com/blogs/aws/opening-the-aws-european-sovereign-cloud (accessed 2026-11-08)
[^s7]: Amazon Web Services, "Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html (accessed 2026-11-08)
