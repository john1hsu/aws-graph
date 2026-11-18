---
id: kt:aws-application-discovery-service
type: Service
title: AWS Application Discovery Service
aliases: [Application Discovery Service, AWS ADS, Discovery Agent, Application Discovery Service Agentless Collector]
domain: migration-and-hybrid
summary: >-
  AWS's migration-planning service that collects configuration, utilization and network-connection
  data about on-premises servers and databases through an agentless vCenter collector, per-server
  agents or file imports, and surfaces it in AWS Migration Hub; it closed to new customers on
  2025-11-07, with AWS Transform as the recommended path for new discovery work.
verified: 2026-11-18
status: closed-to-new-customers
broader: [kt:aws-migration-and-transfer-services]
related: [kt:application-portfolio-assessment, kt:migration-wave-planning, kt:vmware-migration-to-aws]
relations:
  - {predicate: alternativeTo, object: kt:aws-transform}
  - {predicate: integratesWith, object: kt:aws-migration-hub}
  - {predicate: integratesWith, object: kt:aws-database-migration-service}
  - {predicate: integratesWith, object: kt:amazon-athena}
  - {predicate: governedBy, object: kt:application-discovery-service-quotas}
sources:
  - id: s1
    title: "What is AWS Application Discovery Service?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/application-discovery/latest/userguide/what-is-appdiscovery.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "AWS Application Discovery Service availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/application-discovery/latest/userguide/application-discovery-service-availability-change.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "AWS Application Discovery Service quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/he_il/application-discovery/latest/userguide/ads_service_limits.md
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "AWS Application Discovery Agent"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/application-discovery/latest/userguide/discovery-agent.html
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Application Discovery Service Agentless Collector"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/application-discovery/latest/userguide/agentless-collector.html
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "Exploring data in Amazon Athena"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/application-discovery/latest/userguide/explore-data.html
    type: official-docs
    accessed: 2026-11-18
  - id: s7
    title: "AWS Application Discovery Service (Discovery tools for migration)"
    publisher: AWS Prescriptive Guidance
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/migration-tools/discovery-app-discovery-service.html
    type: official-docs
    accessed: 2026-11-18
  - id: s8
    title: "AWS Migration Hub availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/migrationhub/latest/ug/migrationhub-availability-change.html
    type: official-docs
    accessed: 2026-11-18
  - id: s9
    title: "AWS Application Discovery Service introduces Amazon EC2 recommendations"
    publisher: What's New with AWS
    url: https://aws.amazon.com/about-aws/whats-new/2023/06/aws-application-discovery-service-amazon-ec2-recommendations
    type: official-blog
    accessed: 2026-11-18
created: 2026-11-18
---

# AWS Application Discovery Service

AWS Application Discovery Service (ADS) is the inventory-and-telemetry layer of a classic AWS migration. It gathers configuration, utilization and network-connection data about on-premises servers and databases so teams can size targets, map dependencies and group servers into applications. It works with AWS Migration Hub, where discovered servers are viewed and grouped, and with AWS DMS Fleet Advisor for database workloads.[^s1]

**Lifecycle (as of 2026-11):** ADS stopped accepting new customers on 7 November 2025. Existing customers can keep using it as normal. AWS will maintain security and reliability but add no new features. AWS points new discovery projects to AWS Transform, and moving there requires no data migration.[^s2] Migration Hub, ADS's console front end, closed to new customers on the same date, also with AWS Transform as the successor.[^s8]

## How it works

ADS offers three ways to collect data. All of them land in your **Migration Hub home Region**. You must set that Region before discovering anything, and it is the only Region where ADS stores discovery data.[^s1]

- **Agentless Collector.** An OVA appliance that you deploy into VMware vCenter. It inventories VMs and hosts without touching each guest. Its modules collect VMware data, network traffic between servers, and database and analytics metadata. The database data feeds DMS for target recommendations.[^s5] It profiles Oracle, SQL Server, MySQL and PostgreSQL, and polls roughly every 60 minutes.[^s1]
- **Discovery Agent.** Installed on each Windows or Linux server, whether physical, virtual or EC2. It needs root privileges. It samples about every 15 seconds and sends data to AWS over TLS every 15 minutes.[^s4] Only the agent captures running processes and exports time-series data. It is also the only method that supports Athena exploration.[^s1]
- **File import.** You can import a spreadsheet of server details straight into Migration Hub. AWS Partner tools can also write to ADS through its public API.[^s1]

With data exploration turned on (from the Migration Hub console or the `StartContinuousExport` API), agent data streams into an S3 bucket in your account on a schedule. From there you can run predefined Amazon Athena queries on performance, processes and network dependencies, or join the data with CMDB exports.[^s6] Since June 2023, an API has used configuration and CPU/memory utilization to recommend the cheapest EC2 instance type that fits each server.[^s9]

## Quotas, pricing and gotchas

ADS quotas are per account. Of those listed, only the 10,000 imported servers can be raised. The others are fixed:[^s3]

- 1,000 applications
- 400 servers per application
- 25,000 imported server records
- 25,000 import-record deletions per day
- 10 MB per import file
- 1,000 active agents
- 10,000 inactive agents
- 30 tags per server[^s3]

AWS Prescriptive Guidance lists ADS as having no charge (checked 2026-11). Downstream services you turn on, such as S3 and Athena, have their own pricing.[^s7][^s6]

Gotchas:

- The Agentless Collector cannot see inside guests, so it collects no process data. Teams often add agents to the VMs whose dependencies matter.[^s1]
- Prescriptive Guidance notes that ADS maps dependencies only at the server level, not the code or process level. It also cannot pin discovered data to a Region of your choice for sovereignty.[^s7]
- Accounts that never signed up before the cutoff cannot start using ADS.[^s2]

## When to use it (and when not)

Use ADS only if your account already uses it and has a discovery project in flight. AWS says such projects usually run for about four months and can continue to completion.[^s2] For new portfolio assessments, AWS Transform offers agent-based and agentless collectors, VMware analysis, dependency mapping, wave planning and what-if cost analysis.[^s2] If you only need database sizing, the DMS integration covers that slice.[^s5]

## Connections

ADS falls under AWS migration and transfer services. It is the data-gathering step behind application portfolio assessment and migration wave planning.[^s1] It **integrates with** AWS Migration Hub, which is its console and home-Region anchor, and with AWS DMS for database recommendations.[^s1][^s5] It also integrates with Amazon Athena through continuous export.[^s6] It is an **alternative to** AWS Transform, which AWS names as the successor for new customers.[^s2] It is **governed by** its own per-account quotas.[^s3] Its agentless collector targets VMware estates, which ties it to VMware migration to AWS.[^s5]

## Sources

[^s1]: Amazon Web Services, "What is AWS Application Discovery Service?". https://docs.aws.amazon.com/application-discovery/latest/userguide/what-is-appdiscovery.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "AWS Application Discovery Service availability change". https://docs.aws.amazon.com/application-discovery/latest/userguide/application-discovery-service-availability-change.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "AWS Application Discovery Service quotas". https://docs.aws.amazon.com/he_il/application-discovery/latest/userguide/ads_service_limits.md (accessed 2026-11-18)
[^s4]: Amazon Web Services, "AWS Application Discovery Agent". https://docs.aws.amazon.com/application-discovery/latest/userguide/discovery-agent.html (accessed 2026-11-18)
[^s5]: Amazon Web Services, "Application Discovery Service Agentless Collector". https://docs.aws.amazon.com/application-discovery/latest/userguide/agentless-collector.html (accessed 2026-11-18)
[^s6]: Amazon Web Services, "Exploring data in Amazon Athena". https://docs.aws.amazon.com/application-discovery/latest/userguide/explore-data.html (accessed 2026-11-18)
[^s7]: AWS Prescriptive Guidance, "AWS Application Discovery Service". https://docs.aws.amazon.com/prescriptive-guidance/latest/migration-tools/discovery-app-discovery-service.html (accessed 2026-11-18)
[^s8]: Amazon Web Services, "AWS Migration Hub availability change". https://docs.aws.amazon.com/migrationhub/latest/ug/migrationhub-availability-change.html (accessed 2026-11-18)
[^s9]: What's New with AWS, "AWS Application Discovery Service introduces Amazon EC2 recommendations" (2023-06-21). https://aws.amazon.com/about-aws/whats-new/2023/06/aws-application-discovery-service-amazon-ec2-recommendations (accessed 2026-11-18)
