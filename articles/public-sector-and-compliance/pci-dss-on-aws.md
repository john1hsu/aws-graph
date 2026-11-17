---
id: kt:pci-dss-on-aws
type: ComplianceProgram
title: PCI DSS on AWS
aliases: [AWS PCI DSS compliance, PCI DSS compliance on AWS, AWS PCI DSS Level 1 Service Provider, Payment Card Industry Data Security Standard on AWS, AWS PCI compliance package]
domain: public-sector-and-compliance
summary: >-
  How AWS supports workloads that store, process or transmit payment card data: AWS is assessed as a
  PCI DSS Level 1 Service Provider and publishes its Attestation of Compliance and Responsibility Summary
  in AWS Artifact, while each customer still validates its own cardholder data environment.
verified: 2026-11-17
status: current
broader: [kt:aws-compliance-programs]
related: [kt:hipaa-eligible-services, kt:aws-cloudhsm, kt:aws-config, kt:aws-waf, kt:amazon-vpc]
relations:
  - {predicate: configuredVia, object: kt:aws-artifact}
  - {predicate: governedBy, object: kt:shared-responsibility-model}
  - {predicate: monitoredBy, object: kt:aws-security-hub}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: integratesWith, object: kt:aws-payment-cryptography}
sources:
  - id: s1
    title: "PCI DSS Compliance FAQs - AWS Cloud Security"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/pci-faqs
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Spring 2026 PCI DSS and PCI 3DS compliance packages for AWS now available"
    publisher: AWS Security Blog
    url: https://aws.amazon.com/blogs/security/spring-2026-pci-dss-and-pci-3ds-compliance-packages-for-aws-now-available/
    type: official-blog
    accessed: 2026-11-17
  - id: s3
    title: "Coffee with the Council Podcast: Guidance for PCI DSS E-commerce Requirements Effective After 31 March 2025"
    publisher: PCI Security Standards Council
    url: https://blog.pcisecuritystandards.org/coffee-with-the-council-podcast-guidance-for-pci-dss-e-commerce-requirements-effective-after-31-march-2025
    type: standards-body
    accessed: 2026-11-17
  - id: s4
    title: "PCI DSS in Security Hub CSPM"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/securityhub/latest/userguide/pci-standard.html
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "Updated whitepaper: Architecting for PCI DSS Segmentation and Scoping on AWS"
    publisher: AWS Security Blog
    url: https://aws.amazon.com/blogs/security/updated-whitepaper-architecting-for-pci-dss-segmentation-and-scoping-on-aws
    type: official-blog
    accessed: 2026-11-17
  - id: s6
    title: "AWS Services in Scope by Compliance Program"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope
    type: official-docs
    accessed: 2026-11-17
created: 2026-11-17
---

# PCI DSS on AWS

The Payment Card Industry Data Security Standard (PCI DSS) applies to any organization that stores, processes or transmits cardholder data. AWS is assessed as a PCI DSS **Level 1 Service Provider**, the highest assessment level, by an independent Qualified Security Assessor (QSA), Coalfire. AWS itself does not handle customers' cardholder data; customers build their own cardholder data environment (CDE) on AWS and remain responsible for certifying it.[^s1]

## How it works

**The compliance package.** AWS publishes a PCI DSS package in AWS Artifact containing the Attestation of Compliance (AOC) and an AWS Responsibility Summary that spells out which controls fall to the customer. The package is also offered in NIST's machine-readable OSCAL format. AWS holds separate attestations for PCI 3DS, PCI PIN and PCI P2PE; AWS CloudHSM and AWS Payment Cryptography are PCI PIN certified, and Payment Cryptography is also a P2PE decryption component.[^s1]

**Renewal cadence.** AWS renews the package twice a year. The spring 2026 package, announced in August 2026 and again assessed by Coalfire, added Amazon Bedrock AgentCore, AWS Parallel Computing Service and AWS Skill Builder to scope, plus the Asia Pacific (New Zealand) Region.[^s2] The authoritative service list is the PCI tab of the AWS Services in Scope page, where a check mark means the service appears in current reports.[^s6]

**Relying on the AOC.** For the portion of a CDE that runs on AWS, the customer's QSA can rely on the AWS AOC without retesting AWS's controls, and does not need to tour AWS data centers because physical security is covered. There is no special "PCI environment" or API: any resource in an in-scope service sits in a PCI DSS compliant environment. The environment is multi-tenant, and the QSA validated that each customer is segregated.[^s1]

## The standard's current state

PCI DSS v4.0 was published in 2022, and v3.2.1 was retired on 31 March 2024. The 51 future-dated requirements in v4.0.1 (of 64 new ones) stopped being best practices and became mandatory on 31 March 2025. They include the e-commerce anti-skimming requirements 6.4.3 and 11.6.1, which require payment-page scripts to be authorized, integrity-checked and monitored for tampering.[^s3]

## Tooling on AWS

AWS Security Hub CSPM offers PCI DSS v3.2.1 (44 controls) and v4.0.1 (about 140 controls) standards; AWS recommends v4.0.1 and advises enabling it before disabling v3.2.1 to avoid a coverage gap. AWS suggests central configuration across AWS Organizations and enabling the standard in accounts that hold cardholder or sensitive authentication data (as of 2026-11).[^s4] These automated checks support an assessment but do not replace one.[^s1][^s4]

AWS's segmentation-and-scoping whitepaper (updated November 2024 for PCI DSS 4.0) uses AWS Organizations OUs and account structure as the foundation of segmentation. It covers east-west and north-south traffic patterns and firewall rule examples, and argues that careful service choice can shrink the number of systems in the CDE.[^s5]

## Gotchas

- **Your certification is still yours.** Merchants validate annually through a Report on Compliance (ROC) or a Self-Assessment Questionnaire (SAQ), and the payment brands and acquirers, not the PCI Security Standards Council, enforce compliance.[^s1]
- **Check scope per service and Region.** A service missing from the PCI tab can still be used, but deciding what data it may hold is part of the customer's shared responsibility.[^s6] Use the current AOC in AWS Artifact for the list of locations in scope.[^s1]
- **Forensics.** AWS is not a Shared Hosting Provider under PCI DSS, so requirement A1.4 does not apply; customers run their own forensic investigations.[^s1]
- **SAQ A changed.** Requirements 6.4.3, 11.6.1 and 12.3.1 were removed from SAQ A, which gained an eligibility criterion about exposure to script-based attacks instead.[^s3]

## When to use it (and when not)

Use the AWS AOC whenever a QSA assesses a CDE hosted on AWS: it removes the infrastructure layer from your testing scope. The bigger win is scope reduction: isolate the CDE in dedicated accounts and VPCs, tokenize or offload card handling, and keep unrelated workloads out of scope.[^s5] For health data the comparable mechanism is the HIPAA BAA, and for U.S. federal workloads it is FedRAMP; each is a separate program with its own service list.[^s6]

## Connections

PCI DSS on AWS is one of the **AWS compliance programs**. Its reports come through **AWS Artifact**, and the split of duties follows the **shared responsibility model** via the Responsibility Summary.[^s1] **AWS Security Hub** monitors accounts against the PCI DSS standard, and **AWS Organizations** supplies both central configuration and the OU structure used for segmentation.[^s4][^s5] **AWS CloudHSM** and **AWS Payment Cryptography** carry the PCI PIN attestations for key and PIN handling.[^s1] **HIPAA eligible services** are the analogous program for health data.[^s6]

## Sources
[^s1]: Amazon Web Services, "PCI DSS Compliance FAQs". https://aws.amazon.com/compliance/pci-faqs (accessed 2026-11-17)
[^s2]: AWS Security Blog, "Spring 2026 PCI DSS and PCI 3DS compliance packages for AWS now available" (2026-08-04). https://aws.amazon.com/blogs/security/spring-2026-pci-dss-and-pci-3ds-compliance-packages-for-aws-now-available/ (accessed 2026-11-17)
[^s3]: PCI Security Standards Council, "Coffee with the Council Podcast: Guidance for PCI DSS E-commerce Requirements Effective After 31 March 2025" (2025-03-26). https://blog.pcisecuritystandards.org/coffee-with-the-council-podcast-guidance-for-pci-dss-e-commerce-requirements-effective-after-31-march-2025 (accessed 2026-11-17)
[^s4]: Amazon Web Services, "PCI DSS in Security Hub CSPM". https://docs.aws.amazon.com/securityhub/latest/userguide/pci-standard.html (accessed 2026-11-17)
[^s5]: AWS Security Blog, "Updated whitepaper: Architecting for PCI DSS Segmentation and Scoping on AWS" (2024-11-15). https://aws.amazon.com/blogs/security/updated-whitepaper-architecting-for-pci-dss-segmentation-and-scoping-on-aws (accessed 2026-11-17)
[^s6]: Amazon Web Services, "AWS Services in Scope by Compliance Program". https://aws.amazon.com/compliance/services-in-scope (accessed 2026-11-17)
