---
id: kt:aws-cost-explorer
type: Service
title: AWS Cost Explorer
aliases: [Cost Explorer, Cost Explorer API]
domain: cost-management
summary: >-
  AWS's built-in tool in the Billing and Cost Management console for viewing, filtering,
  grouping and forecasting AWS cost and usage data, with a free console and a pay-per-request
  API.
verified: 2026-11-09
status: current
broader: [kt:aws-cost-management-services]
related: [kt:aws-budgets, kt:aws-cost-anomaly-detection, kt:aws-cost-and-usage-report]
relations:
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: integratesWith, object: kt:savings-plans}
  - {predicate: integratesWith, object: kt:reserved-instances}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Analyzing your costs and usage with AWS Cost Explorer - AWS Cost Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/console/billing/costexplorer
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "Enabling Cost Explorer - AWS Cost Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cost-management/latest/userguide/ce-enable.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Configuring multi-year and granular data - AWS Cost Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cost-management/latest/userguide/ce-configuring-data.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "AWS Cost Explorer Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/aws-cost-management/aws-cost-explorer/pricing/
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "AWS Cost Explorer FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/aws-cost-management/aws-cost-explorer/faqs
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "In-Scope AWS Services - AWS Certification (Cloud Practitioner CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS Cost Explorer

AWS Cost Explorer is the cost-analysis tool inside the AWS Billing and Cost Management console. It lets you view and analyze your AWS costs and usage, forecast future spend, and get Reserved Instance purchase recommendations.[^s1] It reads the same underlying dataset as the AWS Cost and Usage Reports, and any view can be downloaded as CSV.[^s1] It is not something you provision: you turn it on once per account or organization, and from then on it is a lens over your billing data.[^s2]

## How it works

You enable Cost Explorer by opening it for the first time in the console and choosing **Launch Cost Explorer**; it cannot be enabled through the API.[^s2] Once enabled it cannot be disabled.[^s1] AWS then prepares data for the current month and the previous 13 months; the current month appears in roughly 24 hours and older months take a few days longer, after which data refreshes at least once every 24 hours (sometimes later, because it depends on upstream billing data).[^s1][^s2] Subscribing to the Cost and Usage Reports does not by itself turn Cost Explorer on.[^s2]

In an AWS Organizations setup, member accounts can launch Cost Explorer once the management account has enabled it, and the management account can deny a member that access.[^s2] Membership changes matter: an account that joins an organization loses access to its earlier standalone cost data, and an account that leaves keeps only data generated while it was standalone.[^s2]

The core workflow is a main graph that you **filter** and **group by** dimensions such as service, Region and member account, then save as a named report.[^s5] AWS ships default reports, including monthly cost by service, monthly cost by linked account, monthly EC2 running hours, daily costs and AWS Marketplace costs, plus Reserved Instance reports and Savings Plans utilization and coverage reports.[^s5][^s1][^s4] Forecasts reach 18 months ahead at monthly granularity and 3 months ahead at daily granularity.[^s5] The console also offers natural-language analysis through Amazon Q Developer ("Analyze with Amazon Q" and "Ask question"), which needs Cost Explorer permissions plus Amazon Q permissions.[^s1][^s5]

Enabling Cost Explorer also automatically sets up **Cost Anomaly Detection** with an AWS services monitor and a daily summary alert subscription, which you can opt out of.[^s2]

## Data depth and granularity

By default you get monthly and daily data. The management account can change Cost Management preferences to add:[^s3]

- **Multi-year data** at monthly granularity, up to 38 months of history.[^s3]
- **Hourly granularity**, either for all services (without resource detail) or for EC2 instance-level usage.[^s3]
- **Daily resource-level data**, per service or for all services.[^s3]

Hourly and resource-level data cover only the past 14 days. Preference changes can take up to 48 hours to appear, and after saving you cannot change them again for 48 hours.[^s3]

## Pricing and gotchas (as of 2026-11)

- **Console:** free of charge.[^s1]
- **API:** $0.01 per request against the primary billing view; each page of a paginated result counts as a separate request. Custom billing views cost $0.01 per source per request.[^s4][^s5]
- **Hourly granularity:** $0.00000033 per usage record per day, which AWS equates to $0.01 per 1,000 usage records a month. The charge is the same whether hourly data is used in one report or in all three that support it.[^s4]

Gotchas: an automated script that pages through a large grouping can quietly add up API charges. Preferences are organization-wide and locked for 48 hours after each change. And Cost Explorer data is not real-time, so it should not be your only alerting mechanism.[^s1][^s3][^s4]

## When to use it (and when not)

Use Cost Explorer for interactive questions such as "which service grew last month?" or "what will this quarter cost?", for checking commitment coverage, and for quick CSV exports.[^s1][^s5] Because its data lags by up to a day, pair it with alerting tools (Cost Anomaly Detection, which it enables automatically, or AWS Budgets) rather than watching graphs.[^s1][^s2][^s6] For line-item analysis at scale, or for joining billing data with your own data in a warehouse, the Cost and Usage Report is the better source; Cost Explorer itself is built on that dataset.[^s1]

## Connections

Cost Explorer sits in AWS's cost-management tool family. The CLF-C02 Cloud Practitioner in-scope services list puts it under Cloud Financial Management, alongside AWS Budgets, the Cost and Usage Report and AWS Marketplace.[^s6] It **integrates with AWS Organizations**, because the management account enables it and controls member access.[^s2] It **integrates with Savings Plans and Reserved Instances** through its purchase recommendations and its utilization and coverage reports.[^s1][^s4] It is **secured by IAM**, since preferences and Amazon Q analysis require IAM permissions.[^s3][^s5] Enabling it switches on **Cost Anomaly Detection**.[^s2] It is **covered by the AWS Certified Cloud Practitioner** exam.[^s6]

## Sources
[^s1]: Amazon Web Services, "Analyzing your costs and usage with AWS Cost Explorer". https://docs.aws.amazon.com/console/billing/costexplorer (accessed 2026-11-09)
[^s2]: Amazon Web Services, "Enabling Cost Explorer". https://docs.aws.amazon.com/cost-management/latest/userguide/ce-enable.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Configuring multi-year and granular data". https://docs.aws.amazon.com/cost-management/latest/userguide/ce-configuring-data.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "AWS Cost Explorer Pricing". https://aws.amazon.com/aws-cost-management/aws-cost-explorer/pricing/ (accessed 2026-11-09)
[^s5]: Amazon Web Services, "AWS Cost Explorer FAQs". https://aws.amazon.com/aws-cost-management/aws-cost-explorer/faqs (accessed 2026-11-09)
[^s6]: Amazon Web Services, "In-Scope AWS Services – AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-09)
