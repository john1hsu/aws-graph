---
id: kt:savings-plans
type: PricingModel
title: Savings Plans
aliases: [AWS Savings Plans, Compute Savings Plans, EC2 Instance Savings Plans, Database Savings Plans, SageMaker AI Savings Plans]
domain: cost-management
summary: >-
  AWS's commitment-based pricing model: you commit to a fixed amount of spend per hour
  ($/hour) for a one- or three-year term and, in return, eligible usage up to that amount is
  billed at discounted Savings Plans rates instead of On-Demand rates.
verified: 2026-11-08
status: current
broader: [kt:aws-pricing-fundamentals]
related: [kt:spot-instances, kt:aws-cost-explorer, kt:aws-organizations]
relations:
  - {predicate: alternativeTo, object: kt:reserved-instances}
  - {predicate: contrastsWith, object: kt:on-demand-capacity-reservations}
  - {predicate: configuredVia, object: kt:aws-cost-explorer}
  - {predicate: integratesWith, object: kt:amazon-ec2}
  - {predicate: integratesWith, object: kt:aws-fargate}
  - {predicate: integratesWith, object: kt:aws-lambda}
sources:
  - id: s1
    title: "What are Savings Plans? - Savings Plans"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cost-management/latest/userguide/manage-sp.html
    type: official-docs
    accessed: 2026-11-08
  - id: s2
    title: "Savings Plans FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/savingsplans/faq/
    type: official-docs
    accessed: 2026-11-08
  - id: s3
    title: "Compute Savings Plans and Reserved Instances - Savings Plans"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/savingsplans/latest/userguide/sp-ris.html
    type: official-docs
    accessed: 2026-11-08
  - id: s4
    title: "Understanding how Savings Plans apply to your usage - Savings Plans"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/savingsplans/latest/userguide/sp-applying.html
    type: official-docs
    accessed: 2026-11-08
  - id: s5
    title: "Announcing Database Savings Plans with up to 35% savings"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/database-savings-plans-savings/
    type: official-blog
    accessed: 2026-11-08
  - id: s6
    title: "Select the best pricing model - Cost Optimization Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/select-the-best-pricing-model.html
    type: official-docs
    accessed: 2026-11-08
created: 2026-11-08
---

# Savings Plans

Savings Plans are a pricing model, not a service you deploy. You commit to a consistent amount of usage, expressed as dollars per hour, for a one-year term (365 days) or a three-year term (1,095 days). Eligible usage up to that hourly amount is billed at Savings Plans rates, which are lower than On-Demand, and the rate stays fixed for the whole term. Plans are recommended, purchased and tracked in AWS Cost Explorer, which also offers utilization and coverage reporting and budget alerts.[^s1]

## Plan types

AWS currently sells four families of Savings Plans. The maximum discounts below are AWS's headline figures as of 2026-11; actual rates depend on the instance, Region and payment option.[^s2]

- **Compute Savings Plans**, up to 66% off. They apply to EC2 instance usage whatever the instance family, size, Availability Zone, Region, operating system or tenancy, and also to AWS Fargate and AWS Lambda.[^s2][^s3]
- **EC2 Instance Savings Plans**, up to 72% off. They are locked to one instance family in one Region, but still float across sizes, OS and tenancy within that family. They do not cover Fargate or Lambda.[^s3]
- **SageMaker AI Savings Plans**, up to 64% off. They cover eligible Amazon SageMaker AI instance usage (such as Studio notebooks, training, real-time inference and batch transform) across instance families, sizes and Regions.[^s1][^s2]
- **Database Savings Plans**, up to 35% off, launched on 2 December 2025 during re:Invent. They cover provisioned and serverless usage across Aurora, RDS, DynamoDB, ElastiCache, DocumentDB, Neptune, Keyspaces, Timestream and AWS DMS, regardless of engine, instance family, size, deployment option or Region. At launch they were available in all commercial Regions except China.[^s5] As of 2026-11 the FAQ also lists Amazon OpenSearch Service and says newly eligible services join existing commitments automatically. Instance coverage starts at generation 7, ElastiCache coverage is Valkey only, and Timestream coverage is InfluxDB only.[^s2]

## How it works

Compute, EC2 Instance and SageMaker AI plans can be paid All Upfront (cheapest), Partial Upfront (at least half paid up front) or No Upfront (billed monthly). Database Savings Plans are one-year, no-upfront commitments; customers who want to prepay use the Billing console's advance pay feature instead.[^s2][^s5]

Billing is computed hour by hour. Any EC2 Reserved Instances apply first. Next come EC2 Instance Savings Plans, ahead of Compute Savings Plans because the latter have broader applicability. Within a plan, the commitment is spent on the usage with the highest savings percentage first; ties go to the usage with the lowest Savings Plans rate. Usage beyond the commitment is charged On-Demand, and an hour's unused commitment cannot be carried into the next hour.[^s4]

In AWS Organizations or any consolidated billing family, a plan can be bought from any account. By default its benefit is shared across the family, applying to the owner account's usage first and then to other accounts. You can restrict the benefit to the purchasing account, or use Cost Categories to scope or prioritize it for groups of accounts.[^s2][^s4]

## Quotas, pricing and gotchas

- **Commitments are hard to unwind.** The only stated exit is a return policy: a plan with an hourly commitment of $100 or less can be returned within 7 days of purchase, if the return happens in the same calendar month. Upfront charges are fully refunded (as of 2026-11).[^s2]
- **No capacity guarantee.** Savings Plans reserve no capacity. If you need capacity assurance, pair them with On-Demand Capacity Reservations; the Savings Plans rates still apply to that usage.[^s3]
- **No stacking for databases.** Database Savings Plans and Reserved Instances or DynamoDB reserved capacity cannot both discount the same workload.[^s2]
- **Over-commitment is paid for.** Because the commitment is consumed per hour, sizing it to peak instead of baseline usage leaves money unspent.[^s4]

## When to use it (and when not)

Savings Plans fit steady, baseline usage. Compute Savings Plans give savings comparable to Convertible Reserved Instances without manual exchanges. EC2 Instance Savings Plans match Standard RI discounts for teams that are sure about the instance family and Region.[^s3] For EC2, AWS's Well-Architected Cost Optimization pillar recommends Savings Plans over Reserved Instances because they give the same discounts with more flexibility and less management. It also advises buying commitments in small, regular increments instead of one large purchase, and reviewing them every two weeks to a month.[^s6] Interruption-tolerant or spiky work is usually cheaper on Spot Instances or On-Demand.

Practice COST07-BP05 recommends buying plans in an account with no resources, such as the management account. That way the benefit flows to the highest-discount usage across the organization.[^s6]

## Connections

Savings Plans sit under **AWS pricing fundamentals** as one of the commitment models. They are an **alternative to Reserved Instances**: existing RIs keep working alongside them, and AWS suggests moving to Savings Plans as RIs expire.[^s2] They **contrast with On-Demand Capacity Reservations**, which reserve capacity but give no discount; the two are designed to be used together.[^s3] They are **configured via AWS Cost Explorer**,[^s1] and their benefit is shared across **AWS Organizations** accounts.[^s2] Compute plans **integrate with Amazon EC2, AWS Fargate and AWS Lambda**, the services whose usage they discount. **Spot Instances** are the other main way to cut EC2 cost below On-Demand.[^s3][^s6]

## Sources
[^s1]: Amazon Web Services, "What are Savings Plans? - Savings Plans". https://docs.aws.amazon.com/cost-management/latest/userguide/manage-sp.html (accessed 2026-11-08)
[^s2]: Amazon Web Services, "Savings Plans FAQs". https://aws.amazon.com/savingsplans/faq/ (accessed 2026-11-08)
[^s3]: Amazon Web Services, "Compute Savings Plans and Reserved Instances - Savings Plans". https://docs.aws.amazon.com/savingsplans/latest/userguide/sp-ris.html (accessed 2026-11-08)
[^s4]: Amazon Web Services, "Understanding how Savings Plans apply to your usage - Savings Plans". https://docs.aws.amazon.com/savingsplans/latest/userguide/sp-applying.html (accessed 2026-11-08)
[^s5]: Amazon Web Services, "Announcing Database Savings Plans with up to 35% savings" (What's New, 2025-12-02). https://aws.amazon.com/about-aws/whats-new/2025/12/database-savings-plans-savings/ (accessed 2026-11-08)
[^s6]: Amazon Web Services, "Select the best pricing model - Cost Optimization Pillar". https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/select-the-best-pricing-model.html (accessed 2026-11-08)
