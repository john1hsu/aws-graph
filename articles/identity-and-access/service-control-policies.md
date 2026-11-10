---
id: kt:service-control-policies
type: Feature
title: Service control policies (SCPs)
aliases: [SCP, SCPs, AWS Organizations service control policies]
domain: identity-and-access
summary: >-
  An AWS Organizations policy type that sets the maximum permissions available to IAM users
  and roles (including the root user) in member accounts; SCPs act as guardrails and never
  grant access themselves.
verified: 2026-11-10
status: current
broader: [kt:aws-organizations]
related: [kt:aws-iam, kt:aws-control-tower, kt:multi-account-strategy]
relations:
  - {predicate: contrastsWith, object: kt:permissions-boundaries}
  - {predicate: contrastsWith, object: kt:resource-control-policies}
  - {predicate: governedBy, object: kt:organizations-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "Service control policies (SCPs) - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "SCP evaluation - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps_evaluation.md
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Quotas and service limits for AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.md
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "AWS Organizations now supports higher quotas for service control policies (SCPs)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/05/aws-organizations-increased-scp-quotas/
    type: official-blog
    accessed: 2026-11-10
  - id: s5
    title: "Unlock new possibilities: AWS Organizations service control policy now supports full IAM language"
    publisher: AWS Security Blog
    url: https://aws.amazon.com/blogs/security/unlock-new-possibilities-aws-organizations-service-control-policy-now-supports-full-iam-language
    type: official-blog
    accessed: 2026-11-10
  - id: s6
    title: "AWS Certified Solutions Architect - Associate (SAA-C03) Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.pdf
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# Service control policies (SCPs)

Service control policies are the AWS Organizations policy type for central permission guardrails. An SCP defines the **maximum** permissions that IAM users and roles in a member account can use; it grants nothing on its own, so administrators still need identity-based or resource-based policies to give access.[^s1] SCPs use almost the same JSON grammar as IAM policies and are available only when an organization has **all features** enabled, not in a consolidated-billing-only organization.[^s1]

## How SCPs are evaluated

SCPs attach to the organization root, to organizational units (OUs) and to individual accounts, and an account is constrained by every SCP on its path from the root.[^s1] Evaluation is deny-by-default with two rules:[^s2]

- **Allow must exist at every level.** For an action to be permitted in an account, an SCP that allows it must be attached at the root, at each OU in the direct path, and at the account itself. That is why Organizations attaches the AWS managed `FullAWSAccess` policy to every root, OU and account when SCPs are enabled.[^s2]
- **A Deny anywhere wins.** An explicit `Deny` in any SCP on the path blocks the action for every account beneath it, regardless of lower-level allows.[^s2]

The resulting effective permissions are the intersection of what SCPs and resource control policies (RCPs) allow with what the identity-based and resource-based policies allow; where a permissions boundary also applies, the boundary, the SCP and the identity policy must all allow the action.[^s1] A blocked action stays blocked even for a principal holding `AdministratorAccess`, and SCPs apply to the **root user** of member accounts too.[^s1]

## What SCPs do not touch

SCPs never affect users or roles in the **management account**, only member accounts (delegated-administrator member accounts are affected). They also never restrict **service-linked roles**, do not directly change resource-based policies, and do not apply to principals from outside the organization even when a member-account resource grants them access.[^s1] A short list of root-user tasks, such as registering for the Enterprise support plan, is also outside their reach.[^s1] The practical consequence: keep workloads out of the management account, because guardrails cannot follow them there.[^s1]

## Policy language

Since September 2025 SCPs support the full IAM policy language. Conditions and specific resource ARNs are allowed in `Allow` statements, `NotAction` works with `Allow`, `NotResource` works with both effects, and wildcards may appear at the start or middle of action strings. The change is available in commercial and GovCloud (US) Regions and existing SCPs keep working.[^s5] Common patterns include a single `Deny` with `NotResource` to build a resource perimeter, and an `Allow` conditioned on `aws:RequestedRegion` to limit services to approved Regions.[^s5]

## Quotas (as of 2026-11, global)

Organizations quotas are global and managed through Service Quotas in us-east-1.[^s3]

- Up to **10,000 SCPs** per organization.[^s3]
- Each SCP can be at most **10,240 characters**. Console saves strip whitespace outside quotes; CLI and SDK saves count every character as submitted.[^s3]
- At most **10 SCPs attached directly** to a root, OU or account, and at least 1 at all times; these attachment limits are hard limits, and inherited policies do not count against them.[^s3]

The size and per-target limits doubled on 2026-05-15 (from 5,120 characters and 5 policies), automatically for every organization in commercial, GovCloud (US) and China Regions.[^s4]

## Gotchas and good practice

- Removing `FullAWSAccess` without a replacement allow makes every action in affected member accounts fail.[^s1]
- Disabling the SCP policy type on a root detaches every SCP in it, and the old attachments are not restored when you re-enable it.[^s1]
- Do not attach an untested SCP to the root. Move accounts into a test OU a few at a time, and use IAM service last accessed data and CloudTrail to see which services they actually use.[^s1]
- **Deny lists** (keep `FullAWSAccess`, add targeted denies) are easier to maintain. **Allow lists** (replace `FullAWSAccess` with only approved services) block new AWS services by default, but overlapping allows can open more access than intended.[^s2] AWS suggests one broad allow plus targeted denies, and validating policies with IAM Access Analyzer, because `NotAction`, `NotResource` and wildcards can also match actions or resources AWS adds later.[^s5]

## When to use SCPs (and alternatives)

Use SCPs for organization-wide invariants that no account administrator should be able to override: stopping accounts from leaving the organization, protecting security tooling, or restricting Regions.[^s2][^s5] Use **permissions boundaries** when you need to cap one principal inside an account; use **RCPs**, which feed into the same effective-permission intersection, when the cap should apply to resources rather than to the principals in an account, remembering that SCPs never limit principals from outside the organization.[^s1]

## Connections

SCPs are a feature of **AWS Organizations** and bound the permissions that **AWS IAM** policies grant.[^s1] They are evaluated alongside, and **contrast with**, **permissions boundaries** and **resource control policies**, the other two ceiling-style controls.[^s1] They are **governed by** the **AWS Organizations quotas** above.[^s3] Together with **AWS Control Tower** they underpin a **multi-account strategy**: the SAA-C03 exam guide (Task 1.1, Design secure access to AWS resources) names Control Tower and SCPs as examples of a multi-account security strategy, so the topic is **covered by** the **AWS Certified Solutions Architect – Associate** exam.[^s6]

## Sources
[^s1]: Amazon Web Services, "Service control policies (SCPs) - AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html (accessed 2026-11-10)
[^s2]: Amazon Web Services, "SCP evaluation - AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps_evaluation.md (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Quotas and service limits for AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.md (accessed 2026-11-10)
[^s4]: Amazon Web Services, "AWS Organizations now supports higher quotas for service control policies (SCPs)" (2026-05-15). https://aws.amazon.com/about-aws/whats-new/2026/05/aws-organizations-increased-scp-quotas/ (accessed 2026-11-10)
[^s5]: AWS Security Blog, "Unlock new possibilities: AWS Organizations service control policy now supports full IAM language" (2025-09-19). https://aws.amazon.com/blogs/security/unlock-new-possibilities-aws-organizations-service-control-policy-now-supports-full-iam-language (accessed 2026-11-10)
[^s6]: Amazon Web Services, "AWS Certified Solutions Architect - Associate (SAA-C03) Exam Guide". https://docs.aws.amazon.com/pdfs/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.pdf (accessed 2026-11-10)
