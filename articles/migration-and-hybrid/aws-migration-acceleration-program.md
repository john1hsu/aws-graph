---
id: kt:aws-migration-acceleration-program
type: Concept
title: AWS Migration Acceleration Program
aliases: [Migration Acceleration Program, AWS MAP, MAP 2.0, MAP for Windows, MAP for VMware]
domain: migration-and-hybrid
summary: >-
  AWS's commercial program for large enterprise migrations, which packages a three-phase
  methodology (assess, mobilize, migrate and modernize), partner and AWS Professional Services
  support, tooling and training with AWS investments — service credits or partner funding — earned
  by tagging migrated resources with the map-migrated tag.
verified: 2026-11-13
status: current
broader: [kt:aws-migration-and-transfer-services]
related: [kt:seven-rs-migration-strategies, kt:aws-cloud-adoption-framework, kt:aws-transform, kt:vmware-migration-to-aws]
relations:
  - {predicate: integratesWith, object: kt:aws-transform-mgn}
  - {predicate: dependsOn, object: kt:cost-allocation-tags}
  - {predicate: monitoredBy, object: kt:aws-cost-explorer}
  - {predicate: enables, object: kt:migration-wave-planning}
sources:
  - id: s1
    title: "AWS Migration Acceleration Program (MAP)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/map/
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Getting started - AWS Migration Acceleration Program 2.0 Tagging Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/MAP/latest/userguide/getting-started.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "Tagging key combinations - AWS Migration Acceleration Program 2.0 Tagging Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/MAP/latest/userguide/setting-up.html
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "Migration Acceleration Program (MAP) - AWS Migration Hub launch guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/migrationhub/latest/launchguide/map.html
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "Announcing streamlined Migration Acceleration Program (MAP) funding and approval process in AWS Partner Central"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2024/07/streamlined-map-funding-approval-aws-partner-central
    type: official-blog
    accessed: 2026-11-13
  - id: s6
    title: "AWS Migration Acceleration Program for VMware"
    publisher: Amazon Web Services (AWS Migration & Modernization Blog)
    url: https://aws.amazon.com/blogs/migration-and-modernization/aws-migration-acceleration-program-for-vmware
    type: official-blog
    accessed: 2026-11-13
created: 2026-11-13
---

# AWS Migration Acceleration Program

The **AWS Migration Acceleration Program (MAP)** is not a service you call through an API; it is AWS's commercial program for enterprise migrations. AWS describes it as a methodology built from thousands of enterprise migrations that bundles tools, training, partner expertise and financial investment, organized into three phases.[^s1] The money is the part practitioners most often meet in the console: AWS investments arrive as service credits or partner funding meant to offset one-time migration costs for qualified migrations.[^s6] Those credits are calculated from resources tagged `map-migrated`.[^s2]

## How it works: three phases

- **Assess** — find capability gaps across the six perspectives of the AWS Cloud Adoption Framework (business, people, process, platform, operations and security) and build a realistic total-cost-of-ownership model for the migration.[^s1]
- **Mobilize** — close those gaps, build the operational foundation, and settle migration strategies so that decisions get made faster.[^s1]
- **Migrate and modernize** — execute the plan with AWS migration services, supported by AWS Professional Services and AWS Migration Competency Partners.[^s1]

AWS also runs specialized tracks on the same framework: Microsoft Windows workloads (rehost to Amazon EC2 or modernize to open-source operating systems and databases to cut licensing), VMware, SAP (including planning an SAP RISE migration), and mainframe modernization with its own partner network and incentives.[^s1] The VMware track, announced in August 2024, adds no-cost Optimization and Licensing Assessments, AWS-provided licences for validated partner migration tools on qualified migrations, and a training catalogue of over 500 courses.[^s6]

Partners engage through AWS Partner Central. A July 2024 change introduced a MAP template there for partner-led migrations up to $10M in annual recurring revenue, with fewer approval stages and automatically created claim milestones for Mobilize-phase funding; it applies to partners at the Validated stage and to Migration Competency Partners.[^s5]

## Tagging: how credits are earned (MAP 2.0)

MAP 2.0 identifies migrated workloads by tags and uses them to report spend and calculate incentives; AWS states it cannot provide incentives if migrated resources are untagged.[^s2] The rules, as of 2026-11:

- **Key:** `map-migrated`. It is automatically activated as a cost allocation tag and doesn't count against your cost allocation tag quota. Cost Explorer must be enabled.[^s2]
- **Value:** a lowercase prefix plus your Migration Program Engagement (MPE) ID from the MAP agreement — `mig` for general migrations, `sap` for SAP and `oracle` for Oracle workloads. The MPE ID is either 5 digits or 10 uppercase alphanumeric characters, giving values like `mig12345` or `migABCDE12345`.[^s3]
- **Choosing the value** depends on the destination service, the database licence agreement, whether the source is on premises or already on Amazon EC2, and whether the workload is on the specialized SAP/Oracle list.[^s3]
- **Applying it:** in CloudFormation or Terraform templates, one resource at a time in the console, or in bulk with Tag Editor.[^s2] AWS's best practices add enforcing tags with cfn-lint, enabling the "Add MAP tag to Launched Instances" option in the rehost service so EC2 instances and EBS volumes are tagged at launch, and using AWS Config rules with EventBridge to catch untagged resources.[^s4]

## Gotchas

- **Untagged spend earns nothing.** Build a Cost Explorer report of untagged, MAP-eligible spend, excluding ineligible services and data transfer, and check it each wave.[^s4]
- **Only listed services count.** Eligibility is defined by AWS's MAP 2.0 included-services list, not by everything in the account.[^s3][^s4]
- **Dashboard timing.** Agreements signed after November 2022 get the MAP 2.0 dashboard in the Billing console, but it appears only after the first tranche of credits.[^s4]
- **Tooling moved.** AWS Migration Hub stopped accepting new customers on 7 November 2025, and AWS points new users to AWS Transform instead; MAP itself continues.[^s4]
- AWS's headline figures (an average of 31% infrastructure savings, 69% less unplanned downtime) are AWS-reported customer outcomes, not guarantees.[^s1]

## When to use it

MAP fits a large, committed migration of many workloads where AWS or a partner will run the assess–mobilize–migrate cadence and where credits materially offset dual-running costs. For a handful of servers, the migration services themselves (and the 7 Rs decision) are enough; there is no MAP agreement, MPE ID or tag to manage.[^s1][^s2]

## Connections

- **Broader:** MAP is the commercial and methodological wrapper around AWS migration and transfer services (`kt:aws-migration-and-transfer-services`).[^s1]
- **Related:** the Assess phase is organized by the AWS Cloud Adoption Framework (`kt:aws-cloud-adoption-framework`), and Mobilize settles the per-workload 7 Rs strategies (`kt:seven-rs-migration-strategies`).[^s1] AWS Transform (`kt:aws-transform`) is where AWS directs former Migration Hub users.[^s4] VMware migration (`kt:vmware-migration-to-aws`) has its own MAP track.[^s6]
- **Integrates with** AWS Transform MGN (`kt:aws-transform-mgn`), whose launch setting adds the MAP tag to replicated instances and volumes.[^s4]
- **Depends on** cost allocation tags (`kt:cost-allocation-tags`) and is **monitored by** AWS Cost Explorer (`kt:aws-cost-explorer`), which reports tagged and untagged spend.[^s2][^s4]
- **Enables** migration wave planning (`kt:migration-wave-planning`) as part of the Mobilize phase.[^s1]

## Sources
[^s1]: Amazon Web Services, "AWS Migration Acceleration Program (MAP)". https://aws.amazon.com/map/ (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Getting started - AWS Migration Acceleration Program 2.0 Tagging Guide". https://docs.aws.amazon.com/MAP/latest/userguide/getting-started.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Tagging key combinations - AWS Migration Acceleration Program 2.0 Tagging Guide". https://docs.aws.amazon.com/MAP/latest/userguide/setting-up.html (accessed 2026-11-13)
[^s4]: Amazon Web Services, "Migration Acceleration Program (MAP) - AWS Migration Hub launch guide". https://docs.aws.amazon.com/migrationhub/latest/launchguide/map.html (accessed 2026-11-13)
[^s5]: Amazon Web Services, "Announcing streamlined Migration Acceleration Program (MAP) funding and approval process in AWS Partner Central" (2024-07-01). https://aws.amazon.com/about-aws/whats-new/2024/07/streamlined-map-funding-approval-aws-partner-central (accessed 2026-11-13)
[^s6]: Samantha Westien and Kate Behbehani, Amazon Web Services, "AWS Migration Acceleration Program for VMware" (2024-08-16). https://aws.amazon.com/blogs/migration-and-modernization/aws-migration-acceleration-program-for-vmware (accessed 2026-11-13)
