---
id: kt:amazon-inspector
type: Service
title: Amazon Inspector
aliases: [Inspector, Amazon Inspector v2, inspector2]
domain: security
summary: >-
  AWS's automated vulnerability management service, which continually scans EC2 instances, machine
  images, ECR container images, Lambda functions and source code repositories for software
  vulnerabilities, code issues and unintended network exposure, and ranks findings with a contextual
  risk score.
verified: 2026-11-13
status: current
broader: [kt:aws-security-services]
related: [kt:amazon-guardduty, kt:aws-security-hub, kt:amazon-ecr, kt:aws-lambda, kt:amazon-ec2]
relations:
  - {predicate: dependsOn, object: kt:aws-systems-manager}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: configuredVia, object: kt:aws-organizations}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
  - {predicate: coveredBy, object: kt:aws-certified-security-specialty}
sources:
  - id: s1
    title: "Automated scan types in Amazon Inspector"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/inspector/latest/user/scanning-resources.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Amazon Inspector FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/inspector/faqs
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "Amazon Inspector pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/inspector/pricing/
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "Amazon Inspector launches code security to shift security left in development"
    publisher: What's New with AWS
    url: https://aws.amazon.com/about-aws/whats-new/2025/06/amazon-inspector-code-security-shift-security-development
    type: official-blog
    accessed: 2026-11-13
  - id: s5
    title: "Amazon Inspector agentless vulnerability assessments for Amazon EC2 are now Generally Available (GA)"
    publisher: What's New with AWS
    url: https://aws.amazon.com/about-aws/whats-new/2024/04/amazon-inspector-agentless-vulnerability-assessments-ec2-ga
    type: official-blog
    accessed: 2026-11-13
  - id: s6
    title: "Amazon Inspector Classic end of support"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/inspector/v1/userguide/inspector-migration.html
    type: official-docs
    accessed: 2026-11-13
  - id: s7
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-13
  - id: s8
    title: "In-Scope AWS Services (AWS Certified Cloud Practitioner CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-13
  - id: s9
    title: "In-Scope AWS Services (AWS Certified Security - Specialty SCS-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/zh_cn/aws-certification/latest/userguide/scs-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-13
created: 2026-11-13
---

# Amazon Inspector

Amazon Inspector is AWS's automated vulnerability management service. It continually scans Amazon EC2 instances, AWS Lambda functions and Amazon ECR container images, and more recently CI/CD pipelines and code repositories, for software vulnerabilities and unintended network exposure.[^s2] The current service (API namespace `inspector2`) replaced the assessment-template model of **Amazon Inspector Classic**. It drops Classic's scale limits, uses the AWS Systems Manager (SSM) Agent instead of a standalone agent, rescans on events rather than on a schedule, and adds a risk score, SBOM export and code scanning.[^s2] AWS set Inspector Classic's retirement for 18 December 2024 and told customers to export reports, delete assessment templates and turn on the new service.[^s6]

## How it works

Activating Inspector automatically enrolls the account in **EC2 scanning**, **ECR scanning** and **Lambda standard scanning**. **Lambda code scanning**, **machine image (AMI) scanning** and **Code Security** are switched on separately. Turning on Lambda code scanning also turns on standard scanning.[^s1]

- **EC2** scanning checks OS and language packages for CVEs and analyzes network reachability. It works through the SSM Agent on the instance (agent-based) or through EBS snapshots (agentless).[^s1] Agentless scanning became generally available in April 2024. In the default **hybrid** mode, Inspector uses SSM where the agent is installed and configured and falls back to snapshots everywhere else.[^s5] Agentless scans run every 24 hours. Agent-based scans rerun whenever packages change or new CVEs are published.[^s2]
- **Machine image** scanning builds an inventory from the snapshots behind AMIs your account owns, without launching an instance. It rescans when new CVEs appear. A one-off on-demand AMI scan works even without activating Inspector, but its findings go only to EventBridge.[^s1]
- **ECR** scanning switches repositories from basic to enhanced scanning and covers only `ACTIVE` images pushed, activated or pulled within a configurable re-scan duration.[^s1]
- **Lambda** scanning discovers functions and layers, scans them on deployment and rescans when CVEs are published. Code scanning additionally checks application package dependencies.[^s1] Only `$LATEST` is continually assessed.[^s2]
- **Code Security** became generally available on 17 June 2025. It offers SAST, software composition analysis and infrastructure-as-code scanning, with native GitHub and GitLab integration. It launched in 10 Regions.[^s4] It uses the Amazon Q Developer scanning engine.[^s1]

Each finding gets an **Inspector risk score** that adjusts the CVE data with network reachability, exploitability and threat-trend signals. The score maps to a severity: 9.0–10.0 is Critical and 7.0–8.9 is High.[^s2] Findings flow to AWS Security Hub and to Amazon EventBridge. SBOMs can be exported in CycloneDX or SPDX format, and on-demand CIS benchmark assessments are available for EC2.[^s2]

## Quotas, pricing and gotchas

Pricing is per resource. US East (N. Virginia) examples as of 2026-11: EC2 agent-based scanning at $1.2528 and agentless at $1.75 per instance per month. ECR images cost $0.09 for the initial scan and $0.01 per rescan, with rescans only for continual-scan repositories. Lambda standard scanning costs $0.30 per function per month, and code scanning adds $0.60 more. Code Security costs $0.15 per repository per scan type, and repositories over 10 MB count as multiple repositories.[^s3] Monthly charges are prorated over a 720-hour month. New accounts get a 15-day free trial that excludes CIS assessments and on-demand AMI scans.[^s3]

Gotchas: new accounts default to a 7-day ECR re-scan duration, while older accounts may have 14 or 90 days.[^s1] Images that age out of the window stop being monitored until someone pulls them again.[^s2] There is no suspended state, only deactivation. Raising the SSM inventory interval from 30 minutes to 12 hours can delay EC2 findings. Code repositories don't get automatic CVE-based rescans, and member accounts can't change the organization's EC2 scan mode.[^s2]

## When to use it (and when not)

Inspector finds weaknesses **before** they are exploited. Amazon GuardDuty detects threats **while** they happen, and Security Hub aggregates both.[^s2] Turn on Inspector wherever you run EC2, containers in ECR or Lambda, and add Code Security when you want the same findings at pull-request time.[^s4] It doesn't patch anything: remediation stays with you, through Systems Manager or by rebuilding images.[^s1][^s2]

## Connections

Inspector belongs to the **AWS security services** family (**broader**). It is **related to** Amazon GuardDuty and AWS Security Hub, which receives its findings, and to the resources it scans: Amazon EC2, Amazon ECR and AWS Lambda.[^s1][^s2] Agent-based EC2 scanning **depends on AWS Systems Manager**.[^s5] Inspector **integrates with Amazon EventBridge** for finding events, and an AWS Organizations delegated administrator **configures** it across member accounts.[^s2] Its scanning is enabled per Region (**scopedTo** Regions), and Code Security launched in a subset of them.[^s4] AWS's FedRAMP services-in-scope list marks Amazon Inspector for both the East/West and GovCloud authorizations (**compliesWith** FedRAMP on AWS).[^s7] It appears under Security, Identity and Compliance in the in-scope services lists for the Cloud Practitioner (CLF-C02) and Security – Specialty (SCS-C02) exams (**coveredBy**).[^s8][^s9]

## Sources
[^s1]: Amazon Web Services, "Automated scan types in Amazon Inspector". https://docs.aws.amazon.com/inspector/latest/user/scanning-resources.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Amazon Inspector FAQs". https://aws.amazon.com/inspector/faqs (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Amazon Inspector pricing". https://aws.amazon.com/inspector/pricing/ (accessed 2026-11-13)
[^s4]: What's New with AWS, "Amazon Inspector launches code security to shift security left in development" (17 June 2025). https://aws.amazon.com/about-aws/whats-new/2025/06/amazon-inspector-code-security-shift-security-development (accessed 2026-11-13)
[^s5]: What's New with AWS, "Amazon Inspector agentless vulnerability assessments for Amazon EC2 are now Generally Available (GA)" (22 April 2024). https://aws.amazon.com/about-aws/whats-new/2024/04/amazon-inspector-agentless-vulnerability-assessments-ec2-ga (accessed 2026-11-13)
[^s6]: Amazon Web Services, "Amazon Inspector Classic end of support". https://docs.aws.amazon.com/inspector/v1/userguide/inspector-migration.html (accessed 2026-11-13)
[^s7]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-13)
[^s8]: Amazon Web Services, "In-Scope AWS Services" (AWS Certified Cloud Practitioner CLF-C02). https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-13)
[^s9]: Amazon Web Services, "In-Scope AWS Services" (AWS Certified Security - Specialty SCS-C02). https://docs.aws.amazon.com/zh_cn/aws-certification/latest/userguide/scs-02-in-scope-services.html (accessed 2026-11-13)
