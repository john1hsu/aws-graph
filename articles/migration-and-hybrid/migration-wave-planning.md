---
id: kt:migration-wave-planning
type: Concept
title: Migration wave planning
aliases: [Wave planning, Migration waves, Move group planning, Migration wave plan]
domain: migration-and-hybrid
summary: >-
  The practice, in AWS's large-migration guidance, of grouping applications and servers into
  dependency-aware move groups and scheduling those groups into time-boxed migration waves, so a
  portfolio moves to AWS in repeatable, low-risk increments.
verified: 2026-11-17
status: concept
broader: [kt:aws-migration-and-transfer-services, kt:aws-migration-acceleration-program]
related: [kt:vmware-migration-to-aws, kt:aws-transform-mgn, kt:aws-control-tower]
relations:
  - {predicate: dependsOn, object: kt:seven-rs-migration-strategies}
  - {predicate: dependsOn, object: kt:application-portfolio-assessment}
  - {predicate: configuredVia, object: kt:aws-transform}
  - {predicate: alternativeTo, object: kt:aws-migration-hub}
sources:
  - id: s1
    title: "Wave planning - Application portfolio assessment guide for AWS Cloud migration"
    publisher: Amazon Web Services (AWS Prescriptive Guidance)
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/application-portfolio-assessment-guide/wave-planning.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Task 5: Defining the wave planning process - Portfolio playbook for AWS large migrations"
    publisher: Amazon Web Services (AWS Prescriptive Guidance)
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-portfolio-playbook/wave-planning.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Review groupings and waves - AWS Transform User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/de_de/transform/latest/userguide/transform-vmware-review-groupings-and-waves.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "Agile migration planning: Accelerate wave planning with AWS Transform"
    publisher: Amazon Web Services (AWS Migration & Modernization Blog)
    url: https://aws.amazon.com/blogs/migration-and-modernization/agile-migration-planning-accelerate-wave-planning-with-aws-transform/
    type: official-blog
    accessed: 2026-11-17
  - id: s5
    title: "AWS Migration Hub availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/migrationhub/latest/ug/migrationhub-availability-change.html
    type: official-docs
    accessed: 2026-11-17
created: 2026-11-17
---

# Migration wave planning

Migration wave planning is how AWS's migration guidance turns a large application portfolio into a schedule. Applications are grouped into **waves** that migrate together, which keeps each batch manageable, limits risk and gives the program a repeatable rhythm.[^s1] It sits after portfolio assessment and per-application strategy selection: once each workload has a target design and one of the 7 Rs assigned, wave planning decides *when* and *with what else* it moves.[^s1]

## Vocabulary: applications, move groups, waves

AWS Prescriptive Guidance uses a small hierarchy. A business application is a set of software and infrastructure components. A **dependency group** (the large-migration playbook calls it a **move group**) is a block of servers or applications that must cut over together, for example an app server and the database it needs at low latency. A **wave** is the top-level unit and contains one or more of these groups; the **migration date** is when a wave or group is stopped at the source and made live in AWS.[^s1][^s2]

Dependencies are classed as **hard** when they depend on location (splitting the pair across on-premises and AWS would break it) or **soft** when they do not, so the two sides can move separately. Hard dependencies can also be nontechnical, such as a shared maintenance window or the availability of one team.[^s1] The portfolio playbook records infrastructure dependencies (shared databases, central file stores), business dependencies (approved outage windows) and operational ones (patch windows), then turns only the dependencies that truly require co-migration into prioritized **move group rules**, such as "applications sharing a database move together".[^s2]

## How a wave plan is built

The assessment guide describes a loop: gather portfolio data and target designs, define the standard activities every wave runs, lay out empty waves against hard dates, assign dependency groups up to a maximum wave size, then check resources and iterate.[^s1] Dates to plan around include data-center exit and colocation contract end dates, refresh and release cycles, and periods to avoid such as month-end, year-end and holidays.[^s1] Each wave typically runs through design, cutover planning, pre-migration (landing zone, tooling, replication), cutover, hypercare and a formal closure with metrics and lessons learned.[^s1]

Waves can overlap: the first half of a wave (infrastructure deployment, validation, data sync) and the second half (cutover, testing, handover) are often done by different teams, so one team can start the next wave early.[^s1] Common ways to slice waves are by migration pattern, business domain, technical capability, environment or business priority, and programs usually combine them.[^s1]

## Sizing guidance and numbers

AWS gives no fixed wave length, but the assessment guide recommends **6–10 weeks per wave**; work that needs longer, such as a full rewrite, is better run outside the wave cadence.[^s1] The large-migration portfolio playbook adds practical heuristics: plan **4–5 waves ahead**, keep waves 1–5 under **10 servers** of low-complexity, non-production workloads, and in its example criteria cap a wave at **50 servers**. It also cites a team of four architects migrating up to 50 servers a week for rehost patterns.[^s2] These are guidance figures, not service quotas.

## Gotchas

- **Shared services distort dependency maps.** Active Directory, backup and monitoring connect to everything; filter them out and build them in AWS before the first wave.[^s1][^s2]
- **Reschedule whole move groups, never single servers**, or hard dependencies get split.[^s2]
- **Plan continuously.** Portfolios drift during long programs; keep a single source of truth for scope and re-iterate the plan as dependencies change.[^s1][^s2]
- **Interview owners.** Batch jobs and asynchronous links may not show up in network data.[^s1]

## Tooling

AWS Transform automates much of this. In the VMware workflow it scopes discovery data, groups servers into applications, generates move groups from technical dependencies (shared databases, message queues) and business ones (criticality, RPO/RTO, data center, owner), and then builds prioritized waves you can review and edit before assigning rehost or containerize per wave.[^s3] An AWS blog post from July 2026 shows the agent ingesting RVTools, CSV and unstructured inputs, recalculating waves for a deadline and rebalancing when servers move between groups, with a human reviewing each recommendation.[^s4] AWS Migration Hub, which earlier tooling relied on, closed to new customers on 2025-11-07, and AWS names AWS Transform as the replacement.[^s5]

## Connections

Wave planning **depends on** the 7 Rs strategy decisions and on application portfolio assessment, which supply the strategy, target design and dependency data each wave needs.[^s1] It sits within the AWS migration services and the Migration Acceleration Program's mobilize work, is **configured via** AWS Transform today, and is an **alternative to** the older AWS Migration Hub planning flow.[^s3][^s5] Waves are then executed with tools such as AWS Transform MGN, are common in VMware exits, and assume a landing zone (often AWS Control Tower) is ready before pre-migration starts.[^s1][^s3]

## Sources
[^s1]: Amazon Web Services, "Wave planning - Application portfolio assessment guide for AWS Cloud migration" (AWS Prescriptive Guidance). https://docs.aws.amazon.com/prescriptive-guidance/latest/application-portfolio-assessment-guide/wave-planning.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Task 5: Defining the wave planning process - Portfolio playbook for AWS large migrations" (AWS Prescriptive Guidance). https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-portfolio-playbook/wave-planning.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Review groupings and waves - AWS Transform User Guide". https://docs.aws.amazon.com/de_de/transform/latest/userguide/transform-vmware-review-groupings-and-waves.html (accessed 2026-11-17)
[^s4]: Mahesh Shetty, Amazon Web Services, "Agile migration planning: Accelerate wave planning with AWS Transform" (AWS Migration & Modernization Blog, 2026-07-07). https://aws.amazon.com/blogs/migration-and-modernization/agile-migration-planning-accelerate-wave-planning-with-aws-transform/ (accessed 2026-11-17)
[^s5]: Amazon Web Services, "AWS Migration Hub availability change". https://docs.aws.amazon.com/migrationhub/latest/ug/migrationhub-availability-change.html (accessed 2026-11-17)
