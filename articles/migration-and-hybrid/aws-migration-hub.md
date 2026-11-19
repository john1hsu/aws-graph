---
id: kt:aws-migration-hub
type: Service
title: AWS Migration Hub
aliases: [Migration Hub, Migration Hub home Region]
domain: migration-and-hybrid
summary: >-
  AWS's single-console service for tracking discovery, planning and per-application migration status
  across migration tools, with all tracking data held in one chosen home Region; it closed to new
  customers on 2025-11-07, and AWS recommends AWS Transform for new migration projects.
verified: 2026-11-19
status: closed-to-new-customers
broader: [kt:aws-migration-and-transfer-services]
related: [kt:migration-wave-planning, kt:seven-rs-migration-strategies, kt:aws-migration-acceleration-program]
relations:
  - {predicate: alternativeTo, object: kt:aws-transform}
  - {predicate: integratesWith, object: kt:aws-application-discovery-service}
  - {predicate: integratesWith, object: kt:aws-transform-mgn}
  - {predicate: integratesWith, object: kt:aws-database-migration-service}
  - {predicate: governedBy, object: kt:application-discovery-service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What Is AWS Migration Hub?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/migrationhub/latest/ug/whatishub.md
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "AWS Migration Hub availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/migrationhub/latest/ug/migrationhub-availability-change.md
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Managing your AWS Migration Hub home Region"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/migrationhub/latest/ug/home-region.md
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "AWS Migration Hub endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/migrationhubn.md
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Track the status of your migrations in AWS Migration Hub"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/migrationhub/latest/ug/migrate-wt-track.html
    type: official-docs
    accessed: 2026-11-19
  - id: s6
    title: "What is Migration Hub Strategy Recommendations?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/migrationhub-strategy/latest/userguide/what-is-mhub-strategy.md
    type: official-docs
    accessed: 2026-11-19
  - id: s7
    title: "What is AWS Migration Hub Refactor Spaces?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/migrationhub-refactor-spaces/latest/userguide/what-is-mhub-refactor-spaces.html
    type: official-docs
    accessed: 2026-11-19
  - id: s8
    title: "AWS Certified Cloud Practitioner - Exam Guide (CLF-C02)"
    publisher: AWS Certification
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02.pdf
    type: official-learning
    accessed: 2026-11-19
  - id: s9
    title: "Automated Application Migration Status in AWS Migration Hub"
    publisher: What's New with AWS
    url: https://aws.amazon.com/about-aws/whats-new/2018/10/aws_migration_hub_saves_time_migrating_with_application_migration_status_automation
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# AWS Migration Hub

AWS Migration Hub is the tracking console of the classic AWS migration toolchain. It gives one place to see discovered servers, group them into applications, and follow each application's migration, whichever supported tool is doing the actual move. You can view the servers and databases behind each application and see how far along each one is.[^s1]

**Lifecycle (as of 2026-11):** Migration Hub stopped accepting new customers on 7 November 2025. Existing customers can keep using it to finish in-flight migrations. AWS will keep shipping security updates and keep the service available, but it will add no new features. AWS names AWS Transform, launched in May 2025, as the recommended successor. AWS says Transform carries over Hub capabilities such as Strategy Recommendations, EC2 instance recommendations, Journeys and Orchestrator. Moving to it needs no data migration: you finish old projects in Migration Hub and start new ones in Transform.[^s2] AWS has not announced a shutdown date, so the status here is closed to new customers, not deprecated.[^s2]

## How it works

**Home Region.** Before any write action from the console, SDK or CLI, you pick a Migration Hub home Region. All discovery and migration-tracking data lives there, and that Region's console shows migrations into every target Region you use.[^s1][^s3] Only tracking data sits in the home Region; workloads can land in any Region the migration tool supports.[^s1] Write API calls (create, notify, associate, import, put) from outside the home Region are rejected. Read calls work from anywhere, and agents and collectors can still be registered elsewhere.[^s3] To change the home Region you delete the home Region control and create a new one; deleting it leaves the existing data in place.[^s3]

As of 2026-11, AWS lists seven Regions that can serve as the home Region: us-east-1, us-west-2, ap-southeast-2, ap-northeast-1, eu-central-1, eu-west-1 and eu-west-2.[^s4]

**Discover, group, track.** You can discover servers first (via AWS Application Discovery Service) and then group them into applications, or start migrating straight away and group servers as you go.[^s1] Status updates come from AWS Application Migration Service for servers and from AWS Database Migration Service for databases.[^s1] Applications move through *Not started*, *In progress* and *Completed*. The status flips to In progress on its own the first time a migration task starts for one of the application's servers. Marking it Completed is a manual step you take after testing.[^s5] AWS added that automatic status change in October 2018, at no additional cost.[^s9]

**Companion capabilities.** Three tools grew up under the Migration Hub name:

- *Strategy Recommendations* analyzes server inventory, runtime environments and binaries (IIS, Tomcat and JBoss apps), and optionally source code and databases. It then recommends a rehost, replatform or refactor path and the tools to use.[^s6]
- *Refactor Spaces* sets up the networking, routing and IAM policies for strangler-fig refactoring into microservices. It orchestrates API Gateway and Network Load Balancer across accounts. It also closed to new customers on 7 November 2025.[^s7]
- *Orchestrator* automates server and enterprise-application migrations from customizable predefined workflow templates, giving one place to run and track them.[^s1]

## Quotas, pricing and gotchas

- **Quotas.** Migration Hub has no quotas of its own; AWS's endpoints page says its quotas are the Application Discovery Service quotas.[^s4]
- **Pricing.** AWS announced the automatic application-status feature at no additional cost. This graph has not verified a current price for the service as a whole, so confirm with AWS before budgeting.[^s9]
- **Ungrouped servers stay invisible.** A server's migration updates appear under an application only once it has been grouped into that application.[^s5]
- **Choose the home Region carefully.** Every write call depends on it, and discovery data for the account lives only there.[^s3]
- **Exam material lags.** The CLF-C02 in-scope list still includes AWS Migration Hub under Migration and Transfer, while Refactor Spaces is listed as out of scope.[^s8]

## When to use it (and when not)

Use Migration Hub only if your account is already an existing customer finishing a migration started there. That way you keep the application-level progress view you have built.[^s2] For any new assessment, planning or migration program, AWS's guidance is to start in AWS Transform.[^s2] If you just need to move servers or databases, the underlying tools (Application Migration Service and DMS) do the work; Migration Hub only reports on it.[^s1]

## Connections

Migration Hub sits under **AWS migration and transfer services** as the tracking layer of that category. It is **alternative to** AWS Transform, which AWS positions as its successor for new customers.[^s2] It **integrates with** Application Discovery Service (inventory), AWS Transform MGN (the graph's node for Application Migration Service) and DMS (status reporting).[^s1] It is **governed by** the Application Discovery Service quotas.[^s4] It is **scoped to** a single home Region, an AWS Region you choose.[^s3] Its application grouping and strategy recommendations feed **migration wave planning** and the **7 Rs** decisions, and it is **covered by** the AWS Certified Cloud Practitioner exam (CLF-C02).[^s6][^s8]

## Sources
[^s1]: Amazon Web Services, "What Is AWS Migration Hub?". https://docs.aws.amazon.com/migrationhub/latest/ug/whatishub.md (accessed 2026-11-19)
[^s2]: Amazon Web Services, "AWS Migration Hub availability change". https://docs.aws.amazon.com/migrationhub/latest/ug/migrationhub-availability-change.md (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Managing your AWS Migration Hub home Region". https://docs.aws.amazon.com/migrationhub/latest/ug/home-region.md (accessed 2026-11-19)
[^s4]: Amazon Web Services, "AWS Migration Hub endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/migrationhubn.md (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Track the status of your migrations in AWS Migration Hub". https://docs.aws.amazon.com/migrationhub/latest/ug/migrate-wt-track.html (accessed 2026-11-19)
[^s6]: Amazon Web Services, "What is Migration Hub Strategy Recommendations?". https://docs.aws.amazon.com/migrationhub-strategy/latest/userguide/what-is-mhub-strategy.md (accessed 2026-11-19)
[^s7]: Amazon Web Services, "What is AWS Migration Hub Refactor Spaces?". https://docs.aws.amazon.com/migrationhub-refactor-spaces/latest/userguide/what-is-mhub-refactor-spaces.html (accessed 2026-11-19)
[^s8]: AWS Certification, "AWS Certified Cloud Practitioner - Exam Guide (CLF-C02)". https://docs.aws.amazon.com/pdfs/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02.pdf (accessed 2026-11-19)
[^s9]: What's New with AWS, "Automated Application Migration Status in AWS Migration Hub". https://aws.amazon.com/about-aws/whats-new/2018/10/aws_migration_hub_saves_time_migrating_with_application_migration_status_automation (accessed 2026-11-19)
