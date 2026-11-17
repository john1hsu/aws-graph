---
id: kt:aws-certified-cloudops-engineer-associate
type: Certification
title: AWS Certified CloudOps Engineer - Associate
aliases: [SOA-C03, AWS Certified SysOps Administrator - Associate, SysOps Administrator Associate, SOA-C02, AWS CloudOps Engineer Associate]
domain: certifications
summary: >-
  AWS's associate-level operations certification (exam SOA-C03), validating the ability to monitor,
  automate, secure, network and keep AWS workloads available; it replaced the SysOps Administrator -
  Associate name and SOA-C02 exam in September 2025.
verified: 2026-11-17
status: current
broader: [kt:aws-certification-program]
related: [kt:aws-certified-devops-engineer-professional, kt:amazon-cloudwatch, kt:aws-systems-manager, kt:aws-cloudformation, kt:aws-backup, kt:aws-skill-builder]
relations:
  - {predicate: contrastsWith, object: kt:aws-certified-solutions-architect-associate}
  - {predicate: contrastsWith, object: kt:aws-certified-developer-associate}
sources:
  - id: s1
    title: "AWS Certified CloudOps Engineer - Associate (SOA-C03) Exam Guide (PDF)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/sysops-administrator-associate-03/sysops-administrator-associate-03.pdf
    type: official-learning
    accessed: 2026-11-17
  - id: s2
    title: "AWS Certified CloudOps Engineer - Associate"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-cloudops-engineer-associate
    type: official-learning
    accessed: 2026-11-17
  - id: s3
    title: "Exam update and new name for operations certification"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/exam-update-and-new-name-for-operations-certification
    type: official-blog
    accessed: 2026-11-17
  - id: s4
    title: "AWS Certification - Recertification"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/recertification/
    type: official-learning
    accessed: 2026-11-17
created: 2026-11-17
---

# AWS Certified CloudOps Engineer - Associate

AWS Certified CloudOps Engineer - Associate is AWS's associate-level certification for people who run workloads in production. It validates monitoring and maintaining workloads, applying security controls and networking concepts, running business-continuity procedures, and optimizing cost and performance.[^s2] AWS targets candidates with about one year of hands-on experience deploying, managing, networking and securing workloads on AWS, and suggests Cloud Practitioner first for people with no IT background.[^s2] The exam guide adds at least a year in an operations role such as system administrator, plus general knowledge of logging, DNS and TCP/IP, scripting, an operating system, containers and Git-based CI/CD.[^s1]

## History: from SysOps to CloudOps

The certification used to be called AWS Certified SysOps Administrator - Associate. AWS announced the new name in July 2025 to reflect how cloud operations work has changed and to match current industry terms.[^s3] The rename came with a new exam version: SOA-C03 brings containers into scope and puts more weight on multi-account and multi-Region operations, automation and infrastructure as code. AWS says no task statements were removed in the change.[^s3] The last day to sit SOA-C02 was September 29, 2025, and SOA-C03 has been the exam since September 30, 2025.[^s3][^s1]

Only people who pass SOA-C03 get the new name. Existing SysOps holders do not need to retake anything, and their credential stays valid for three years from when they earned it.[^s2][^s3] Someone who earns both gets two separate badges.[^s3]

## Exam format (as of 2026-11)

The exam has 65 multiple-choice or multiple-response questions in 130 minutes. It costs 150 USD (foreign-exchange charges may apply) and is taken at a Pearson VUE test center or online proctored.[^s2] It is offered in English, Japanese, Korean and Simplified Chinese, but the product page says the Korean and Simplified Chinese versions will be retired after November 19, 2026.[^s2] Of the 65 questions, 50 are scored and 15 are unmarked, unscored trial items. Scores run from 100 to 1,000 with 720 to pass under a compensatory model, so only the overall score counts. Unanswered questions are marked wrong and guessing is not penalized.[^s1] The current guide describes only multiple-choice and multiple-response items, not hands-on lab questions.[^s1]

## What the exam covers

SOA-C03 has five domains: Monitoring, Logging, Analysis, Remediation, and Performance Optimization (22%); Reliability and Business Continuity (22%); Deployment, Provisioning, and Automation (22%); Security and Compliance (16%); and Networking and Content Delivery (18%).[^s1]

Compared with SOA-C02, three of these domains each gained weight, and the old standalone cost-and-performance domain was folded into the others.[^s1] Monitoring covers CloudWatch metrics, alarms and cross-account dashboards, plus the CloudWatch agent on EC2, ECS and EKS. It also covers Amazon Managed Service for Prometheus and Grafana, and automated fixes with EventBridge, Lambda and Systems Manager Automation runbooks.[^s1] Reliability covers Auto Scaling, caching, ELB and Route 53 health checks, Multi-AZ designs, and AWS Backup. It also covers point-in-time restores mapped to RTO and RPO, and the backup-and-restore, pilot light, warm standby and active/active disaster-recovery patterns.[^s1]

Deployment covers EC2 Image Builder, CloudFormation and the AWS CDK, StackSets and AWS RAM for multi-account sharing, and third-party tools such as Terraform. Security covers IAM troubleshooting with Access Analyzer and the policy simulator, Organizations with SCPs, IAM Identity Center, Config conformance packs, KMS, ACM and Secrets Manager. It also covers findings from Security Hub, GuardDuty and Inspector.[^s1] Networking covers VPC building blocks, endpoints and PrivateLink, DNS Firewall, WAF and Shield, and Route 53 Resolver. It also covers troubleshooting with VPC Flow Logs and load-balancer, WAF and CloudFront logs.[^s1]

## Gotchas

The guide is revised in place: version 1.2 is dated October 29, 2026, after versions dated September 2025 and June 2026, and AWS publishes revisions at least a month before they reach the exam.[^s1] Recent in-scope additions include Amazon Bedrock, Kiro, AWS DevOps Agent and AWS Security Agent, so older study notes miss them.[^s1] The guide also lists tasks outside the candidate's job, such as designing distributed or hybrid architectures, building CI/CD pipelines, capacity planning and billing management.[^s1]

## Renewal and when to take it

The credential is valid for three years. You renew it by passing the latest CloudOps exam or the DevOps Engineer - Professional exam, or extend it by one year through Skill Builder maintenance with a paid subscription within 90 days of expiry.[^s4] AWS suggests DevOps Engineer - Professional and Security - Specialty as next steps.[^s2]

Choose it if your job is operating and troubleshooting what already exists. Solutions Architect - Associate tests designing workloads, and Developer - Associate tests building applications on AWS services.[^s1][^s2]

## Connections

It is the operations-track associate in the AWS Certification program and the natural step before DevOps Engineer - Professional, which also renews it.[^s2][^s4] Its heaviest service links are Amazon CloudWatch, AWS Systems Manager, AWS CloudFormation and AWS Backup.[^s1] AWS Skill Builder hosts the official prep material and the maintenance route.[^s2][^s4] It contrasts with the Solutions Architect and Developer associates, which share many services but test different jobs.[^s1]

## Sources
[^s1]: Amazon Web Services, "AWS Certified CloudOps Engineer - Associate (SOA-C03) Exam Guide (PDF)", version 1.2. https://docs.aws.amazon.com/aws-certification/latest/sysops-administrator-associate-03/sysops-administrator-associate-03.pdf (accessed 2026-11-17)
[^s2]: Amazon Web Services, "AWS Certified CloudOps Engineer - Associate". https://aws.amazon.com/certification/certified-cloudops-engineer-associate (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Exam update and new name for operations certification", AWS Training and Certification Blog, 2025-07-15 (updated 2025-09-09). https://aws.amazon.com/blogs/training-and-certification/exam-update-and-new-name-for-operations-certification (accessed 2026-11-17)
[^s4]: Amazon Web Services, "AWS Certification - Recertification". https://aws.amazon.com/certification/recertification/ (accessed 2026-11-17)
