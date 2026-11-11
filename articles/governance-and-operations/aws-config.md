---
id: kt:aws-config
type: Service
title: AWS Config
aliases: [Config]
domain: governance-and-operations
summary: >-
  AWS's resource inventory, configuration-history and compliance service: it records the
  configuration of supported resources as configuration items, evaluates them against
  managed or custom Config rules and conformance packs, and aggregates the results across
  accounts and Regions.
verified: 2026-11-11
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:aws-control-tower, kt:aws-cloudtrail, kt:aws-organizations, kt:aws-security-hub]
relations:
  - {predicate: dependsOn, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:amazon-sns}
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: governedBy, object: kt:aws-config-quotas}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What Is AWS Config? - AWS Config"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.md
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "How AWS Config Works - AWS Config"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/config/latest/developerguide/how-does-config-work.md
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Components of an AWS Config Rule - AWS Config"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config_components.md
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Service Limits for AWS Config"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/config/latest/developerguide/configlimits.md
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "AWS Config pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/config/pricing
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "AWS Config now supports a service-linked recorder"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2024/12/aws-config-service-linked-recorder
    type: official-blog
    accessed: 2026-11-11
  - id: s7
    title: "AWS Config now supports internal service linked rules"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/aws-config-supports-internal-service-linked-rules
    type: official-blog
    accessed: 2026-11-11
  - id: s8
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-11
  - id: s9
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# AWS Config

AWS Config gives a detailed, time-indexed view of how the AWS resources in an account are configured, how they relate to one another, and how those configurations changed over time.[^s1] It answers questions like "what did this security group allow last Tuesday?" or "which IAM policy was attached to this role on a given date?", and it evaluates resources against rules so misconfigurations are flagged as noncompliant without you polling each service yourself.[^s1] Its typical uses are resource administration, auditing and compliance evidence, change troubleshooting (including finding the last known good configuration), and security analysis.[^s1]

## How it works

**Recording.** When you turn on the configuration recorder, Config discovers the supported resources in the Region and creates a *configuration item* for each, then creates new items whenever a configuration changes, keeping history from the moment recording started.[^s2] By default it records every supported resource type in the Region, but you can limit it to the types you care about.[^s2] It tracks changes by calling each resource's Describe or List APIs, also capturing related resources: removing an egress rule from a security group, for example, triggers fresh items for the group and for every instance attached to it.[^s2] It also re-examines resources periodically, so changes not made through the API are still captured.[^s2]

**Delivery.** A delivery channel sends a configuration history file to your S3 bucket every six hours for each recorded resource type that changed, and a full configuration snapshot on request; both are JSON.[^s2] An optional SNS topic receives the configuration stream (change notifications delivered within minutes) plus compliance-change and delivery-status messages.[^s2] Setup therefore needs an S3 bucket, optionally an SNS topic, and an IAM role that lets Config reach them.[^s1]

**Rules.** A Config rule returns COMPLIANT, NON_COMPLIANT, ERROR or NOT_APPLICABLE for each resource in its scope.[^s3] Rules fire on configuration changes (scoped by resource type, resource ID or tag), periodically at a frequency you choose such as every 24 hours, or both (hybrid).[^s3] Each rule can run in *detective* mode against deployed resources or *proactive* mode against resource properties before deployment; proactive rules neither block deployment nor remediate.[^s3] Custom rules are backed by Lambda functions holding the evaluation logic.[^s2] *Conformance packs* bundle rules to deploy and monitor as one unit, *remediation* fixes noncompliant resources, *aggregators* pull configuration and compliance data from many accounts and Regions into one account and Region, and *advanced queries* query the current configuration state of resources using sample or custom queries.[^s1] Organization-level aggregators and per-account aggregators both exist and count toward the same aggregator limit.[^s4]

## Quotas, pricing and gotchas

From the AWS Config service limits page (as of 2026-11): 1,000 Config rules per Region per account, not adjustable; 50 conformance packs per account and 50 per organization, each with at most 130 rules, not adjustable, and rules inside conformance packs count against the 1,000-rule limit.[^s4] Aggregators are limited to 50 (adjustable), each covering up to 10,000 accounts (not adjustable), and 300 saved advanced queries per account and Region (adjustable).[^s4]

Pricing is per item and per evaluation. In AWS's US East (N. Virginia) examples, as of 2026-11, continuous recording costs $0.003 per configuration item, periodic recording $0.012 per periodic item (one per resource per day), and rule and conformance-pack evaluations $0.001 each for the first 100,000.[^s5] S3 storage, SNS notifications and the Lambda functions behind custom rules are billed at their own standard rates.[^s5] The gotcha is churn: every recorded change is a billable configuration item, so workloads that constantly create and destroy resources rack up items quickly; daily periodic recording and recording only the resource types you need are the two levers.[^s2][^s5] Recording is best effort; delays happen and AWS points to Config's CloudWatch metrics for diagnosis.[^s2]

## What changed (2024-12 to 2026-06): service-linked recorders and rules

In December 2024 AWS introduced *service-linked recorders*: immutable recorders that another AWS service deploys and manages for its own features, running independently of your recorder; Amazon CloudWatch was first, using one to audit telemetry configuration at no extra cost.[^s6] In June 2026 Config added *internal service-linked rules*, letting services such as AWS Security Hub CSPM run Config managed rules for their own checks, with results going to that service at no charge and independently of customer recorders and rules.[^s7]

## When to use it (and when not)

Use Config when you need a resource inventory, a configuration history for audits, or continuous compliance checks across accounts.[^s1] It complements rather than replaces CloudTrail: Config's unit is the resource's configuration state over time, captured by describing resources, not a log of who made each API call.[^s2] For enforcement before deployment, pair Config's proactive mode with preventive controls such as SCPs, because proactive rules only report.[^s3]

## Connections

Config sits within AWS management and governance services, `dependsOn` **Amazon S3** for history and snapshots, and `integratesWith` **Amazon SNS** for its configuration stream and **AWS Lambda** for custom rule logic.[^s1][^s2] **AWS Control Tower** builds on Config for governance, organization-level aggregators and conformance packs work across **AWS Organizations**, and **AWS Security Hub** now consumes service-linked rules.[^s4][^s7] **AWS CloudTrail** is its API-activity counterpart.[^s2] Limits are `governedBy` **AWS Config quotas**,[^s4] it `compliesWith` **FedRAMP on AWS** (listed for the US East/West and GovCloud authorizations),[^s8] and it is `coveredBy` the **Solutions Architect – Associate** exam, which lists it under Management and Governance.[^s9]

## Sources

[^s1]: Amazon Web Services, "What Is AWS Config? - AWS Config". https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.md (accessed 2026-11-11)
[^s2]: Amazon Web Services, "How AWS Config Works - AWS Config". https://docs.aws.amazon.com/config/latest/developerguide/how-does-config-work.md (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Components of an AWS Config Rule - AWS Config". https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config_components.md (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Service Limits for AWS Config". https://docs.aws.amazon.com/config/latest/developerguide/configlimits.md (accessed 2026-11-11)
[^s5]: Amazon Web Services, "AWS Config pricing". https://aws.amazon.com/config/pricing (accessed 2026-11-11)
[^s6]: Amazon Web Services, "AWS Config now supports a service-linked recorder" (What's New, 2024-12-06). https://aws.amazon.com/about-aws/whats-new/2024/12/aws-config-service-linked-recorder (accessed 2026-11-11)
[^s7]: Amazon Web Services, "AWS Config now supports internal service linked rules" (What's New, 2026-06-02). https://aws.amazon.com/about-aws/whats-new/2026/06/aws-config-supports-internal-service-linked-rules (accessed 2026-11-11)
[^s8]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-11)
[^s9]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-11)
