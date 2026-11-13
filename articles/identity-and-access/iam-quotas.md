---
id: kt:iam-quotas
type: Limit
title: IAM quotas
aliases: [IAM and AWS STS quotas, IAM limits, IAM service quotas]
domain: identity-and-access
summary: >-
  The per-account object counts, attachment limits, character and size limits, and STS request
  rate that constrain AWS Identity and Access Management and AWS STS; some are adjustable through
  Service Quotas (requested in US East (N. Virginia)), many are fixed.
verified: 2026-11-13
status: current
broader: [kt:aws-iam]
related: [kt:aws-sts, kt:iam-roles, kt:service-quotas, kt:multi-account-strategy]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-partitions}
  - {predicate: contrastsWith, object: kt:organizations-quotas}
sources:
  - id: s1
    title: "IAM and AWS STS quotas - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.md
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "AWS Identity and Access Management endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/iam-service.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "AWS IAM increases quotas (What's New with AWS, 2026-05-05)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/05/aws-iam-increased-quotas/
    type: official-blog
    accessed: 2026-11-13
  - id: s4
    title: "Methods to assume a role - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use.html
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "Requesting a quota increase - Service Quotas User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/servicequotas/latest/userguide/request-quota-increase.html
    type: official-docs
    accessed: 2026-11-13
created: 2026-11-13
---

# IAM quotas

IAM quotas are the limits AWS places on Identity and Access Management and the Security Token Service: how many objects of each kind an account may hold, how many policies can be attached to an identity, how long names and policy documents may be, and how fast an account may call STS.[^s1] They fall into three groups that behave very differently: adjustable object quotas, fixed object quotas, and fixed character and size limits for which AWS accepts no increase requests.[^s1][^s2] The `GetAccountSummary` API (or `aws iam get-account-summary`) reports an account's current usage against its IAM quotas.[^s1]

## Adjustable quotas and how to raise them

IAM is a global service within a partition, so its quotas are per account rather than per Region; the General Reference lists the same value for every supported Region, and the console procedure has you switch to US East (N. Virginia) before searching for IAM in Service Quotas.[^s1][^s2] Requests up to a published maximum are approved automatically, usually within a few minutes.[^s1] The defaults and auto-approved maximums, as of 2026-11, are:[^s1]

| Quota | Default | Auto-approved maximum |
|---|---|---|
| Roles per account | 1,000 | 10,000 |
| Customer managed policies per account | 1,500 | 10,000 |
| Instance profiles per account | 1,000 | 10,000 |
| Groups per account | 300 | 500 |
| Managed policies per role | 20 | 25 |
| Managed policies per user | 10 | 20 |
| Role trust policy length | 2,048 characters | 8,192 characters |
| OpenID Connect providers per account | 100 | 700[^s1] |

Several of these ceilings were raised on 2026-05-05: roles, customer managed policies and instance profiles went from 5,000 to 10,000, managed policies per role from 20 to 25, trust policy length from 4,096 to 8,192 characters and OIDC providers from 100 to 700.[^s3] Commercial-Region accounts request increases through Service Quotas, whereas AWS GovCloud (US) and China accounts go through AWS Support.[^s3] Service Quotas itself only accepts a value above the current one, may route larger requests to a Support case that can be approved, partly approved or denied, and needs permission to create a service-linked role when used from the CLI.[^s5]

## Fixed quotas

Many IAM quotas are marked not adjustable: 5,000 IAM users per account, 10 groups per user, 10 managed policies per group, 2 access keys and 2 signing certificates per user, 5 SSH public keys and 8 MFA devices per user, 100 SAML providers per account, 50 tags per user or role, and 5 saved versions per managed policy before an old version must be deleted.[^s2] Server certificates sit at 20 per account and the auto-approval maximum is also 20.[^s1][^s2]

Character limits cannot be increased at all. A customer managed policy may hold 6,144 characters; inline policies are unlimited in number but their combined size is capped per identity at 2,048 characters for a user, 5,120 for a group and 10,240 for a role. Whitespace is not counted in either case.[^s1] User and role names are limited to 64 characters (for console Switch Role, path plus role name must also fit in 64), group, policy and instance profile names to 128, and paths to 512.[^s1]

## STS limits

STS calls made with AWS credentials (AssumeRole, GetCallerIdentity, GetSessionToken, GetFederationToken and others) share a default quota of 600 requests per second per account per Region; calls made by AWS service principals do not count, and in cross-account AssumeRole only the calling account's quota is consumed. Increases need a Support ticket.[^s1] Session policies (one inline JSON document plus up to 10 managed policy ARNs, 2,048 characters combined) and up to 50 session tags all add to a separately limited session token size, reported by `SessionTokenUtilization`.[^s1] Role sessions last from 15 minutes up to the role's maximum session duration (1 to 12 hours), defaulting to one hour, and role chaining caps a session at one hour.[^s4]

## Gotchas and design implications

- **Users hit a hard wall.** The 5,000-user cap is fixed, so large human populations belong in a federated identity provider or IAM Identity Center, with workloads on roles.[^s2][^s4]
- **Policy size runs out before policy count.** With 6,144 characters per managed policy and 25 attachments per role at most, very fine-grained permission sets often need consolidation (wildcards, conditions, tags) rather than more policies.[^s1][^s3]
- **Version churn.** Only five versions are kept per managed policy, so pipelines that update policies must prune old versions.[^s2]
- **STS throttling is Regional and per caller.** Busy fleets that call GetCallerIdentity or AssumeRole in a loop share the 600 RPS budget with everything else in that account and Region.[^s1]

## Connections

- **Broader: AWS Identity and Access Management** (`kt:aws-iam`), whose objects these quotas bound.[^s1]
- **Related to AWS STS** (`kt:aws-sts`), whose request rate and session limits are documented alongside, and **IAM roles** (`kt:iam-roles`), the object type most often scaled up.[^s1][^s4]
- **Configured via Service Quotas** (`kt:service-quotas`) for the adjustable items.[^s1][^s5]
- **Scoped to AWS partitions** (`kt:aws-partitions`): each partition has its own IAM endpoint, and GovCloud and China use Support rather than Service Quotas for increases.[^s2][^s3]
- **Related to multi-account strategy** (`kt:multi-account-strategy`) and **contrasts with AWS Organizations quotas** (`kt:organizations-quotas`): the IAM object quotas above are counted per account, so each account in a multi-account design has its own IAM headroom, distinct from the limits that apply to the organization itself.[^s1][^s2]

## Sources
[^s1]: Amazon Web Services, "IAM and AWS STS quotas". https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.md (accessed 2026-11-13)
[^s2]: Amazon Web Services, AWS General Reference, "AWS Identity and Access Management endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/iam-service.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, What's New, "AWS IAM increases quotas" (2026-05-05). https://aws.amazon.com/about-aws/whats-new/2026/05/aws-iam-increased-quotas/ (accessed 2026-11-13)
[^s4]: Amazon Web Services, "Methods to assume a role". https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use.html (accessed 2026-11-13)
[^s5]: Amazon Web Services, Service Quotas User Guide, "Requesting a quota increase". https://docs.aws.amazon.com/servicequotas/latest/userguide/request-quota-increase.html (accessed 2026-11-13)
