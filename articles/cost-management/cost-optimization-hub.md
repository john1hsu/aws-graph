---
id: kt:cost-optimization-hub
type: Feature
title: AWS Cost Optimization Hub
aliases: [Cost Optimization Hub]
domain: cost-management
summary: >-
  A free feature of AWS Billing and Cost Management that gathers rightsizing, idle-resource,
  upgrade, Graviton and commitment (Savings Plans and reservation) recommendations from across
  an organization's accounts and Regions into one deduplicated, prioritized list, with savings
  estimated after your existing discounts and a daily Cost Efficiency score.
verified: 2026-11-20
status: current
broader: [kt:aws-billing-and-cost-management, kt:aws-cost-management-services]
related: [kt:cost-optimization-pillar, kt:aws-trusted-advisor, kt:reserved-instances]
relations:
  - {predicate: partOf, object: kt:aws-billing-and-cost-management}
  - {predicate: dependsOn, object: kt:aws-compute-optimizer}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: integratesWith, object: kt:savings-plans}
  - {predicate: integratesWith, object: kt:aws-data-exports}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
sources:
  - id: s1
    title: "Identifying opportunities with Cost Optimization Hub - AWS Cost Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cost-management/latest/userguide/cost-optimization-hub.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Getting started with Cost Optimization Hub - AWS Cost Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cost-management/latest/userguide/coh-getting-started.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "AWS Cost Optimization Hub FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/aws-cost-management/cost-optimization-hub/faqs/
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "Introducing Cost Optimization Hub"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2023/11/cost-optimization-hub
    type: official-blog
    accessed: 2026-11-20
  - id: s5
    title: "AWS Cost Optimization Hub introduces Cost Efficiency metric to measure and track cloud cost efficiency"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/aws-cost-optimization-hub-cost-efficiency-metric-measure-track
    type: official-blog
    accessed: 2026-11-20
  - id: s6
    title: "Cost Optimization Hub now supports Savings Plans and reservations preferences"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/05/cost-optimization-hub-savings-plans-reservations-preferences
    type: official-blog
    accessed: 2026-11-20
  - id: s7
    title: "AWS Cost Optimization Hub now supports CSV download"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/04/aws-cost-optimization-hub-csv-download/
    type: official-blog
    accessed: 2026-11-20
created: 2026-11-20
---

# AWS Cost Optimization Hub

Cost Optimization Hub is a feature of AWS Billing and Cost Management, not a separately provisioned service. It pulls cost-saving recommendations from across your accounts and Regions into one place and ranks them. The recommendations cover rightsizing, deleting idle resources, and buying Savings Plans or Reserved Instances. Savings estimates take your existing commercial terms, such as current RIs and Savings Plans, into account.[^s1] AWS launched it in November 2023 at no additional charge in all Regions except the AWS China and AWS GovCloud (US) Regions.[^s4]

## How it works

You opt in once, from the console, the API, the AWS CLI or an SDK. A management account can opt in only itself or itself plus every member account. Covering members requires an organization with all features enabled, and opting in turns on trusted access for the feature. One delegated administrator can be registered per organization. Recommendations are stored in US East (N. Virginia), and the first import can take up to 24 hours.[^s2] Rightsizing and idle findings come from AWS Compute Optimizer, which you must opt in to separately; otherwise those recommendations are not imported.[^s1][^s2]

As of 2026-11 the user guide lists 25 supported resource and commitment types. These include EC2 instances and Auto Scaling groups, EBS volumes, Lambda functions, ECS tasks on Fargate, and RDS instances and storage. Aurora cluster storage, ElastiCache, MemoryDB, DynamoDB, DocumentDB, SageMaker endpoints, WorkSpaces and NAT gateways are also covered, along with Savings Plans and reservation types for several services.[^s1] The FAQ groups recommended actions into rightsize, upgrade (for example EBS io1 to io2), Graviton migration, and purchasing Savings Plans or Reserved Instances. You can filter and group them by Region, account, resource type, tag key, restart requirement and rollback possibility.[^s3]

The hub also avoids double counting. When several actions apply to one resource, it counts only the one that saves most; an idle EC2 instance is counted as "stop" rather than "rightsize". It also lowers Savings Plans estimates in proportion to idle instances you could stop. Estimates default to your discounted rates, but you can switch to public On-Demand prices. Each recommendation carries an implementation-effort rating based on whether it needs a restart, configuration changes or code rewrites.[^s3]

## Cost Efficiency metric and exports

In November 2025 AWS added a **Cost Efficiency** metric. It shows what share of optimizable spend could still be saved through rightsizing, idle and commitment recommendations, and it refreshes daily. The score improves as you act on recommendations and drops when new inefficient resources appear.[^s5] The FAQ defines the score as 1 minus potential savings divided by total optimizable spend, and lets you view trends at daily, monthly or custom granularity.[^s3]

Since May 2025 you can set commitment preferences, choosing a 1- or 3-year term and All, Partial or No Upfront payment, so commitment savings estimates match how you actually buy.[^s6] Since April 2026 the console offers a one-click CSV download that keeps your current filters, sorting and grouping. It complements the existing Data Exports option for scheduled delivery to Amazon S3.[^s7]

## Gotchas

- **It is a finder, not a fixer.** The hub surfaces and ranks opportunities, but you still carry out each change in the owning service's console or through automation. AWS describes its savings figure as a quick approximation, not a guarantee.[^s3]
- **Opt-out is per account.** A management account cannot opt every member out at once; each member opts out in its own Cost Management preferences.[^s2]
- **Coverage gaps.** No China or GovCloud (US) availability per the launch and later announcements, and no rightsizing data unless Compute Optimizer is enabled.[^s2][^s6]

## When to use it (and when not)

Use the hub as the FinOps team's starting point in multi-account organizations, where it replaces checking Compute Optimizer, Savings Plans recommendations and each service's console separately. Its Cost Efficiency trend is useful as a KPI.[^s3][^s5] For a single resource type, the owning tool, such as Compute Optimizer's detailed utilization graphs, gives more depth.[^s1]

## Connections

Cost Optimization Hub is **part of AWS Billing and Cost Management** and one of the **AWS cost management services**.[^s1] It **depends on AWS Compute Optimizer** for rightsizing and idle data, and it publishes discount-aware savings back to Compute Optimizer.[^s2][^s3] It **integrates with AWS Organizations** for organization-wide views and delegated administration, and its API calls are **monitored by AWS CloudTrail**.[^s2][^s3] It recommends **Savings Plans** and **Reserved Instances** purchases and honors the ones you already hold.[^s1][^s6] It **integrates with AWS Data Exports** for scheduled delivery to S3.[^s7] It is related to the Well-Architected **Cost Optimization Pillar** (the practices) and **AWS Trusted Advisor** (a separate set of checks), while the hub's own role is consolidating and prioritizing recommendations.[^s1]

## Sources
[^s1]: Amazon Web Services, "Identifying opportunities with Cost Optimization Hub - AWS Cost Management". https://docs.aws.amazon.com/cost-management/latest/userguide/cost-optimization-hub.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "Getting started with Cost Optimization Hub - AWS Cost Management". https://docs.aws.amazon.com/cost-management/latest/userguide/coh-getting-started.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "AWS Cost Optimization Hub FAQs". https://aws.amazon.com/aws-cost-management/cost-optimization-hub/faqs/ (accessed 2026-11-20)
[^s4]: Amazon Web Services, "Introducing Cost Optimization Hub" (What's New, 2023-11-26). https://aws.amazon.com/about-aws/whats-new/2023/11/cost-optimization-hub (accessed 2026-11-20)
[^s5]: Amazon Web Services, "AWS Cost Optimization Hub introduces Cost Efficiency metric to measure and track cloud cost efficiency" (What's New, 2025-11-19). https://aws.amazon.com/about-aws/whats-new/2025/11/aws-cost-optimization-hub-cost-efficiency-metric-measure-track (accessed 2026-11-20)
[^s6]: Amazon Web Services, "Cost Optimization Hub now supports Savings Plans and reservations preferences" (What's New, 2025-05-28). https://aws.amazon.com/about-aws/whats-new/2025/05/cost-optimization-hub-savings-plans-reservations-preferences (accessed 2026-11-20)
[^s7]: Amazon Web Services, "AWS Cost Optimization Hub now supports CSV download" (What's New, 2026-04-28). https://aws.amazon.com/about-aws/whats-new/2026/04/aws-cost-optimization-hub-csv-download/ (accessed 2026-11-20)
