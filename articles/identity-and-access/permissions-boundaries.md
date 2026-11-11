---
id: kt:permissions-boundaries
type: Feature
title: IAM permissions boundaries
aliases: [permissions boundary, permission boundaries, IAM permission boundary]
domain: identity-and-access
summary: >-
  An advanced AWS IAM feature that attaches a managed policy to an IAM user or role as the
  maximum set of permissions its identity-based policies can grant; it limits access but never
  grants any on its own, and is mainly used to delegate safe role creation to developers.
verified: 2026-11-11
status: current
broader: [kt:aws-iam]
related: [kt:iam-policy-evaluation-logic, kt:iam-roles, kt:aws-sts]
relations:
  - {predicate: contrastsWith, object: kt:service-control-policies}
  - {predicate: governedBy, object: kt:iam-quotas}
sources:
  - id: s1
    title: "Permissions boundaries for IAM entities - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/access_policies_boundaries.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "When and where to use IAM permissions boundaries"
    publisher: AWS Security Blog
    url: https://aws.amazon.com/blogs/security/when-and-where-to-use-iam-permissions-boundaries
    type: official-blog
    accessed: 2026-11-11
  - id: s3
    title: "Delegate permission management to developers by using IAM permissions boundaries"
    publisher: AWS Security Blog
    url: https://aws.amazon.com/blogs/security/delegate-permission-management-to-developers-using-iam-permissions-boundaries
    type: official-blog
    accessed: 2026-11-11
  - id: s4
    title: "IAM and AWS STS quotas - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.md
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "AWS IAM now provides higher maximum quotas for roles, role trust policies, instance profiles, managed policies, and identity providers"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/05/aws-iam-increased-quotas/
    type: official-blog
    accessed: 2026-11-11
created: 2026-11-11
---

# IAM permissions boundaries

A permissions boundary is an advanced AWS IAM feature: you attach an AWS managed or customer managed policy to an IAM **user** or **role**, not as a permissions policy but as the ceiling on what that entity's identity-based policies are allowed to grant.[^s1] The entity can perform only actions that both its identity-based policies and its boundary allow. A boundary therefore never grants access by itself: an entity whose boundary allows `s3:*`, `ec2:*` and `cloudwatch:*` but whose permissions policy allows only `iam:CreateUser` can do nothing, because the two sets do not overlap.[^s1] AWS describes boundaries as a preventative guardrail rather than a detective control.[^s2]

## How boundaries are evaluated

For identity-based access, the effective permissions are the **intersection** of the identity-based policies and the boundary, and an explicit `Deny` in either one overrides any allow.[^s1] Other policy types stack on the same intersection: when an AWS Organizations service control policy applies, the SCP, the boundary and the identity-based policy must all allow a request; when a role or federated session is created with a session policy, the session policy must allow it too.[^s1]

Resource-based policies are where the nuance sits, because a boundary restricts through an **implicit** deny. Within one account:[^s1]

- A resource policy that names an **IAM user's ARN** grants access that the user's boundary does not limit.[^s1]
- A resource policy that names an **IAM role's ARN** is limited by the role's boundary (and by any session policy).[^s1]
- A resource policy that names a **role session ARN** grants access directly, unaffected by the identity policy, boundary or session policy.[^s1]

The practical consequence is that a boundary alone does not guarantee a resource is unreachable. AWS's own example: a user whose boundary covers no Secrets Manager actions can still read a secret whose resource policy allows him, because the boundary contains no explicit deny for it.[^s1] Put explicit denies in the boundary for anything that must never be reachable. Also avoid `Deny` with `NotPrincipal` in resource policies: it always denies any principal that has a boundary attached, and AWS recommends `ArnNotEquals` on `aws:PrincipalArn` instead.[^s1] The rules above are for same-account requests.[^s1]

## Delegating IAM safely

The main use case is letting developers create IAM roles for their own workloads, such as an EC2 instance writing logs or a Lambda function processing data, without handing them unrestricted IAM.[^s2] The pattern has three parts:[^s1][^s3]

1. The central team writes a boundary policy that describes the most any workload role may do.[^s2]
2. The developer's own policy allows actions such as `iam:CreateRole`, `iam:AttachRolePolicy` and `iam:PutUserPermissionsBoundary` only when the `iam:PermissionsBoundary` condition key equals that boundary's ARN, so a create call without the boundary fails.[^s1][^s3]
3. The developer is denied the actions that would weaken the guardrail: deleting a user or role boundary, and creating, deleting or switching versions of the boundary policy itself.[^s1][^s3]

A naming or path convention (for example a common role-name prefix) scopes which roles and policies the developer may touch and which roles they may pass to services with `iam:PassRole`, and makes delegated roles easy to audit.[^s3] AWS also advises that delegation trusts the delegate with every service the boundary reaches, so keep boundaries as narrow as the use case allows.[^s1]

## Quotas and gotchas (as of 2026-11, global)

- A boundary is a managed policy, so it is subject to the managed policy size limit of **6,144 characters** (white space not counted), which cannot be increased.[^s4]
- Defaults for attached managed policies are **20 per role** (adjustable to 25) and **10 per user** (adjustable to 20); increases up to those maximums are approved automatically through Service Quotas in us-east-1.[^s4] The role maximum rose from 20 to 25 in May 2026.[^s5]
- Attach boundaries to the **roles developers create**, not to the developers themselves: developers need a large policy space, and copying a developer's whole policy into each workload boundary wastes it. Group boundaries by use case (automation, analytics) instead.[^s2]

## When to use it (and alternatives)

Use a boundary when one principal, or a class of delegated roles, needs a cap inside an account.[^s2] For coarse guardrails that should apply to every principal in an account, AWS recommends **service control policies**, because one SCP covers the whole account while a boundary must be attached principal by principal.[^s2] Explicit denies in identity-based policies are the simpler choice when you are not delegating policy authorship.[^s2]

## Connections

Permissions boundaries are a feature of **AWS IAM** and a core input to **IAM policy evaluation logic**, where they intersect with identity-based, resource-based and session policies.[^s1] They apply to **IAM roles** (and users), and session policies passed through **AWS STS** add a further intersection on top.[^s1] They **contrast with** **service control policies**: both are ceilings that grant nothing, but SCPs bound whole accounts from AWS Organizations while boundaries bound individual principals.[^s1][^s2] Their size and attachment counts are **governed by** the **IAM quotas**.[^s4]

## Sources
[^s1]: Amazon Web Services, "Permissions boundaries for IAM entities - AWS Identity and Access Management". https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/access_policies_boundaries.html (accessed 2026-11-11)
[^s2]: AWS Security Blog, "When and where to use IAM permissions boundaries" (2022-06-01). https://aws.amazon.com/blogs/security/when-and-where-to-use-iam-permissions-boundaries (accessed 2026-11-11)
[^s3]: AWS Security Blog, "Delegate permission management to developers by using IAM permissions boundaries" (2018-07-13). https://aws.amazon.com/blogs/security/delegate-permission-management-to-developers-using-iam-permissions-boundaries (accessed 2026-11-11)
[^s4]: Amazon Web Services, "IAM and AWS STS quotas - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.md (accessed 2026-11-11)
[^s5]: Amazon Web Services, "AWS IAM now provides higher maximum quotas for roles, role trust policies, instance profiles, managed policies, and identity providers" (2026-05-05). https://aws.amazon.com/about-aws/whats-new/2026/05/aws-iam-increased-quotas/ (accessed 2026-11-11)
