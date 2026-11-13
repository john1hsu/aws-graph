---
id: kt:shared-responsibility-model
type: Concept
title: AWS shared responsibility model
aliases: [Shared responsibility model, Shared Responsibility Model, Security of the cloud vs security in the cloud, Shared security responsibility model]
domain: global-infrastructure
summary: >-
  The AWS shared responsibility model divides security and compliance duties between AWS, which secures
  the cloud itself (facilities, hardware, network, host OS and virtualization), and the customer, who
  secures what they run in it; the customer's share shrinks as services become more managed.
verified: 2026-11-13
status: concept
broader: [kt:security-pillar, kt:aws-compliance-programs]
related: [kt:aws-artifact, kt:aws-iam, kt:amazon-ec2, kt:aws-fargate]
relations:
  - {predicate: usedBy, object: kt:aws-lambda}
  - {predicate: usedBy, object: kt:amazon-rds}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Shared responsibility - Security Pillar (AWS Well-Architected Framework)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/shared-responsibility.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Understanding the security scope - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-accelerating-security-maturity/understanding-the-security-scope.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "Understanding the shared responsibility model for Lambda runtime management - AWS Lambda"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/runtime-management-shared.html
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "AWS Certified Cloud Practitioner (CLF-C02) exam guide - Domain 2: Security and Compliance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain2.html
    type: official-learning
    accessed: 2026-11-13
created: 2026-11-13
---

# AWS shared responsibility model

The shared responsibility model is AWS's framing of who does what for security and compliance on its platform. AWS operates, manages and controls everything from the host operating system and virtualization layer down to the physical security of its data centers; the customer is responsible for what they build and run on top, and the exact split depends on which services they choose.[^s1] AWS summarizes the two halves as security *of* the cloud (AWS's job) versus security *in* the cloud (the customer's job).[^s1] Because every workload sits on the same AWS-run infrastructure, the model is the starting point for any security architecture, compliance assessment or audit on AWS.

## How it works

**AWS: security of the cloud.** AWS protects the infrastructure that runs all AWS services: hardware, software, networking and the facilities themselves.[^s1]

**Customer: security in the cloud.** The customer's duties are set by the services they select, and the more abstracted a service is, the less security configuration the customer has to do.[^s1] AWS Prescriptive Guidance describes the customer's scope narrowing across three service styles:[^s2]

- **Infrastructure services** such as Amazon EC2 leave the largest scope with the customer: platform security, OS patching and application management. On EC2 the customer manages the guest OS (including updates and security patches), any software they install, and the security groups that act as instance firewalls.[^s1][^s2]
- **Container services** such as Amazon ECS, Amazon EKS, Amazon ECR and AWS Fargate shift more work, such as OS and platform management, to AWS.[^s2]
- **Serverless and abstracted services** leave AWS with nearly all of the stack. For services like Amazon S3 and Amazon DynamoDB, AWS runs the infrastructure, OS and platform, and the customer manages their data (including encryption choices), classifies assets and grants permissions with IAM.[^s1][^s2]

## Inherited, shared and customer-specific controls

The Well-Architected Security Pillar sorts controls into three groups.[^s1] **Inherited controls**, such as physical and environmental protections, come entirely from AWS. **Shared controls** apply at both layers in different contexts: AWS configures its own infrastructure devices while customers configure their guest operating systems, databases and applications, and each side trains its own staff. **Customer-specific controls** belong only to the customer, for example routing or zoning data within particular security environments.[^s1]

Patch management is the clearest shared control. For single-tenant managed services such as Amazon RDS, Amazon ElastiCache and Amazon OpenSearch Service, AWS finds vulnerabilities, builds and releases patches and notifies customers, while the customer picks maintenance windows, applies service updates or schedules restarts in the time AWS gives. For multi-tenant services such as Amazon DynamoDB, Amazon S3 and ElastiCache Serverless, AWS patches with no customer action required.[^s1]

## Gotchas

- **"Managed" is not "hands-off".** AWS Lambda shows how settings move the line. AWS publishes patched managed runtimes and base images, but who applies them depends on configuration: in the recommended Auto update mode Lambda applies runtime updates itself; in Function update mode a function only picks up patches when it is updated; Manual mode gets no automatic patches and is meant only for temporary rollback.[^s3]
- **Container images are the customer's to rebuild.** For Lambda functions packaged as container images, patches reach the function only after the customer rebuilds from the latest base image and redeploys.[^s3]
- **Data, identity and configuration never move to AWS.** Even on the most abstracted services, encryption choices, data classification and IAM permissions stay with the customer.[^s1][^s2]
- **Check each service's documentation.** The split differs service by service, so AWS advises reviewing each service's docs alongside applicable laws and regulations.[^s1]

## When it matters

Use the model when choosing services: picking managed or serverless options is a Well-Architected best practice partly because it moves patching and vulnerability management to AWS and cuts the time and effort a team spends securing the platform.[^s2] Use it again in compliance work, where AWS's audited infrastructure controls cover the inherited part and the customer must evidence the rest. It is core exam material: the Cloud Practitioner (CLF-C02) exam guide's Task Statement 2.1 asks candidates to describe AWS's, the customer's and the shared responsibilities, and how they shift between services such as Amazon RDS, AWS Lambda and Amazon EC2. Domain 2 (Security and Compliance) carries 30% of the scored content.[^s4]

## Connections

The model sits under the **Security Pillar** of the Well-Architected Framework, which defines it, and alongside **AWS compliance programs** and **AWS Artifact**, which deal with the inherited and shared controls AWS operates.[^s1] **Amazon EC2** and **AWS Fargate** mark the infrastructure and container ends of the spectrum, while **AWS Lambda** and **Amazon RDS** use the model for runtime and database patching respectively.[^s1][^s2][^s3] **AWS IAM** is the customer's main tool for the part they always keep: access permissions.[^s1] The topic is **covered by** the AWS Certified Cloud Practitioner exam.[^s4]

## Sources
[^s1]: Amazon Web Services, "Shared responsibility - Security Pillar (AWS Well-Architected Framework)". https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/shared-responsibility.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Understanding the security scope - AWS Prescriptive Guidance". https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-accelerating-security-maturity/understanding-the-security-scope.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Understanding the shared responsibility model for Lambda runtime management - AWS Lambda". https://docs.aws.amazon.com/lambda/latest/dg/runtime-management-shared.html (accessed 2026-11-13)
[^s4]: Amazon Web Services, "AWS Certified Cloud Practitioner (CLF-C02) exam guide - Domain 2: Security and Compliance". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain2.html (accessed 2026-11-13)
