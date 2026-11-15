---
id: kt:aws-cloud-adoption-framework
type: Concept
title: AWS Cloud Adoption Framework
aliases: [AWS CAF, Cloud Adoption Framework, AWS CAF 3.0]
domain: architecture-practice
summary: >-
  AWS's organization-level guidance for cloud-powered digital transformation, which groups the
  foundational capabilities an organization needs into six perspectives (Business, People,
  Governance, Platform, Security, Operations) and sequences the work in four iterative phases.
verified: 2026-11-15
status: concept
broader: [kt:cloud-architecture]
related: [kt:aws-well-architected-framework, kt:seven-rs-migration-strategies, kt:aws-migration-acceleration-program, kt:multi-account-strategy, kt:aws-management-and-governance-services]
relations:
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "AWS Cloud Adoption Framework (AWS CAF)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/professional-services/CAF/
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Foundational capabilities - An Overview of the AWS Cloud Adoption Framework"
    publisher: Amazon Web Services (AWS Whitepaper)
    url: https://docs.aws.amazon.com/whitepapers/latest/overview-aws-cloud-adoption-framework/foundational-capabilities.html
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Accelerating business outcomes with cloud powered digital transformation - An Overview of the AWS Cloud Adoption Framework"
    publisher: Amazon Web Services (AWS Whitepaper)
    url: https://docs.aws.amazon.com/whitepapers/latest/overview-aws-cloud-adoption-framework/accelerating-business-outcomes.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "Platform perspective: infrastructure and applications - An Overview of the AWS Cloud Adoption Framework"
    publisher: Amazon Web Services (AWS Whitepaper)
    url: https://docs.aws.amazon.com/whitepapers/latest/overview-aws-cloud-adoption-framework/platform-perspective.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "AWS Cloud Adoption Framework (CAF) 3.0 is Now Available"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/aws-cloud-adoption-framework-caf-3-0-is-now-available
    type: official-blog
    accessed: 2026-11-15
  - id: s6
    title: "Domain 1: Cloud Concepts - AWS Certified Cloud Practitioner (CLF-C02) exam guide"
    publisher: Amazon Web Services (AWS Certification)
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain1.html
    type: official-learning
    accessed: 2026-11-15
created: 2026-11-15
---

# AWS Cloud Adoption Framework

The AWS Cloud Adoption Framework (AWS CAF) is AWS's guidance for organizations planning a cloud-powered digital transformation. It packages AWS's experience into a description of the organizational capabilities that successful transformations depend on, plus advice for raising cloud readiness.[^s1] Where the Well-Architected Framework asks whether one workload is built well, CAF asks whether the organization around those workloads is ready: its strategy, people, governance and operating model.[^s1][^s2] Like Well-Architected it is guidance, not a service, so there is nothing to provision or pay for.

## How it works

CAF describes a value chain. Four **transformation domains** build on each other: technology (migrating and modernizing infrastructure, applications and data platforms) enables process change (digitizing and automating operations), which enables organizational change (teams organized around products and value streams), which enables product change (new products, services and revenue models).[^s3] The intended **business outcomes** are reduced business risk, better environmental, social and governance (ESG) performance, increased revenue and improved operational efficiency.[^s3]

The domains rest on **foundational capabilities**, grouped into six **perspectives**, each with typical stakeholders:[^s2]

- **Business:** makes cloud investments accelerate transformation and outcomes (CEO, CFO, COO, CIO, CTO).[^s2]
- **People:** culture, organizational structure, leadership and workforce (CIO, COO, CTO, cloud director).[^s2]
- **Governance:** orchestrates cloud initiatives to maximize benefit and limit transformation risk (chief transformation officer, CFO, chief data and risk officers).[^s2]
- **Platform:** an enterprise-grade, scalable hybrid cloud platform and modernized, cloud-native workloads (CTO, architects, engineers).[^s2]
- **Security:** confidentiality, integrity and availability of data and workloads (CISO, chief compliance officer, internal audit).[^s2]
- **Operations:** cloud services delivered at the level the business needs (infrastructure and operations leaders, SREs, IT service managers).[^s2]

Each perspective breaks into named capabilities. The Platform perspective, for example, lists seven: platform architecture, data architecture, platform engineering (a compliant multi-account environment with reusable cloud products), data engineering, provisioning and orchestration, modern application development, and CI/CD.[^s4]

The work runs in four **phases**: *Envision* (find and prioritize opportunities tied to strategic objectives), *Align* (find capability gaps and cross-team dependencies, and align stakeholders), *Launch* (ship production pilots that show incremental value) and *Scale* (expand pilots and sustain the benefits).[^s1] AWS stresses that the phases are iterative and incremental rather than a one-time plan.[^s5]

## Versions and gotchas

The current model is CAF 3.0, announced on the AWS News Blog in November 2021. That release refocused the framework on digital transformation, gave more weight to data and analytics, and grew the capability count to 47 from 31 in version 2.0.[^s5] Older material describes earlier structures, so check a document's version before mapping its terms onto today's perspectives. AWS keeps extending CAF with companion guidance, including a CAF for AI whitepaper linked from the CAF page; as of 2026-11 that page also cites Cloud Value Benchmarking figures such as 27% lower cost per user after migrating from on-premises.[^s1]

A common mistake is treating CAF as a migration checklist. It is broader than moving servers: the Platform, Security and Operations perspectives cover technical readiness, while Business, People and Governance cover the organizational change that many stalled programs neglect.[^s2][^s3] It also does not prescribe an order: the Align phase is where you choose which capability gaps to close first.[^s1]

## When to use it (and when not)

Use CAF at the start of a cloud program or a re-plan: to build an executive-level case, assess readiness across the six perspectives, and sequence workstreams through pilots.[^s1][^s5] Use the Well-Architected Framework instead when reviewing a specific workload's design, and the 7 Rs when choosing a migration strategy per application. CAF frames those decisions; it does not replace them.[^s2]

## Connections

CAF sits under general **cloud architecture** practice and is **related** to the AWS Well-Architected Framework, its workload-level counterpart.[^s2] It is related to the AWS Migration Acceleration Program and the per-workload **7 Rs migration strategies** because migrating and modernizing is the technology domain that starts CAF's value chain.[^s3] Its Platform perspective's call for a compliant multi-account environment links it to **multi-account strategy**, and its Governance and Operations perspectives map to AWS's **management and governance services**.[^s2][^s4] The `coveredBy` edge reflects the CLF-C02 exam guide, whose Task Statement 1.3 on migration lists understanding the components of AWS CAF, with examples drawn from its business outcomes.[^s6]

## Sources
[^s1]: Amazon Web Services, "AWS Cloud Adoption Framework (AWS CAF)". https://aws.amazon.com/professional-services/CAF/ (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Foundational capabilities - An Overview of the AWS Cloud Adoption Framework" (AWS Whitepaper). https://docs.aws.amazon.com/whitepapers/latest/overview-aws-cloud-adoption-framework/foundational-capabilities.html (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Accelerating business outcomes with cloud powered digital transformation - An Overview of the AWS Cloud Adoption Framework" (AWS Whitepaper). https://docs.aws.amazon.com/whitepapers/latest/overview-aws-cloud-adoption-framework/accelerating-business-outcomes.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "Platform perspective: infrastructure and applications - An Overview of the AWS Cloud Adoption Framework" (AWS Whitepaper). https://docs.aws.amazon.com/whitepapers/latest/overview-aws-cloud-adoption-framework/platform-perspective.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "AWS Cloud Adoption Framework (CAF) 3.0 is Now Available" (AWS News Blog, 2021-11-23). https://aws.amazon.com/blogs/aws/aws-cloud-adoption-framework-caf-3-0-is-now-available (accessed 2026-11-15)
[^s6]: Amazon Web Services, "Domain 1: Cloud Concepts - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain1.html (accessed 2026-11-15)
