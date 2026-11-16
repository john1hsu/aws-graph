---
id: kt:aws-certified-devops-engineer-professional
type: Certification
title: AWS Certified DevOps Engineer - Professional
aliases: [DOP-C02, AWS DevOps Engineer Professional, AWS DevOps Pro, AWS DOP]
domain: certifications
summary: >-
  AWS's professional-level certification for DevOps engineers, validating skill in provisioning,
  operating and managing distributed systems on AWS: CI/CD, infrastructure as code, resilience,
  monitoring, incident response and automated security and compliance (exam DOP-C02).
verified: 2026-11-16
status: current
broader: [kt:aws-certification-program]
related: [kt:aws-certified-developer-associate, kt:aws-certified-cloudops-engineer-associate, kt:aws-codepipeline, kt:aws-cloudformation, kt:aws-systems-manager, kt:blue-green-deployments]
relations:
  - {predicate: contrastsWith, object: kt:aws-certified-solutions-architect-professional}
sources:
  - id: s1
    title: "AWS Certified DevOps Engineer - Professional (DOP-C02) Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/devops-engineer-professional-02/devops-engineer-professional-02.html
    type: official-learning
    accessed: 2026-11-16
  - id: s2
    title: "Content Domain 1: SDLC Automation (DOP-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/devops-engineer-professional-02/devops-engineer-professional-02-domain1.html
    type: official-learning
    accessed: 2026-11-16
  - id: s3
    title: "Content Domain 2: Configuration Management and IaC (DOP-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/devops-engineer-professional-02/devops-engineer-professional-02-domain2.html
    type: official-learning
    accessed: 2026-11-16
  - id: s4
    title: "AWS Certified DevOps Engineer - Professional"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-devops-engineer-professional/
    type: official-learning
    accessed: 2026-11-16
  - id: s5
    title: "AWS Recertification"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/recertification/
    type: official-learning
    accessed: 2026-11-16
  - id: s6
    title: "Updated AWS Certified DevOps Engineer - Professional: registration now open"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/training-and-certification/updated-aws-certified-devops-engineer-professional-registration-now-open/
    type: official-blog
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS Certified DevOps Engineer - Professional

AWS Certified DevOps Engineer - Professional is the professional-level credential in the AWS Certification program for people who build and run delivery pipelines and operations on AWS. The DOP-C02 exam guide says it validates technical expertise in provisioning, operating and managing distributed systems and services on AWS, including continuous delivery, automated security controls and compliance validation, monitoring and logging, highly available and self-healing systems, and tooling that automates operational processes.[^s1] The target candidate has two or more years of experience provisioning, operating and managing AWS environments, plus experience with the software development lifecycle and with programming or scripting.[^s1][^s4]

## Exam format and scoring

As of 2026-11, the certification page lists a Professional-category exam of 75 multiple-choice or multiple-response questions in 180 minutes, priced at 300 USD (foreign-exchange and other costs may apply), taken at a Pearson VUE test center or online with a proctor, in English, Japanese, Korean and Simplified Chinese.[^s4] The page also notes that the Korean version will be retired after December 31, 2026.[^s4] Per the exam guide, 65 questions are scored and 10 are unidentified unscored items; results are pass/fail on a 100–1,000 scale with **750** to pass, and the scoring is compensatory, so you must pass the exam overall rather than each domain.[^s1] Unanswered questions count as wrong, and there is no penalty for guessing.[^s1] As of this check, neither the exam guide nor the certification page announces a successor exam version.[^s1][^s4]

## What DOP-C02 covers

The six content domains are SDLC Automation (22%), Configuration Management and IaC (17%), Resilient Cloud Solutions (15%), Monitoring and Logging (15%), Incident and Event Response (14%) and Security and Compliance (17%).[^s1] Domain 1 covers CI/CD pipelines for single- and multi-account setups, build stages in AWS CodeBuild, handling build secrets with Secrets Manager and Parameter Store, automated testing, artifact repositories (CodeArtifact, Amazon S3, Amazon ECR), EC2 Image Builder, and deployment strategies for EC2, ECS, EKS and Lambda, including mutable versus immutable patterns and blue/green and canary releases with AWS CodeDeploy.[^s2] Domain 2 covers writing IaC with AWS CloudFormation, AWS SAM and AWS CDK, rolling out StackSets across accounts and Regions, reusable components via Service Catalog and CDK constructs, provisioning accounts with AWS Organizations and AWS Control Tower, SCPs and cross-account roles, and automating inventory, patching and desired state with Systems Manager, AWS Config, Lambda and Step Functions.[^s3]

The guide also says what is out of scope: advanced networking, deep-level security advice to developers, database design and tuning, and full-stack application development.[^s1]

## Exam history

DOP-C02 replaced DOP-C01 on March 7, 2023, with March 6, 2023 the last day for the older version.[^s6] AWS said no content was removed in that revision; the outline was reordered to align with the AWS Well-Architected Framework, high availability and disaster recovery moved into the Resilient Cloud Solutions domain, and provisioning security moved into Security and Compliance.[^s6]

## Recertification and gotchas

The credential is valid for three years and is renewed by passing the latest version of the exam.[^s4][^s5] It can alternatively be maintained for one year through AWS Skill Builder with a paid subscription, but only while the certification is active and within 90 days of expiry.[^s5] Passing DevOps Engineer - Professional also renews AWS Certified Developer - Associate and AWS Certified CloudOps Engineer - Associate (formerly SysOps Administrator - Associate), and, like any Professional exam, AWS Certified Cloud Practitioner.[^s5] Holders of an AWS certification get 50% off their next exam.[^s4]

## When to take it (and when not)

It suits engineers who already own pipelines, IaC and day-two operations; the guide assumes years of hands-on operation, not service familiarity.[^s1] Candidates whose work is mainly architecture design across complex organizations are better matched by Solutions Architect - Professional, while those earlier in their career usually start with the Developer or CloudOps Engineer associate exams, which this exam also renews.[^s5]

## Connections

The certification sits at the professional tier of the AWS Certification program and renews AWS Certified Developer - Associate and AWS Certified CloudOps Engineer - Associate.[^s5] It contrasts with Solutions Architect - Professional: the same 750-to-pass, 180-minute, 300 USD professional format, but focused on delivery and operations rather than architecture design.[^s1][^s4] Its heaviest service links are AWS CodePipeline and CodeDeploy-based blue/green releases, AWS CloudFormation and AWS Systems Manager.[^s2][^s3]

## Sources
[^s1]: Amazon Web Services, "AWS Certified DevOps Engineer - Professional (DOP-C02) Exam Guide". https://docs.aws.amazon.com/aws-certification/latest/devops-engineer-professional-02/devops-engineer-professional-02.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Content Domain 1: SDLC Automation (DOP-C02)". https://docs.aws.amazon.com/aws-certification/latest/devops-engineer-professional-02/devops-engineer-professional-02-domain1.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Content Domain 2: Configuration Management and IaC (DOP-C02)". https://docs.aws.amazon.com/aws-certification/latest/devops-engineer-professional-02/devops-engineer-professional-02-domain2.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "AWS Certified DevOps Engineer - Professional". https://aws.amazon.com/certification/certified-devops-engineer-professional/ (accessed 2026-11-16)
[^s5]: Amazon Web Services, "AWS Recertification". https://aws.amazon.com/certification/recertification/ (accessed 2026-11-16)
[^s6]: Amazon Web Services, "Updated AWS Certified DevOps Engineer - Professional: registration now open". https://aws.amazon.com/blogs/training-and-certification/updated-aws-certified-devops-engineer-professional-registration-now-open/ (accessed 2026-11-16)
