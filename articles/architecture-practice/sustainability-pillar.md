---
id: kt:sustainability-pillar
type: Concept
title: Sustainability Pillar
aliases: [AWS Well-Architected Sustainability Pillar, Well-Architected sustainability pillar, Sustainability Pillar whitepaper]
domain: architecture-practice
summary: >-
  The sixth pillar of the AWS Well-Architected Framework, concerned with reducing the environmental
  impact of cloud workloads by maximizing utilization and minimizing the resources provisioned, organized
  into six best practice areas, six design principles and an iterative improvement process.
verified: 2026-11-13
status: concept
broader: [kt:aws-well-architected-framework]
related: [kt:cost-optimization-pillar, kt:performance-efficiency-pillar, kt:shared-responsibility-model, kt:aws-graviton]
relations:
  - {predicate: partOf, object: kt:aws-well-architected-framework}
  - {predicate: monitoredBy, object: kt:aws-sustainability-console}
  - {predicate: dependsOn, object: kt:amazon-ec2-auto-scaling}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "Sustainability Pillar - AWS Well-Architected Framework (introduction)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/sustainability-pillar.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Design principles (sustainability) - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/sus-design-principles.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "The shared responsibility model - Sustainability Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/the-shared-responsibility-model.html
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "Definition (sustainability) - AWS Well-Architected Framework (2024-06-27 version)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/2024-06-27/framework/sus-def.html
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "SUS01-BP01 Choose Region based on both business requirements and sustainability goals"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/sus_sus_region_a2.html
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "Alignment to demand - AWS Well-Architected Framework (2024-06-27 version)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/2024-06-27/framework/sus-user-behavior-patterns.html
    type: official-docs
    accessed: 2026-11-13
  - id: s7
    title: "Improvement process - Sustainability Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/improvement-process.html
    type: official-docs
    accessed: 2026-11-13
  - id: s8
    title: "Evaluate specific improvements - Sustainability Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/evaluate-specific-improvements.html
    type: official-docs
    accessed: 2026-11-13
  - id: s9
    title: "Sustainability as a non-functional requirement - Sustainability Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/sustainability-as-a-non-functional-requirement.html
    type: official-docs
    accessed: 2026-11-13
  - id: s10
    title: "Document revisions - Sustainability Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/document-revisions.html
    type: official-docs
    accessed: 2026-11-13
  - id: s11
    title: "Announcing the AWS Sustainability console: programmatic access, configurable CSV reports, and Scope 1-3 reporting in one place"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/announcing-the-aws-sustainability-console-programmatic-access-configurable-csv-reports-and-scope-1-3-reporting-in-one-place
    type: official-blog
    accessed: 2026-11-13
  - id: s12
    title: "AWS' Customer Carbon Footprint Tool now includes Scope 3 emissions data"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/10/aws-customer-carbon-footprint-tool-scope-3-emissions-data
    type: official-blog
    accessed: 2026-11-13
created: 2026-11-13
---

# Sustainability Pillar

The Sustainability Pillar is the newest of the six pillars of the AWS Well-Architected Framework. It addresses environmental sustainability: design principles, operational guidance, best practices, trade-offs and improvement plans that help teams build workloads that maximize efficiency and reduce waste.[^s1] AWS first published it on December 2, 2021; the current whitepaper is dated November 6, 2024 (as of 2026-11). That revision updated guidance in SUS 1 and SUS 3–6 and added SUS06-BP01, "Communicate and cascade your sustainability goals."[^s10][^s1] It is aimed at technology roles such as CTOs, architects, developers and operations staff.[^s1]

## Shared responsibility for sustainability

The pillar borrows the security model's split. AWS is responsible for sustainability **of** the cloud: efficient shared infrastructure, water stewardship and sourcing renewable power. Customers are responsible for sustainability **in** the cloud: optimizing workloads and utilization and minimizing the total resources deployed.[^s3] In practice that means efficient code, languages and algorithms, efficient data storage, right-sized compute, and lower demands on end-user hardware.[^s3]

## Design principles

The framework lists six principles.[^s2]

- **Understand your impact:** measure current impact and model future impact, comparing resources and emissions per unit of work to set KPIs.[^s2]
- **Establish sustainability goals:** set long-term per-workload goals, such as fewer compute and storage resources per transaction, and design so growth lowers impact intensity.[^s2]
- **Maximize utilization:** AWS's example is that two hosts at 30% utilization are less efficient than one at 60%, because each host draws baseline power.[^s2]
- **Anticipate and adopt new, more efficient hardware and software**, and design for flexibility so you can switch quickly.[^s2]
- **Use managed services**, which share infrastructure across many customers. Examples given are AWS Fargate, S3 Lifecycle configurations and EC2 Auto Scaling.[^s2]
- **Reduce the downstream impact** of workloads on customers' devices and energy use.[^s2]

## How it works: six areas and an improvement loop

Best practices are grouped into six areas: Region selection, alignment to demand, software and architecture, data, hardware and services, and process and culture.[^s4]

- **Region selection (SUS 1):** SUS01-BP01, rated medium risk, says to shortlist Regions on compliance, service availability, cost and latency first. Then prefer Regions near Amazon renewable energy projects or with lower published grid carbon intensity. Its anti-patterns are choosing a Region because it is near your own office and putting the whole workload in one location.[^s5]
- **Alignment to demand (SUS 2):** scale infrastructure with user load, align SLAs (availability, retention) with sustainability goals, remove unused assets, place workloads near users, and give team members shared rather than idle high-powered machines.[^s6]

Improvement runs as a repeating six-step cycle: identify targets, evaluate specific improvements, prioritize and plan, test and validate, deploy to production, then measure and replicate. AWS advises removing waste and low utilization before tuning individual workloads.[^s7] To evaluate a change, the pillar uses **proxy metrics**: vCPU minutes for compute, GB provisioned for storage, and GB or packets transferred for network. Each is divided by a business metric such as transactions or active users to give a KPI like vCPU minutes per transaction. AWS cautions against financial business metrics, because changes in transaction value distort comparisons.[^s8]

## Gotchas and trade-offs

Sustainability is framed as a non-functional requirement that usually lowers cost too, because you pay only for what you use. It often needs no sacrifice in uptime or latency. Where trade-offs exist, the pillar names three levers: lower result quality (approximate computing), slower response times (batching), and lower availability targets (on-demand replacement instead of idle static-stability capacity).[^s9] The pillar notes that relaxing availability this way raises utilization and lowers cost without affecting normal operation.[^s9]

## What changed (2026-03)

Measuring impact, the first design principle, relied on the Customer Carbon Footprint Tool in the Billing console. In October 2025 it gained Scope 3 data (server manufacturing, facility power, equipment transport) and Scope 1 natural gas and refrigerants, covering all three GHG Protocol scopes with history back to January 2022.[^s12] On March 31, 2026 AWS launched the standalone **AWS Sustainability console**, at no additional cost. It reports Scope 1–3 emissions in MTCO2e by Region and service, using market- and location-based methods. It adds custom CSV reports, an API, and a permissions model that does not require billing access. AWS stated that the Customer Carbon Footprint Tool would be deprecated on June 30, 2026.[^s11]

## Connections

The pillar is **part of** the AWS Well-Architected Framework.[^s1] Workload emissions are **monitored by** the AWS Sustainability console.[^s11] Its alignment-to-demand guidance **depends on** demand-based scaling such as Amazon EC2 Auto Scaling, and Region choice ties it to AWS Regions.[^s2][^s5] It is **related** to the cost optimization and performance efficiency pillars, which share its focus on utilization. It is also related to the shared responsibility model it adapts, and to AWS Graviton as an example of adopting more efficient hardware.[^s2][^s3][^s9]

## Sources
[^s1]: Amazon Web Services, "Sustainability Pillar - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/sustainability-pillar.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Design principles - AWS Well-Architected Framework (sustainability)". https://docs.aws.amazon.com/wellarchitected/latest/framework/sus-design-principles.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "The shared responsibility model - Sustainability Pillar". https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/the-shared-responsibility-model.html (accessed 2026-11-13)
[^s4]: Amazon Web Services, "Definition - AWS Well-Architected Framework (sustainability, 2024-06-27 version)". https://docs.aws.amazon.com/wellarchitected/2024-06-27/framework/sus-def.html (accessed 2026-11-13)
[^s5]: Amazon Web Services, "SUS01-BP01 Choose Region based on both business requirements and sustainability goals". https://docs.aws.amazon.com/wellarchitected/latest/framework/sus_sus_region_a2.html (accessed 2026-11-13)
[^s6]: Amazon Web Services, "Alignment to demand - AWS Well-Architected Framework (2024-06-27 version)". https://docs.aws.amazon.com/wellarchitected/2024-06-27/framework/sus-user-behavior-patterns.html (accessed 2026-11-13)
[^s7]: Amazon Web Services, "Improvement process - Sustainability Pillar". https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/improvement-process.html (accessed 2026-11-13)
[^s8]: Amazon Web Services, "Evaluate specific improvements - Sustainability Pillar". https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/evaluate-specific-improvements.html (accessed 2026-11-13)
[^s9]: Amazon Web Services, "Sustainability as a non-functional requirement - Sustainability Pillar". https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/sustainability-as-a-non-functional-requirement.html (accessed 2026-11-13)
[^s10]: Amazon Web Services, "Document revisions - Sustainability Pillar". https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/document-revisions.html (accessed 2026-11-13)
[^s11]: AWS News Blog, "Announcing the AWS Sustainability console: programmatic access, configurable CSV reports, and Scope 1-3 reporting in one place" (published 2026-03-31, updated 2026-05-19). https://aws.amazon.com/blogs/aws/announcing-the-aws-sustainability-console-programmatic-access-configurable-csv-reports-and-scope-1-3-reporting-in-one-place (accessed 2026-11-13)
[^s12]: What's New with AWS, "AWS' Customer Carbon Footprint Tool now includes Scope 3 emissions data" (2025-10-22). https://aws.amazon.com/about-aws/whats-new/2025/10/aws-customer-carbon-footprint-tool-scope-3-emissions-data (accessed 2026-11-13)
