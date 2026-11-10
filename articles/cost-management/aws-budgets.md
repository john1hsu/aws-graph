---
id: kt:aws-budgets
type: Service
title: AWS Budgets
aliases: [Budgets, AWS Budgets Actions, Budget actions]
domain: cost-management
summary: >-
  AWS's cost-control service for setting cost, usage, Reserved Instance and Savings Plans
  budgets that send alerts on actual or forecasted overruns and can run actions such as
  applying an IAM policy or SCP or stopping EC2 and RDS instances.
verified: 2026-11-10
status: current
broader: [kt:aws-cost-management-services]
related: [kt:aws-cost-explorer, kt:aws-cost-anomaly-detection, kt:amazon-sns]
relations:
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: integratesWith, object: kt:savings-plans}
  - {predicate: integratesWith, object: kt:reserved-instances}
  - {predicate: integratesWith, object: kt:service-control-policies}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Managing your costs with AWS Budgets - AWS Cost Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Budget methods - AWS Cost Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cost-management/latest/userguide/budget-methods.html
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Configuring budget actions - AWS Cost Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-controls.md
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Configuring a budget action - AWS Cost Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-action-configure.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "AWS Budgets Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/aws-cost-management/aws-budgets/pricing
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "AWS Budgets FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/aws-cost-management/aws-budgets/faqs/
    type: official-docs
    accessed: 2026-11-10
  - id: s7
    title: "In-Scope AWS Services - AWS Certification (Cloud Practitioner CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# AWS Budgets

AWS Budgets lets you set a target for what you expect to spend or use on AWS, then tracks actual and forecasted figures against it and warns you when you cross a threshold.[^s1] Beyond plain cost and usage, it can also watch the utilization and coverage of Reserved Instances (RIs) and Savings Plans.[^s1] Through budget actions it can also enforce guardrails automatically.[^s3]

## Budget types and methods

AWS Budgets offers six budget types:[^s1]

- **Cost budgets** set a spending target, for example across the account or for selected services.[^s1]
- **Usage budgets** set a target for usage of one or more services.[^s1]
- **RI utilization** and **RI coverage** budgets alert when utilization of purchased RIs, or the share of instance hours they cover, falls below a target.[^s1]
- **Savings Plans utilization** and **Savings Plans coverage** budgets do the same for Savings Plans.[^s1]

Budgets can track monthly, quarterly or yearly periods, or a custom period that matches a fiscal year, project or grant; utilization and coverage budgets can also be daily.[^s1][^s6] They can be filtered by dimensions such as service, Availability Zone and member account, and can track unblended, blended, net unblended, amortized or net amortized cost, including or excluding discounts, refunds, support fees and taxes.[^s1][^s6]

A cost or usage budget's amount is set by one of three methods:[^s2]

- **Fixed:** the same amount every period.[^s2]
- **Planned:** a different amount per period, available only for monthly or quarterly budgets, up to 12 months or 4 quarters ahead. After that the last amount stays fixed.[^s2]
- **Auto-adjusting:** at the start of each period, AWS Budgets sets the amount from your cost or usage over a historical or forecasted baseline range, such as the last six months. Periods with no data at the start of the baseline are excluded, and all alert subscribers are told when the amount changes.[^s2]

## Alerts

Alerts can fire on **actual** spend or on **forecasted** spend, so you can hear about a likely overrun before it happens. For RI and Savings Plans budgets, they fire when utilization or coverage drops below target.[^s1][^s6] You can attach up to five alerts to a budget, and each alert can notify up to 10 email addresses and/or publish to an Amazon SNS topic. AWS also documents receiving budget alerts in chat applications.[^s6][^s1] Budget data is refreshed up to three times a day, usually 8 to 12 hours apart, and billing lags real usage. So costs can keep climbing after an alert arrives; Budgets is not a real-time hard cap.[^s1]

## Budget actions

A threshold on a cost or usage budget can also trigger **budget actions**, which run either automatically or after you approve them.[^s3][^s4] The available actions are:[^s3][^s4]

- applying an IAM policy, for example a custom deny policy that stops a user, group or role from launching more EC2 resources;[^s3]
- attaching a service control policy (SCP), which only an AWS Organizations management account can do;[^s4]
- targeting specific Amazon EC2 or Amazon RDS instances.[^s3]

Several actions can share one threshold, such as 90 percent of forecasted monthly cost. A management account can apply an SCP to another account but cannot target EC2 or RDS instances in another account.[^s3] Budgets needs an IAM role it can assume to run actions; AWS recommends attaching the AWS managed policy so new capabilities work without editing permissions.[^s4]

## Pricing and limits (as of 2026-11)

- **Monitoring and notifications:** free.[^s5]
- **Action-enabled budgets:** the first two per month are free, however many actions each has; each additional action-enabled budget costs $0.10 per day.[^s5][^s6]
- **Budget reports** (scheduled emails, daily, weekly or monthly): $0.01 per report delivered.[^s5]
- **Quota:** up to 20,000 budgets; AWS Customer Support can raise it.[^s6]

The pricing page lists these as single rates with no per-Region variation.[^s5]

## Organizations and access

With consolidated billing, the management account can use IAM policies to control who in member accounts can create, edit, delete or view budgets. Member accounts can create their own budgets by default but cannot create or edit budgets for other accounts. A budget is visible only to users who have access to both the account that owns it and the budget itself, and cross-account access to budgets isn't supported.[^s1]

## When to use it (and when not)

Use Budgets as the default guardrail on every account: a cost budget with forecasted and actual alerts, plus utilization and coverage budgets for any RI or Savings Plans commitments.[^s1] Add budget actions where a hard stop is acceptable, such as sandbox accounts, ideally with manual approval for production.[^s3][^s4] Because its data refreshes only a few times a day, pair it with Cost Anomaly Detection to catch unexpected spikes, and use Cost Explorer to find out *why* a budget fired.[^s1]

## Connections

Budgets belongs to AWS's cost-management tool family. The CLF-C02 Cloud Practitioner in-scope services list puts it under Cloud Financial Management, alongside Cost Explorer, the Cost and Usage Reports and AWS Marketplace, so it is **covered by the AWS Certified Cloud Practitioner** exam.[^s7] It **integrates with Savings Plans and Reserved Instances** through utilization and coverage budgets.[^s1] It **integrates with AWS Organizations and service control policies** because a management account can enforce budgets with SCPs on member accounts.[^s3][^s4] It is **secured by IAM**, both through budget permissions and through the role that runs actions.[^s1][^s4] It delivers alerts through **Amazon SNS**.[^s1][^s6]

## Sources
[^s1]: Amazon Web Services, "Managing your costs with AWS Budgets". https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Budget methods". https://docs.aws.amazon.com/cost-management/latest/userguide/budget-methods.html (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Configuring budget actions". https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-controls.md (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Configuring a budget action". https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-action-configure.html (accessed 2026-11-10)
[^s5]: Amazon Web Services, "AWS Budgets Pricing". https://aws.amazon.com/aws-cost-management/aws-budgets/pricing (accessed 2026-11-10)
[^s6]: Amazon Web Services, "AWS Budgets FAQs". https://aws.amazon.com/aws-cost-management/aws-budgets/faqs/ (accessed 2026-11-10)
[^s7]: Amazon Web Services, "In-Scope AWS Services – AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-10)
