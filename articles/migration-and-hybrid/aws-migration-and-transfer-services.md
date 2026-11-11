---
id: kt:aws-migration-and-transfer-services
type: Concept
title: AWS migration and transfer services
aliases: [AWS Migration and Transfer, Migration and Transfer category, AWS migration services, migration and transfer services on AWS]
domain: migration-and-hybrid
summary: >-
  The Migration and Transfer category of the AWS portfolio: services for assessing an on-premises estate,
  moving servers, databases and mainframe workloads to AWS, and transferring data online or offline, now
  increasingly organized around AWS Transform after Migration Hub and Snowball Edge closed to new customers.
verified: 2026-11-11
status: concept
broader: [kt:amazon-web-services]
related: [kt:seven-rs-migration-strategies, kt:aws-direct-connect]
relations:
  - {predicate: hasPart, object: kt:aws-transform-mgn}
  - {predicate: hasPart, object: kt:aws-database-migration-service}
  - {predicate: hasPart, object: kt:aws-datasync}
  - {predicate: hasPart, object: kt:aws-transform}
  - {predicate: hasPart, object: kt:aws-transfer-family}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Migration and transfer - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/migration-services.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Choosing AWS migration services and tools"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/migration-on-aws-how-to-choose/migration-on-aws-how-to-choose.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "AWS Migration Hub availability change - AWS Migration Hub"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/migrationhub/latest/ug/migrationhub-availability-change.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "AWS Snowball Edge availability change - AWS Snowball Edge Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/snowball/latest/developer-guide/snowball-edge-availability-change.html
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "AWS Data Transfer Terminal pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/data-transfer-terminal/pricing
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "AWS Transform for VMware is now generally available"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/05/aws-transform-vmware-generally-available
    type: official-blog
    accessed: 2026-11-11
  - id: s7
    title: "In-Scope AWS Services - AWS Certification (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# AWS migration and transfer services

"Migration and transfer" is a category of the AWS portfolio, not a single product. It groups the services that help you discover what runs on premises, move servers, databases and mainframe workloads into AWS, and copy data into and out of AWS storage. The AWS overview whitepaper lists AWS Application Discovery Service, AWS Transform MGN, AWS Database Migration Service (AWS DMS), AWS Mainframe Modernization, AWS Migration Hub, the AWS Snow Family, AWS DataSync and AWS Transfer Family in this category.[^s1]

## How the category is organized

AWS's decision guide for migration tools (last updated October 2025) arranges the work into four groups:[^s2]

- **Assess and mobilize:** inventory assets and dependencies, build the business case and prepare landing zones. Tools include Experience-Based Acceleration, the Migration Acceleration Program (MAP), Optimization and Licensing Assessment, and AWS Transform.[^s2]
- **Migrate applications and databases:** rehost, replatform or re-architect workloads with AWS Transform MGN, AWS DMS with DMS Schema Conversion, and AWS Transform.[^s2]
- **Hybrid cloud storage:** keep on-premises applications connected to cloud storage during the transition with AWS Storage Gateway and AWS Direct Connect.[^s2]
- **Online data transfer:** move data over the network with AWS DataSync, AWS Transfer Family and Amazon S3 Transfer Acceleration.[^s2]

The same guide lists the questions to ask before choosing: whether you have an asset inventory, which migration strategy each workload follows, whether some workloads will stay on premises, and what your data transfer needs are. It also weighs downtime tolerance, cost, compliance, bandwidth and data volume, database and application compatibility, and dependencies that set migration order.[^s2]

## The shift to AWS Transform

The category's tracking and planning layer has moved. AWS Migration Hub closed to new customers on November 7, 2025. Existing customers can finish current projects with continued security updates but no new features, and AWS points new projects to AWS Transform, launched in May 2025. AWS Transform takes over Migration Hub capabilities such as Strategy Recommendations, EC2 instance recommendations, Journeys and Orchestrator.[^s3] AWS Transform for VMware became generally available on May 15, 2025. It uses agentic AI to cover discovery, dependency mapping, network translation and wave planning, with human review before execution. AWS reports that in testing it planned waves for 500 VMs in about 15 minutes.[^s6] AWS Application Migration Service, the rehosting engine, now carries the name AWS Transform MGN.[^s1]

## Offline transfer after Snowball Edge

AWS Snowball Edge is no longer available to new customers, and AWS no longer offers any Snow Family device for new customers to order. Existing users are unaffected. AWS recommends DataSync for online transfers. For offline transfers it recommends AWS Data Transfer Terminals (physical sites where you bring your own storage devices), Marketplace partner offerings, and AWS Outposts for edge compute.[^s4]

Data Transfer Terminals are billed per port hour, with a one-port-hour minimum per reservation, and no charge based on data volume. Each terminal has at least two 100 GbE ports. As of 2026-11, a port hour costs USD 300 when both the terminal and the AWS endpoint are in North America (or both in Europe), and USD 500 from North America to Europe or from Europe to a non-European endpoint. Asia-Pacific and Australia rates are higher.[^s5]

## Gotchas

- Study material written before late 2025 still teaches Migration Hub and Snowball as the default answers. For new customers, plan around AWS Transform and Data Transfer Terminals or DataSync instead.[^s3][^s4]
- Service names in this category change often. The CLF-C02 in-scope list still uses "AWS Application Migration Service" for what is now AWS Transform MGN, and it still includes Migration Hub and the Snow Family.[^s7][^s1]
- A Data Transfer Terminal reservation is charged for the hours you reserve, and routes that cross continents cost more. Book a terminal on the same continent as the destination Region where you can.[^s5]

## Connections

- **Broader:** Amazon Web Services (`kt:amazon-web-services`); this is one of the portfolio's service categories.[^s1]
- **Has part:** AWS Transform MGN for rehosting servers, AWS DMS for databases, AWS DataSync for online file and object transfer, AWS Transfer Family for managed file transfers into and out of S3 or EFS,[^s1] and AWS Transform, the agentic successor to Migration Hub's planning features.[^s3][^s6]
- **Related:** the seven Rs migration strategies (`kt:seven-rs-migration-strategies`), which match the guide's rehost, replatform and re-architect choices,[^s2] and AWS Direct Connect, which the guide recommends for large, ongoing transfers.[^s2]
- **Covered by AWS Certified Cloud Practitioner:** the CLF-C02 in-scope list has a Migration and Transfer category covering Application Discovery Service, Application Migration Service, DMS, Migration Evaluator, Migration Hub, AWS SCT and the Snow Family.[^s7]

## Sources
[^s1]: Amazon Web Services, "Migration and transfer - Overview of Amazon Web Services" (whitepaper). https://docs.aws.amazon.com/whitepapers/latest/aws-overview/migration-services.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Choosing AWS migration services and tools" (decision guide, updated 2025-10-16). https://docs.aws.amazon.com/decision-guides/latest/migration-on-aws-how-to-choose/migration-on-aws-how-to-choose.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "AWS Migration Hub availability change - AWS Migration Hub". https://docs.aws.amazon.com/migrationhub/latest/ug/migrationhub-availability-change.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "AWS Snowball Edge availability change - AWS Snowball Edge Developer Guide". https://docs.aws.amazon.com/snowball/latest/developer-guide/snowball-edge-availability-change.html (accessed 2026-11-11)
[^s5]: Amazon Web Services, "AWS Data Transfer Terminal pricing". https://aws.amazon.com/data-transfer-terminal/pricing (accessed 2026-11-11)
[^s6]: Amazon Web Services, What's New with AWS, "AWS Transform for VMware is now generally available" (2025-05-15). https://aws.amazon.com/about-aws/whats-new/2025/05/aws-transform-vmware-generally-available (accessed 2026-11-11)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certification" (CLF-C02). https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-11)
