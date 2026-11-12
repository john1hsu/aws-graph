---
id: kt:seven-rs-migration-strategies
type: Concept
title: 7 Rs migration strategies
aliases: [Seven Rs of migration, 7 Rs, 6 Rs migration strategies, AWS migration strategies, rehost replatform refactor retire retain relocate repurchase]
domain: migration-and-hybrid
summary: >-
  AWS's set of seven per-workload migration strategies — retire, retain, rehost, relocate,
  repurchase, replatform and refactor/re-architect — used during portfolio assessment to decide
  how each application moves (or doesn't) to the AWS Cloud.
verified: 2026-11-12
status: concept
broader: [kt:aws-migration-and-transfer-services, kt:aws-cloud-adoption-framework]
related: [kt:aws-transform-mgn, kt:aws-database-migration-service, kt:aws-graviton, kt:amazon-rds, kt:aws-certified-solutions-architect-professional]
relations:
  - {predicate: dependsOn, object: kt:application-portfolio-assessment}
  - {predicate: enables, object: kt:migration-wave-planning}
sources:
  - id: s1
    title: "About the migration strategies - AWS Prescriptive Guidance (Large migrations to the AWS Cloud)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-guide/migration-strategies.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "6 Strategies for Migrating Applications to the Cloud"
    publisher: Amazon Web Services (AWS Executive in Residence Blog, Stephen Orban)
    url: https://aws.amazon.com/blogs/enterprise-strategy/6-strategies-for-migrating-applications-to-the-cloud
    type: official-blog
    accessed: 2026-11-12
  - id: s3
    title: "Iterating the 6 Rs migration strategy selection - Application portfolio assessment guide for AWS Cloud migration"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/application-portfolio-assessment-guide/iterating-7-rs-migration-strategy-selection.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "Overview - Best practices for assessing applications to be retired during a migration to the AWS Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/migration-retiring-applications/overview.html
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "Phases of a large migration - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-guide/phases.html
    type: official-docs
    accessed: 2026-11-12
created: 2026-11-12
---

# 7 Rs migration strategies

The **7 Rs** are the migration strategies AWS Prescriptive Guidance uses to decide how each workload gets to the cloud: **retire, retain, rehost, relocate, repurchase, replatform, and refactor or re-architect**.[^s1] They are not services; they are labels applied to every application (and, in finer-grained planning, every component and its infrastructure) so that a portfolio of hundreds of systems can be estimated, sequenced and migrated in waves.[^s1][^s3]

## Where the framework came from

AWS's version started as six strategies in a November 2016 post by Stephen Orban on the AWS enterprise strategy blog, which extended the five Rs Gartner described in 2011.[^s2] Those six were rehost, replatform, repurchase, refactor/re-architect, retire and retain.[^s2] Current Prescriptive Guidance adds a seventh, **relocate**, giving the 7 Rs.[^s1] Some AWS pages still say "6 Rs" — the portfolio-assessment guide's page on iterating strategy selection is titled that way even though its URL says 7 Rs — so treat both names as the same framework.[^s3]

## The seven strategies

- **Retire** — decommission or archive the application and shut down its servers. Prescriptive Guidance suggests utilization data to find candidates: "zombie" applications average under 5% CPU and memory, "idle" ones 5–20% over 90 days.[^s1] Retiring strengthens the business case, but decisions are often deferred when subject-matter experts have left or documentation is thin.[^s4] Orban estimated that as much as 10% (sometimes 20%) of an enterprise portfolio is no longer useful.[^s2]
- **Retain** — leave it in the source environment for now, for reasons such as data residency, high risk, unresolved dependencies, a recent upgrade, or mainframe and non-x86 Unix platforms.[^s1]
- **Rehost ("lift and shift")** — move without changes. AWS names AWS Transform MGN (formerly AWS Application Migration Service), the Cloud Migration Factory solution and VM Import/Export as tooling.[^s1] Orban reported most applications in large, deadline-driven migrations were rehosted, and cited GE Oil & Gas estimating about 30% savings from rehosting alone.[^s2]
- **Relocate** — move servers, instances or objects to a cloud version of the same platform, or to a different VPC, Region or account (for example moving an Amazon RDS instance to another account). AWS calls it the quickest route because the architecture is untouched.[^s1]
- **Repurchase ("drop and shop")** — replace the application with a different product or version, typically moving from a traditional licence to SaaS; data migration, identity integration and networking still need work.[^s1][^s2]
- **Replatform ("lift, tinker and shift")** — move with targeted optimizations, such as SQL Server to Amazon RDS for SQL Server, moving to AWS Graviton, Windows to Linux, or VMs into containers with Porting Assistant for .NET or AWS App2Container.[^s1]
- **Refactor or re-architect** — redesign the application with cloud-native features; AWS describes it as the most complex and costly strategy, used when a monolith or mainframe can't meet business demands.[^s1][^s2]

## How the strategies are chosen

Strategy selection happens early — during the mobilize phase or the initial portfolio assessment — and should be driven by discovery-tool utilization and performance data rather than guesswork.[^s1] A large migration runs through assess, mobilize, and migrate-and-modernize phases; in mobilize, a portfolio workstream collects metadata, prioritizes applications and plans waves.[^s5] The portfolio-assessment guide starts from a default decision tree, then revises it after pilot migrations and tests it against sample applications until it still matches business drivers and prioritization criteria. The resulting assignments feed effort, capacity and skills estimates and wave plans.[^s3]

## Gotchas and guidance

- **Migrate first, modernize later.** For large migrations AWS lists rehost, replatform, relocate and retire as the common strategies and does not recommend refactoring; rehosted workloads are easier to optimize once they are running on AWS.[^s1][^s5]
- **Strategies are revisited.** The initial plan should be refined for later waves as lessons and new data arrive, so a "retain" today may become a rehost next year.[^s4][^s3]
- **Repurchase isn't free of migration work.** Security and compliance assessment, data migration and authentication integration remain.[^s1]
- Picking a strategy doesn't pick a tool: AWS's rehost tooling (Transform MGN, VM Import/Export) is different from its replatform tooling (Graviton, Porting Assistant for .NET, App2Container), and relocate and retire name no tool at all.[^s1]

## Connections

- **Broader:** the strategies organize the work done with AWS migration and transfer services (`kt:aws-migration-and-transfer-services`) and sit inside cloud-adoption planning (`kt:aws-cloud-adoption-framework`).[^s1][^s5]
- **Related:** AWS Transform MGN (`kt:aws-transform-mgn`) is AWS's named rehost tool; Amazon RDS (`kt:amazon-rds`) is AWS's worked replatform example (SQL Server to RDS for SQL Server) and also the relocate example; AWS Database Migration Service (`kt:aws-database-migration-service`) is the graph's companion service for moving the data itself; AWS Graviton (`kt:aws-graviton`) is a replatform option; the AWS Certified Solutions Architect – Professional (`kt:aws-certified-solutions-architect-professional`) article links here for migration planning.[^s1]
- **Depends on** application portfolio assessment (`kt:application-portfolio-assessment`) for the data behind each decision, and **enables** migration wave planning (`kt:migration-wave-planning`).[^s3][^s5]

## Sources
[^s1]: Amazon Web Services, "About the migration strategies - AWS Prescriptive Guidance". https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-guide/migration-strategies.html (accessed 2026-11-12)
[^s2]: Stephen Orban, Amazon Web Services, "6 Strategies for Migrating Applications to the Cloud" (2016-11-01). https://aws.amazon.com/blogs/enterprise-strategy/6-strategies-for-migrating-applications-to-the-cloud (accessed 2026-11-12)
[^s3]: Amazon Web Services, "Iterating the 6 Rs migration strategy selection - Application portfolio assessment guide for AWS Cloud migration". https://docs.aws.amazon.com/prescriptive-guidance/latest/application-portfolio-assessment-guide/iterating-7-rs-migration-strategy-selection.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, "Overview - Best practices for assessing applications to be retired during a migration to the AWS Cloud". https://docs.aws.amazon.com/prescriptive-guidance/latest/migration-retiring-applications/overview.html (accessed 2026-11-12)
[^s5]: Amazon Web Services, "Phases of a large migration - AWS Prescriptive Guidance". https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-guide/phases.html (accessed 2026-11-12)
