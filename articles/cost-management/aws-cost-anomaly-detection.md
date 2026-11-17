---
id: kt:aws-cost-anomaly-detection
type: Feature
title: AWS Cost Anomaly Detection
aliases: [Cost Anomaly Detection, CAD, AWS CAD]
domain: cost-management
summary: >-
  A machine-learning feature of AWS Billing and Cost Management that compares actual spend
  with expected spend, flags unusual cost spikes per service, account, tag or cost category,
  and sends alerts with ranked root causes by email, Amazon SNS or AWS User Notifications.
verified: 2026-11-17
status: current
broader: [kt:aws-billing-and-cost-management, kt:aws-cost-management-services]
related: [kt:aws-budgets, kt:aws-cost-explorer, kt:cost-allocation-tags, kt:amazon-sns]
relations:
  - {predicate: dependsOn, object: kt:aws-cost-explorer}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: integratesWith, object: kt:aws-cloudtrail}
  - {predicate: integratesWith, object: kt:amazon-q-developer}
  - {predicate: contrastsWith, object: kt:aws-budgets}
sources:
  - id: s1
    title: "Getting started with AWS Cost Anomaly Detection - AWS Cost Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cost-management/latest/userguide/getting-started-ad.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "AWS Cost Anomaly Detection FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/aws-cost-management/aws-cost-anomaly-detection/faqs
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "AWS Cost Anomaly Detection expands AWS managed monitoring"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/aws-cost-anomaly-detection-managed-monitoring
    type: official-blog
    accessed: 2026-11-17
  - id: s4
    title: "AWS Cost Anomaly Detection accelerates anomaly identification"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/aws-cost-anomaly-detection-accelerates-anomaly
    type: official-blog
    accessed: 2026-11-17
  - id: s5
    title: "AWS now provides AI-powered cost investigations for cost anomalies"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/aws-ai-powered-cost-investigations/
    type: official-blog
    accessed: 2026-11-17
  - id: s6
    title: "AWS Cost Anomaly Detection enables advanced alerting through AWS User Notifications"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/05/aws-cost-anomaly-detection-advanced-alerting-user-notifications
    type: official-blog
    accessed: 2026-11-17
  - id: s7
    title: "AWS Cost Anomaly Detection supports third-party models on Amazon Bedrock"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/08/aws-cost-anomaly-detection-bedrock-3P/
    type: official-blog
    accessed: 2026-11-17
created: 2026-11-17
---

# AWS Cost Anomaly Detection

AWS Cost Anomaly Detection is a feature of AWS Billing and Cost Management that uses machine-learning models trained on an account's own spending history to predict expected spend, compare it with actual spend, and flag the difference when it is unusual.[^s1] Each detected anomaly comes with a root-cause analysis, and alerts can go out by email, Amazon SNS or AWS User Notifications.[^s1][^s6] It is built on Cost Explorer data; the FAQ states there is no way to turn it on without Cost Explorer.[^s2]

## How it works

You configure two things. A **cost monitor** chooses one dimension to watch: AWS services, linked accounts, cost allocation tags or cost categories.[^s1] Each monitor uses one of two methods. An **AWS managed** monitor tracks every value of its dimension separately and automatically picks up new values (a new account, a new tag value); a **customer managed** monitor (formerly "custom monitor") aggregates a handful of values you pick.[^s1] Until November 2025, managed monitoring covered only AWS services; it now extends to linked accounts, tags and cost categories, so one monitor on a "team" tag can replace hundreds of per-team monitors.[^s3]

An **alert subscription** decides who hears about anomalies and when. Options are individual alerts as soon as an anomaly is found (requires an SNS topic), a daily email summary of the top 10 alerts by cost impact, or a weekly email summary.[^s1] Thresholds can be absolute dollars, a percentage of expected spend, or both joined with AND/OR; the threshold only gates notifications, and below-threshold anomalies still show in the console.[^s1][^s2] Since May 2025, alerts can also flow through AWS User Notifications (via Amazon EventBridge), which allows per-service or per-account rules with different thresholds and delivery to email, chat channels or the AWS Console Mobile Application.[^s6]

For each anomaly the service reports **cost impact** (actual minus expected spend) and **impact percentage**, a severity that reflects how abnormal the spike is relative to past volatility rather than its dollar size, and ranked potential root causes across service, account, Region and usage type.[^s1] The FAQ says up to 10 contributing factors are listed, each with an estimated dollar attribution.[^s2] Since June 2026, an "Investigate with Amazon Q" option produces a plain-language explanation, classifies the change as usage- or rate-driven, and for usage changes correlates CloudTrail events to the API calls and IAM principals involved.[^s5]

## Detection timing

A new monitor needs at least 10 days of usage data before it can find anomalies, and a newly used service is not evaluated until it has 10 days of history.[^s2] Detection runs roughly three times a day after billing data is processed, and because Cost Explorer data can lag by up to 24 hours, an anomaly may surface up to a day after the usage that caused it.[^s2] A November 2025 algorithm update compares rolling 24-hour windows (instead of incomplete calendar days) against the same time of day on prior days, which AWS says speeds detection and reduces false positives.[^s4]

## Quotas, pricing and gotchas (as of 2026-11)

- An AWS managed monitor tracks up to 5,000 values per dimension (the top values by spend); a customer managed monitor takes up to 10 selected values.[^s1]
- The FAQ cites one AWS services monitor plus up to 500 custom monitors per alert subscription, and up to 10 email recipients or one SNS topic per subscription.[^s2]
- Linked-account, tag and cost-category monitors can be created only in the Organizations management account; member accounts get only the AWS services monitor, and anomalies are visible only in the account that owns the monitor.[^s1][^s2]
- New Cost Explorer users (Payer or Regular accounts, since March 27, 2023) get a default AWS services monitor and a daily summary to the root email, alerting above 40% of expected spend and a $100 minimum; opting out means deleting the monitor and subscription.[^s2]
- Pricing: AWS describes the default monitoring and the expanded managed monitoring as no additional charge, and AI investigations are free apart from CloudWatch Logs Insights scan charges for organization-wide trails delivered to CloudWatch Logs.[^s2][^s3][^s5]
- Regional scope: the FAQ lists all commercial Regions including China, but the 2025 detection and User Notifications updates and the August 2026 coverage of third-party Amazon Bedrock models exclude AWS GovCloud (US) and China.[^s2][^s4][^s6][^s7]
- Avoid overlapping monitors across dimensions, which produce duplicate alerts; a customer managed monitor cannot be converted to AWS managed.[^s1]

## When to use it (and when not)

Use Cost Anomaly Detection as the "something unexpected happened" signal: a forgotten GPU cluster, a runaway data transfer bill or a sudden jump in model-inference spend, including third-party Bedrock models, which the managed services monitor now evaluates automatically.[^s7][^s1] It does not cap spend or take action. For fixed limits and forecast-based guardrails, use AWS Budgets, and for interactive digging, follow the anomaly's "View in Cost Explorer" link.[^s1][^s2] Managed monitors suit broad, low-maintenance coverage; customer managed monitors suit high-priority workloads that need their own thresholds.[^s1]

## Connections

Cost Anomaly Detection is part of **AWS Billing and Cost Management** and one of the **AWS cost management services**.[^s1] It **depends on AWS Cost Explorer**, whose data it analyses and whose graphs it links to.[^s2] It **integrates with AWS Organizations**, since organization-wide monitors live in the management account, and with **AWS CloudTrail** and **Amazon Q** for AI investigations of root causes.[^s1][^s5] Individual alerts are delivered through **Amazon SNS**, and an SNS topic can be connected to **Amazon Q Developer in chat applications** to post alerts to Slack or Amazon Chime.[^s1] Monitors can be keyed on **cost allocation tags**.[^s3] It **contrasts with AWS Budgets**: Budgets checks spend against limits you set, while Cost Anomaly Detection learns what normal looks like and flags deviations.[^s1][^s2]

## Sources

[^s1]: Amazon Web Services, "Getting started with AWS Cost Anomaly Detection - AWS Cost Management". https://docs.aws.amazon.com/cost-management/latest/userguide/getting-started-ad.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "AWS Cost Anomaly Detection FAQs". https://aws.amazon.com/aws-cost-management/aws-cost-anomaly-detection/faqs (accessed 2026-11-17)
[^s3]: Amazon Web Services, "AWS Cost Anomaly Detection expands AWS managed monitoring" (What's New, 2025-11-19). https://aws.amazon.com/about-aws/whats-new/2025/11/aws-cost-anomaly-detection-managed-monitoring (accessed 2026-11-17)
[^s4]: Amazon Web Services, "AWS Cost Anomaly Detection accelerates anomaly identification" (What's New, 2025-11-21). https://aws.amazon.com/about-aws/whats-new/2025/11/aws-cost-anomaly-detection-accelerates-anomaly (accessed 2026-11-17)
[^s5]: Amazon Web Services, "AWS now provides AI-powered cost investigations for cost anomalies" (What's New, 2026-06-08). https://aws.amazon.com/about-aws/whats-new/2026/06/aws-ai-powered-cost-investigations/ (accessed 2026-11-17)
[^s6]: Amazon Web Services, "AWS Cost Anomaly Detection enables advanced alerting through AWS User Notifications" (What's New, 2025-05-21). https://aws.amazon.com/about-aws/whats-new/2025/05/aws-cost-anomaly-detection-advanced-alerting-user-notifications (accessed 2026-11-17)
[^s7]: Amazon Web Services, "AWS Cost Anomaly Detection supports third-party models on Amazon Bedrock" (What's New, 2026-08-19). https://aws.amazon.com/about-aws/whats-new/2026/08/aws-cost-anomaly-detection-bedrock-3P/ (accessed 2026-11-17)
