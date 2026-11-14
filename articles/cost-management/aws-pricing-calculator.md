---
id: kt:aws-pricing-calculator
type: Tool
title: AWS Pricing Calculator
aliases: [Pricing Calculator, calculator.aws, In-console Pricing Calculator]
domain: cost-management
summary: >-
  AWS's cost-estimation tool, offered as a free public website (calculator.aws) and as a feature of
  the Billing and Cost Management console that can import historical usage and apply an account's
  own discounts, Savings Plans and Reserved Instances to workload or whole-bill estimates.
verified: 2026-11-14
status: current
broader: [kt:aws-cost-management-services]
related: [kt:aws-pricing-fundamentals]
relations:
  - {predicate: dependsOn, object: kt:aws-cost-explorer}
  - {predicate: integratesWith, object: kt:savings-plans}
  - {predicate: integratesWith, object: kt:reserved-instances}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is AWS Pricing Calculator?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pricing-calculator/latest/userguide/what-is-pricing-calculator.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "Sharing your estimate - AWS Pricing Calculator"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pricing-calculator/latest/userguide/save-share-estimate.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Generating estimates with Pricing Calculator - AWS Cost Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cost-management/latest/userguide/pricing-calculator.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "AWS Pricing Calculator FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/aws-cost-management/aws-pricing-calculator/faqs
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "AWS Pricing Calculator, now generally available, supports discounts and purchase commitment"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/05/aws-pricing-calculator-discounts-purchase-commitments/
    type: official-blog
    accessed: 2026-11-14
  - id: s6
    title: "Content Domain 4: Billing, Pricing, and Support - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.html
    type: official-learning
    accessed: 2026-11-14
created: 2026-11-14
---

# AWS Pricing Calculator

AWS Pricing Calculator is the tool AWS provides for estimating what a workload will cost before it is built or changed. The original form is a free, web-based planner at calculator.aws that anyone can use without an AWS account or prior cloud experience; it is aimed both at prospective customers sizing AWS and at existing customers planning new workloads.[^s1] A second, authenticated form lives inside the AWS Billing and Cost Management console, where estimates can start from an account's real usage and reflect its negotiated discounts and purchase commitments.[^s3] AWS made that in-console calculator generally available on 30 May 2025, in all commercial Regions except China.[^s5]

## How it works

In the public calculator you add services, choose a Region and configuration, and enter expected usage; the tool shows the calculation behind each line and lets you organise lines into groups that mirror your architecture.[^s1] Prices are drawn from the AWS Price List API, and where an AWS marketing page disagrees with the calculator, AWS says the marketing-page price is the one it uses.[^s1] Estimates can be exported as CSV or PDF, and saving one stores it on AWS public servers behind a unique link.[^s1] A saved estimate is not auto-updated: every change produces a new link, and links created on or after 31 May 2023 stay valid for one year.[^s2]

The in-console calculator offers two estimate types. A **workload estimate** models the incremental usage of a specific application or architecture change; it is available to standalone, management and member accounts, and its usage can come from newly configured services, imported historical usage, or a public-calculator share link.[^s3] A **bill estimate** applies modelled usage and commitment changes to the whole consolidated bill, starting from last month's usage and existing Savings Plans and Reserved Instances; it is non-destructive and only management or standalone accounts can create one.[^s3] Rates can be shown before discounts, after discounts, or after discounts and purchase commitments, and in an organization the management account chooses which of these member accounts see.[^s3] Workload estimates can be exported as CSV or JSON with resource-level detail.[^s5]

## Pricing, limits and gotchas

- The public calculator is free.[^s1] In the console, workload estimates are free; bill estimates are free for the first five per calendar month and cost $2 each after that, with failed estimates not charged (as of 2026-11, per the AWS Cost Management User Guide; not Region-specific).[^s3]
- Cost Explorer must be enabled for the console calculator to import historical usage, and member accounts see only public rates unless the management account has enabled discount visibility.[^s4]
- Estimates cover AWS fees only: taxes, promotional credits, Marketplace and other third-party licence fees are excluded, and AWS Support is excluded unless added.[^s4][^s1]
- Spot usage cannot be modelled, a month is assumed to be 730 hours, results are in US dollars, and tiered pricing assumes new usage stacks on last month's tier.[^s4]
- Workload estimates assume no unused Reserved Instances or Savings Plans, so they can overstate real spend.[^s4]
- Anyone holding a public-calculator link can open the estimate, so treat links as shared content.[^s2]

## When to use it (and when not)

Use the public calculator for early sizing, comparing Regions or configurations, and sharing a quick quote; use the in-console workload estimate when you want your effective rates applied, and the bill estimate when planning commitment purchases or migrations against the whole organization's bill.[^s3] It is a forward-looking estimator, not a record of spend: analysing what you have already been charged is Cost Explorer's job, and alerting on overruns is AWS Budgets'. AWS stresses that estimates are not guarantees and depend on how accurate your inputs are.[^s3][^s4]

## Connections

The calculator is one of the AWS cost-management tools and the practical companion to AWS pricing fundamentals: it is where pay-as-you-go and commitment pricing get turned into numbers. Its in-console version depends on Cost Explorer for historical usage, models Savings Plans and Reserved Instances, and respects the consolidated-billing and discount settings of an AWS Organizations management account.[^s3][^s4] The Cloud Practitioner exam guide (CLF-C02) lists understanding the uses and capabilities of AWS Pricing Calculator as a skill under Task Statement 4.2.[^s6]

## Sources

[^s1]: Amazon Web Services, "What is AWS Pricing Calculator?". https://docs.aws.amazon.com/pricing-calculator/latest/userguide/what-is-pricing-calculator.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "Sharing your estimate - AWS Pricing Calculator". https://docs.aws.amazon.com/pricing-calculator/latest/userguide/save-share-estimate.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, "Generating estimates with Pricing Calculator - AWS Cost Management". https://docs.aws.amazon.com/cost-management/latest/userguide/pricing-calculator.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, "AWS Pricing Calculator FAQs". https://aws.amazon.com/aws-cost-management/aws-pricing-calculator/faqs (accessed 2026-11-14)
[^s5]: Amazon Web Services, "AWS Pricing Calculator, now generally available, supports discounts and purchase commitment" (What's New, 2025-05-30). https://aws.amazon.com/about-aws/whats-new/2025/05/aws-pricing-calculator-discounts-purchase-commitments/ (accessed 2026-11-14)
[^s6]: Amazon Web Services, "Content Domain 4: Billing, Pricing, and Support - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.html (accessed 2026-11-14)
