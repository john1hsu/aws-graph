---
id: kt:iam-roles
type: Resource
title: IAM roles
aliases: [IAM role, AWS IAM role]
domain: identity-and-access
summary: >-
  An IAM identity with a trust policy and permissions policies that people, applications,
  AWS services or federated users assume to get temporary security credentials, instead of
  holding long-term passwords or access keys.
verified: 2026-11-14
status: current
broader: [kt:aws-iam]
related: [kt:iam-identity-center, kt:permissions-boundaries, kt:iam-policy-evaluation-logic, kt:multi-account-strategy]
relations:
  - {predicate: dependsOn, object: kt:aws-sts}
  - {predicate: governedBy, object: kt:iam-quotas}
  - {predicate: contrastsWith, object: kt:iam-users}
  - {predicate: integratesWith, object: kt:amazon-ec2}
sources:
  - id: s1
    title: "IAM roles - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "Methods to assume a role - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "IAM and AWS STS quotas - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "The confused deputy problem - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "AWS Identity and Access Management streamlines assignment of IAM roles to workforce users with account access manager (What's New with AWS)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/08/aws-iam-aam/
    type: official-blog
    accessed: 2026-11-14
created: 2026-11-14
---

# IAM roles

An IAM role is an identity you create in an AWS account and give specific permissions. Like an IAM user, it carries permissions policies that decide what it can do; unlike a user, it is not tied to one person and has no long-term password or access keys. Whoever assumes the role receives temporary security credentials for a role session.[^s1] Roles can be assumed by IAM users in the same or another account, by other roles, by AWS service principals (for example code running on Amazon EC2 or AWS Lambda, or features such as S3 replication), and by external users authenticated through a SAML 2.0 or OpenID Connect identity provider.[^s1] No roles exist in a new account by default; services add service-linked roles as you start using them.[^s1]

## How it works

Every role has two halves. The **trust policy** is a required resource-based policy on the role that names the principals allowed to assume it (users, roles, accounts or services); it cannot use a wildcard as part of an ARN in its Principal element. The **permissions policies** are identity-based policies that define which actions and resources the session may use.[^s1] For cross-account delegation, the caller also needs an identity-based permission to assume the role; while acting in the role the caller gives up its own permissions and gets the role's instead.[^s1]

Roles are assumed through console role switching, the AWS CLI, or the AWS STS operations AssumeRole, AssumeRoleWithSAML and AssumeRoleWithWebIdentity. Sessions last 15 minutes minimum and default to 1 hour; the upper bound is the role's maximum session duration setting, which can be 1 to 12 hours.[^s2] Using one role's credentials to assume another is **role chaining**, and it caps the session at one hour whatever the roles' configured maximums; requesting a longer DurationSeconds in a chain makes the call fail.[^s1][^s2] EC2 instance-profile credentials are not bound by the role's maximum session duration, and services such as EC2, ECS, EKS and Lambda refresh credentials automatically.[^s2]

AWS distinguishes **service roles**, which a service assumes on your behalf and which administrators can create and edit, from **service-linked roles**, which are owned by the service: administrators can view but not edit their permissions, and the related resources must be deleted before the role can be.[^s1] A permissions boundary cannot be applied to a service-linked role.[^s1]

## Quotas and gotchas

As of 2026-11, roles per account default to 1,000 and can be raised to 10,000; role trust policies default to 2,048 characters, adjustable to 8,192; a role can have 20 managed policies attached by default, adjustable to 25; instance profiles default to 1,000, adjustable to 10,000.[^s3] Fixed limits include 10,240 characters of inline policy per role, a 64-character role name, a 512-character path, a 64-character role session name, and 2,048 characters for session policies.[^s3] If a role will be used with console Switch Role, path plus name must fit in 64 characters.[^s2]

The main design risk is the **confused deputy**: a third party you trust could be tricked by another of its customers into using your role. AWS's mitigation for cross-account roles is a per-customer external ID checked with an sts:ExternalId condition in the trust policy; for AWS services acting on your resources it recommends aws:SourceArn, aws:SourceAccount, aws:SourceOrgID or aws:SourceOrgPaths conditions, which resource control policies can enforce organization-wide.[^s4]

## When to use it (and when not)

AWS recommends roles and federation for almost all access, and keeps IAM users only for cases that cannot use temporary credentials, such as some third-party tools, CodeCommit SSH or service-specific credentials, and tightly controlled emergency access.[^s1] Cross-account access usually goes through a role, though services with resource-based policies (S3 buckets, SNS topics, SQS queues) can grant another account access directly without one.[^s1] For workforce users, IAM's account access manager, announced in August 2026, assigns existing IAM roles across an organization's accounts to IAM Identity Center users and groups, at no additional cost in all commercial Regions enabled by default.[^s5][^s1]

## Connections

Roles are a resource of **AWS IAM** and are issued credentials by **AWS STS**, whose AssumeRole family of operations creates every role session.[^s2] They are the commonly confused counterpart of IAM users, which hold long-term credentials.[^s1] Their counts and policy sizes are governed by **IAM quotas**.[^s3] A role's effective access comes from **IAM policy evaluation logic** and can be capped by **permissions boundaries**; workforce access to roles is centralized through **IAM Identity Center** and account access manager.[^s1][^s5] EC2 delivers role credentials to instances through instance profiles, and cross-account roles are the backbone of a **multi-account strategy**.[^s2][^s1]

## Sources
[^s1]: Amazon Web Services, "IAM roles - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "Methods to assume a role - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, "IAM and AWS STS quotas - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, "The confused deputy problem - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, "AWS Identity and Access Management streamlines assignment of IAM roles to workforce users with account access manager", What's New with AWS, 2026-08-10. https://aws.amazon.com/about-aws/whats-new/2026/08/aws-iam-aam/ (accessed 2026-11-14)
