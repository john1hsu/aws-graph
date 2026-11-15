---
id: kt:resource-control-policies
type: Feature
title: Resource control policies (RCPs)
aliases: [RCP, RCPs, AWS Organizations resource control policies]
domain: identity-and-access
summary: >-
  An AWS Organizations authorization policy type that sets the maximum permissions available
  on resources in member accounts, for any caller, including principals outside the organization;
  RCPs only restrict and never grant access.
verified: 2026-11-15
status: current
broader: [kt:aws-organizations]
related: [kt:aws-iam, kt:amazon-s3, kt:aws-sts]
relations:
  - {predicate: contrastsWith, object: kt:service-control-policies}
  - {predicate: governedBy, object: kt:organizations-quotas}
  - {predicate: implements, object: kt:data-perimeter}
sources:
  - id: s1
    title: "Resource control policies (RCPs) - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_rcps.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "RCP evaluation - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_rcps_evaluation.html
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "RCP syntax - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_rcps_syntax.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "Quotas and service limits for AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "Introducing resource control policies (RCPs) to centrally restrict access to AWS resources"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2024/11/resource-control-policies-restrict-access-aws-resources
    type: official-blog
    accessed: 2026-11-15
  - id: s6
    title: "Effectively implementing resource control policies in a multi-account environment"
    publisher: AWS Security Blog
    url: https://aws.amazon.com/blogs/security/effectively-implementing-resource-controls-policies-in-a-multi-account-environment
    type: official-blog
    accessed: 2026-11-15
created: 2026-11-15
---

# Resource control policies (RCPs)

Resource control policies are an AWS Organizations policy type that puts a ceiling on the permissions available **on resources** in member accounts. Like SCPs they grant nothing: a principal still needs an identity-based or resource-based policy to get access, and effective permissions are the intersection of what RCPs, SCPs and those IAM policies allow.[^s1] RCPs require an organization with **all features** enabled.[^s1] AWS launched them on 2024-11-13 in all commercial Regions, initially covering Amazon S3, AWS STS, AWS KMS, Amazon SQS and AWS Secrets Manager, to let teams centrally restrict external access regardless of individual resource policies.[^s5]

## How RCPs are evaluated

RCPs attach to the organization root, to OUs and to accounts. The policies that apply to a request are those on the path to the account that **owns the resource being accessed**, and a `Deny` in any of them blocks the action even if the resource owner's own resource policy allows full access.[^s1][^s2] The key difference from SCPs is who is constrained: RCPs act on resources, not callers. They apply whether or not the caller belongs to the organization, including the root user, so a bucket in a member account that is shared with an outside account remains bound by its owner's RCPs.[^s1] Which resource type an action touches follows the Service Authorization Reference; for `s3:GetObject` it is the object, so the bucket owner's RCPs apply.[^s1]

When the policy type is enabled, Organizations attaches the AWS managed **`RCPFullAWSAccess`** policy to the root, every OU and every account. It lets everything pass RCP evaluation, so existing access keeps working, and it **cannot be detached**; you restrict access by attaching additional deny policies alongside it.[^s2][^s3]

## Policy language

The grammar is deliberately narrower than SCPs. Customer-managed RCPs must use `"Effect": "Deny"` (`Allow` is reserved for `RCPFullAWSAccess`), every statement needs `Principal` set to `"*"`, and `NotPrincipal` and `NotAction` are not supported. A bare `"Action": "*"` is not permitted either: you must name a service prefix, although wildcards such as `s3:*` work. Callers are targeted with `Condition` keys, which are allowed in deny statements; the docs' example denies S3 requests that do not use TLS via `aws:SecureTransport`.[^s3] Only services on the supported list can be controlled; it has grown well beyond the launch five to include DynamoDB, ECR, EFS, CloudWatch Logs, SNS and many others (as of 2026-11).[^s1]

## Quotas (as of 2026-11, global)

- Up to **2,000 RCPs** per organization.[^s4]
- Each RCP can be at most **5,120 characters**, and every character, including whitespace, counts.[^s4][^s3]
- At most **5 RCPs attached directly** to a root, OU or account. `RCPFullAWSAccess` counts toward that 5, which leaves room for four of your own per target; policy attachment limits are hard limits.[^s4]

## What RCPs do not touch, and gotchas

- They do not affect resources in the **management account**; delegated-administrator member accounts are affected.[^s1]
- They do not restrict **service-linked roles**, do not apply to **AWS managed KMS keys**, and do not affect `kms:RetireGrant`.[^s1]
- A broad deny can break AWS services acting on your behalf. AWS's guidance is to exempt service principals with `aws:PrincipalIsAWSService` where service roles are in scope, and to carve out partners with keys such as `aws:PrincipalAccount` or an exception tag, using an SCP to restrict who can change that tag.[^s6]
- Roll out gradually: test accounts first, then low OUs, never straight to the root, and watch CloudTrail for Access Denied errors.[^s1] Use IAM Access Analyzer and CloudTrail queries to find existing external access before enforcing.[^s6]

## When to use RCPs (and alternatives)

Use an **SCP** to cap what principals inside your member accounts can do; use an **RCP** to cap what anyone, especially identities outside the organization, can do to your resources.[^s1] Typical RCP objectives are a data-perimeter rule that only trusted identities reach company data, protection against the cross-service confused-deputy problem, and uniform controls such as encryption in transit across every bucket.[^s6] RCPs are coarse-grained guardrails and do not replace least-privilege identity and resource policies.[^s6]

## Connections

RCPs are a feature of **AWS Organizations** and a ceiling on the permissions granted through **AWS IAM**.[^s1] They **contrast with** **service control policies**: SCPs bound principals, RCPs bound resources, and both feed the same effective-permission intersection.[^s1] They are **governed by** the **AWS Organizations quotas** listed above.[^s4] Because they can block outside identities from organization resources, they **implement** the resource side of a **data perimeter**, with **Amazon S3** buckets and **AWS STS** role assumption among the most common targets.[^s5][^s6]

## Sources
[^s1]: Amazon Web Services, "Resource control policies (RCPs) - AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_rcps.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "RCP evaluation - AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_rcps_evaluation.html (accessed 2026-11-15)
[^s3]: Amazon Web Services, "RCP syntax - AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_rcps_syntax.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "Quotas and service limits for AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, What's New, "Introducing resource control policies (RCPs) to centrally restrict access to AWS resources" (2024-11-13). https://aws.amazon.com/about-aws/whats-new/2024/11/resource-control-policies-restrict-access-aws-resources (accessed 2026-11-15)
[^s6]: AWS Security Blog, "Effectively implementing resource control policies in a multi-account environment" (2025-03-26). https://aws.amazon.com/blogs/security/effectively-implementing-resource-controls-policies-in-a-multi-account-environment (accessed 2026-11-15)
