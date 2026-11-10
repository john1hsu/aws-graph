---
id: kt:aws-control-tower
type: Service
title: AWS Control Tower
aliases: [Control Tower, AWS Control Tower landing zone]
domain: governance-and-operations
summary: >-
  AWS's managed service for setting up and governing a multi-account AWS environment: it
  orchestrates AWS Organizations, IAM Identity Center, Service Catalog, AWS Config and
  CloudTrail to build a landing zone, applies preventive, detective and proactive controls,
  and vends new accounts through Account Factory.
verified: 2026-11-10
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:iam-identity-center, kt:multi-account-strategy, kt:service-control-policies]
relations:
  - {predicate: dependsOn, object: kt:aws-organizations}
  - {predicate: monitoredBy, object: kt:aws-config}
  - {predicate: integratesWith, object: kt:aws-cloudtrail}
  - {predicate: governedBy, object: kt:control-tower-quotas}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What Is AWS Control Tower? - AWS Control Tower"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/controltower/latest/userguide/what-is-control-tower.md
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "How AWS Control Tower works - AWS Control Tower"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/controltower/latest/userguide/how-control-tower-works.md
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Key changes (landing zone v4.0) - AWS Control Tower"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/controltower/latest/userguide/key-changes-lz-v4.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Limitations and quotas in AWS Control Tower"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/controltower/latest/userguide/limits.md
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "AWS Control Tower pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/controltower/pricing/
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "AWS Control Tower introduces a controls-dedicated experience"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/aws-control-tower-controls-dedicated-experience
    type: official-blog
    accessed: 2026-11-10
  - id: s7
    title: "AWS Control Tower in AWS GovCloud (US)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-controltower.md
    type: official-docs
    accessed: 2026-11-10
  - id: s8
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-10
  - id: s9
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# AWS Control Tower

AWS Control Tower is the managed way to stand up and govern a multi-account AWS environment along prescriptive best practices. Rather than being a new control plane, it orchestrates existing services, chiefly AWS Organizations, AWS Service Catalog and IAM Identity Center, to build a **landing zone**, which AWS says takes less than an hour.[^s1] It then keeps that environment from drifting away from best practice by applying **controls** (also called guardrails), and lets distributed teams self-serve new accounts through **Account Factory** while central administrators watch compliance from a **dashboard**.[^s1]

## How it works

**Landing zone.** A landing zone is the enterprise-wide container of organizational units (OUs), accounts and users that you want governed.[^s1] Through the console, setup creates a Security OU (the default "service integration" OU) and an optional Sandbox OU, enrolls accounts for enabled integrations such as AWS Config, CloudTrail or AWS Backup, and optionally creates an Identity Center directory with preconfigured groups.[^s2] In landing zone 3.3 and earlier the Security OU holds two shared accounts, Log Archive and Audit.[^s2] Control Tower deploys its resources with CloudFormation StackSets, one stack instance per account and Region, and you must not modify or delete the resources it manages outside supported methods, or the landing zone enters an unknown state.[^s2]

**Controls.** Each control is a plain-language rule of one of three kinds (*preventive*, *detective* or *proactive*) and carries one of three guidance levels (*mandatory*, *strongly recommended* or *elective*).[^s1] Mandatory preventive and detective controls are applied at setup; preventive controls are enforced through Organizations policies such as SCPs and RCPs, while detective controls run as AWS Config rules.[^s2][^s5] Preventive controls do not apply to the management account.[^s2]

**Account Factory.** Account Factory is a configurable account template, built on Service Catalog provisioned products, that creates and enrolls accounts and applies controls and policies to them automatically.[^s1]

## What changed (2025-11): landing zone 4.0

Landing zone 4.0 loosened the prescribed structure. Control Tower no longer creates or manages a Security OU; you define the structure, and the OU holding all accounts for a service integration becomes the designated Security OU.[^s3] Every integration (Config, CloudTrail, security roles, Backup) can be enabled or disabled, so a landing zone can exist without any, and drift notifications move from SNS to Amazon EventBridge in the management account.[^s3] Alongside it, AWS launched a controls-dedicated experience: more than 750 managed controls can be deployed into an existing organization without a full Control Tower deployment, and the S3 buckets and SNS notifications for the Config and CloudTrail integrations are now separate.[^s6]

## Quotas, pricing and gotchas

As listed in the Control Tower limits page (as of 2026-11): generally one operation runs at a time, with two exceptions, up to 100 control operations in progress (10 running, the rest queued) and up to 5 concurrent account provision, update or enroll operations; unmanaging accounts is one at a time.[^s4] A landing zone's organization supports up to 10,000 accounts, OUs with more than 1,000 directly nested accounts cannot be registered, and Control Tower lists a limit of 10 SCPs per OU.[^s4] For landing-zone setup quota problems AWS directs you to AWS Support.[^s4] Customizations for Control Tower (CfCT) and Account Factory for Terraform (AFT) cannot be built in several newer Regions, such as Europe (Zurich) and Asia Pacific (Taipei).[^s4]

Control Tower itself has no additional charge; you pay for the services it drives, mainly AWS Config, CloudTrail and S3, plus Service Catalog, CloudWatch, SNS and VPC where used.[^s5] AWS's us-east-1 pricing examples (as of 2026-11) use $0.003 per Config configuration item recorded, $0.001 per rule evaluation for the first 100,000, and $2.00 per 100,000 CloudTrail management events.[^s5] Its own worked example warns that ephemeral workloads producing 4,000 configuration items per account per Region per day cost about $12 a day, so detective controls in high-churn accounts are the cost to watch.[^s5]

**GovCloud.** Control Tower runs in AWS GovCloud (US-West) and (US-East), but cannot create accounts there: you create them from the commercial-Region management account via `CreateGovCloudAccount`, bring existing Audit and Log Archive accounts, and RCP controls are unavailable.[^s7] It is authorized at FedRAMP High and DoD SRG IL4/IL5 in GovCloud, and AWS's FedRAMP scope page lists it for both the US East/West and GovCloud authorizations.[^s7][^s8]

## When to use it (and when not)

Use Control Tower when you host more than a handful of accounts and want a standard, prescriptive way to vend and govern them.[^s1] If you only need guardrails on an organization you already structured, the 4.0 controls-dedicated experience lets you adopt managed controls without the full landing zone.[^s6] Teams needing custom structure can still build directly in AWS Organizations and register existing OUs and accounts later.[^s1]

## Connections

Control Tower `dependsOn` **AWS Organizations**, which supplies accounts, OUs, **service control policies** and RCPs, and it sits within AWS management and governance services as the orchestration layer of a **multi-account strategy**.[^s1] It wires workforce access through **IAM Identity Center**, is `monitoredBy` **AWS Config** for detective controls, and `integratesWith` **AWS CloudTrail** for centralized logging.[^s2][^s3] Its limits are `governedBy` **Control Tower quotas**,[^s4] it `compliesWith` **FedRAMP on AWS**,[^s8] and it is `coveredBy` the **Solutions Architect – Associate** exam, whose in-scope list includes it under Management and Governance.[^s9]

## Sources

[^s1]: Amazon Web Services, "What Is AWS Control Tower? - AWS Control Tower". https://docs.aws.amazon.com/controltower/latest/userguide/what-is-control-tower.md (accessed 2026-11-10)
[^s2]: Amazon Web Services, "How AWS Control Tower works - AWS Control Tower". https://docs.aws.amazon.com/controltower/latest/userguide/how-control-tower-works.md (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Key changes (landing zone v4.0) - AWS Control Tower". https://docs.aws.amazon.com/controltower/latest/userguide/key-changes-lz-v4.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Limitations and quotas in AWS Control Tower". https://docs.aws.amazon.com/controltower/latest/userguide/limits.md (accessed 2026-11-10)
[^s5]: Amazon Web Services, "AWS Control Tower pricing". https://aws.amazon.com/controltower/pricing/ (accessed 2026-11-10)
[^s6]: Amazon Web Services, "AWS Control Tower introduces a controls-dedicated experience" (What's New, 2025-11-21). https://aws.amazon.com/about-aws/whats-new/2025/11/aws-control-tower-controls-dedicated-experience (accessed 2026-11-10)
[^s7]: Amazon Web Services, "AWS Control Tower in AWS GovCloud (US)". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-controltower.md (accessed 2026-11-10)
[^s8]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-10)
[^s9]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-10)
