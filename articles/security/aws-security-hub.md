---
id: kt:aws-security-hub
type: Service
title: AWS Security Hub
aliases: [Security Hub, Security Hub Essentials]
domain: security
summary: >-
  AWS's unified cloud security service, generally available since December 2025, which correlates
  findings from Security Hub CSPM, Amazon GuardDuty, Amazon Inspector and Amazon Macie into
  prioritized exposure findings with attack paths, trends and automated response workflows.
verified: 2026-11-12
status: current
broader: [kt:aws-security-services]
related: [kt:amazon-guardduty, kt:amazon-inspector, kt:amazon-macie]
relations:
  - {predicate: hasPart, object: kt:aws-security-hub-cspm}
  - {predicate: dependsOn, object: kt:aws-config}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: configuredVia, object: kt:aws-organizations}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Introduction to AWS Security Hub"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub-v2.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "What are Security Hub and Security Hub CSPM?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/securityhub/latest/userguide/what-are-securityhub-services.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Security Hub concepts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-v2-concepts.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "AWS Security Hub pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/security-hub/pricing/
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "AWS Security Hub now generally available with near real-time analytics and risk prioritization"
    publisher: AWS News Blog
    url: https://aws.amazon.com/blogs/aws/aws-security-hub-now-generally-available-with-near-real-time-analytics-and-risk-prioritization
    type: official-blog
    accessed: 2026-11-12
  - id: s6
    title: "AWS Security Hub launches Extended plan for pay-as-you-go partner solutions"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/02/sec-hub-extended
    type: official-blog
    accessed: 2026-11-12
  - id: s7
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-12
  - id: s8
    title: "In-Scope AWS Services (AWS Certified Cloud Practitioner CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-12
created: 2026-11-12
---

# AWS Security Hub

AWS Security Hub is AWS's unified cloud security service. It takes security signals from several sources, correlates and enriches them, and presents the result as prioritized issues with visualizations and context, plus automated workflows for responding to them.[^s1] Its findings come from Security Hub CSPM (posture), Amazon Inspector (vulnerabilities), Amazon Macie (sensitive data), Amazon GuardDuty (threats) and IAM Access Analyzer.[^s1]

The name changed meaning in 2025. The original service, which checks resources against security standards and best practices, is now called **Security Hub CSPM**, while "Security Hub" names the newer unified service that sits on top of it.[^s2] The unified service was previewed at re:Inforce 2025 and became generally available on 2 December 2025, with trends and a security-coverage widget added at GA.[^s5] Older material that says "Security Hub" usually means what is now Security Hub CSPM.

## How it works

The core output is the **exposure finding**: a weakness, misconfiguration or other gap that an active threat could exploit, such as an internet-reachable EC2 instance running software with highly exploitable vulnerabilities.[^s3] Each contributing finding is a **signal**, and each security deviation inside a signal is a **trait** of type Assumability, Misconfiguration, Reachability, Sensitive Data or Vulnerability. A resource with no or too few traits gets no exposure finding.[^s3] Exposures are recalculated in near real time as findings arrive, and the exposure page draws a potential **attack path** showing how an attacker could reach and control the resource.[^s5]

Other features include a dashboard of exposures, threats, coverage and resources; unused-access analysis that flags IAM roles, users, keys and permissions unused for 90 days and proposes scoped-down policies; findings in the Open Cybersecurity Schema Framework (OCSF); and ticket creation in Jira Cloud or ServiceNow ITSM.[^s1] Enabling the service creates a service-linked IAM Access Analyzer.[^s1] It also creates AWS Config service-linked recorders in each account and Region, plus one in us-east-1 for global resource types, to collect the resource data that exposure analysis and inventory need.[^s3]

Across Regions you choose an **aggregation Region** that collects findings and inventory from linked Regions. Automation rules and third-party ticketing can be configured only there.[^s3] Across accounts, the organization management account names one Security Hub administrator account in all Regions, which also becomes the delegated administrator in AWS Organizations. An organization has only one administrator, and an account cannot be both administrator and member.[^s3]

## Pricing and gotchas

Pricing is resource-based (as of 2026-11). The default **Essentials** plan covers posture management, vulnerability management, threat detection, identity analysis and risk analytics, with coverage across AWS and Azure.[^s4] Only four resource types are metered, in resource units: one EC2 instance, 18 ECR images, 12 Lambda functions or 125 IAM users and roles each count as one unit. Other monitored resources are included.[^s4] The pricing page's worked example (one account in US East) uses $3.75 per resource unit per month. A **Threat Analytics** add-on powered by GuardDuty costs $4.00 per million CloudTrail management events.[^s4] Every account in each Region gets a 30-day Essentials trial, even if it already used CSPM or Inspector trials; add-ons are excluded.[^s4] The page states no Regional price differences, so check the console cost estimator before enabling across an organization.[^s4][^s5]

Since 26 February 2026 an **Extended** plan has added curated partner security tools to the same bill on pay-as-you-go or flat-rate terms. AWS is the seller of record, and the plan is available in the commercial Regions where Security Hub runs.[^s6]

Gotchas: Security Hub without CSPM cannot use posture findings for exposures, so AWS recommends enabling both, plus GuardDuty, Inspector and Macie.[^s2] Trends data is kept for up to one year.[^s5] As of 2026-11, AWS's FedRAMP services-in-scope list names Security Hub CSPM (East/West and GovCloud) but has no separate entry for the unified service.[^s7]

## When to use it (and when not)

Use Security Hub as the triage layer once you run more than one detection service, especially across an AWS Organization: it turns separate alerts into ranked exposures with attack paths.[^s1][^s5] If you only need compliance checks against standards, Security Hub CSPM alone is enough.[^s2] Security Hub prioritizes and routes findings rather than generating detections itself, so it needs GuardDuty, Inspector or CSPM underneath.[^s2]

## Connections

Security Hub belongs to the **AWS security services** family (**broader**) and **has part** Security Hub CSPM, whose findings flow in automatically.[^s2] It is **related** to Amazon GuardDuty, Amazon Inspector and Amazon Macie, its other finding sources.[^s1] It **depends on AWS Config** service-linked recorders for resource data[^s3] and **integrates with Amazon EventBridge** for automated response.[^s5] It is **configured via AWS Organizations** delegated administration and **scoped to AWS Regions**, joined through an aggregation Region.[^s3] The CLF-C02 exam's in-scope services list names AWS Security Hub under Security, Identity and Compliance (**coveredBy**).[^s8]

## Sources
[^s1]: Amazon Web Services, "Introduction to AWS Security Hub". https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub-v2.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "What are Security Hub and Security Hub CSPM?". https://docs.aws.amazon.com/securityhub/latest/userguide/what-are-securityhub-services.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "Security Hub concepts". https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-v2-concepts.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, "AWS Security Hub pricing". https://aws.amazon.com/security-hub/pricing/ (accessed 2026-11-12)
[^s5]: AWS News Blog, "AWS Security Hub now generally available with near real-time analytics and risk prioritization" (2 December 2025). https://aws.amazon.com/blogs/aws/aws-security-hub-now-generally-available-with-near-real-time-analytics-and-risk-prioritization (accessed 2026-11-12)
[^s6]: Amazon Web Services, What's New, "AWS Security Hub launches Extended plan for pay-as-you-go partner solutions" (26 February 2026). https://aws.amazon.com/about-aws/whats-new/2026/02/sec-hub-extended (accessed 2026-11-12)
[^s7]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-12)
[^s8]: Amazon Web Services, "In-Scope AWS Services" (AWS Certified Cloud Practitioner CLF-C02). https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-12)
