---
id: kt:aws-certified-solutions-architect-professional
type: Certification
title: AWS Certified Solutions Architect - Professional
aliases: [SAP-C02, SAP-C03, AWS Solutions Architect Professional, AWS SAP]
domain: certifications
summary: >-
  AWS's professional-level architecture certification, validating advanced skill in designing
  optimized AWS solutions for complex organizations, migrations and modernization in line with the
  AWS Well-Architected Framework; exam SAP-C02 is being replaced by SAP-C03 in November 2026.
verified: 2026-11-11
status: current
broader: [kt:aws-certification-program]
related: [kt:aws-certified-solutions-architect-associate, kt:aws-well-architected-framework, kt:aws-organizations, kt:aws-control-tower, kt:disaster-recovery-strategies, kt:seven-rs-migration-strategies]
sources:
  - id: s1
    title: "AWS Certified Solutions Architect - Professional (SAP-C02) Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-professional-02/solutions-architect-professional-02.html
    type: official-learning
    accessed: 2026-11-11
  - id: s2
    title: "Content Domain 1: Design Solutions for Organizational Complexity (SAP-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-professional-02/solutions-architect-professional-02-domain1.html
    type: official-learning
    accessed: 2026-11-11
  - id: s3
    title: "Content Domain 4: Accelerate Workload Migration and Modernization (SAP-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-professional-02/solutions-architect-professional-02-domain4.html
    type: official-learning
    accessed: 2026-11-11
  - id: s4
    title: "AWS Certified Solutions Architect - Professional"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-solutions-architect-professional/?ch=sec&d=1&sec=rmg
    type: official-learning
    accessed: 2026-11-11
  - id: s5
    title: "Certification updates from AWS Training and Certification: September 2026"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/training-and-certification/september-2026-new-offerings/
    type: official-blog
    accessed: 2026-11-11
created: 2026-11-11
---

# AWS Certified Solutions Architect - Professional

AWS Certified Solutions Architect - Professional is the professional-level architecture credential in the AWS Certification program. Its exam guide says it validates advanced skill in designing optimized AWS solutions based on the AWS Well-Architected Framework: designing for organizational complexity, designing new solutions, continuously improving existing ones, and accelerating workload migration and modernization.[^s1] The target candidate has two or more years of experience using AWS to design and implement cloud solutions and gives architectural guidance across multiple applications and projects in a complex organization.[^s1][^s4] There are no formal prerequisites.[^s4]

## Exam version change: SAP-C02 to SAP-C03

The certification is mid-transition as of 2026-11. AWS announced on September 1, 2026 that registration for the new SAP-C03 exam opened on October 27, 2026, that **November 16, 2026** is the last day to sit SAP-C02, and that SAP-C03 delivery begins on **November 17, 2026**.[^s5] A credential earned on SAP-C02 stays active until its original expiry date.[^s5] The certification page now links to an SAP-C03 exam guide and prep plan.[^s4]

AWS describes SAP-C03 as reorganized into five domains — cloud-native architecture design and implementation; security, compliance and governance; cost-optimized architecture design; resilience, migration and business continuity; and operational excellence and automation.[^s5] New skills span generative and agentic AI (Amazon Bedrock, Bedrock AgentCore, RAG architectures), resilience engineering (AWS Fault Injection Service, AWS Resilience Hub, Application Recovery Controller, Systems Manager runbooks), serverless and Step Functions patterns, DevSecOps and observability, Lake Formation with Apache Iceberg and AWS Clean Rooms, and post-quantum cryptography in AWS KMS (ML-DSA and ML-KEM).[^s5] The SAP-C02 guide already previewed this direction with unscored "emerging topics" on AI security controls, such as Bedrock Guardrails, AgentCore Identity and Step Functions approval workflows.[^s1]

## Exam format and scoring

As of 2026-11, the certification page lists 75 multiple-choice or multiple-response questions in 180 minutes, a price of 300 USD (foreign-exchange and other costs may apply), and delivery at a Pearson VUE test center or online with a proctor, in English, Japanese, Korean, Portuguese (Brazil), Simplified Chinese and Spanish (Latin America).[^s4] AWS gave the same length, duration and price for SAP-C03.[^s5] Under the SAP-C02 guide, 65 questions are scored and 10 are unidentified unscored items; results are pass/fail on a 100–1,000 scale with **750** to pass, and the model is compensatory, so you pass the exam overall rather than each domain.[^s1] Multiple-response items need every correct option selected, and unanswered questions count as wrong with no extra guessing penalty.[^s1]

## What SAP-C02 covers

The SAP-C02 domains are Design Solutions for Organizational Complexity (26%), Design for New Solutions (29%), Continuous Improvement for Existing Solutions (25%) and Accelerate Workload Migration and Modernization (20%).[^s1] Domain 1 spans hybrid and multi-VPC networking (Direct Connect, VPN, transitive routing, Route 53 Resolver), cross-account security controls, disaster recovery against RTO and RPO targets, multi-account design with AWS Organizations and AWS Control Tower, and cost visibility through tagging and purchasing options.[^s2] Domain 4 tests migration assessment against the 7 Rs and TCO, choosing transfer tools (DataSync, Transfer Family, Snow Family, Application Migration Service, DMS and SCT), and modernizing onto serverless, containers, purpose-built databases and integration services.[^s3]

## Gotchas and recertification

Material written for SAP-C02 maps poorly onto SAP-C03's five-domain layout and new AI and resilience skills, so check which exam you are booked on.[^s5] The certification is valid for three years and is renewed by passing the latest version of the exam.[^s4] Earning any AWS certification gives a 50% discount on the next exam.[^s4]

## When to take it (and when not)

It suits architects who already design multi-account, hybrid and migration-heavy AWS estates; the candidate profile assumes years of hands-on design work rather than service familiarity.[^s1] Because there are no prerequisites, less experienced engineers can book it directly, but the Associate exam covers the same Well-Architected framing at a narrower scope and is the usual stepping stone.[^s1][^s4] AWS suggests taking SAP-C02 before its cut-off only if you are already prepared, and otherwise waiting for SAP-C03.[^s5]

## Connections

The credential sits above AWS Certified Solutions Architect - Associate in the AWS Certification program, and its domains lean on graph nodes such as the AWS Well-Architected Framework, AWS Organizations, AWS Control Tower and disaster recovery strategies.[^s1][^s2] Its migration domain draws on the 7 Rs migration strategies and AWS migration services.[^s3]

## Sources
[^s1]: Amazon Web Services, "AWS Certified Solutions Architect - Professional (SAP-C02) Exam Guide". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-professional-02/solutions-architect-professional-02.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Content Domain 1: Design Solutions for Organizational Complexity (SAP-C02)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-professional-02/solutions-architect-professional-02-domain1.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Content Domain 4: Accelerate Workload Migration and Modernization (SAP-C02)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-professional-02/solutions-architect-professional-02-domain4.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "AWS Certified Solutions Architect - Professional". https://aws.amazon.com/certification/certified-solutions-architect-professional/?ch=sec&d=1&sec=rmg (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Certification updates from AWS Training and Certification: September 2026". https://aws.amazon.com/blogs/training-and-certification/september-2026-new-offerings/ (accessed 2026-11-11)
