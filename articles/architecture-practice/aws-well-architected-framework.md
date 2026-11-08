---
id: kt:aws-well-architected-framework
type: Concept
title: AWS Well-Architected Framework
aliases: [Well-Architected Framework, AWS Well-Architected]
domain: architecture-practice
summary: >-
  AWS's published set of architectural best practices and review questions, organized into six
  pillars (operational excellence, security, reliability, performance efficiency, cost optimization
  and sustainability), used to evaluate workloads and decide trade-offs.
verified: 2026-11-08
status: concept
broader: [kt:cloud-architecture]
related: [kt:aws-cloud-adoption-framework, kt:disaster-recovery-strategies]
relations:
  - {predicate: hasPart, object: kt:operational-excellence-pillar}
  - {predicate: hasPart, object: kt:security-pillar}
  - {predicate: hasPart, object: kt:reliability-pillar}
  - {predicate: integratesWith, object: kt:aws-well-architected-tool}
sources:
  - id: s1
    title: "Definitions - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/definitions.md
    type: official-docs
    accessed: 2026-11-08
  - id: s2
    title: "AWS Well-Architected Tool FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/well-architected-tool/faqs/
    type: official-docs
    accessed: 2026-11-08
  - id: s3
    title: "AWS Well-Architected Tool endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/wellarchitected.html
    type: official-docs
    accessed: 2026-11-08
  - id: s4
    title: "Custom lenses for workloads in AWS WA Tool"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/userguide/lenses-custom.html
    type: official-docs
    accessed: 2026-11-08
  - id: s5
    title: "New Guidance in the Well-Architected Tool"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/04/new-guidance-well-architected-tool
    type: official-blog
    accessed: 2026-11-08
  - id: s6
    title: "Architecting for AI excellence: AWS launches three Well-Architected Lenses at re:Invent 2025"
    publisher: Amazon Web Services (AWS Architecture Blog)
    url: https://aws.amazon.com/blogs/architecture/architecting-for-ai-excellence-aws-launches-three-well-architected-lenses-at-reinvent-2025
    type: official-blog
    accessed: 2026-11-08
created: 2026-11-08
---

# AWS Well-Architected Framework

The AWS Well-Architected Framework is AWS's codified set of best practices for designing and running workloads in the cloud. It distils what AWS solutions architects learn from customer systems in production into a consistent set of practices plus a list of questions you can ask of any architecture to see how closely it aligns with them.[^s1] It is guidance, not a product: you apply it in design reviews, and AWS offers the free AWS Well-Architected Tool to run those reviews and track the results.[^s2]

## How it works

The framework is organized into six pillars.[^s1]

- **Operational excellence:** supporting development and running workloads effectively, gaining insight into operations, and continuously improving processes.[^s1]
- **Security:** using cloud capabilities to protect data, systems and assets and improve security posture.[^s1]
- **Reliability:** a workload performing its intended function correctly and consistently, and being operable and testable across its lifecycle.[^s1]
- **Performance efficiency:** using computing resources efficiently and staying efficient as demand and technology change.[^s1]
- **Cost optimization:** delivering business value at the lowest price point.[^s1]
- **Sustainability:** reducing energy consumption and resource use across all components of a workload.[^s1]

The framework also fixes its vocabulary. A *component* is the code, configuration and AWS resources that meet a requirement; a *workload* is a set of components that together deliver business value; *architecture* is how components interact; *milestones* mark key architectural changes through design, implementation, testing, go-live and production; and the *technology portfolio* is all the workloads a business needs. Each improvement is tagged with a high, medium or low level of effort.[^s1]

Pillars are not equally negotiable. AWS says you trade pillars against each other according to business context, for example accepting lower reliability for cheaper, lower-impact development environments or paying more for reliability in mission-critical systems, but security and operational excellence are generally not traded off.[^s1]

**Lenses** extend the framework to a technology or industry. In the Well-Architected Tool you can review a workload against the core framework, AWS-provided lenses, or custom lenses you author with your own pillars, questions, best practices and improvement plans, then share them with other accounts.[^s2][^s4] At re:Invent 2025 AWS announced a new Responsible AI Lens and major updates to its Machine Learning and Generative AI lenses (the latter adding agentic AI guidance).[^s6]

## Quotas, versions and gotchas

The framework is revised continuously rather than in numbered releases. In April 2025 AWS shipped an update to the Well-Architected Tool that it said refreshed every pillar and added 78 new best practices, including the first update to 14 Reliability pillar best practices since 2022.[^s5] When you cite it, cite the dated version you reviewed against.

The Well-Architected Tool has no additional charge; you pay only for the AWS resources you run.[^s2] Its quotas, per supported Region as of 2026-11 and none of them adjustable, include 1,000 workloads per account, 100 milestones per workload, 20 lenses per workload, 15 lenses per account, 10 pillars per lens, 20 questions per pillar, 15 choices per question, a 500 KB lens size, 100 versions per lens and 20 shares per workload.[^s3] The tool has endpoints in 20 Regions, including AWS GovCloud (US-East) and AWS GovCloud (US-West).[^s3]

A common mistake is treating a review as a one-off audit. AWS recommends reviewing at major milestones in a workload's lifecycle; the tool then returns a prioritized improvement plan from your answers.[^s2] Its APIs let you feed review data into existing governance workflows rather than keeping it in the console.[^s2]

## When to use it (and when not)

Use the framework when designing a new workload, before a launch, after a major change, or to give teams a shared vocabulary for trade-offs.[^s1][^s2] It covers how a single workload is built and run. It is not an organization-wide cloud adoption plan, which is the job of the AWS Cloud Adoption Framework, and it does not replace service-specific documentation or compliance attestations.

## Connections

The framework **has part** each of its six pillars; operational excellence, security and reliability are linked here as the first pillar nodes, and each pillar has its own whitepaper and best practices.[^s1] It **integrates with** the AWS Well-Architected Tool, which turns the framework and its lenses into reviews, milestones and improvement plans.[^s2][^s4] It sits under general cloud architecture practice, is **related** to the AWS Cloud Adoption Framework (organizational adoption rather than workload design), and to disaster recovery strategies, which put the reliability pillar into practice.[^s1]

## Sources
[^s1]: Amazon Web Services, "Definitions - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/definitions.md (accessed 2026-11-08)
[^s2]: Amazon Web Services, "AWS Well-Architected Tool FAQs". https://aws.amazon.com/well-architected-tool/faqs/ (accessed 2026-11-08)
[^s3]: Amazon Web Services, "AWS Well-Architected Tool endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/wellarchitected.html (accessed 2026-11-08)
[^s4]: Amazon Web Services, "Custom lenses for workloads in AWS WA Tool". https://docs.aws.amazon.com/wellarchitected/latest/userguide/lenses-custom.html (accessed 2026-11-08)
[^s5]: Amazon Web Services, "New Guidance in the Well-Architected Tool" (What's New, 2025-04-09). https://aws.amazon.com/about-aws/whats-new/2025/04/new-guidance-well-architected-tool (accessed 2026-11-08)
[^s6]: Amazon Web Services, "Architecting for AI excellence: AWS launches three Well-Architected Lenses at re:Invent 2025" (AWS Architecture Blog). https://aws.amazon.com/blogs/architecture/architecting-for-ai-excellence-aws-launches-three-well-architected-lenses-at-reinvent-2025 (accessed 2026-11-08)
