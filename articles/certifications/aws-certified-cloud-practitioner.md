---
id: kt:aws-certified-cloud-practitioner
type: Certification
title: AWS Certified Cloud Practitioner
aliases: [CLF-C02, AWS Cloud Practitioner, CCP]
domain: certifications
summary: >-
  AWS's foundational-level certification (exam CLF-C02) validating broad, non-technical-depth
  knowledge of cloud concepts, AWS security and compliance, core AWS services, and AWS billing,
  pricing and support, aimed at people new to the cloud or working alongside AWS roles.
verified: 2026-11-09
status: current
broader: [kt:aws-certification-program]
related: [kt:aws-well-architected-framework, kt:aws-cloud-adoption-framework, kt:shared-responsibility-model, kt:aws-support-plans, kt:aws-certified-solutions-architect-associate]
relations:
  - {predicate: contrastsWith, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "AWS Certified Cloud Practitioner - Exam Guide (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02.pdf
    type: official-learning
    accessed: 2026-11-09
  - id: s2
    title: "AWS Certified Cloud Practitioner"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-cloud-practitioner/
    type: official-learning
    accessed: 2026-11-09
  - id: s3
    title: "In-Scope AWS Services - AWS Certification (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS Certified Cloud Practitioner

AWS Certified Cloud Practitioner is AWS's foundational-level certification, currently delivered as exam CLF-C02.[^s1][^s2] It checks that a candidate understands cloud concepts, security and compliance, the core AWS services and cloud economics well enough to talk about them, not that they can build on AWS.[^s1] AWS pitches it at people new to the cloud, including those without an IT background and line-of-business staff; up to six months of AWS exposure is helpful but not required, and there are no formal prerequisites.[^s2]

## Exam format and scoring

As of 2026-11, the exam has 65 questions in 90 minutes, costs 100 USD (foreign-exchange and other costs may apply), and is taken at a Pearson VUE test center or as an online proctored exam.[^s2] Fifty questions are scored and 15 are unidentified, unscored items AWS is trialling.[^s1] Questions are multiple choice (one right answer, three distractors) or multiple response (two or more right answers out of five or more options); unanswered questions count as wrong and guessing carries no penalty.[^s1] Results are pass/fail on a scaled score of 100–1,000 with 700 to pass, under a compensatory model, so section feedback is only guidance.[^s1]

The credential lasts three years. AWS lists three ways to keep it: pass the latest Cloud Practitioner exam, pass any Associate- or Professional-level AWS exam, or complete the game-based "AWS Cloud Quest: Recertify Cloud Practitioner" course, which needs no exam.[^s2] The exam is offered in twelve languages, but AWS says the German and Italian versions are retired after December 31, 2026.[^s2]

## What the exam covers

The scored content has four domains: Cloud Concepts (24%), Security and Compliance (30%), Cloud Technology and Services (34%), and Billing, Pricing, and Support (12%).[^s1]

- **Cloud Concepts** covers cloud benefits such as elasticity and global reach, the six Well-Architected pillars, the AWS Cloud Adoption Framework and migration strategies, and cloud economics such as fixed versus variable cost, licensing and rightsizing.[^s1]
- **Security and Compliance** centres on the shared responsibility model and how it shifts between services like Amazon EC2, Amazon RDS and AWS Lambda, plus IAM practices (root-user protection, least privilege, MFA, IAM Identity Center, federation) and services such as AWS Artifact, CloudTrail, Config, GuardDuty, Inspector, Security Hub, Shield and WAF.[^s1]
- **Cloud Technology and Services** spans ways to deploy (console, CLI, SDKs, APIs, infrastructure as code), Regions, Availability Zones and edge locations, and compute, database, networking, storage, AI/ML and analytics services.[^s1]
- **Billing, Pricing, and Support** covers On-Demand, Reserved, Spot, Savings Plans and Dedicated options; Budgets, Cost Explorer, Pricing Calculator and consolidated billing in AWS Organizations; and the AWS Support plans, re:Post, Partner Network and Marketplace.[^s1]

The in-scope service list is broad, grouped by category from Analytics to Storage, and includes newer names such as Amazon SageMaker AI, Amazon Q and Amazon WorkSpaces Secure Browser; AWS calls it non-exhaustive and subject to change.[^s3]

## Gotchas and scope

Coding, designing cloud architectures, troubleshooting, implementation, and load or performance testing are explicitly outside what the target candidate is expected to do.[^s1] Although the exam code has stayed CLF-C02, the guide itself is revised: its history lists versions 1.0 through 1.3 dated June 24 to October 29, 2026, with updates published about a month before they apply, and the support-plan task now names Basic, Business Support+, Enterprise Support and AWS Unified Operations.[^s1] Study material written against older guide versions can therefore be out of date on service names and support tiers.[^s1]

## When to take it (and when not)

It suits non-technical and early-career people who need a shared AWS vocabulary, and technical staff who want a first credential before an Associate exam.[^s2] Engineers with hands-on experience can usually skip it: an Associate-level exam such as Solutions Architect – Associate goes deeper and, on AWS's stated rules, also renews Cloud Practitioner.[^s2]

## Connections

Cloud Practitioner sits at the foundational level of the AWS Certification program next to AWS Certified AI Practitioner; the two contrast in focus, general cloud literacy here versus AI and generative AI there.[^s1][^s2] Its Cloud Concepts domain draws directly on the AWS Well-Architected Framework and the AWS Cloud Adoption Framework, its security domain on the shared responsibility model, and its billing domain on AWS Support plans.[^s1] Associate certifications such as Solutions Architect – Associate are the usual next step and a recertification path.[^s2]

## Sources
[^s1]: Amazon Web Services, "AWS Certified Cloud Practitioner - Exam Guide (CLF-C02)". https://docs.aws.amazon.com/pdfs/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02.pdf (accessed 2026-11-09)
[^s2]: Amazon Web Services, "AWS Certified Cloud Practitioner". https://aws.amazon.com/certification/certified-cloud-practitioner/ (accessed 2026-11-09)
[^s3]: Amazon Web Services, "In-Scope AWS Services - AWS Certification (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-09)
