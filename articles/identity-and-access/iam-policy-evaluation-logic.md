---
id: kt:iam-policy-evaluation-logic
type: Concept
title: IAM policy evaluation logic
aliases: [policy evaluation logic, IAM policy evaluation, AWS policy evaluation logic, enforcement code logic]
domain: identity-and-access
summary: >-
  The rules AWS applies to every authenticated request to decide allow or deny: start from an
  implicit deny, let any applicable explicit Deny win, require an Allow from each guardrail policy
  type (RCPs, SCPs, permissions boundaries, session policies), and combine identity-based and
  resource-based grants differently for same-account and cross-account requests.
verified: 2026-11-12
status: concept
broader: [kt:aws-iam]
related: [kt:permissions-boundaries, kt:iam-roles, kt:aws-sts, kt:aws-kms, kt:iam-policy-simulator]
relations:
  - {predicate: dependsOn, object: kt:service-control-policies}
  - {predicate: dependsOn, object: kt:resource-control-policies}
  - {predicate: integratesWith, object: kt:aws-organizations}
sources:
  - id: s1
    title: "Policy evaluation logic - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/AccessPolicyLanguage_EvaluationLogic.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "How AWS enforcement code logic evaluates requests to allow or deny access - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/jp_ja/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Policy evaluation for requests within a single account - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-basics.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "Cross-account policy evaluation logic - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic-cross-account.html
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "Service control policies (SCPs) - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html
    type: official-docs
    accessed: 2026-11-12
  - id: s6
    title: "IAM policy testing with the IAM policy simulator - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UsingPolicySimulatorGuide/iam-policy-simulator-guide.html
    type: official-docs
    accessed: 2026-11-12
created: 2026-11-12
---

# IAM policy evaluation logic

IAM policy evaluation logic is the decision procedure AWS runs on every request to work out whether it is allowed. AWS first authenticates the principal (a few services, such as Amazon S3, accept some anonymous requests), then builds a **request context** from the request to find out which policies apply, and finally evaluates every applicable policy type to reach a single allow or deny.[^s1] The policy types in play are identity-based policies, resource-based policies, permissions boundaries, session policies, and the two AWS Organizations guardrails: service control policies (SCPs) and resource control policies (RCPs).[^s1][^s2]

## The three ground rules

1. **Default deny.** Every request starts out implicitly denied; the only exception is the account root user, which has full access unless something else restricts it.[^s2]
2. **Explicit deny wins.** If any applicable policy of any type contains a `Deny` that matches the request, the answer is deny, whatever else allows it.[^s1][^s2]
3. **Something must allow it.** A request succeeds only if a policy explicitly allows it and no guardrail in its path withholds an allow.[^s2]

## The enforcement order

AWS documents the enforcement code as a sequence of checks, each of which can end evaluation:[^s2]

- **Deny pass.** All applicable SCPs, RCPs, resource-based, identity-based, boundary and session policies are scanned for a matching explicit `Deny`; one hit returns a final deny.[^s2]
- **RCPs.** RCPs constrain resources in the accounts they are attached to; if none allows the action, the request is denied. When RCPs are enabled, the AWS managed `RCPFullAWSAccess` policy is attached to the root, every OU and every account and cannot be detached, so an allow is always present unless you add denies.[^s2]
- **SCPs.** SCPs constrain principals in the accounts they are attached to and, likewise, must allow the action.[^s2]
- **Resource-based policies.** For most resources an explicit allow in either the resource-based or the identity-based policy is enough. Role trust policies and KMS key policies are exceptions: they must explicitly allow the principal themselves.[^s2] Who the resource policy names also matters, because a grant to a role ARN stays subject to the role's boundary and session policy, while a grant to a specific role session or IAM user ARN is not cut back by those implicit denies.[^s2]
- **Identity-based policies.** If none of the principal's own or group policies allows the action, it is implicitly denied.[^s2]
- **Permissions boundaries**, then **session policies** for role and federated sessions, must each allow the action if present; a session with no session policy passed proceeds to allow.[^s2]

## Same account vs. cross-account

Within one account, identity-based and resource-based permissions form a **union**: if either one allows the action and nothing explicitly denies it, the request is allowed.[^s1][^s3] AWS's worked example has a user whose policy allows `s3:*` on his own bucket but explicitly denies any bucket matching `*log*`; writes to his bucket succeed, and writes to a "-logs" bucket fail despite the broad allow.[^s3] Boundaries, SCPs and RCPs instead **intersect** with those grants: adding a boundary can only shrink what an identity can do.[^s1]

Across accounts the union disappears. AWS evaluates the request twice, once in the trusted account (the principal's identity-based policy plus anything limiting it, such as a boundary) and once in the trusting account (the resource-based policy plus anything limiting it), and allows the request only if **both** evaluations return allow.[^s4] An explicit deny in either account blocks it.[^s4] For roles, the resource-based policy is the role trust policy, which decides who may assume the role, while the role's permissions policy decides what the temporary credentials can then reach.[^s4]

## Gotchas

- SCPs never grant access, do not apply to users or roles in the **management account**, never restrict **service-linked roles**, and do not limit principals from outside the organization that reach your resources through resource-based policies. They do apply to the root user of member accounts.[^s5]
- Because a resource-based grant to an IAM user is not limited by implicit denies in that user's boundary, a guardrail that must hold everywhere should be written as an explicit deny.[^s2]
- To test a design, the **IAM policy simulator** returns allow or deny for an action and resource and, for allows and explicit denies, names the deciding policy. It can include identity policies, one boundary, resource-based policies and SCPs, but it does not support RCPs, and AWS warns results can differ from live behaviour for VPC endpoint policies, role chaining and multiple resource policies on one resource.[^s6]

## Connections

This logic is the core of **AWS IAM** and is where **permissions boundaries**, **IAM roles** and session policies issued by **AWS STS** interact.[^s1][^s2] It **depends on** the AWS Organizations guardrails, **service control policies** and **resource control policies**, which feed the deny and allow checks for whole accounts, and so **integrates with** **AWS Organizations**.[^s2][^s5] Key policies in **AWS KMS** are one of the documented exceptions where the resource policy itself must allow the principal.[^s2] It is closely related to the **IAM policy simulator**, which replays it for test requests.[^s6]

## Sources
[^s1]: Amazon Web Services, "Policy evaluation logic - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/AccessPolicyLanguage_EvaluationLogic.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "How AWS enforcement code logic evaluates requests to allow or deny access - AWS Identity and Access Management". https://docs.aws.amazon.com/jp_ja/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "Policy evaluation for requests within a single account - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-basics.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, "Cross-account policy evaluation logic - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic-cross-account.html (accessed 2026-11-12)
[^s5]: Amazon Web Services, "Service control policies (SCPs) - AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html (accessed 2026-11-12)
[^s6]: Amazon Web Services, "IAM policy testing with the IAM policy simulator - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UsingPolicySimulatorGuide/iam-policy-simulator-guide.html (accessed 2026-11-12)
