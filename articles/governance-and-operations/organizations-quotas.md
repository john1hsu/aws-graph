---
id: kt:organizations-quotas
type: Limit
title: AWS Organizations quotas
aliases: [Organizations quotas, AWS Organizations limits, Organizations service limits]
domain: governance-and-operations
summary: >-
  The organization-wide limits on accounts, OUs, policies, attachments, invitations, account
  closures and API request rates in AWS Organizations; a handful are adjustable through Service
  Quotas in us-east-1, while most structural and policy-attachment limits are fixed.
verified: 2026-11-14
status: current
broader: [kt:aws-organizations]
related: [kt:service-control-policies, kt:multi-account-strategy, kt:aws-control-tower, kt:resource-control-policies]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: contrastsWith, object: kt:iam-quotas}
  - {predicate: scopedTo, object: kt:aws-partitions}
sources:
  - id: s1
    title: "Quotas and service limits for AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "AWS Organizations endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_gb/general/latest/gr/ao.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Close an AWS account - AWS Account Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/accounts/latest/reference/manage-acct-closing.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Using Service Quotas request templates - Service Quotas User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/servicequotas/latest/userguide/organization-templates.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "AWS Organizations now supports higher quotas for service control policies (What's New with AWS, 2026-05-15)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/id/about-aws/whats-new/2026/05/aws-organizations-increased-scp-quotas/
    type: official-blog
    accessed: 2026-11-14
  - id: s6
    title: "AWS Organizations increases RCP quota to 2,000 per organization (What's New with AWS, 2026-07-22)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/07/aws-organizations-resource-control-policy-limit-increase-2000
    type: official-blog
    accessed: 2026-11-14
  - id: s7
    title: "AWS Organizations now provides maximum account quota visibility in Service Quotas (What's New with AWS, 2026-08-03)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/08/aws-organizations/
    type: official-blog
    accessed: 2026-11-14
created: 2026-11-14
---

# AWS Organizations quotas

AWS Organizations quotas cap how large and how busy an organization can be: the number of member accounts, the shape of the OU tree, how many policies of each type exist and how many attach to one target, how quickly accounts can be invited, created or closed, and how fast the Organizations APIs can be called.[^s1] Unless noted, they apply to the organization as a whole, and many bind only actions taken from the management account.[^s1] Because Organizations is a global service hosted in US East (N. Virginia), its quotas are viewed and requested in `us-east-1`; each partition (commercial, AWS GovCloud (US), China) has its own single endpoint and therefore its own organization.[^s1][^s2]

## Adjustable quotas

Only three Organizations quotas can be raised: the maximum number of accounts, inbound billing transfers (default 0, up to 1,000) and the length of a bill-transfer chain (2 for Distributors only).[^s2] The account quota defaults to 10, and new organizations may start lower. Only the management account can request more, up to 50,000 depending on AWS's review of the customer's qualifications and needs.[^s1] Since 2026-08-03 the management account can see this quota and its current utilization directly in Service Quotas (US East (N. Virginia)) instead of asking AWS Support.[^s7]

Two things inflate the account count. Pending invitations count until they are accepted, declined, cancelled or expire, and a closed member account keeps counting for its 90-day post-closure period, after which AWS permanently closes it.[^s1][^s3] AWS suggests removing an account from the organization before closing it if you need the slot back at once.[^s3] A few integrated services also have lower per-organization account ceilings than Organizations itself, for example 250 for AWS Audit Manager, 1,200 for Amazon Detective and 7,000 for IAM Identity Center (all adjustable).[^s1]

## Fixed structural and policy limits (as of 2026-11)

| Item | Limit |
|---|---|
| Roots per organization | 1 |
| OUs per organization | 2,000 |
| OU nesting under the root | 5 levels |
| Tags per root, OU or account | 50 |
| SCPs per organization / size | 10,000 / 10,240 characters |
| RCPs per organization / size | 2,000 / 5,120 characters |
| Declarative, backup, tag, chat applications and Security Hub policies | 1,000 each, 10,000 characters |
| AI services opt-out policies | 1,000, 2,500 characters |
| Resource-based delegation policy | 40,000 characters[^s1] |

Direct attachments are hard limits counted per root, OU or account, ignoring inherited policies: 10 for SCPs, declarative, backup, tag and Security Hub policies, and 5 for RCPs, chat applications and AI opt-out policies.[^s1] With SCPs enabled every target must keep at least one SCP, and the AWS-managed `RCPFullAWSAccess` policy is attached everywhere RCPs are enabled, cannot be detached and uses one of the five RCP slots.[^s1] The console strips insignificant JSON whitespace before counting characters, whereas the CLI and SDKs store the document exactly as sent.[^s1]

## Operational limits

Up to 5 member accounts can be created and 3 closed concurrently; a created account must be at least 4 days old before it can be removed.[^s1] Invitation attempts are capped at 20 per 24 hours (or the account maximum, if higher), and invitations expire after 15 days, all-features requests after 90.[^s1] Closures within a rolling 30-day window are limited to 250 accounts or 20% of member accounts, whichever is higher, with a ceiling of 1,000; this is not adjustable, and the window opens with the first closure rather than on a calendar month.[^s1][^s3] API calls are throttled with a token bucket per account and per organization; `CreateAccount` allows 0.1 requests per second with a burst of 3.1, while `CloseAccount` allows only 0.05 per second, so bulk automation must pace itself and retry.[^s1]

## What changed (2026)

On 2026-05-15 AWS doubled SCPs per target from 5 to 10 and SCP size from 5,120 to 10,240 characters, across commercial, GovCloud (US) and China Regions.[^s5] On 2026-07-22 the RCP count per organization rose from 1,000 to 2,000, applied automatically at no cost.[^s6] Older blog posts and training material that cite the earlier values are out of date.[^s5][^s6]

## Gotchas

- **Plan the OU tree for five levels.** Deep, per-team hierarchies hit the nesting limit; flatter OUs with tags scale better.[^s1]
- **Policy slots, not policy count, run out first.** Ten direct SCP attachments per target is fixed, so consolidate statements rather than adding policies.[^s1][^s5]
- **Quota templates are only for new accounts.** A Service Quotas request template (up to 10 quotas, all-features organizations, commercial non-opt-in Regions) auto-requests increases for accounts created afterwards, not existing ones.[^s4]

## Connections

- **Broader: AWS Organizations** (`kt:aws-organizations`), whose objects these limits bound.[^s1]
- **Related to service control policies** (`kt:service-control-policies`) and **resource control policies** (`kt:resource-control-policies`), whose counts, sizes and attachments are capped here.[^s1][^s6]
- **Related to multi-account strategy** (`kt:multi-account-strategy`) and **AWS Control Tower** (`kt:aws-control-tower`, 10,000 accounts, not adjustable), which plan around these ceilings.[^s1]
- **Configured via Service Quotas** (`kt:service-quotas`) for the adjustable items and request templates.[^s2][^s4]
- **Contrasts with IAM quotas** (`kt:iam-quotas`), which are counted per account rather than per organization.[^s1]
- **Scoped to AWS partitions** (`kt:aws-partitions`): each partition has its own Organizations endpoint.[^s2]

## Sources
[^s1]: Amazon Web Services, "Quotas and service limits for AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, AWS General Reference, "AWS Organizations endpoints and quotas". https://docs.aws.amazon.com/en_gb/general/latest/gr/ao.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, AWS Account Management, "Close an AWS account". https://docs.aws.amazon.com/accounts/latest/reference/manage-acct-closing.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, Service Quotas User Guide, "Using Service Quotas request templates". https://docs.aws.amazon.com/servicequotas/latest/userguide/organization-templates.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, What's New, "AWS Organizations now supports higher quotas for service control policies (SCPs)" (2026-05-15). https://aws.amazon.com/id/about-aws/whats-new/2026/05/aws-organizations-increased-scp-quotas/ (accessed 2026-11-14)
[^s6]: Amazon Web Services, What's New, "AWS Organizations increases RCP quota to 2,000 per organization" (2026-07-22). https://aws.amazon.com/about-aws/whats-new/2026/07/aws-organizations-resource-control-policy-limit-increase-2000 (accessed 2026-11-14)
[^s7]: Amazon Web Services, What's New, "AWS Organizations now provides maximum account quota visibility in Service Quotas" (2026-08-03). https://aws.amazon.com/about-aws/whats-new/2026/08/aws-organizations/ (accessed 2026-11-14)
