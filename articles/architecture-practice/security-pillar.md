---
id: kt:security-pillar
type: Concept
title: Security Pillar
aliases: [AWS Well-Architected Security Pillar, Well-Architected security pillar, Security Pillar whitepaper]
domain: architecture-practice
summary: >-
  The pillar of the AWS Well-Architected Framework concerned with protecting data, systems and assets,
  built on seven design principles and seven best practice areas (security foundations, identity and
  access management, detection, infrastructure protection, data protection, incident response and
  application security) covering questions SEC 1 to SEC 11.
verified: 2026-11-11
status: concept
broader: [kt:aws-well-architected-framework]
related: [kt:operational-excellence-pillar, kt:reliability-pillar, kt:aws-security-services, kt:aws-iam, kt:shared-responsibility-model]
relations:
  - {predicate: partOf, object: kt:aws-well-architected-framework}
  - {predicate: dependsOn, object: kt:aws-organizations}
  - {predicate: integratesWith, object: kt:aws-control-tower}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Security Pillar - AWS Well-Architected Framework (introduction)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Design principles - AWS Well-Architected Framework (Security)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-design.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Definition - AWS Well-Architected Framework (Security)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-def.md
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Document revisions - Security Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/document-revisions.html
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Security foundations - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-security.md
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "SEC01-BP01 Separate workloads using accounts - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/sec_securely_operate_multi_accounts.html
    type: official-docs
    accessed: 2026-11-11
  - id: s7
    title: "Incident response - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-incresp.html
    type: official-docs
    accessed: 2026-11-11
  - id: s8
    title: "Application security - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-appsec.html
    type: official-docs
    accessed: 2026-11-11
  - id: s9
    title: "AWS Certified Cloud Practitioner (CLF-C02) - Domain 1: Cloud Concepts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain1.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# Security Pillar

The Security Pillar is one of the six pillars of the AWS Well-Architected Framework, next to operational excellence, reliability, performance efficiency, cost optimization and sustainability. Its whitepaper explains how to apply current AWS recommendations when designing, delivering and maintaining secure workloads, so that teams can protect data and systems, control access and respond automatically to security events. It is written for CTOs, CISOs, architects, developers and operators, and it points to other resources for implementation details and architectural patterns rather than giving them itself. The current whitepaper is dated November 6, 2024 (as of 2026-11).[^s1]

That November 2024 revision added new guidance across SEC 2 to SEC 4 and SEC 6 to SEC 11. It followed a June 2024 overhaul that reordered and consolidated many best practices, and an October 2023 rewrite of incident response that added the operations and post-incident activity areas.[^s4]

## Design principles

The framework lists seven security design principles.[^s2]

- **Implement a strong identity foundation:** least privilege, separation of duties, centralized identity management and no long-term static credentials.[^s2]
- **Maintain traceability:** monitor, alert on and audit actions in real time, and feed logs and metrics into systems that investigate and act automatically.[^s2]
- **Apply security at all layers:** defense in depth from the network edge and VPC through load balancers, instances, operating system, application and code.[^s2]
- **Automate security best practices:** define controls as code in version-controlled templates.[^s2]
- **Protect data in transit and at rest:** classify data by sensitivity, then apply encryption, tokenization and access control.[^s2]
- **Keep people away from data:** reduce direct access and manual processing to cut mishandling and human error.[^s2]
- **Prepare for security events:** keep incident management processes aligned to the organization, run simulations and automate detection, investigation and recovery.[^s2]

## How it works

The pillar groups its best practices into seven areas: security foundations, identity and access management, detection, infrastructure protection, data protection, incident response and application security. AWS frames them as practices to have in place before you architect a workload. It also ties the pillar to the shared responsibility model: AWS physically secures the underlying infrastructure, and customers own how they use the services.[^s3]

Security foundations is a single question, SEC 1: how do you securely operate your workload? It asks teams to carry the processes defined under operational excellence into every security area, to track AWS and industry guidance and threat intelligence so their threat model evolves, and to automate security testing and validation.[^s5] The first best practice, SEC01-BP01, is rated high risk if not established. It recommends separate AWS accounts for workloads, environments and operations, organized into organizational units (OUs) whose controls member accounts inherit. A landing zone, either custom or built with AWS Control Tower, supplies the baseline and guardrails. Two anti-patterns stand out: mixing workloads of different data sensitivity in one account, and running a poorly designed OU structure.[^s6]

Incident response is SEC 10: how do you anticipate, respond to, and recover from incidents? The guidance assumes incidents happen even with mature preventive and detective controls. It stresses detailed logging, automated response through AWS APIs, pre-provisioned tooling and a CloudFormation-built clean room for forensics, quick access for the security team, and regular game days.[^s7] Application security is SEC 11, which covers validating an application's security properties across design, development and deployment. Its four areas are organization and culture, security *of* the pipeline, security *in* the pipeline, and dependency management. Threat modeling during design catches flaws while they are still cheap to fix.[^s8]

## Gotchas and when to use it

The pillar is a review lens, not a service. It costs nothing in itself, but it names no specific controls you must buy, and the whitepaper deliberately leaves out implementation patterns.[^s1] Because the guidance has been revised several times since 2023, check a design review against the current version rather than an older copy or PDF.[^s4] Use the pillar when setting a security baseline for a new landing zone, reviewing a workload before launch, or preparing a Well-Architected review. Use the concrete services, such as IAM, detection and data protection tools, to put its practices into effect.[^s3][^s6]

## Connections

The Security Pillar is **part of** the AWS Well-Architected Framework.[^s1] It **depends on** AWS Organizations for the account and OU separation that SEC01-BP01 requires, and **integrates with** AWS Control Tower as a ready-made landing zone with mandatory and optional controls.[^s6] It is **related** to the operational excellence pillar, whose processes SEC 1 reuses,[^s5] to the reliability pillar as a sibling pillar,[^s1] to AWS IAM through the identity-foundation principle,[^s2] to AWS security services, which implement its practice areas,[^s3] and to the shared responsibility model it cites.[^s3] It is **covered by** AWS Certified Cloud Practitioner (CLF-C02), whose Task Statement 1.2 asks candidates to understand and tell apart the Well-Architected pillars, security among them.[^s9]

## Sources
[^s1]: Amazon Web Services, "Security Pillar - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Design principles - AWS Well-Architected Framework (Security)". https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-design.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Definition - AWS Well-Architected Framework (Security)". https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-def.md (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Document revisions - Security Pillar". https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/document-revisions.html (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Security foundations - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-security.md (accessed 2026-11-11)
[^s6]: Amazon Web Services, "SEC01-BP01 Separate workloads using accounts". https://docs.aws.amazon.com/wellarchitected/latest/framework/sec_securely_operate_multi_accounts.html (accessed 2026-11-11)
[^s7]: Amazon Web Services, "Incident response - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-incresp.html (accessed 2026-11-11)
[^s8]: Amazon Web Services, "Application security - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-appsec.html (accessed 2026-11-11)
[^s9]: Amazon Web Services, "AWS Certified Cloud Practitioner (CLF-C02) - Domain 1: Cloud Concepts". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain1.html (accessed 2026-11-11)
