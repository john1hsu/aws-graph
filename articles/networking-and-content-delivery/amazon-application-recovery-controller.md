---
id: kt:amazon-application-recovery-controller
type: Service
title: Amazon Application Recovery Controller
aliases: [ARC, Amazon ARC, Route 53 Application Recovery Controller, Amazon Route 53 Application Recovery Controller, Route 53 ARC]
domain: networking-and-content-delivery
summary: >-
  AWS's application recovery service: zonal shift and zonal autoshift move traffic for supported
  resources away from an impaired Availability Zone, while Region switch plans and routing controls
  fail an application over to a replica in another AWS Region.
verified: 2026-11-15
status: current
broader: [kt:aws-networking-and-content-delivery]
related: [kt:availability-zones, kt:disaster-recovery-strategies, kt:multi-region-architecture, kt:reliability-pillar]
relations:
  - {predicate: integratesWith, object: kt:elastic-load-balancing}
  - {predicate: integratesWith, object: kt:amazon-route-53}
  - {predicate: integratesWith, object: kt:aws-global-accelerator}
  - {predicate: integratesWith, object: kt:amazon-ec2-auto-scaling}
  - {predicate: integratesWith, object: kt:aurora-global-database}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
sources:
  - id: s1
    title: "What is ARC? - Amazon Application Recovery Controller (ARC)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/r53recovery/latest/dg/what-is-route53-recovery.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Amazon Application Recovery Controller FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/application-recovery-controller/faqs/
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Amazon Application Recovery Controller pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/application-recovery-controller/pricing/
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "Amazon Application Recovery Controller (ARC) readiness check availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/r53recovery/latest/dg/arc-readiness-availability-change.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "Compare multi-AZ and multi-Region recovery capabilities in ARC"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/r53recovery/latest/dg/compare-capabilities.html
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "Introducing Amazon Application Recovery Controller Region switch: A multi-Region application recovery service"
    publisher: AWS News Blog
    url: https://aws.amazon.com/blogs/aws/introducing-amazon-application-recovery-controller-region-switch-a-multi-region-application-recovery-service/
    type: official-blog
    accessed: 2026-11-15
  - id: s7
    title: "Amazon Application Recovery Controller (ARC) endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/arc.html
    type: official-docs
    accessed: 2026-11-15
  - id: s8
    title: "Amazon Application Recovery Controller (ARC) in AWS GovCloud (US)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-arc-zonal-shift.html
    type: official-docs
    accessed: 2026-11-15
created: 2026-11-15
---

# Amazon Application Recovery Controller

Amazon Application Recovery Controller (ARC) is the AWS service for recovering an application quickly when part of the AWS infrastructure it runs on is impaired. It is organised around two failure scopes: **multi-AZ recovery** inside one Region (zonal shift and zonal autoshift) and **multi-Region recovery** (Region switch and routing control), plus an older readiness-check feature.[^s1] The service grew out of Amazon Route 53, and its documentation paths and control-plane endpoints still carry `r53recovery` and `route53-recovery` names, which is why "Route 53 ARC" remains a common alias.[^s1][^s7]

## How it works

**Zonal shift** is a manual, temporary instruction to move traffic for a supported resource out of one Availability Zone and into the healthy AZs of the same Region, for example after a bad deployment or an AWS impairment in that AZ.[^s1] Every zonal shift must have an expiry, initially between 1 minute and 3 days (72 hours), which you can extend or cancel. The FAQ lists Application Load Balancers, Network Load Balancers, EC2 Auto Scaling groups and Amazon EKS clusters as supported resources.[^s2]

**Zonal autoshift** lets AWS start and end that shift on your behalf once you enable it for a resource, based on internal telemetry from sources such as the AWS network, Amazon EC2 and Elastic Load Balancing.[^s1] To prove the remaining AZs can absorb the load, autoshift requires weekly **practice runs** that shift traffic away from one AZ for about 30 minutes and record a SUCCEEDED or FAILED outcome; you can block out windows when practice runs must not happen.[^s2]

**Region switch**, launched in August 2025, orchestrates failing over (or, for active/active designs, shifting away from) a whole application stack into another Region using plans that can span multiple accounts.[^s6][^s2] Plans are built from execution blocks; at launch these covered EC2 Auto Scaling, ARC routing controls, Aurora global database, Route 53 health checks, EKS and ECS resource scaling, Lambda custom actions, manual approval and nested Region switch plans.[^s6] Plans run in the Region being activated, so recovery does not depend on the impaired Region, and **plan evaluation** checks each plan's resources and IAM permissions every 30 minutes.[^s6] A plan can be started manually or from CloudWatch alarm state changes.[^s2]

**Routing control** provides on/off switches, hosted on a cluster of redundant endpoints in five Regions, that flip Route 53 health checks to move DNS traffic between Regional replicas. **Safety rules** (assertion and gating rules) block switch combinations that would, for instance, turn off every replica at once.[^s2][^s1] The cluster data-plane endpoints sit in us-east-1, eu-west-1, us-west-2, ap-northeast-1 and ap-southeast-2, and AWS recommends client retry logic that can try all five.[^s7]

## Quotas, pricing and gotchas

- **Pricing (as of 2026-11, from the global pricing page):** zonal shift and zonal autoshift carry no additional charge; Region switch is $70 per plan per month, prorated; routing control is $2.50 per hour per cluster; readiness check is $0.045 per hour per check.[^s3]
- **Cluster limit:** an account can have at most 2 routing control clusters, each hosting many routing controls.[^s2][^s3]
- **Duration semantics differ:** zonal shifts always expire, whereas routing control and Region switch leave traffic on the replica until you move it back.[^s5]
- **Pre-scale:** AWS advises having enough capacity already running in the remaining AZs instead of relying on auto scaling to catch up after a shift.[^s2]
- **Readiness check is closed to new customers.** Existing users can keep using it, and AWS points new multi-Region users to Region switch plan evaluation instead; the pricing page dates the cut-off for new customers to April 30, 2026.[^s4][^s3] AWS also says readiness checks are not meant to sit in the critical failover path.[^s1]
- **Partition coverage:** in AWS GovCloud (US) the GovCloud user guide lists zonal shift and Region switch, and says readiness checks and routing control are not available.[^s8] Zonal shift endpoints exist in every Region, including the China Regions.[^s7]

## When to use it (and when not)

Use zonal shift or autoshift for multi-AZ applications behind supported resources: they are free, need little setup, and act within the Region. Use Region switch for coordinated, multi-step, multi-account Regional failover, and routing control when you only need highly available, guarded DNS failover switches between replicas.[^s5] ARC orchestrates traffic and recovery steps; it does not replicate data, so it complements, rather than replaces, the backup, pilot-light and warm-standby choices in a disaster recovery strategy.[^s1][^s6]

## Connections

- **Broader:** part of AWS networking and content delivery (`kt:aws-networking-and-content-delivery`), alongside Route 53 (`kt:amazon-route-53`), whose health checks routing control drives.[^s2]
- **Integrates with** Elastic Load Balancing (`kt:elastic-load-balancing`) and EC2 Auto Scaling (`kt:amazon-ec2-auto-scaling`) as zonal-shift resources, and with Aurora global database (`kt:aurora-global-database`) as a Region switch execution block.[^s2][^s6] The AWS Global Accelerator article (`kt:aws-global-accelerator`) records the reverse integration edge.
- **Monitored by / triggered from** Amazon CloudWatch (`kt:amazon-cloudwatch`) alarms that can start Region switch plans.[^s2]
- **Related to** Availability Zones (`kt:availability-zones`), the unit zonal shift evacuates; disaster recovery strategies (`kt:disaster-recovery-strategies`) and multi-Region architecture (`kt:multi-region-architecture`), which Region switch automates; and the reliability pillar (`kt:reliability-pillar`).[^s1][^s5]

## Sources
[^s1]: Amazon Web Services, "What is ARC? - Amazon Application Recovery Controller (ARC)". https://docs.aws.amazon.com/r53recovery/latest/dg/what-is-route53-recovery.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Amazon Application Recovery Controller FAQs". https://aws.amazon.com/application-recovery-controller/faqs/ (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Amazon Application Recovery Controller pricing". https://aws.amazon.com/application-recovery-controller/pricing/ (accessed 2026-11-15)
[^s4]: Amazon Web Services, "Amazon Application Recovery Controller (ARC) readiness check availability change". https://docs.aws.amazon.com/r53recovery/latest/dg/arc-readiness-availability-change.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "Compare multi-AZ and multi-Region recovery capabilities in ARC". https://docs.aws.amazon.com/r53recovery/latest/dg/compare-capabilities.html (accessed 2026-11-15)
[^s6]: AWS News Blog, "Introducing Amazon Application Recovery Controller Region switch: A multi-Region application recovery service". https://aws.amazon.com/blogs/aws/introducing-amazon-application-recovery-controller-region-switch-a-multi-region-application-recovery-service/ (accessed 2026-11-15)
[^s7]: Amazon Web Services, "Amazon Application Recovery Controller (ARC) endpoints and quotas - AWS General Reference". https://docs.aws.amazon.com/general/latest/gr/arc.html (accessed 2026-11-15)
[^s8]: Amazon Web Services, "Amazon Application Recovery Controller (ARC) in AWS GovCloud (US)". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-arc-zonal-shift.html (accessed 2026-11-15)
