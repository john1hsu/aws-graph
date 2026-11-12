---
id: kt:aws-compliance-programs
type: Concept
title: AWS compliance programs
aliases: [AWS Compliance Programs, AWS assurance programs, AWS risk and compliance program]
domain: public-sector-and-compliance
summary: >-
  The umbrella of third-party certifications and attestations, laws and regulations, and industry
  frameworks that AWS is audited against or aligns with, together with the Services in Scope lists and
  AWS Artifact reports customers use to inherit AWS's side of the controls.
verified: 2026-11-12
status: concept
broader: [kt:aws-security-services]
related: [kt:aws-config, kt:aws-european-sovereign-cloud]
relations:
  - {predicate: hasPart, object: kt:fedramp-on-aws}
  - {predicate: hasPart, object: kt:dod-cloud-computing-srg-impact-levels}
  - {predicate: hasPart, object: kt:hipaa-eligible-services}
  - {predicate: dependsOn, object: kt:aws-artifact}
  - {predicate: contrastsWith, object: kt:shared-responsibility-model}
sources:
  - id: s1
    title: "AWS Compliance Programs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/en/compliance/programs/
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "AWS Compliance FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/faq/
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "AWS risk and compliance program - Amazon Web Services: Risk and Compliance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-risk-and-compliance/aws-risk-and-compliance-program.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "AWS Services in Scope by Compliance Program"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "AWS Services in Scope by Compliance Program: SOC"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/SOC
    type: official-docs
    accessed: 2026-11-12
  - id: s6
    title: "Compliance Validation for AWS Config"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/config/latest/developerguide/config-compliance.html
    type: official-docs
    accessed: 2026-11-12
created: 2026-11-12
---

# AWS compliance programs

"AWS compliance programs" is the collective name AWS uses for the assurance work that lets customers understand the controls AWS operates and build regulated workloads on top of them.[^s1] It is not one product. It is a catalogue of certifications, regulations and frameworks, plus the evidence and scoping lists that go with each one. AWS's risk and compliance whitepaper, now marked as historical reference, says AWS takes part in more than 50 independent audit programs covering global and regional frameworks.[^s3]

## How it works

AWS sorts its programs into three groups:[^s1]

- **Certifications and attestations.** An independent third-party auditor assesses AWS and issues a certificate, audit report or attestation. Examples include SOC 1, 2 and 3, ISO 27001, PCI DSS, FedRAMP and Germany's C5.[^s1]
- **Laws, regulations and privacy.** These include HIPAA, FISMA, ITAR, DORA and GDPR. There is no formal certification for a cloud provider here. AWS supplies security features, enablers and legal agreements, such as the Business Associate Addendum (BAA) and a data processing agreement, and the customer stays responsible for its own compliance.[^s1]
- **Alignments and frameworks.** These are published requirements for an industry or function, such as NIST CSF, NIST SP 800-53 and FINMA. AWS supports them with mapping documents, playbooks and whitepapers.[^s1]

The programs page lists entries under Global, Americas, Asia Pacific, and Europe, Middle East and Africa, with a separate group for the AWS European Sovereign Cloud.[^s1]

Behind the external audits sits AWS's internal risk program. A business risk management function reports key risks to senior leadership and the board. Recurring controls are automated where possible, for example deployment pipelines, access reviews, log collection and vulnerability scanning. Each service also gets security and architecture readiness reviews before launch and continued scanning after launch.[^s3] AWS also publishes a CSA STAR self-assessment that maps its controls to the Cloud Security Alliance CAIQ.[^s3]

## Evidence and scope

**Reports.** AWS Artifact is the self-service, no-cost portal for SOC reports, PCI attestations, certifications and agreements such as the BAA. New reports appear there as they are released.[^s2][^s3] SOC reports cover 12-month periods. SOC 1 is issued quarterly, and SOC 2 and SOC 3 every six months. The SOC 3 report is public, and AWS issues continued-operations letters instead of bridge letters (as of 2026-11).[^s2]

**Services in Scope.** Certifications apply to specific services, not to all of AWS. The Services in Scope page lists 27 programs, each with its own tab of services. A checkmark means the service appears in current reports, and a service's generally available features are in scope unless they are specifically excluded (as of 2026-11).[^s4] Exclusions are real. For SOC, for example, Bedrock Marketplace, CloudFront Embedded POPs and AWS Directory Service Simple AD are carved out.[^s5] AWS adds services based on expected use cases, customer feedback and demand, and reviews new features at the next assessment opportunity.[^s4][^s5]

## Gotchas

- **Inheritance is partial.** You inherit AWS's controls only for in-scope services and only if you apply the controls assigned to you. Using HITRUST-in-scope services, for example, does not give you a HITRUST certification; you still need your own.[^s2]
- **"Not listed" is not "forbidden".** You can use a service that isn't in scope for a program. You must decide whether regulated data will touch it.[^s4][^s5]
- **No HIPAA certificate exists** for any cloud provider. In a HIPAA account, you can use any service, but protected health information should go only into HIPAA-eligible services.[^s2]
- AWS's newer sign-up experience isn't designed for regulated workloads. For those, AWS points you to the advanced sign-up or to activating advanced features.[^s6]
- Your own obligations depend on how sensitive your data is, your company's compliance objectives and the laws that apply to you.[^s6]

## When to use it

Start here when a workload faces an audit or a procurement questionnaire. First identify the program, then check every service in the design against that program's Services in Scope tab, then download the matching evidence from Artifact.[^s2][^s4] For vendor due-diligence questionnaires, AWS points to Artifact, the CSA CAIQ, and the CyberGRX and CyberVadis assessments.[^s2] To prove your *own* resources stay compliant, add configuration tooling such as AWS Config on top. Each service's "compliance validation" docs page links back to the Services in Scope and Artifact resources.[^s6]

## Connections

This is the parent of the graph's individual programs: it **has part** **FedRAMP on AWS**, the **DoD Cloud Computing SRG impact levels** and **HIPAA eligible services**.[^s1][^s4] It **depends on** **AWS Artifact** to deliver the evidence.[^s2][^s3] It **contrasts with** the **shared responsibility model**: the programs certify AWS's side, while customers own compliance for what they build.[^s1][^s4] It is related to **AWS Config**, which covers the customer's side of continuous checks,[^s6] and to the **AWS European Sovereign Cloud**, which AWS lists as its own compliance grouping.[^s1] It sits under **AWS security services**.

## Sources
[^s1]: Amazon Web Services, "AWS Compliance Programs". https://aws.amazon.com/en/compliance/programs/ (accessed 2026-11-12)
[^s2]: Amazon Web Services, "AWS Compliance FAQs". https://aws.amazon.com/compliance/faq/ (accessed 2026-11-12)
[^s3]: Amazon Web Services, "AWS risk and compliance program" (Amazon Web Services: Risk and Compliance whitepaper). https://docs.aws.amazon.com/whitepapers/latest/aws-risk-and-compliance/aws-risk-and-compliance-program.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, "AWS Services in Scope by Compliance Program". https://aws.amazon.com/compliance/services-in-scope (accessed 2026-11-12)
[^s5]: Amazon Web Services, "AWS Services in Scope by Compliance Program: SOC". https://aws.amazon.com/compliance/services-in-scope/SOC (accessed 2026-11-12)
[^s6]: Amazon Web Services, "Compliance Validation for AWS Config". https://docs.aws.amazon.com/config/latest/developerguide/config-compliance.html (accessed 2026-11-12)
