---
id: kt:aws-certified-security-specialty
type: Certification
title: AWS Certified Security - Specialty
aliases: [SCS-C03, SCS-C02, AWS Security Specialty, AWS Certified Security Specialty]
domain: certifications
summary: >-
  AWS's specialty-level certification for people responsible for securing cloud solutions, validating
  knowledge of detection, incident response, infrastructure security, identity, data protection and
  security governance on AWS; the current exam version is SCS-C03.
verified: 2026-11-13
status: current
broader: [kt:aws-certification-program]
related: [kt:aws-security-services, kt:security-pillar, kt:aws-iam, kt:aws-kms, kt:amazon-guardduty]
relations:
  - {predicate: contrastsWith, object: kt:aws-certified-solutions-architect-professional}
sources:
  - id: s1
    title: "AWS Certified Security - Specialty (SCS-C03) exam guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/security-specialty-03/security-specialty-03.md
    type: official-learning
    accessed: 2026-11-13
  - id: s2
    title: "AWS Certified Security - Specialty"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-security-specialty
    type: official-learning
    accessed: 2026-11-13
  - id: s3
    title: "AWS Recertification"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/recertification/
    type: official-learning
    accessed: 2026-11-13
  - id: s4
    title: "AWS expands AI certification portfolio and updates security certification"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/big-news-aws-expands-ai-certification-portfolio-and-updates-security-certification
    type: official-blog
    accessed: 2026-11-13
  - id: s5
    title: "AWS Training and Certification new offerings: December 2025"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/december-2025-new-offerings
    type: official-blog
    accessed: 2026-11-13
created: 2026-11-13
---

# AWS Certified Security - Specialty

AWS Certified Security - Specialty is AWS's specialty-level certification for people whose job includes securing cloud solutions.[^s1][^s2] It validates that a candidate understands how to secure AWS products and services: applying data classification and AWS data-protection mechanisms, implementing encryption and secure internet protocols, using AWS security services to protect production environments, weighing cost, security and deployment complexity against application requirements, and understanding security operations and risk.[^s1] The exam guide describes a target candidate with the equivalent of 3–5 years of experience securing cloud solutions, while the product page asks for five years of IT security experience and at least two years of hands-on work securing AWS workloads.[^s1][^s2] There are no formal prerequisites, though many candidates first hold a Solutions Architect certification.[^s2]

## Exam format and scoring

As of 2026-11, the product page lists 65 questions in 170 minutes for 300 USD (foreign-exchange and other costs may apply), delivered at a Pearson VUE test center or as an online proctored exam.[^s2] Fifty questions are scored and 15 are unidentified, unscored trial items; unanswered questions count as wrong and guessing is not penalized.[^s1] Besides multiple choice and multiple response, the SCS-C03 guide lists **ordering** questions (put 3–5 responses in the right sequence) and **matching** questions (pair responses with 3–7 prompts), each scored all-or-nothing.[^s1] Results are pass/fail on a scaled score of 100–1,000 with **750** to pass, under a compensatory model where only the overall score matters.[^s1]

The exam is offered in English, Japanese, Korean, Portuguese (Brazil), Simplified Chinese and Spanish (Latin America), but AWS states that the Simplified Chinese, Spanish (Latin America) and Portuguese (Brazil) versions retire after December 31, 2026.[^s2]

## What SCS-C03 covers

The SCS-C03 guide splits scored content into six domains:[^s1]

- **Detection (16%)**[^s1]
- **Incident Response (14%)**[^s1]
- **Infrastructure Security (18%)**[^s1]
- **Identity and Access Management (20%)**[^s1]
- **Data Protection (18%)**[^s1]
- **Security Foundations and Governance (14%)**[^s1]

Recommended knowledge spans the shared responsibility model, identity at scale, multi-account governance, software supply-chain risk, vulnerability management, layer 3–7 firewall rules at scale, root-cause analysis, audit response, logging and monitoring, encryption at rest and in transit, and backup-based disaster recovery controls.[^s1] Designing cryptographic algorithms, packet-level traffic analysis, architecting whole cloud deployments, managing end-user compute and training ML models are explicitly out of scope.[^s1]

## SCS-C02 to SCS-C03 transition

AWS announced the update on October 14, 2025, opening SCS-C03 registration and giving SCS-C02 candidates until December 1, 2025 to finish.[^s4] The revision restructured the domains so Detection and Incident Response are separate sections, and added a dedicated focus on generative AI and machine learning security.[^s4] AWS's December 2025 roundup reiterated that greater generative AI and ML security focus and pointed to an updated Exam Prep Plan on AWS Skill Builder.[^s5] Gotcha: study material written for SCS-C02 will miss the AI/ML security content and the new question formats.[^s1][^s4]

## Validity and recertification

The credential is valid for three years.[^s2] It renews for another three years by passing the latest version of the exam (the 50% voucher in the certification account can be used), or it can be maintained for one year through AWS Skill Builder with an active paid subscription, starting within 90 days of expiry while the certification is still active.[^s3] Unlike associate and professional exams, passing Security - Specialty is not listed as a way to renew Cloud Practitioner or any associate certification.[^s3] Any AWS certification earns 50% off the next exam.[^s2]

## When to take it (and when not)

It fits security engineers, cloud security architects and SecOps staff who run detection, response, identity and encryption controls on AWS day to day.[^s1] People whose main job is designing whole architectures are better served by the Solutions Architect track, because architecting overall deployments is out of scope here.[^s1]

## Connections

It sits in the specialty tier of the AWS Certification program, alongside the professional-level certifications rather than below them.[^s2] Its domains (detection, identity, data protection, infrastructure security, incident response and governance) line up with the topics of the Well-Architected Security Pillar and with AWS's security service family, such as IAM for identity, AWS KMS for encryption and Amazon GuardDuty for threat detection.[^s1] It contrasts with Solutions Architect - Professional: both are advanced credentials, but this one goes deep on security operations while explicitly excluding overall architecture design.[^s1]

## Sources
[^s1]: Amazon Web Services, "AWS Certified Security - Specialty (SCS-C03) exam guide". https://docs.aws.amazon.com/aws-certification/latest/security-specialty-03/security-specialty-03.md (accessed 2026-11-13)
[^s2]: Amazon Web Services, "AWS Certified Security - Specialty". https://aws.amazon.com/certification/certified-security-specialty (accessed 2026-11-13)
[^s3]: Amazon Web Services, "AWS Recertification". https://aws.amazon.com/certification/recertification/ (accessed 2026-11-13)
[^s4]: Amazon Web Services, "AWS expands AI certification portfolio and updates security certification". https://aws.amazon.com/blogs/training-and-certification/big-news-aws-expands-ai-certification-portfolio-and-updates-security-certification (accessed 2026-11-13)
[^s5]: Amazon Web Services, "AWS Training and Certification new offerings: December 2025". https://aws.amazon.com/blogs/training-and-certification/december-2025-new-offerings (accessed 2026-11-13)
