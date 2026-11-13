---
id: kt:aws-pricing-fundamentals
type: Concept
title: AWS pricing fundamentals
aliases: [How AWS pricing works, AWS pricing principles, AWS pricing models]
domain: cost-management
summary: >-
  The core principles behind how AWS bills: pay-as-you-go for what you consume, lower rates in
  exchange for a usage commitment, lower per-unit rates at higher volume, and three main cost
  drivers (compute, storage and outbound data transfer).
verified: 2026-11-13
status: concept
broader: [kt:amazon-web-services]
related: [kt:cost-optimization-pillar, kt:aws-cost-explorer, kt:data-transfer-costs]
relations:
  - {predicate: hasPart, object: kt:savings-plans}
  - {predicate: hasPart, object: kt:reserved-instances}
  - {predicate: hasPart, object: kt:spot-instances}
  - {predicate: hasPart, object: kt:aws-free-tier}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Key principles - How AWS Pricing Works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/how-aws-pricing-works/key-principles
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "How AWS Pricing Works - Abstract and introduction"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/how-aws-pricing-works/abstract-and-introduction.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "AWS Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/pricing/?nc2=h_ql_pr_ln
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "Content Domain 4: Billing, Pricing, and Support - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.html
    type: official-learning
    accessed: 2026-11-13
  - id: s5
    title: "Amazon VPC Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/vpc/pricing/
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "AWS Cost Optimization - How AWS Pricing Works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/how-aws-pricing-works/aws-cost-optimization.html
    type: official-docs
    accessed: 2026-11-13
created: 2026-11-13
---

# AWS pricing fundamentals

AWS prices each service independently and mostly on a consumption basis: you pay for the individual services you use, for as long as you use them, without complex licensing and without termination fees once you stop.[^s2] AWS's own pricing page compares the model to a utility bill such as water or electricity, and groups its approach under a few principles: pay-as-you-go, save when you commit, and pay less by using more.[^s3] AWS's long-standing whitepaper *How AWS Pricing Works* (published February 2023 and now marked as kept for historical reference) explains the same principles in more depth; current rates always live on each service's pricing page.[^s1][^s2]

## The three cost drivers

The whitepaper identifies three fundamental drivers of cost: compute, storage and outbound data transfer.[^s1] Compute is billed by the hour or by the second from launch until you stop or terminate a resource, unless a reservation fixed the price in advance; storage and transfer are typically billed per GB.[^s1] Outbound transfer is aggregated across services and charged at the outbound rate, appearing on the bill as AWS Data Transfer Out.[^s1] In most cases inbound transfer, and transfer between AWS services in the same Region, is free, but AWS explicitly warns that exceptions exist, so rates should be checked before designing around them.[^s1]

## Pay-as-you-go, commit, and volume

**Pay-as-you-go.** Fixed costs such as data centres become variable costs that track consumption.[^s1] On-Demand Instances bill compute or database capacity by the hour or second, with a 60-second minimum, and no upfront payment or long-term commitment.[^s1] Simply turning off instances you don't need can cut their cost by 70 percent or more versus running them 24/7.[^s1]

**Save when you commit.** Savings Plans trade a commitment to a consistent hourly spend ($/hour) over one or three years for lower rates on EC2, SageMaker AI, Lambda and Fargate usage.[^s1][^s3] Reservations (Reserved Instances) give a larger discount, which the whitepaper puts at up to 75 percent, for paying for capacity ahead of time; for RDS, Redshift, ElastiCache and OpenSearch Service it cites up to 72 percent versus On-Demand, with All, Partial and No Upfront payment options.[^s1][^s6] Spot Instances need no commitment and use spare capacity at up to 90 percent off On-Demand, at the cost of possible interruption.[^s1] The pricing page also notes Private Pricing for customers making larger commitments across 200+ eligible services.[^s3]

**Pay less by using more.** Tiered pricing lowers the per-unit price as volume grows, for example in S3 storage and in data transferred out of EC2.[^s3][^s1] Choosing a cheaper storage class for rarely accessed data is the storage equivalent.[^s3]

## Prices, quotas and gotchas

- Hidden network charges are the classic surprise. As an example (US East (Ohio), as of 2026-11), a NAT gateway costs $0.045 per hour plus $0.045 per GB processed, and you also pay transfer charges if the NAT gateway and the instance using it sit in different Availability Zones.[^s5]
- Every public IPv4 address, in use or idle, costs $0.005 per hour (as of 2026-11, across commercial, GovCloud (US) and China Regions).[^s5]
- VPC peering traffic that crosses Availability Zones within a Region is billed at $0.01/GB in each direction (as of 2026-11).[^s5]
- AWS prices exclude applicable taxes and duties such as VAT and sales tax unless stated otherwise.[^s1]
- Rates differ by Region; use the AWS Pricing Calculator to estimate a single resource or a whole solution before building.[^s3]

## Applying the principles

AWS's cost-optimization guidance follows directly from these principles: choose the right pricing model (Spot, Compute or SageMaker Savings Plans, RIs for managed databases), match capacity to demand by stopping or right-sizing idle resources, and run processes that find waste such as unattached EBS volumes and idle load balancers.[^s6] For the Cloud Practitioner exam, Domain 4 (12% of scored content) expects you to compare compute purchasing options, describe Reserved Instance flexibility, and understand inbound versus outbound and inter-Region transfer costs, plus storage tiers.[^s4]

## Connections

The commitment and discount models are parts of this concept with their own articles: **Savings Plans**, **Reserved Instances**, **Spot Instances** and the no-cost entry point **AWS Free Tier**.[^s1][^s3] Network charges are expanded in **data transfer costs**.[^s5] **AWS Cost Explorer** supplies commitment recommendations and usage reporting, and the **Cost Optimization Pillar** of Well-Architected turns the principles into design practice.[^s3][^s6] The topic is listed in the **AWS Certified Cloud Practitioner** exam guide.[^s4]

## Sources
[^s1]: Amazon Web Services, "Key principles - How AWS Pricing Works". https://docs.aws.amazon.com/whitepapers/latest/how-aws-pricing-works/key-principles (accessed 2026-11-13)
[^s2]: Amazon Web Services, "How AWS Pricing Works - Abstract and introduction". https://docs.aws.amazon.com/whitepapers/latest/how-aws-pricing-works/abstract-and-introduction.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "AWS Pricing". https://aws.amazon.com/pricing/?nc2=h_ql_pr_ln (accessed 2026-11-13)
[^s4]: Amazon Web Services, "Content Domain 4: Billing, Pricing, and Support - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.html (accessed 2026-11-13)
[^s5]: Amazon Web Services, "Amazon VPC Pricing". https://aws.amazon.com/vpc/pricing/ (accessed 2026-11-13)
[^s6]: Amazon Web Services, "AWS Cost Optimization - How AWS Pricing Works". https://docs.aws.amazon.com/whitepapers/latest/how-aws-pricing-works/aws-cost-optimization.html (accessed 2026-11-13)
