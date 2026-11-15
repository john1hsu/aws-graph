---
id: kt:aws-cost-management-services
type: Concept
title: AWS cost management services
aliases: [AWS cost management, AWS Cloud Financial Management services, AWS cost management tools, cost management on AWS, AWS cost management category]
domain: cost-management
summary: >-
  The AWS service category for seeing, organizing, planning and reducing cloud spend: cost analysis (Cost
  Explorer, data exports, Cost Anomaly Detection), cost organization (cost allocation tags, cost categories),
  budgeting and planning (AWS Budgets, AWS Pricing Calculator), savings (Cost Optimization Hub, Savings Plans,
  reservations) and showback or chargeback (AWS Billing Conductor).
verified: 2026-11-15
status: concept
broader: [kt:amazon-web-services]
related: [kt:aws-billing-and-cost-management, kt:aws-pricing-fundamentals, kt:cost-optimization-pillar, kt:aws-management-and-governance-services]
relations:
  - {predicate: hasPart, object: kt:aws-cost-explorer}
  - {predicate: hasPart, object: kt:aws-budgets}
  - {predicate: hasPart, object: kt:aws-pricing-calculator}
  - {predicate: hasPart, object: kt:cost-allocation-tags}
  - {predicate: hasPart, object: kt:aws-cost-anomaly-detection}
  - {predicate: hasPart, object: kt:savings-plans}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Cloud Financial Management - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/aws-cost-management.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "What is AWS Billing and Cost Management? - AWS Billing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-what-is.md
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Practice Cloud Financial Management - Cost Optimization Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/practice-cloud-financial-management.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "AWS Cost Explorer Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/aws-cost-management/aws-cost-explorer/pricing/
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "AWS Budgets Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/aws-cost-management/aws-budgets/pricing
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "Content Domain 4: Billing, Pricing, and Support - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.md
    type: official-learning
    accessed: 2026-11-15
  - id: s7
    title: "Top announcements of AWS re:Invent 2025"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/top-announcements-of-aws-reinvent-2025
    type: official-blog
    accessed: 2026-11-15
created: 2026-11-15
---

# AWS cost management services

"Cost management" is the AWS service category for understanding and controlling what you spend on AWS. The AWS overview whitepaper files it under Cloud Financial Management and presents it as a set of solutions to manage and optimize spend, whether a workload was born in the cloud or is still migrating. As of 2026-11 that page lists AWS Billing Conductor, AWS Cost Explorer, AWS Budgets, the AWS Cost and Usage Report, Reserved Instance reporting and Savings Plans.[^s1] This node is not a single service you turn on. It is the map of those tools, most of which live in the AWS Billing and Cost Management console.[^s2]

## The feature groups

The AWS Billing user guide sorts the console into five groups. One handles billing itself; the other four are about cost:[^s2]

- **Billing and payments** – the Bills page, purchase orders, payments, payment profiles, credits, billing preferences and billing transfer, which lets one external account manage and pay the consolidated bills of several organizations.[^s2]
- **Cost analysis** – Cost Explorer for charting, filtering, grouping and forecasting spend; data exports of the billing datasets; Cost Anomaly Detection alerts; Free Tier usage tracking; and split cost allocation data for shared Amazon ECS resources.[^s2]
- **Cost organization** – cost categories, which map spend to teams, applications or environments and can split shared costs, and cost allocation tags, which group spend by resource tag. Both show up in Cost Explorer and data exports.[^s2]
- **Budgeting and planning** – AWS Budgets, an in-console Pricing Calculator that applies your own discounts and commitments, and the public Pricing Calculator website, which uses On-Demand rates.[^s2]
- **Savings and commitments** – Cost Optimization Hub, which gathers savings recommendations such as deleting unused resources, rightsizing and commitment purchases, plus Savings Plans and reservations for services including EC2, RDS, Redshift and DynamoDB.[^s2]

Around the console sit related services. AWS Billing Conductor builds a second, custom version of monthly billing data for showback and chargeback, but does not change what AWS actually charges. AWS Organizations consolidated billing gives one bill across accounts and pools usage so volume discounts, Reserved Instances and Savings Plans are shared, at no extra fee. The AWS Price List API returns service and price data in JSON or CSV.[^s2]

## How the practice fits together

The Well-Architected cost optimization pillar frames the tools as one practice, Cloud Financial Management. It is organized as see (measure spend and create accountability), save (pick pricing models and resources), plan (flexible budgets and forecasts) and run (day-to-day billing control and guardrails).[^s3] Its nine COST01 best practices include setting ownership of cost optimization, partnering finance with technology, setting cloud budgets and forecasts, and monitoring cost proactively. It names Cost Explorer, the Cost and Usage Report, Budgets, Cost Anomaly Detection, cost categories, tag policies, SCPs and AWS Service Catalog as supporting tools.[^s3]

## Pricing and gotchas

Most of the category costs little, but not all of it is free. As of 2026-11, the Cost Explorer console is free, while each Cost Explorer API request costs $0.01 on the primary billing view (and $0.01 per source for custom billing views). Hourly granularity costs $0.01 per 1,000 usage records per month.[^s4] AWS Budgets monitoring and notifications are free, and so are the first two action-enabled budgets each month; after that each action-enabled budget costs $0.10 per day, and emailed budget reports cost $0.01 each.[^s5] These are global billing prices, not Region-specific.

By default, IAM users and roles cannot open the Billing and Cost Management console until the account turns on the **Activate IAM Access** setting, a common surprise for new accounts.[^s2] In an organization, the management account controls what member accounts can see of Cost Explorer through Cost Management preferences.[^s2] Finally, the toolset keeps growing. At re:Invent 2025 (held 2025-11-30 to 2025-12-04), AWS announced Database Savings Plans, a commitment model that spans database services and deployment options.[^s7]

## Connections

This node is the cost branch of `kt:amazon-web-services`. Its `hasPart` edges point to the tools with their own articles or frontier nodes: `kt:aws-cost-explorer`, `kt:aws-budgets`, `kt:aws-pricing-calculator`, `kt:cost-allocation-tags`, `kt:aws-cost-anomaly-detection` and `kt:savings-plans`.[^s1][^s2] It is related to `kt:aws-billing-and-cost-management`, the console and billing service that hosts most of these features, and `integratesWith` `kt:aws-organizations` through consolidated billing.[^s2] It is related to `kt:aws-pricing-fundamentals`, which explains what is being billed, to `kt:cost-optimization-pillar`, whose Cloud Financial Management practice uses these tools, and to `kt:aws-management-and-governance-services`, the neighbouring category for running and controlling environments.[^s3] The `coveredBy` edge reflects CLF-C02 Task Statement 4.2, "Understand resources for billing, budget, and cost management". It covers Budgets, Cost Explorer, Pricing Calculator, Organizations consolidated billing and cost allocation tags.[^s6]

## Sources
[^s1]: Amazon Web Services, "Cloud Financial Management - Overview of Amazon Web Services". https://docs.aws.amazon.com/whitepapers/latest/aws-overview/aws-cost-management.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "What is AWS Billing and Cost Management? - AWS Billing". https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-what-is.md (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Practice Cloud Financial Management - Cost Optimization Pillar". https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/practice-cloud-financial-management.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "AWS Cost Explorer Pricing". https://aws.amazon.com/aws-cost-management/aws-cost-explorer/pricing/ (accessed 2026-11-15)
[^s5]: Amazon Web Services, "AWS Budgets Pricing". https://aws.amazon.com/aws-cost-management/aws-budgets/pricing (accessed 2026-11-15)
[^s6]: Amazon Web Services, "Content Domain 4: Billing, Pricing, and Support - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.md (accessed 2026-11-15)
[^s7]: Amazon Web Services, "Top announcements of AWS re:Invent 2025". https://aws.amazon.com/blogs/aws/top-announcements-of-aws-reinvent-2025 (accessed 2026-11-15)
