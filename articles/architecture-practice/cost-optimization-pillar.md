---
id: kt:cost-optimization-pillar
type: Concept
title: Cost Optimization Pillar
aliases: [AWS Well-Architected Cost Optimization Pillar, Well-Architected cost optimization pillar, Cost Optimization Pillar whitepaper]
domain: architecture-practice
summary: >-
  The pillar of the AWS Well-Architected Framework concerned with running workloads that deliver business
  value at the lowest price point, organized into five best practice areas (from Cloud Financial Management
  to optimizing over time), eleven COST questions and five design principles.
verified: 2026-11-13
status: concept
broader: [kt:aws-well-architected-framework]
related: [kt:performance-efficiency-pillar, kt:sustainability-pillar, kt:savings-plans, kt:spot-instances]
relations:
  - {predicate: partOf, object: kt:aws-well-architected-framework}
  - {predicate: monitoredBy, object: kt:aws-cost-explorer}
  - {predicate: monitoredBy, object: kt:aws-budgets}
sources:
  - id: s1
    title: "Cost Optimization Pillar - AWS Well-Architected Framework (abstract and introduction)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Design principles - Cost Optimization Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/design-principles.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "COST 1. How do you implement cloud financial management? - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/cost-01.html
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "Expenditure and usage awareness - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/cost-aware.html
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "Cost-effective resources - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/cost-cereso.html
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "COST07-BP01 Perform pricing model analysis - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/cost_pricing_model_analysis.html
    type: official-docs
    accessed: 2026-11-13
  - id: s7
    title: "Manage demand and supply resources - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/cost-mandem.html
    type: official-docs
    accessed: 2026-11-13
  - id: s8
    title: "Optimize over time - AWS Well-Architected Framework (2023-10-03 version)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/2023-10-03/framework/cost-opti.html
    type: official-docs
    accessed: 2026-11-13
  - id: s9
    title: "Best practices (cost optimization) - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/cost-bp.html
    type: official-docs
    accessed: 2026-11-13
created: 2026-11-13
---

# Cost Optimization Pillar

The Cost Optimization Pillar is one of the six pillars of the AWS Well-Architected Framework, alongside operational excellence, security, reliability, performance efficiency and sustainability. Its whitepaper, dated June 27, 2024 (as of 2026-11), is written for both technology and finance roles, from CTOs and CFOs to financial planners, business analysts and developers, and deliberately points to other resources rather than giving implementation patterns.[^s1] It describes a cost-optimized workload as one that uses all its resources fully, reaches its outcome at the lowest possible price point and still meets functional requirements.[^s1]

## Design principles

The pillar sets out five design principles.[^s2]

- **Implement cloud financial management:** treat cost efficiency as a capability that needs dedicated people, programs and processes, much like security or operations.[^s2]
- **Adopt a consumption model:** pay only for what you use and scale with business need. AWS's example: stopping dev and test environments outside a roughly eight-hour working day saves about 75% (40 of 168 weekly hours).[^s2]
- **Measure overall efficiency:** track business output against the cost of delivering it.[^s2]
- **Stop spending money on undifferentiated heavy lifting:** let AWS and managed services carry data center and OS operations.[^s2]
- **Analyze and attribute expenditure:** use the cloud's per-workload visibility to tie costs to revenue streams and owners and to measure ROI.[^s2]

## How it works: five areas, eleven questions

Guidance is grouped into five best practice areas: practice Cloud Financial Management, expenditure and usage awareness, cost-effective resources, manage demand and supply resources, and optimize over time.[^s9]

- **Cloud Financial Management (COST 1)** has nine best practices, including naming an owner for cost optimization, a finance–technology partnership, budgets and forecasts, proactive monitoring, a cost-aware culture and quantifying the business value of savings.[^s3]
- **Expenditure and usage awareness (COST 2–4)** covers governing usage, monitoring usage and cost, and decommissioning resources. It relies on account structure in AWS Organizations or Control Tower, cost allocation tags, Cost Explorer, Budgets alerts, and the Cost and Usage Report at hourly granularity (queried with Athena) for deep analysis.[^s4]
- **Cost-effective resources (COST 5–8)** asks how cost shapes service selection; how to size the type and number of resources; how to use pricing models; and how to plan for data transfer charges.[^s5]
- **Manage demand and supply (COST 9)** aims to avoid both over-provisioning and over-utilization. It works on the supply side with time- or demand-based Auto Scaling, and on the demand side with API Gateway throttling, SQS buffering or deferred batch work.[^s7]
- **Optimize over time (COST 10–11)** asks teams to re-examine decisions as AWS ships new services, and to find and automate the operations that eat the most staff time.[^s8]

## Numbers and gotchas

The framework puts Savings Plans and Reserved Instances at up to 75% off On-Demand, and Spot Instances at up to 90% off, with Spot suited to fleets where individual servers can come and go, such as stateless web tiers, batch and HPC (figures as of 2026-11).[^s5] COST07-BP01, rated high risk if missing, adds that shutting EC2 and RDS instances after hours and on weekends can cut costs by 70% or more versus running them around the clock. It also warns that On-Demand Capacity Reservations are not a discount: they bill at the On-Demand rate whether used or not.[^s6] A subtler point from COST 5–8: the cheapest instance is not always cheapest overall. A job that takes five hours on a small server can cost more than one hour on a server twice the price.[^s5]

## When to use it

Use the pillar for a Well-Architected review of spend, when setting up a FinOps or Cloud Financial Management practice, or before committing to Savings Plans or Reserved Instances. Because COST 9 treats over-utilization as harmful as over-provisioning, the goal is the cheapest configuration that still meets requirements, not the lowest bill at any cost; savings that degrade performance or functional requirements fall outside the pillar's own definition.[^s1][^s7]

## Connections

The pillar is **part of** the AWS Well-Architected Framework.[^s1] Its awareness practices are **monitored by** AWS Cost Explorer and AWS Budgets, the visibility and alerting tools named in COST 2–4.[^s4] It is **related** to Savings Plans and Spot Instances as the main pricing levers in COST 7,[^s5][^s6] and sits beside the performance efficiency and sustainability pillars, which share its focus on matching supply to demand.[^s1][^s7]

## Sources
[^s1]: Amazon Web Services, "Cost Optimization Pillar - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Design principles - Cost Optimization Pillar". https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/design-principles.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "COST 1. How do you implement cloud financial management?". https://docs.aws.amazon.com/wellarchitected/latest/framework/cost-01.html (accessed 2026-11-13)
[^s4]: Amazon Web Services, "Expenditure and usage awareness - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/cost-aware.html (accessed 2026-11-13)
[^s5]: Amazon Web Services, "Cost-effective resources - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/cost-cereso.html (accessed 2026-11-13)
[^s6]: Amazon Web Services, "COST07-BP01 Perform pricing model analysis". https://docs.aws.amazon.com/wellarchitected/latest/framework/cost_pricing_model_analysis.html (accessed 2026-11-13)
[^s7]: Amazon Web Services, "Manage demand and supply resources - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/cost-mandem.html (accessed 2026-11-13)
[^s8]: Amazon Web Services, "Optimize over time - AWS Well-Architected Framework" (2023-10-03 version). https://docs.aws.amazon.com/wellarchitected/2023-10-03/framework/cost-opti.html (accessed 2026-11-13)
[^s9]: Amazon Web Services, "Best practices - AWS Well-Architected Framework (cost optimization)". https://docs.aws.amazon.com/wellarchitected/latest/framework/cost-bp.html (accessed 2026-11-13)
