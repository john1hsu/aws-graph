---
id: kt:aws-shield
type: Service
title: AWS Shield
aliases: [Shield, AWS Shield Standard, AWS Shield Advanced, Shield Advanced]
domain: security
summary: >-
  AWS's managed DDoS protection service: Shield Standard automatically defends every AWS customer
  against common network- and transport-layer attacks at no charge, while the paid Shield Advanced
  subscription adds protection for explicitly enrolled resources, AWS WAF-based application-layer
  mitigation, the Shield Response Team and DDoS cost protection.
verified: 2026-11-15
status: current
broader: [kt:aws-security-services]
related: [kt:aws-waf, kt:aws-firewall-manager, kt:edge-locations]
relations:
  - {predicate: integratesWith, object: kt:amazon-cloudfront}
  - {predicate: integratesWith, object: kt:amazon-route-53}
  - {predicate: integratesWith, object: kt:aws-global-accelerator}
  - {predicate: integratesWith, object: kt:elastic-load-balancing}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "How AWS Shield and Shield Advanced work"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/waf/latest/developerguide/ddos-overview
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "AWS Shield Standard overview"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/waf/latest/developerguide/ddos-standard-summary.html
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "AWS Shield Advanced capabilities and options"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/waf/latest/developerguide/ddos-advanced-summary-capabilities.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "AWS Shield Advanced protected resources"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/waf/latest/developerguide/ddos-advanced-summary-protected-resources.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "AWS Shield pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/shield/pricing
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "AWS Shield FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/shield/faqs
    type: official-docs
    accessed: 2026-11-15
  - id: s7
    title: "AWS Shield network security director now supports multi-account analysis"
    publisher: What's New with AWS
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/aws-shield-network-security-director-multi-account-analysis
    type: official-blog
    accessed: 2026-11-15
  - id: s8
    title: "In-Scope AWS Services (AWS Certified Cloud Practitioner CLF-C02), French edition"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-15
created: 2026-11-15
---

# AWS Shield

AWS Shield is AWS's managed service for detecting and mitigating distributed denial of service (DDoS) attacks against applications running on AWS. It comes in two tiers: **Shield Standard**, which is switched on automatically for every AWS customer at no extra charge, and **Shield Advanced**, a paid subscription with stronger, more tailored protection for resources you enroll.[^s1][^s2] AWS groups the attacks it handles into network volumetric attacks (layer 3), network protocol attacks such as TCP SYN floods (layer 4), and application-layer request floods (layer 7).[^s1]

## Shield Standard

Shield Standard defends against the most common network- and transport-layer DDoS attacks for all applications on AWS, with nothing to configure.[^s2] The FAQ states there is no limit on how many resources it covers and that it applies across Regions and edge locations.[^s6] It is strongest for traffic entering through the AWS edge: Route 53 hosted zones, CloudFront distributions and Global Accelerator standard accelerators get availability protection against all known layer 3 and 4 attacks. AWS therefore recommends deciding where your application perimeter sits. If users arrive through Route 53 and CloudFront or Global Accelerator, the perimeter is the AWS edge; if their first hop is a Regional endpoint, it is your VPC.[^s2] Shield Standard does not filter layer 7 request floods; that job belongs to AWS WAF rules.[^s2][^s3]

## Shield Advanced

Shield Advanced protects only the resources you explicitly add, either directly or through an AWS Firewall Manager Shield Advanced policy. The supported types are CloudFront distributions (including staging distributions linked to a protected primary), Route 53 hosted zones, Global Accelerator standard accelerators, Elastic IP addresses, Application Load Balancers and Classic Load Balancers. EC2 instances and Network Load Balancers are covered only through an attached Elastic IP.[^s4] The FAQ gives a default of up to 1,000 protected resources of each supported type; more needs a limit increase through AWS Support.[^s6]

On top of enrollment, the subscription adds:[^s3]

- **Application-layer protection through AWS WAF.** Protected resources need an associated web ACL. Automatic layer 7 mitigation adds a rule group consuming 150 WCUs, rate-limits known DDoS sources, and creates and manages WAF rules during an attack in count or block mode.
- **Health-based detection** using Route 53 health checks, which reduces false positives. It works for every protected type except Route 53 hosted zones.
- **Protection groups** that detect and mitigate across a set of resources treated as one application. Membership criteria can add new resources automatically.
- **Enhanced visibility** through the Shield API, the console and Amazon CloudWatch metrics.
- **Shield Response Team (SRT)** access for custom mitigations during an attack, plus optional **proactive engagement**, where the SRT contacts you when a health check goes unhealthy during a detected event. Both require a Business or Enterprise Support plan, and proactive engagement also needs health-based detection.
- **Cost protection**, issued as Shield Advanced service credits for bill spikes caused by an attack, including spikes in Shield data transfer out fees.

## Quotas, pricing and gotchas

As listed on the pricing page in 2026-11, Shield Advanced costs $3,000 per month per payer account, and one fee covers all linked accounts in an AWS Organization. The subscription is a one-year commitment that renews automatically. You can cancel only between 30 and 5 days before renewal and still owe fees through the end of the term.[^s5] Data transfer out from protected resources carries an extra Shield fee per GB. The page's own worked examples use $0.050 per GB for an Application Load Balancer and $0.025 per GB for CloudFront.[^s5] The subscription includes AWS WAF web ACL, rule and request charges on protected resources up to 50 billion requests per payer per calendar month, with overage at $0.15 per million requests, but only for web ACLs within 1,500 WCUs and default body inspection size. Bot Control, Fraud Control, CAPTCHA and managed rule subscriptions are still billed separately.[^s3][^s5] The usual mistake is subscribing without enrolling anything: Shield Advanced does nothing for resources you have not added.[^s4]

The Shield console also hosts **network security director**, which maps network topology and flags missing or misconfigured protections such as WAF, security groups and network ACLs. As of 2026-11 it is still in **preview** and is not part of the Shield Advanced subscription. A December 2025 update added multi-account analysis through a delegated administrator and five more Regions.[^s5][^s6][^s7]

## When to use it (and when not)

Every AWS workload already has Shield Standard, so the real question is whether Shield Advanced is worth $3,000 a month plus a one-year term. It makes sense for internet-facing applications where downtime is very costly: you get SRT help during attacks, cost protection against scaling bills, and WAF charges that are largely absorbed.[^s3][^s5] Smaller workloads can often get by with Shield Standard, edge services such as CloudFront and Route 53, and AWS WAF rate-based rules paid as you go.[^s2][^s3]

## Connections

AWS Shield is one of the AWS security services (**broader**) and appears in the Security, Identity and Compliance list of in-scope services for the Cloud Practitioner exam (**coveredBy**).[^s8] It **integrates with** the front doors it protects: Amazon CloudFront, Amazon Route 53, AWS Global Accelerator and Elastic Load Balancing.[^s4] It is **related to** the AWS edge locations where Standard is most effective[^s2], to AWS WAF, which supplies its layer 7 mitigations, and to AWS Firewall Manager, which rolls Shield Advanced protection out across accounts.[^s3] Attack metrics are reported to Amazon CloudWatch (**monitoredBy**).[^s3]

## Sources
[^s1]: Amazon Web Services, "How AWS Shield and Shield Advanced work". https://docs.aws.amazon.com/waf/latest/developerguide/ddos-overview (accessed 2026-11-15)
[^s2]: Amazon Web Services, "AWS Shield Standard overview". https://docs.aws.amazon.com/waf/latest/developerguide/ddos-standard-summary.html (accessed 2026-11-15)
[^s3]: Amazon Web Services, "AWS Shield Advanced capabilities and options". https://docs.aws.amazon.com/en_us/waf/latest/developerguide/ddos-advanced-summary-capabilities.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "AWS Shield Advanced protected resources". https://docs.aws.amazon.com/waf/latest/developerguide/ddos-advanced-summary-protected-resources.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "AWS Shield pricing". https://aws.amazon.com/shield/pricing (accessed 2026-11-15)
[^s6]: Amazon Web Services, "AWS Shield FAQs". https://aws.amazon.com/shield/faqs (accessed 2026-11-15)
[^s7]: What's New with AWS, "AWS Shield network security director now supports multi-account analysis" (2025-12-12). https://aws.amazon.com/about-aws/whats-new/2025/12/aws-shield-network-security-director-multi-account-analysis (accessed 2026-11-15)
[^s8]: Amazon Web Services, "In-Scope AWS Services" for AWS Certified Cloud Practitioner (CLF-C02), French edition. https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-15)
