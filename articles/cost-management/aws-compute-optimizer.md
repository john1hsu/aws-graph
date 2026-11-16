---
id: kt:aws-compute-optimizer
type: Service
title: AWS Compute Optimizer
aliases: [Compute Optimizer]
domain: cost-management
summary: >-
  AWS's opt-in recommendation service that analyzes resource configuration and CloudWatch utilization
  metrics to produce rightsizing and idle-resource recommendations for EC2, Auto Scaling groups, EBS,
  Lambda, ECS on Fargate, RDS and Aurora and a growing set of other services, free by default with a
  paid enhanced-metrics option.
verified: 2026-11-16
status: current
broader: [kt:aws-cost-management-services]
related: [kt:cost-optimization-pillar, kt:aws-trusted-advisor]
relations:
  - {predicate: dependsOn, object: kt:amazon-cloudwatch}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: integratesWith, object: kt:cost-optimization-hub}
  - {predicate: integratesWith, object: kt:amazon-ec2}
sources:
  - id: s1
    title: "What is AWS Compute Optimizer? - AWS Compute Optimizer"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/compute-optimizer/latest/ug/what-is-compute-optimizer.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "AWS resources supported by Compute Optimizer - AWS Compute Optimizer"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/compute-optimizer/latest/ug/supported-resources.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "AWS Compute Optimizer pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compute-optimizer/pricing/
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "New for AWS Compute Optimizer – Enhanced Infrastructure Metrics to Extend the Look-Back Period to Three Months"
    publisher: AWS News Blog
    url: https://aws.amazon.com/blogs/aws/new-for-aws-compute-optimizer-enhanced-infrastructure-metrics-to-extend-the-look-back-period-to-three-months
    type: official-blog
    accessed: 2026-11-16
  - id: s5
    title: "AWS Compute Optimizer now supports 32-day lookback for EBS volume and ECS service rightsizing recommendations"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/aws-compute-optimizer-ebs-ecs-32-day-lookback/
    type: official-blog
    accessed: 2026-11-16
  - id: s6
    title: "AWS Compute Optimizer now supports idle recommendations for six additional resource types"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/aws-compute-optimizer-six-new-idle
    type: official-blog
    accessed: 2026-11-16
  - id: s7
    title: "Identifying opportunities with Cost Optimization Hub - AWS Cost Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cost-management/latest/userguide/cost-optimization-hub.html
    type: official-docs
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS Compute Optimizer

AWS Compute Optimizer is the AWS service that looks at how your resources are configured and how hard they are actually working, then tells you whether each one is optimal, over-provisioned, under-provisioned or idle. Its output is a set of rightsizing and idle-resource recommendations, each with utilization graphs that compare recent usage against what the recommended option would deliver, so you can trade price against performance before you resize, move, stop or delete anything.[^s1]

## How it works

Nothing happens until you opt in. A standalone account, an organization member account or an organization's management account can opt in; opting in from the management account lets you see findings across many member accounts and Regions.[^s1] Compute Optimizer then combines resource specifications (for an EC2 instance, its vCPUs, memory and storage) with Amazon CloudWatch utilization metrics such as CPU, network and disk throughput. It can also take EC2 memory metrics from third-party observability tools such as Datadog and Dynatrace to improve rightsizing accuracy.[^s1]

Results appear in a dashboard of findings, a recommendations page with the top option per resource, and a detail page showing the top three options with graphs. You can export them to track recommendations over time, and findings also surface in the consoles of supported services such as EC2.[^s1] Recommendation preferences let you tune CPU and memory headroom and thresholds, the lookback period and preferred instance families, at the organization, account or Region level.[^s1]

## What it covers

Rightsizing recommendations cover EC2 instances (C, D, G, Hpc, I, M, P, R, T, U, X and Z series; Spot Instances excluded), EC2 Auto Scaling groups, EBS volumes attached to an instance (st1, sc1, gp2, gp3, io1, io2 and io2 Block Express), Lambda function memory, task sizing for ECS services on Fargate, and RDS and Aurora DB instances and storage for the MySQL- and PostgreSQL-compatible engines. Aurora Serverless v2 is not supported, and the Aurora storage recommendation only suggests moving from Aurora Standard to I/O-Optimized. It also gives license recommendations for Microsoft SQL Server on EC2.[^s2]

Idle recommendations cover Auto Scaling groups, NAT gateways, provisioned DynamoDB tables, ElastiCache (Redis or Valkey), MemoryDB, DocumentDB, WorkSpaces and SageMaker endpoints.[^s2] Six of those types were added in June 2026; idleness is judged from service-specific signals such as consumed capacity, cache hits, active connections and CPU.[^s6] A resource only gets a recommendation once it meets per-resource metric requirements, so brand-new resources may show nothing at first.[^s1][^s2]

## Pricing, lookback and gotchas

As of 2026-11 the recommendations themselves carry no extra charge; you pay only for the underlying resources and any CloudWatch monitoring. The default analysis uses the last 14 days of metrics, or 32 days where that option is selected.[^s3] Since June 2026 the 32-day lookback can be chosen at no additional cost for EC2 instances, Auto Scaling groups, RDS, EBS volumes and ECS services, at organization, account or resource level; it is offered in every Compute Optimizer Region except AWS GovCloud (US) and the China Regions.[^s5]

The paid **enhanced infrastructure metrics** preference extends the lookback to about three months (93 days in the user guide) for EC2 and RDS, which helps with quarterly patterns.[^s1][^s3] The pricing page lists one rate, with no Region breakdown: $0.0003360215 per resource per hour, about $0.25 for a resource running a full 31-day month (as of 2026-11). Auto Scaling groups are billed per running instance, not per group.[^s3] It can be switched on per organization, account or resource, and changes can take a few hours to show up in recommendations; AWS suggests leaving it off for short-lived CI/CD test instances.[^s4]

## When to use it (and when not)

Turn it on in every account, ideally from the management account, as the standing source of rightsizing and idle-resource findings; review it before buying Savings Plans or reservations so you don't commit to capacity you are about to shrink.[^s1][^s7] Pick a longer lookback for workloads with month-end or quarterly peaks.[^s5][^s4] It is a recommendation engine, not an automation tool: it doesn't resize anything for you, and its estimates reflect the metrics it can see, so add memory metrics before trusting EC2 downsizing advice.[^s1]

## Connections

Compute Optimizer is one of the **AWS cost management services**, and its rightsizing and idle findings are a practical input to the **Cost Optimization Pillar** and to the cost checks in **AWS Trusted Advisor** (both related nodes), since all three aim at matching provisioned capacity to actual use.[^s7][^s1] It **depends on Amazon CloudWatch** for the utilization metrics it analyzes, and **integrates with Amazon EC2**, where findings appear in the service console.[^s1] It **integrates with AWS Organizations** for organization-wide opt-in, preferences and cross-account findings.[^s1][^s4] It **integrates with Cost Optimization Hub**, which brings Compute Optimizer's rightsizing and idle recommendations together with Savings Plans and Reserved Instance recommendations, de-duplicates overlapping savings, and prices them with your own discounts; once the Hub is enabled, its savings estimates also show in the Compute Optimizer console.[^s7][^s6]

## Sources
[^s1]: Amazon Web Services, "What is AWS Compute Optimizer?". https://docs.aws.amazon.com/compute-optimizer/latest/ug/what-is-compute-optimizer.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "AWS resources supported by Compute Optimizer". https://docs.aws.amazon.com/compute-optimizer/latest/ug/supported-resources.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "AWS Compute Optimizer pricing". https://aws.amazon.com/compute-optimizer/pricing/ (accessed 2026-11-16)
[^s4]: AWS News Blog, "New for AWS Compute Optimizer – Enhanced Infrastructure Metrics to Extend the Look-Back Period to Three Months". https://aws.amazon.com/blogs/aws/new-for-aws-compute-optimizer-enhanced-infrastructure-metrics-to-extend-the-look-back-period-to-three-months (accessed 2026-11-16)
[^s5]: Amazon Web Services, "AWS Compute Optimizer now supports 32-day lookback for EBS volume and ECS service rightsizing recommendations" (2026-06-03). https://aws.amazon.com/about-aws/whats-new/2026/06/aws-compute-optimizer-ebs-ecs-32-day-lookback/ (accessed 2026-11-16)
[^s6]: Amazon Web Services, "AWS Compute Optimizer now supports idle recommendations for six additional resource types" (2026-06-08). https://aws.amazon.com/about-aws/whats-new/2026/06/aws-compute-optimizer-six-new-idle (accessed 2026-11-16)
[^s7]: Amazon Web Services, "Identifying opportunities with Cost Optimization Hub". https://docs.aws.amazon.com/cost-management/latest/userguide/cost-optimization-hub.html (accessed 2026-11-16)
