---
id: kt:iam-access-analyzer
type: Service
title: AWS IAM Access Analyzer
aliases: [IAM Access Analyzer, Access Analyzer, AWS Identity and Access Management Access Analyzer]
domain: identity-and-access
summary: >-
  An AWS IAM capability that uses automated reasoning to find resources shared outside your
  account or organization, show which internal principals can reach critical resources, flag
  unused roles, keys and permissions, and validate or generate least-privilege policies.
verified: 2026-11-18
status: current
broader: [kt:aws-iam, kt:aws-security-services]
related: [kt:iam-policy-evaluation-logic, kt:resource-control-policies]
relations:
  - {predicate: dependsOn, object: kt:aws-cloudtrail}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "Using AWS Identity and Access Management Access Analyzer"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM//latest/UserGuide/what-is-access-analyzer.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "AWS IAM Access Analyzer pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/iam/access-analyzer/pricing
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "IAM Access Analyzer quotas - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-quotas.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Verify internal access to critical AWS resources with new IAM Access Analyzer capabilities"
    publisher: AWS News Blog
    url: https://aws.amazon.com/blogs/aws/verify-internal-access-to-critical-aws-resources-with-new-iam-access-analyzer-capabilities
    type: official-blog
    accessed: 2026-11-18
  - id: s5
    title: "Refine unused access using IAM Access Analyzer recommendations"
    publisher: AWS Security Blog
    url: https://aws.amazon.com/blogs/security/refine-unused-access-using-iam-access-analyzer-recommendations
    type: official-blog
    accessed: 2026-11-18
  - id: s6
    title: "IAM Access Analyzer supports additional analysis findings and checks in AWS GovCloud (US) Regions"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/07/iam-access-analyzer-findings-checks-govcloud
    type: official-blog
    accessed: 2026-11-18
created: 2026-11-18
---

# AWS IAM Access Analyzer

AWS IAM Access Analyzer is the part of AWS Identity and Access Management that helps you find unintended access, unused permissions and policy mistakes, and that can generate least-privilege policies from recorded activity. Its features fall into three analyzer types (external access, internal access and unused access) plus three policy tools: validation, custom policy checks and policy generation.[^s1] Rather than pattern-matching policy text, it uses logic-based (automated) reasoning that evaluates several policies together to work out effective access.[^s1][^s4]

## How it works

**External access analyzer.** When you create one, you pick an AWS account or a whole organization as its *zone of trust*. The analyzer reads resource-based policies and raises a finding for each supported resource that grants access to a principal outside that zone, whether another account, a federated user or an anonymous (public) caller. Each finding names the resource, the external principal and the permissions.[^s1] Supported types include S3 general purpose and directory buckets, IAM role trust policies, KMS keys, Lambda functions and layers, SQS queues, SNS topics, Secrets Manager secrets, EBS and RDS snapshots, ECR repositories, EFS file systems, and DynamoDB tables and streams. You can also preview how a policy change would affect public or cross-account access before deploying it.[^s1]

**Internal access analyzer.** Launched in June 2025, it answers the reverse question: which IAM users and roles *inside* your organization or account can reach selected business-critical resources (S3 buckets, DynamoDB tables and streams, RDS snapshots).[^s1][^s4] Its evaluation includes service control policies and resource control policies, it re-evaluates daily, and findings can be routed through Amazon EventBridge to notify owning teams.[^s4]

**Unused access analyzer.** It continuously monitors IAM roles and users and flags unused roles, unused access keys and passwords, and, for active principals, services and actions that have not been used, based on last-accessed data.[^s1] For unused-permission findings it can generate recommendations: detach a policy that was not used at all, or replace it with one keeping only the actions that were used. Unused roles, keys and passwords get no policy recommendation; you delete or review them instead.[^s5]

**Policy tools.** Policy validation checks policy grammar and AWS best practices whenever you write a policy through the console JSON editor, CLI or API, and returns security warnings, errors, general warnings and suggestions. Custom policy checks test a policy against your own standards, for example whether an update grants new access or allows specific sensitive actions, which suits CI/CD pipelines. Policy generation reads AWS CloudTrail activity for a user or role over a date range and drafts a matching policy.[^s1]

## Quotas, pricing and gotchas

- **Regional scope.** External access analyzers only analyze resources in their own Region, so you need one in every Region where you use supported resources. Unused access findings do not vary by Region, so one analyzer is enough.[^s1]
- **Latency.** Policy changes are usually analyzed within about 30 minutes; some changes, such as RCP updates, may wait for the periodic scan within 24 hours. A rescan can be started on demand.[^s1]
- **Quotas (as of 2026-11).** One account-level analyzer per analyzer type per account per Region; organization-level external or unused analyzers default to 5 per type per account per Region (adjustable to 20); one organization-level internal access analyzer per organization per Region. Archive rules default to 100 per analyzer (adjustable to 1,000). Policy generation is capped at a 90-day CloudTrail range, 25 GB of CloudTrail data and 50 generations per day in most Regions (5 in a few opt-in Regions).[^s3]
- **Pricing (as of 2026-11, AWS's us-east-1 examples).** External access analysis, policy validation and policy generation cost nothing extra. Unused access is $0.20 per IAM role or user analyzed per month; internal access is $9.00 per monitored resource per month; custom policy checks are $0.0020 per API call.[^s2] Each analyzer is billed separately and service-linked roles are excluded, so duplicate unused access analyzers across Regions multiply cost.[^s1]
- **False positives.** Roles used rarely, such as monthly backup, deployment-pipeline or disaster-recovery roles, may be flagged as unused; review before deleting, and archive findings you accept.[^s5]

## When to use it (and when not)

Use the external access analyzer as a cheap always-on guardrail against public or cross-account sharing, and the unused access analyzer to trim permissions toward least privilege across an organization.[^s1][^s5] Internal access analysis is priced per resource, so reserve it for the small set of data stores whose insider access you must prove.[^s2][^s4] It does not replace understanding policy evaluation: findings describe what policies allow, not what callers actually did, which CloudTrail records.[^s1]

## Connections

Access Analyzer is a capability of **AWS IAM**, one of the identity tools in AWS's broader **security services** portfolio.[^s1] It reasons over the same rules described in **IAM policy evaluation logic**, including **resource control policies** and SCPs.[^s4] Policy generation **depends on AWS CloudTrail** activity.[^s1] Organization-wide analyzers use **AWS Organizations** as the zone of trust, and new findings can be pushed to development teams through **Amazon EventBridge**.[^s1][^s4] External access analysis is **scoped to AWS Regions**.[^s1] Since July 2025 unused access, internal access and custom policy checks are also available in the AWS GovCloud (US) Regions.[^s6]

## Sources
[^s1]: Amazon Web Services, "Using AWS Identity and Access Management Access Analyzer". https://docs.aws.amazon.com/IAM//latest/UserGuide/what-is-access-analyzer.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "AWS IAM Access Analyzer pricing". https://aws.amazon.com/iam/access-analyzer/pricing (accessed 2026-11-18)
[^s3]: Amazon Web Services, "IAM Access Analyzer quotas". https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-quotas.html (accessed 2026-11-18)
[^s4]: AWS News Blog, "Verify internal access to critical AWS resources with new IAM Access Analyzer capabilities" (2025-06-17). https://aws.amazon.com/blogs/aws/verify-internal-access-to-critical-aws-resources-with-new-iam-access-analyzer-capabilities (accessed 2026-11-18)
[^s5]: AWS Security Blog, "Refine unused access using IAM Access Analyzer recommendations" (2024-09-18). https://aws.amazon.com/blogs/security/refine-unused-access-using-iam-access-analyzer-recommendations (accessed 2026-11-18)
[^s6]: Amazon Web Services, "IAM Access Analyzer supports additional analysis findings and checks in AWS GovCloud (US) Regions" (2025-07-22). https://aws.amazon.com/about-aws/whats-new/2025/07/iam-access-analyzer-findings-checks-govcloud (accessed 2026-11-18)
