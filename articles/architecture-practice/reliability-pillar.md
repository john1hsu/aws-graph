---
id: kt:reliability-pillar
type: Concept
title: Reliability Pillar
aliases: [AWS Well-Architected Reliability Pillar, Well-Architected reliability pillar, Reliability Pillar whitepaper]
domain: architecture-practice
summary: >-
  The pillar of the AWS Well-Architected Framework concerned with a workload performing its intended
  function correctly and consistently, organized into foundations, workload architecture, change
  management and failure management, with five design principles and availability and DR targets.
verified: 2026-11-10
status: concept
broader: [kt:aws-well-architected-framework]
related: [kt:operational-excellence-pillar, kt:availability-zones]
relations:
  - {predicate: partOf, object: kt:aws-well-architected-framework}
  - {predicate: hasPart, object: kt:disaster-recovery-strategies}
  - {predicate: dependsOn, object: kt:service-quotas}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
sources:
  - id: s1
    title: "Reliability Pillar - AWS Well-Architected Framework (introduction)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.md
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Design principles - Reliability Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/design-principles.html
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Availability - Reliability Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/availability.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Disaster Recovery (DR) objectives - Reliability Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/disaster-recovery-dr-objectives.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Foundations - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel-found.html
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "REL01-BP01 Aware of service quotas and constraints - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_manage_service_limits_aware_quotas_and_constraints.html
    type: official-docs
    accessed: 2026-11-10
  - id: s7
    title: "Failure management - AWS Well-Architected Framework (2025-02-25 version)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/2025-02-25/framework/rel-failmgmt.html
    type: official-docs
    accessed: 2026-11-10
  - id: s8
    title: "Resiliency, and the components of reliability - Reliability Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/resiliency-and-the-components-of-reliability.html
    type: official-docs
    accessed: 2026-11-10
created: 2026-11-10
---

# Reliability Pillar

The Reliability Pillar is one of the six pillars of the AWS Well-Architected Framework, alongside operational excellence, security, performance efficiency, cost optimization and sustainability. Its whitepaper gives guidance for designing, delivering and maintaining AWS environments so that workloads rest on strong foundations, have a resilient architecture, follow consistent change management and have proven failure recovery processes. The current whitepaper carries a publication date of November 6, 2024 (as of 2026-11).[^s1]

AWS treats *resiliency* as the primary factor in reliability: the ability of a workload to recover from infrastructure or service disruptions, acquire capacity dynamically to meet demand, and mitigate problems such as misconfigurations or transient network issues. The other factors, such as operational readiness reviews, encrypted backups, request-rate design and the cost of static stability, are covered by the other pillars.[^s8]

## How it works

The pillar has five design principles.[^s2]

- **Automatically recover from failure:** watch business-relevant KPIs and trigger automated repair when thresholds are breached.[^s2]
- **Test recovery procedures:** use automation to simulate failures and validate recovery before a real incident.[^s2]
- **Scale horizontally:** replace one large resource with several smaller ones so a single failure affects fewer requests.[^s2]
- **Stop guessing capacity:** monitor demand and add or remove resources automatically.[^s2]
- **Manage change through automation:** make infrastructure changes through automation so they are tracked and reviewed.[^s2]

Its best practices are grouped into four areas: foundations, workload architecture, change management and failure management.[^s5][^s7] Foundations covers requirements that extend beyond one workload and should exist before you design anything; its two questions are how you manage service quotas and constraints (REL 1) and how you plan network topology across environments, including IP address management and DNS (REL 2).[^s5] Failure management covers backing up data to meet RTO and RPO (REL 9), fault isolation across boundaries (REL 10), withstanding component failures (REL 11), testing reliability (REL 12) and planning disaster recovery (REL 13). AWS's stance is that failures are expected, so a workload should detect them, replace failed resources rather than repairing them in place, and verify recovery with automated tests.[^s7]

## Availability math and targets

The pillar defines availability as available-for-use time divided by total time, or alternatively successful responses divided by valid requests over short periods.[^s3] It maps targets to example workloads: 99% (about 3 days 15 hours of downtime a year) for batch jobs, 99.9% (8 hours 45 minutes) for internal tools, 99.95% (4 hours 22 minutes) for online commerce, 99.99% (52 minutes) for video delivery and 99.999% (5 minutes) for ATM and telecom workloads.[^s3]

Two calculations matter in design reviews. Hard dependencies multiply, so a service at 99.99% that calls two others at 99.99% lands at about 99.97%. Independent redundant components combine the other way: two 99.9% components in different Availability Zones yield 99.9999%, and when every figure is all nines you can simply add the nines. Where a dependency publishes no figure, estimate it as MTBF / (MTBF + MTTR).[^s3]

Availability is distinct from disaster recovery objectives. Recovery time objective (RTO) is the longest acceptable delay before service is restored, and recovery point objective (RPO) is the longest acceptable time since the last recovery point. Both are set by the business, not AWS. RTO resembles MTTR but is a per-event maximum rather than an average, and RPO has no availability equivalent because it measures data loss.[^s4]

## Quotas and gotchas

Quota awareness (REL01-BP01) is rated high risk if not established. Service Quotas centralizes quotas for more than 250 services, but some limits, such as API Gateway usage-plan rate limits or RDS allocated storage, live in the service itself. Quotas can be Regional or global and differ between Regions, so don't assume a default carries over; Trusted Advisor and the Service Quotas API can track usage. Listed anti-patterns include assuming quotas rise automatically and not load testing at peak.[^s6]

Higher availability is not free. Each step up needs testing against more failure scenarios, automated recovery for every failure type, and slower, more careful deployments, and it narrows dependency choices toward purpose-built services. AWS advises choosing a target that fits the workload's whole lifecycle rather than defaulting to the most nines.[^s3]

## When to use it

Use the pillar when setting availability and DR targets, reviewing a design for single points of failure, or preparing a Well-Architected review. It is about *how much* resilience a workload needs and how to prove it; the concrete recovery patterns (backup and restore through multi-site active/active) are a separate, narrower topic.[^s3][^s7]

## Connections

The Reliability Pillar is **part of** the AWS Well-Architected Framework.[^s1] It **has part** the disaster recovery strategies node, since REL 13 asks how you plan DR against RTO and RPO.[^s7][^s4] It **depends on** Service Quotas for the quota management that REL 1 requires,[^s6] and is **monitored by** Amazon CloudWatch, which the quota guidance recommends for alerting on resource constraints.[^s6] It is **related** to Availability Zones, the independent fault boundaries behind the redundancy math,[^s3] and to the operational excellence pillar, which covers the automation and readiness reviews that reliability relies on.[^s8]

## Sources
[^s1]: Amazon Web Services, "Reliability Pillar - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.md (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Design principles - Reliability Pillar". https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/design-principles.html (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Availability - Reliability Pillar". https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/availability.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Disaster Recovery (DR) objectives - Reliability Pillar". https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/disaster-recovery-dr-objectives.html (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Foundations - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/rel-found.html (accessed 2026-11-10)
[^s6]: Amazon Web Services, "REL01-BP01 Aware of service quotas and constraints". https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_manage_service_limits_aware_quotas_and_constraints.html (accessed 2026-11-10)
[^s7]: Amazon Web Services, "Failure management - AWS Well-Architected Framework (2025-02-25)". https://docs.aws.amazon.com/wellarchitected/2025-02-25/framework/rel-failmgmt.html (accessed 2026-11-10)
[^s8]: Amazon Web Services, "Resiliency, and the components of reliability - Reliability Pillar". https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/resiliency-and-the-components-of-reliability.html (accessed 2026-11-10)
