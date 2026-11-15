---
id: kt:attribute-based-access-control
type: Pattern
title: Attribute-based access control (ABAC) on AWS
aliases: [ABAC, attribute-based access control, tag-based access control, ABAC authorization]
domain: identity-and-access
summary: >-
  An authorization strategy in which AWS IAM policies grant or deny access by comparing attributes
  (tags) on the calling principal or session with tags on the target resource and in the request,
  so a few policies can cover resources and teams that change over time.
verified: 2026-11-15
status: concept
broader: [kt:aws-iam]
related: [kt:iam-identity-center, kt:iam-policy-evaluation-logic]
relations:
  - {predicate: dependsOn, object: kt:tagging-strategy}
  - {predicate: securedBy, object: kt:service-control-policies}
  - {predicate: governedBy, object: kt:iam-quotas}
  - {predicate: contrastsWith, object: kt:role-based-access-control}
sources:
  - id: s1
    title: "Define permissions based on attributes with ABAC authorization - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_attribute-based-access-control.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "IAM tutorial: Define permissions to access AWS resources based on tags"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_attribute-based-access-control
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Pass session tags in AWS STS - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_session-tags.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "Attribute-based access control - AWS IAM Identity Center"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/singlesignon/latest/userguide/abac.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "ABAC for individual resources - Best Practices for Tagging AWS Resources"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/abac-for-individual-resources.html
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "NIST SP 800-162: Guide to Attribute Based Access Control (ABAC) Definition and Considerations"
    publisher: National Institute of Standards and Technology
    url: https://nvlpubs.nist.gov/nistpubs/specialpublications/NIST.SP.800-162.pdf
    type: standards-body
    accessed: 2026-11-15
created: 2026-11-15
---

# Attribute-based access control (ABAC) on AWS

Attribute-based access control is a general authorization model: NIST SP 800-162 defines it as granting or denying a subject's operations on an object based on subject attributes, object attributes, environment conditions and a policy written in terms of them.[^s6] In AWS the attributes are **tags**. You tag IAM principals (users and roles) and AWS resources, then write one policy, or a small set, that allows an action when the principal's tag matches the resource's tag.[^s1] AWS positions ABAC for environments that are growing quickly or where per-resource policy management has become unwieldy.[^s1]

## How it works

The central pattern is a condition that compares a resource tag with a principal tag through a policy variable, for example `aws:ResourceTag/access-project` equal to `${aws:PrincipalTag/access-project}`.[^s2] Two more global condition keys govern tagging itself: `aws:RequestTag/<key>` checks the tags passed in a create or tag request, so the IAM tutorial only lets a user create a secret when the requested project, team and cost-center tags equal the user's own; and `aws:TagKeys` restricts which tag keys a request may carry.[^s2] Because new resources are born with the creator's tags, the creator and teammates can use them immediately without an administrator editing any policy.[^s2]

Principal attributes need not be static IAM tags. A SAML or OIDC identity provider can pass **session tags** when a role is assumed, letting corporate-directory attributes drive the same policies.[^s1] Session tags are accepted by `AssumeRole`, `AssumeRoleWithSAML`, `AssumeRoleWithWebIdentity` and `GetFederationToken`; the role's trust policy must allow `sts:TagSession`, and tag keys can be marked transitive so they survive role chaining.[^s3] IAM Identity Center builds on this with **attributes for access control**: user attributes from any of its identity sources are sent into the session, so one permission set can serve many teams and a directory change (say, a user moving teams) changes access without touching AWS permissions.[^s4]

## Quotas and gotchas

- **Session tag limits (as of 2026-11, per the AWS STS documentation; IAM is a global service):** at most 50 session tags per session, keys up to 128 characters and values up to 256 characters; session tags and session policies also count toward the session token size limit, and the deprecated `PackedPolicySize` is replaced by `SessionTokenUtilization` in the response.[^s3]
- **Single-valued tags only.** IAM and STS do not support multi-valued tags, so a principal cannot hold two values for `access-project`; the tutorial falls back to listing role ARNs for a person on two projects.[^s2][^s3]
- **Uneven service support.** Not every resource type can be tagged, and not every action supports resource-tag conditions; in Secrets Manager, for instance, `ListSecrets` and `GetRandomPassword` need a separate statement without tag conditions.[^s2] The whitepaper advises checking each service's support (the "AWS services that work with IAM" table) before relying on ABAC.[^s5]
- **Tags become security attributes.** Anyone able to change an authorization tag can change who has access, so restrict tag changes with explicit denies, permissions boundaries or organization-wide service control policies.[^s5][^s2]
- **Broader policies win.** ABAC policies only limit access when they are the only policies in play; attaching something like `AdministratorAccess` to the same principal overrides the intended scoping.[^s2]
- **Mis-tagged resources are silently inaccessible**, so consistent tagging at creation is a precondition, not an afterthought.[^s5]

## When to use it (and when not)

AWS contrasts ABAC with traditional role-based access control, where each job function gets a policy that names its resources and must be edited whenever a resource is added.[^s1] ABAC reduces the number of policies, lets permissions scale as teams create resources, and absorbs new projects by adding a tagged role rather than rewriting policy.[^s1] NIST frames the same trade-off: roles mirror fairly static positions, and covering context-specific needs with roles alone leads to "role explosion."[^s6] AWS's tagging guidance treats ABAC as a complement to role-based models in multi-account setups, most useful when several teams share one account.[^s5] Where accounts already separate teams cleanly, or the services involved lack tag-condition support, plain role-based policies remain simpler to audit.[^s5]

## Connections

ABAC is a way of writing **AWS IAM** policies, so it is evaluated by the normal **IAM policy evaluation logic**, including the rule that explicit denies override allows.[^s2] It depends on a disciplined **tagging strategy**, and it is protected by **service control policies** that stop tag tampering across an organization.[^s5] **IAM Identity Center** is the usual way to feed directory attributes into sessions, while the session tag size and count limits sit in the **IAM and STS quotas**.[^s4][^s3] It contrasts with **role-based access control**, which AWS and NIST both describe as the model ABAC generalizes or complements.[^s1][^s6]

## Sources
[^s1]: Amazon Web Services, "Define permissions based on attributes with ABAC authorization". https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_attribute-based-access-control.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "IAM tutorial: Define permissions to access AWS resources based on tags". https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_attribute-based-access-control (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Pass session tags in AWS STS". https://docs.aws.amazon.com/IAM/latest/UserGuide/id_session-tags.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "Attribute-based access control - AWS IAM Identity Center". https://docs.aws.amazon.com/singlesignon/latest/userguide/abac.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "ABAC for individual resources - Best Practices for Tagging AWS Resources". https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/abac-for-individual-resources.html (accessed 2026-11-15)
[^s6]: NIST, "SP 800-162: Guide to Attribute Based Access Control (ABAC) Definition and Considerations". https://nvlpubs.nist.gov/nistpubs/specialpublications/NIST.SP.800-162.pdf (accessed 2026-11-15)
