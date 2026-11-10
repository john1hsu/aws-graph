---
id: kt:aws-certified-solutions-architect-associate
type: Certification
title: AWS Certified Solutions Architect - Associate
aliases: [SAA-C03, AWS Solutions Architect Associate, AWS SAA]
domain: certifications
summary: >-
  AWS's associate-level certification (exam SAA-C03) validating the ability to design secure,
  resilient, high-performing and cost-optimized solutions on AWS in line with the AWS
  Well-Architected Framework, aimed at people working in a solutions architect role.
verified: 2026-11-10
status: current
broader: [kt:aws-certification-program]
related: [kt:aws-well-architected-framework, kt:aws-certified-cloud-practitioner, kt:aws-certified-solutions-architect-professional, kt:disaster-recovery-strategies, kt:amazon-vpc, kt:aws-iam]
sources:
  - id: s1
    title: "AWS Certified Solutions Architect - Associate (SAA-C03) Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.pdf
    type: official-learning
    accessed: 2026-11-10
  - id: s2
    title: "AWS Certified Solutions Architect - Associate"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-solutions-architect-associate
    type: official-learning
    accessed: 2026-11-10
  - id: s3
    title: "AWS Certification Recertification"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/recertification/
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# AWS Certified Solutions Architect - Associate

AWS Certified Solutions Architect - Associate is AWS's associate-level architecture certification, delivered as exam SAA-C03.[^s1][^s2] It tests whether a candidate can design AWS solutions that follow the AWS Well-Architected Framework: meeting today's requirements while allowing for growth, and being secure, resilient, high-performing and cost-optimized, as well as reviewing existing designs for improvement.[^s1] The target candidate works in a solutions architect role, and the exam guide recommends at least one year of hands-on experience designing cloud solutions with AWS services.[^s1] There are no formal prerequisites; AWS suggests that people with no IT background earn AWS Certified Cloud Practitioner first.[^s2]

## Exam format and scoring

As of 2026-11, the exam has 65 multiple-choice or multiple-response questions in 130 minutes, costs 150 USD (foreign-exchange and other costs may apply), and is taken at a Pearson VUE test center or online with a proctor.[^s2] Fifty questions count toward the score; the other 15 are unidentified, unscored items AWS is evaluating for future use.[^s1] Unanswered questions are marked wrong and there is no guessing penalty.[^s1] The result is pass/fail on a scaled score from 100 to 1,000 with **720** required to pass, under a compensatory model: you pass the exam overall rather than each section, so per-section feedback is only a rough indicator.[^s1]

The exam is offered in ten languages; AWS says the Italian version retires after December 31, 2026.[^s2] The English exam now uses short names for some services, with a lookup list behind the in-exam Help button, and AWS plans to extend that to other languages.[^s2]

## What the exam covers

The scored content is split into four domains that mirror Well-Architected pillars:[^s1]

- **Design Secure Architectures (30%)** — multi-account access with IAM, IAM Identity Center, AWS STS roles, Control Tower and SCPs; VPC security with security groups, network ACLs, route tables and NAT gateways; services such as Cognito, GuardDuty, Macie, Shield, WAF and Secrets Manager; and encryption at rest with KMS and in transit with ACM-issued TLS certificates.[^s1]
- **Design Resilient Architectures (26%)** — loose coupling with SQS, API Gateway, Step Functions and event-driven designs; scaling, load balancing and containers (ECS, EKS); multi-AZ and multi-Region failover with Route 53; and disaster recovery strategies (backup and restore, pilot light, warm standby, active-active) measured against RPO and RTO.[^s1]
- **Design High-Performing Architectures (24%)** — choosing storage (S3, EFS, EBS), compute (EC2, Lambda, Fargate, Batch, EMR), databases (Aurora, DynamoDB, ElastiCache, read replicas, proxies), network edges (CloudFront, Global Accelerator, Direct Connect, PrivateLink) and ingestion pipelines (Kinesis, Glue, Athena, Lake Formation, DataSync).[^s1]
- **Design Cost-Optimized Architectures (20%)** — Cost Explorer, Budgets and the Cost and Usage Report; S3 lifecycle tiering; Spot, Reserved Instances and Savings Plans; database selection; and network cost levers such as NAT gateway placement, VPC endpoints and Region-to-Region versus AZ-to-AZ transfer charges.[^s1]

The guide lists in-scope services by category (dozens, from Analytics to Storage) and an out-of-scope list that includes most developer tools such as AWS CDK and CodeBuild, all IoT services, AWS Elemental media services and Amazon Braket.[^s1]

## Gotchas

The exam code has stayed SAA-C03, but the guide is revised in place: its current revision history begins with version 1.0 on August 28, 2026 and version 1.1 on October 29, 2026, which removed Amazon Elastic Transcoder from the in-scope list.[^s1] AWS publishes guide updates about a month before they apply, so study material built on older service lists can be out of date.[^s1] The questions are scenario-based trade-offs, so knowing what each service does is not enough; the four domains are framed around choosing the most secure, resilient, performant or cheapest option for a stated requirement.[^s1]

## Validity and recertification

Like other AWS certifications it is valid for three years.[^s2][^s3] It can be renewed by passing the latest SAA exam or AWS Certified Solutions Architect - Professional (each adds three years), or maintained through AWS Skill Builder within 90 days of expiry with a paid subscription (adds one year).[^s3] Passing it also renews AWS Certified Cloud Practitioner, since any associate-level exam does.[^s3] Holders of an active AWS certification get a 50% discount on their next exam.[^s2]

## When to take it (and when not)

It suits engineers and architects who design or review AWS workloads and want the most broadly recognised AWS associate credential.[^s2] People new to IT are better served starting with Cloud Practitioner, and experienced architects aiming higher can go straight to the Professional exam, which also keeps this associate certification current.[^s2][^s3]

## Connections

The certification sits at the associate level of the AWS Certification program, above Cloud Practitioner and below Solutions Architect - Professional, which renews it.[^s2][^s3] Its domains are built on the AWS Well-Architected Framework, and its task statements lean heavily on graph nodes such as AWS IAM, Amazon VPC and disaster recovery strategies; articles whose subject the guide lists link here with `coveredBy` edges.[^s1]

## Sources
[^s1]: Amazon Web Services, "AWS Certified Solutions Architect - Associate (SAA-C03) Exam Guide". https://docs.aws.amazon.com/pdfs/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.pdf (accessed 2026-11-10)
[^s2]: Amazon Web Services, "AWS Certified Solutions Architect - Associate". https://aws.amazon.com/certification/certified-solutions-architect-associate (accessed 2026-11-10)
[^s3]: Amazon Web Services, "AWS Certification Recertification". https://aws.amazon.com/certification/recertification/ (accessed 2026-11-10)
