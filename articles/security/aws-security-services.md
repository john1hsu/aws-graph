---
id: kt:aws-security-services
type: Concept
title: AWS security services
aliases: [AWS Security, Identity, and Compliance, AWS security, identity and compliance services, security services on AWS]
domain: security
summary: >-
  The family of AWS services for securing workloads in the customer's half of the shared responsibility
  model, which AWS groups into data protection, identity and access management, network and application
  protection, threat detection and continuous monitoring, and compliance and data privacy.
verified: 2026-11-10
status: concept
broader: [kt:cloud-security]
related: [kt:aws-organizations, kt:fedramp-on-aws]
relations:
  - {predicate: hasPart, object: kt:aws-iam}
  - {predicate: hasPart, object: kt:aws-kms}
  - {predicate: hasPart, object: kt:aws-cloudtrail}
  - {predicate: hasPart, object: kt:aws-security-hub}
  - {predicate: hasPart, object: kt:amazon-guardduty}
  - {predicate: implements, object: kt:security-pillar}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Appendix: AWS security, identity, and compliance services - AWS Security Reference Architecture"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/appendix.html
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Security OU - Security Tooling account - AWS Security Reference Architecture"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/security-tooling.md
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "The management account, trusted access, and delegated administrators - AWS Security Reference Architecture"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/management-account.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Design principles - Security pillar - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-design.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Security and compliance - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/security-and-compliance.md
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "AWS Security Hub now generally available with near real-time analytics and risk prioritization"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/aws/aws-security-hub-now-generally-available-with-near-real-time-analytics-and-risk-prioritization/
    type: official-blog
    accessed: 2026-11-10
  - id: s7
    title: "AWS launches AI-enhanced security innovations at re:Invent 2025"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/security/aws-launches-ai-enhanced-security-innovations-at-reinvent-2025
    type: official-blog
    accessed: 2026-11-10
  - id: s8
    title: "Content Domain 2: Security and Compliance - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain2.html
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# AWS security services

"AWS security services" is not one product but the Security, Identity, and Compliance category of the AWS portfolio. Under the shared responsibility model AWS secures the cloud itself, while customers are responsible for security in the cloud: their content, platforms, applications, systems and networks. The services in this family are the software tools AWS offers for that customer half, spanning network security, configuration management, access control and encryption.[^s5]

## What is in the family

The AWS Security Reference Architecture (AWS SRA) sorts the portfolio into five groups:[^s1]

- **Data protection:** AWS KMS for encryption keys, AWS CloudHSM for dedicated HSMs, AWS Certificate Manager and AWS Private CA for certificates, AWS Secrets Manager for credential rotation, and Amazon Macie for discovering sensitive data with machine learning.[^s1]
- **Identity and access management:** IAM, IAM Access Analyzer, IAM Identity Center, Amazon Cognito, AWS Directory Service, AWS Resource Access Manager, AWS Organizations and Amazon Verified Permissions.[^s1]
- **Network and application protection:** AWS Shield (DDoS), AWS WAF, AWS Firewall Manager, AWS Network Firewall, Route 53 DNS Firewall, AWS Verified Access and Network Access Analyzer, alongside networking services such as Amazon VPC and VPC Lattice.[^s1]
- **Threat detection and continuous monitoring:** Amazon GuardDuty, Amazon Inspector, AWS Security Hub and Security Hub CSPM, AWS Config, AWS CloudTrail, Amazon Detective and AWS Security Incident Response, with AWS Lambda for automated response.[^s1]
- **Compliance and data privacy:** AWS Artifact for on-demand compliance reports and agreements, and AWS Audit Manager for continuous audit evidence.[^s1]

## How the pieces fit together

The Well-Architected security pillar gives the design logic the family serves: a strong identity foundation with least privilege, traceability through real-time logging and alerting, defense in depth at every layer, security automated as code, data protected in transit and at rest, keeping people away from data, and preparing for security events.[^s4] In practice, detection services generate findings and an aggregator ranks them. Since December 2025 the generally available AWS Security Hub correlates signals from GuardDuty, Inspector, Macie and Security Hub CSPM into exposure findings in the OCSF format, and can open Jira or ServiceNow tickets or trigger EventBridge automation.[^s6]

In a multi-account organization, the SRA recommends running these services through AWS Organizations trusted access and delegated administration rather than from the management account, whose access should be tightly limited.[^s3] A dedicated Security Tooling account (the "Audit" account in AWS Control Tower) is the delegated administrator for GuardDuty, Security Hub, Macie, Inspector, Detective, AWS Config, Firewall Manager, IAM Access Analyzer and Audit Manager, while a separate Log Archive account holds the organization CloudTrail logs and Config snapshots.[^s2]

## Pricing and gotchas

Each service is priced separately, usually by volume of resources, events or findings, so cost grows with coverage. AWS says Security Hub's exposure calculation and Trends add no charge, and that it uses resource-based pricing with a cost estimator in the console (as of 2026-11; no dollar figures are given there).[^s6] Watch these gotchas:

- Not every service supports delegated administration. AWS Artifact does not, so the SRA makes the Security Tooling account its administrator.[^s2]
- The Org Management account cannot make itself the Security Hub delegated administrator.[^s2]
- Detective's administrator should match the Security Hub CSPM administrator.[^s2]
- To keep GuardDuty findings beyond 90 days, the SRA exports them to an S3 bucket in Log Archive encrypted with a customer managed KMS key.[^s2]
- The SRA also says not every workload needs every service; choose by risk and requirements.[^s2]

## What changed (2025-12)

At re:Invent 2025 AWS expanded the category with AI-driven features: AWS Security Agent for automated application security reviews and penetration testing, agentic investigation in AWS Security Incident Response, and GuardDuty Extended Threat Detection for EC2 and ECS. It also added GuardDuty malware scanning of AWS Backup recovery points and outbound identity federation for IAM.[^s7]

## Connections

- **Broader:** cloud security (`kt:cloud-security`), the discipline these services put into practice.[^s5]
- **Has part:** AWS IAM, AWS KMS, AWS CloudTrail, AWS Security Hub and Amazon GuardDuty, which cover identity, encryption, audit, aggregation and threat detection.[^s1][^s6]
- **Implements** the Well-Architected security pillar's design principles.[^s4]
- **Related:** AWS Organizations, through which the services are deployed and delegated across accounts,[^s3] and FedRAMP on AWS, since compliance evidence such as AWS Artifact reports sits in the same category.[^s1]
- **Covered by AWS Certified Cloud Practitioner:** Domain 2 (Security and Compliance, 30% of scored content) names GuardDuty, Inspector, Security Hub, Shield, WAF, Firewall Manager, Artifact, IAM Identity Center and Secrets Manager.[^s8]

## Sources
[^s1]: Amazon Web Services, "Appendix: AWS security, identity, and compliance services - AWS Security Reference Architecture". https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/appendix.html (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Security OU - Security Tooling account - AWS Security Reference Architecture". https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/security-tooling.md (accessed 2026-11-10)
[^s3]: Amazon Web Services, "The management account, trusted access, and delegated administrators - AWS Security Reference Architecture". https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/management-account.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Design principles - Security pillar - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-design.html (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Security and compliance - Overview of Amazon Web Services" (whitepaper). https://docs.aws.amazon.com/whitepapers/latest/aws-overview/security-and-compliance.md (accessed 2026-11-10)
[^s6]: Amazon Web Services, AWS News Blog, "AWS Security Hub now generally available with near real-time analytics and risk prioritization" (2025-12-02). https://aws.amazon.com/blogs/aws/aws-security-hub-now-generally-available-with-near-real-time-analytics-and-risk-prioritization/ (accessed 2026-11-10)
[^s7]: Amazon Web Services, AWS Security Blog, "AWS launches AI-enhanced security innovations at re:Invent 2025" (2025-12-08). https://aws.amazon.com/blogs/security/aws-launches-ai-enhanced-security-innovations-at-reinvent-2025 (accessed 2026-11-10)
[^s8]: Amazon Web Services, "Content Domain 2: Security and Compliance - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain2.html (accessed 2026-11-10)
