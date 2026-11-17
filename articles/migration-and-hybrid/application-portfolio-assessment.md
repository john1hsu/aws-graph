---
id: kt:application-portfolio-assessment
type: Concept
title: Application portfolio assessment
aliases: [Portfolio assessment, Migration portfolio assessment, Application portfolio discovery, Portfolio discovery and analysis]
domain: migration-and-hybrid
summary: >-
  The continuous workstream in AWS's migration guidance that discovers, analyzes and plans a
  portfolio of applications and their infrastructure, producing the inventory, business case,
  migration strategies and wave plans a large migration program runs on.
verified: 2026-11-17
status: concept
broader: [kt:aws-migration-and-transfer-services, kt:aws-migration-acceleration-program]
related: [kt:aws-cloud-adoption-framework, kt:aws-pricing-calculator, kt:aws-transform-mgn]
relations:
  - {predicate: enables, object: kt:seven-rs-migration-strategies}
  - {predicate: enables, object: kt:migration-wave-planning}
  - {predicate: configuredVia, object: kt:aws-transform}
  - {predicate: configuredVia, object: kt:aws-application-discovery-service}
  - {predicate: alternativeTo, object: kt:migration-evaluator}
sources:
  - id: s1
    title: "Application portfolio assessment guide for AWS Cloud migration"
    publisher: Amazon Web Services (AWS Prescriptive Guidance)
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/application-portfolio-assessment-guide/introduction.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Application portfolio assessment strategy for AWS Cloud migration"
    publisher: Amazon Web Services (AWS Prescriptive Guidance)
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-application-portfolio-assessment-migration/introduction.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Methodology and approach - Application portfolio assessment strategy for AWS Cloud migration"
    publisher: Amazon Web Services (AWS Prescriptive Guidance)
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-application-portfolio-assessment-migration/methodology-approach.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "New agentic migration assessment capabilities now available with AWS Transform"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/05/assessment-capabilities-transform
    type: official-blog
    accessed: 2026-11-17
  - id: s5
    title: "AWS Transform FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/transform/faq
    type: official-docs
    accessed: 2026-11-17
  - id: s6
    title: "AWS Application Discovery Service availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/application-discovery/latest/userguide/application-discovery-service-availability-change.html
    type: official-docs
    accessed: 2026-11-17
created: 2026-11-17
---

# Application portfolio assessment

Application portfolio assessment is the AWS Prescriptive Guidance workstream for understanding everything a migration program has to move. It covers applications and the infrastructure under them (compute, storage and networks) through three activities: **discovery, analysis and planning**.[^s1] AWS describes it as the gathering of application, infrastructure and business-context data so that business-case and migration decisions rest on data, risk and cost come down, and business and IT priorities line up.[^s2] It is not a one-off audit: the guidance frames it as progressive and continuous, running beside the governance, landing zone, security and operations workstreams, which all consume its output.[^s2]

## Two levels: breadth and depth

The methodology switches between two levels of detail.[^s3]

- **Portfolio level (breadth).** High-level attributes for the whole estate, such as application names, environments, product versions, business and technical criticality, and performance and utilization metrics. This supports scoping, prioritization, the directional business case and portfolio management.[^s3]
- **Application level (depth).** The architecture and technology of each application, leading to an AWS target design and a migration strategy and pattern. Because this is slow, it is done in small batches aligned to migration waves, with the next batch discovered in a slightly overlapping sequence while earlier migrations run.[^s3]

## The four stages

AWS lays the work out in four stages mapped to the assess, mobilize and migrate phases; the week ranges are indicative and depend on how the program is organized.[^s1][^s2]

1. **Discovery acceleration and initial planning** (about weeks 1–5, assess): refresh the portfolio view, produce a directional business case and base rationalization models, and pick initial migration candidates.[^s1]
2. **Prioritized applications assessment** (about weeks 6–7, assess and mobilize): assess a few priority applications in depth, draft their target architecture and move them early to build experience and cloud foundations such as the initial landing zone.[^s1][^s2]
3. **Portfolio analysis and migration planning** (about weeks 8–14, mobilize): enrich the dataset iteratively, close data gaps, evolve the business case and build high-confidence wave plans.[^s1]
4. **Continuous assessment and improvement** (week 15 to program end, migrate and modernize): detailed per-wave assessments, wave-plan iteration, and review of migrated workloads for optimization and modernization.[^s1][^s2]

The strategy guide's activity list runs from identifying stakeholders and existing data sources, through automated discovery tooling and progressive dataset enrichment, to prioritization, a wave plan and continuous reassessment.[^s2]

## Tooling (as of 2026-11)

AWS Transform is AWS's current tool for this work. Its assessment accepts existing data such as RVTools exports, CMDB extracts, output from the AWS Transform discovery tool and several third-party discovery tools.[^s4] A May 2026 update added what-if scenarios with adjustable Region, utilization and service-mapping assumptions, side-by-side scenario comparison, cost modeling for EC2, FSx, S3, SQL Server on EC2 and virtual desktops, and more Cloud Value Framework pillars (staff productivity, operational resilience, business agility, sustainability); it is offered in every Region where AWS Transform is available.[^s4] The FAQ says the TCO can be compared across purchase commitments, BYOL vs. license-included and shared vs. dedicated tenancy, and exported as a PDF business case.[^s5]

AWS Application Discovery Service, the older agent and agentless collector, stopped accepting new customers on 2025-11-07. Existing customers keep using it (projects typically run about four months), it gets security updates but no new features, and AWS recommends AWS Transform for new projects.[^s6]

## Gotchas

- **Treat cost outputs as directional.** The AWS Transform FAQ says its estimates are not quotes or guarantees and suggests an account team or Partner for precise planning.[^s5]
- **Don't deep-dive the whole estate at once.** Application-level discovery is expensive; target the next wave, but finish it before that wave's migration date because security, operations and cutover planning depend on it.[^s3]
- **Portfolio gaps become scope errors.** Missing portfolio-level data skews prioritization and the business case, not just technical design.[^s3]
- **Keep it running.** The inventory and dependency data must stay current for the life of the program, while the data needed and the stakeholders change from stage to stage.[^s2]

## When to use it (and alternatives)

Use the full staged approach for multi-wave programs where a business case and wave plan must be defended. For a quick, self-service TCO, the AWS Transform assessment alone is the fast path; AWS positions **Migration Evaluator** as the separate, expert-led option guided by AWS Solutions Architects, with deeper data collection and Microsoft SQL Server analysis.[^s5] For a handful of workloads, a pricing estimate and a per-application 7 Rs decision may be enough.

## Connections

Portfolio assessment sits within the AWS migration services and the Migration Acceleration Program's assess and mobilize work, and it echoes the Cloud Adoption Framework's emphasis on aligning business and IT.[^s1][^s2] It **enables** the 7 Rs strategy decisions (its "base rationalization models") and **enables** migration wave planning, which consumes its high-confidence portfolio data.[^s1] It is **configured via** AWS Transform today and, for existing users, AWS Application Discovery Service, and is an **alternative to** an expert-led Migration Evaluator engagement for building the business case.[^s4][^s5][^s6] Its output then drives execution tools such as AWS Transform MGN.[^s1]

## Sources
[^s1]: Amazon Web Services, "Application portfolio assessment guide for AWS Cloud migration" (AWS Prescriptive Guidance). https://docs.aws.amazon.com/prescriptive-guidance/latest/application-portfolio-assessment-guide/introduction.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Application portfolio assessment strategy for AWS Cloud migration" (AWS Prescriptive Guidance). https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-application-portfolio-assessment-migration/introduction.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Methodology and approach - Application portfolio assessment strategy for AWS Cloud migration" (AWS Prescriptive Guidance). https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-application-portfolio-assessment-migration/methodology-approach.html (accessed 2026-11-17)
[^s4]: Amazon Web Services, "New agentic migration assessment capabilities now available with AWS Transform" (What's New with AWS, 2026-05-22). https://aws.amazon.com/about-aws/whats-new/2026/05/assessment-capabilities-transform (accessed 2026-11-17)
[^s5]: Amazon Web Services, "AWS Transform FAQs". https://aws.amazon.com/transform/faq (accessed 2026-11-17)
[^s6]: Amazon Web Services, "AWS Application Discovery Service availability change". https://docs.aws.amazon.com/application-discovery/latest/userguide/application-discovery-service-availability-change.html (accessed 2026-11-17)
