---
id: kt:aws-trusted-advisor
type: Service
title: AWS Trusted Advisor
aliases: [Trusted Advisor, AWS Trusted Advisor Priority, Trusted Advisor Recommendations]
domain: governance-and-operations
summary: >-
  The AWS Support service that inspects an account against AWS best-practice checks in six
  categories (cost optimization, performance, security, fault tolerance, service limits and
  operational excellence) and flags resources needing action; the full check set, API and
  organization-wide features depend on the account's AWS Support plan.
verified: 2026-11-15
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:service-quotas, kt:aws-security-hub, kt:aws-compute-optimizer, kt:aws-support-plans, kt:aws-well-architected-framework]
relations:
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: implements, object: kt:cost-optimization-pillar}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "AWS Trusted Advisor - AWS Support User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Get started with Trusted Advisor Recommendations - AWS Support User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awssupport/latest/user/get-started-with-aws-trusted-advisor.html
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "AWS Trusted Advisor check reference - AWS Support User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor-check-reference.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "Organizational view for AWS Trusted Advisor - AWS Support User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awssupport/latest/user/organizational-view.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "Get started with AWS Trusted Advisor Priority - AWS Support User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor-priority.html
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "Get started with the Trusted Advisor API - AWS Support User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awssupport/latest/user/get-started-with-aws-trusted-advisor-api.html
    type: official-docs
    accessed: 2026-11-15
  - id: s7
    title: "Change log for AWS Trusted Advisor - AWS Support User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awssupport/latest/user/aws-trusted-advisor-change-log.html
    type: official-docs
    accessed: 2026-11-15
  - id: s8
    title: "AWS Trusted Advisor now delivers more accurate unused NAT Gateway checks powered by AWS Compute Optimizer (What's New with AWS, 2026-02-23)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/02/trusted-advisor-unused-nat-gateway-check/
    type: official-blog
    accessed: 2026-11-15
  - id: s9
    title: "AWS Trusted Advisor in AWS GovCloud (US) - AWS GovCloud (US) User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-ta.html
    type: official-docs
    accessed: 2026-11-15
  - id: s10
    title: "AWS Certified Cloud Practitioner - Exam Guide (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02.pdf
    type: official-learning
    accessed: 2026-11-15
created: 2026-11-15
---

# AWS Trusted Advisor

**AWS Trusted Advisor** inspects an AWS environment and recommends actions that save money, improve availability and performance, or close security gaps, based on best practices AWS has gathered from its customer base.[^s1] It is part of AWS Support rather than a separately provisioned service: what you see depends on your support plan.[^s1][^s3]

## How it works

Trusted Advisor runs a catalogue of **checks** grouped into six categories: cost optimization, performance, security, fault tolerance, service limits and operational excellence.[^s2][^s3] Each check compares your resources with a rule and reports one of four states: red (action recommended), yellow (investigation recommended), green (no problems detected) or gray (items you excluded).[^s2] The Recommendations dashboard totals statuses per category, estimates potential monthly savings from the cost checks, lists status changes from the last 30 days, and can export all checks as an .xls file.[^s2]

Refresh behaviour depends on the plan. On Business Support+, Enterprise Support and Unified Operations, checks refresh automatically every week. Some checks, such as Well-Architected high-risk issues for reliability, refresh several times a day and cannot be refreshed by hand. On Basic and Developer plans you refresh checks yourself in the console.[^s2] You can exclude individual resources from a check, filter results by status, keyword or tag, and receive a weekly summary email in a chosen language.[^s2]

Checks also draw on other services. If AWS Security Hub CSPM is enabled, its controls appear in Trusted Advisor but must be refreshed from Security Hub.[^s2] In February 2026 AWS rebuilt the unused NAT Gateway check on AWS Compute Optimizer. It now looks at more CloudWatch metrics over a 32-day lookback and at route-table associations, and shows estimated monthly savings per gateway. Getting it requires opting in to Compute Optimizer and Cost Optimization Hub.[^s8]

## Plans, organizations and the API

As of 2026-11, **Basic and Developer** customers get every service-limits check plus a short list of others. These are EBS and RDS public snapshots, S3 bucket permissions, MFA on the root account, unrestricted specific ports in security groups, and STS global endpoint usage.[^s3] **Business Support+, Enterprise Support and Unified Operations** customers get the full check set, the Trusted Advisor API, and EventBridge monitoring of check status.[^s1] AWS is ending Developer Support, Business Support and Enterprise On-Ramp on January 1, 2027, except in AWS GovCloud (US). Business Support+ starts at a minimum of $29 per month per account.[^s1][^s6]

The **Trusted Advisor API** returns recommendations and their resources for one account or an organization. It can update a recommendation's lifecycle and exclude resources, and calls from accounts without a qualifying plan get Access Denied.[^s6]

**Organizational view** combines check results from every member account into downloadable JSON or CSV reports. It needs AWS Organizations with all features enabled and a qualifying plan on the management account. Only 50 reports are kept (the oldest is deleted), and only one report builds at a time unless the running one has passed 6 hours. The management account cannot refresh checks for Basic or Developer member accounts.[^s4]

**Trusted Advisor Priority**, for Enterprise Support and Unified Operations, shows recommendations your AWS account team has prioritized. They are drawn from Trusted Advisor, Security Hub CSPM and Well-Architected, or written by the team. Each moves from pending response through in progress to resolved or dismissed. Up to 5 member accounts can be delegated administrators, and summaries arrive by email daily or weekly.[^s5]

## Gotchas

- AWS deprecates checks. Five Reserved Instance and Savings Plans optimization checks were retired in January 2026, and API calls that name a deprecated check ID return errors.[^s7]
- Turning Trusted Advisor off stops all checks and gives console and API users access-denied errors.[^s2]
- In AWS GovCloud (US), summary emails and organizational view are unavailable and some checks need manual refresh.[^s9]

## When to use it

Trusted Advisor is a broad, low-effort first pass on account hygiene. For focused work, the specialists go deeper: Service Quotas for raising limits, Compute Optimizer for rightsizing, and Security Hub for continuous security posture. The AWS Certified Cloud Practitioner exam guide names Trusted Advisor for spotting security issues and for cost-optimization monitoring, and lists it under Management and Governance.[^s10]

## Connections

Trusted Advisor sits among AWS management and governance services and is sold through **AWS Support plans**.[^s1] It `integratesWith` **AWS Organizations** for organizational view and Priority, and `integratesWith` **Amazon EventBridge** for status events.[^s1][^s4][^s5] Its service-limits checks complement **Service Quotas**. Its findings partly come from **AWS Security Hub** and **AWS Compute Optimizer**, and Priority also takes in **Well-Architected** findings.[^s2][^s5][^s8] Its cost checks put the **cost optimization pillar** into practice, and it is `coveredBy` the **AWS Certified Cloud Practitioner** exam.[^s2][^s10]

## Sources

[^s1]: Amazon Web Services, "AWS Trusted Advisor" (AWS Support User Guide). https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Get started with Trusted Advisor Recommendations". https://docs.aws.amazon.com/awssupport/latest/user/get-started-with-aws-trusted-advisor.html (accessed 2026-11-15)
[^s3]: Amazon Web Services, "AWS Trusted Advisor check reference". https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor-check-reference.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "Organizational view for AWS Trusted Advisor". https://docs.aws.amazon.com/awssupport/latest/user/organizational-view.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "Get started with AWS Trusted Advisor Priority". https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor-priority.html (accessed 2026-11-15)
[^s6]: Amazon Web Services, "Get started with the Trusted Advisor API". https://docs.aws.amazon.com/awssupport/latest/user/get-started-with-aws-trusted-advisor-api.html (accessed 2026-11-15)
[^s7]: Amazon Web Services, "Change log for AWS Trusted Advisor". https://docs.aws.amazon.com/awssupport/latest/user/aws-trusted-advisor-change-log.html (accessed 2026-11-15)
[^s8]: Amazon Web Services, "AWS Trusted Advisor now delivers more accurate unused NAT Gateway checks powered by AWS Compute Optimizer" (What's New, 2026-02-23). https://aws.amazon.com/about-aws/whats-new/2026/02/trusted-advisor-unused-nat-gateway-check/ (accessed 2026-11-15)
[^s9]: Amazon Web Services, "AWS Trusted Advisor in AWS GovCloud (US)". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-ta.html (accessed 2026-11-15)
[^s10]: Amazon Web Services, "AWS Certified Cloud Practitioner - Exam Guide (CLF-C02)". https://docs.aws.amazon.com/pdfs/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02.pdf (accessed 2026-11-15)
