---
id: kt:multi-account-strategy
type: Pattern
title: Multi-account strategy
aliases: [AWS multi-account strategy, multi-account environment, Organizing your AWS environment using multiple accounts]
domain: governance-and-operations
summary: >-
  AWS's recommended practice of spreading workloads across many AWS accounts, grouped into
  organizational units (OUs) in AWS Organizations, so that each account acts as a security,
  access, billing and quota boundary that controls can be applied to at the OU level.
verified: 2026-11-12
status: concept
broader: [kt:aws-management-and-governance-services]
related: [kt:iam-identity-center, kt:security-pillar, kt:landing-zone]
relations:
  - {predicate: dependsOn, object: kt:aws-organizations}
  - {predicate: configuredVia, object: kt:aws-control-tower}
  - {predicate: securedBy, object: kt:service-control-policies}
  - {predicate: governedBy, object: kt:organizations-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "Benefits of using multiple AWS accounts - Organizing Your AWS Environment Using Multiple Accounts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/benefits-of-using-multiple-aws-accounts.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Design principles for your multi-account strategy - Organizing Your AWS Environment Using Multiple Accounts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/design-principles-for-your-multi-account-strategy.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Recommended OUs and accounts - Organizing Your AWS Environment Using Multiple Accounts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/recommended-ous-and-accounts
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "Best practices for managing organizational units (OUs) with AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_ous_best_practices.html
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "Best practices for a multi-account environment - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_best-practices.md
    type: official-docs
    accessed: 2026-11-12
  - id: s6
    title: "Quotas and service limits for AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.md
    type: official-docs
    accessed: 2026-11-12
  - id: s7
    title: "SEC01-BP01 Separate workloads using accounts - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/sec_securely_operate_multi_accounts.html
    type: official-docs
    accessed: 2026-11-12
  - id: s8
    title: "AWS Certified Solutions Architect - Associate (SAA-C03) Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.pdf
    type: official-learning
    accessed: 2026-11-12
created: 2026-11-12
---

# Multi-account strategy

A multi-account strategy is AWS's guidance for running workloads in many AWS accounts rather than one. An AWS account is a natural boundary for security, access and billing, so separating workloads into accounts contains mistakes and attacks and makes ownership explicit.[^s1][^s5] AWS's whitepaper *Organizing Your AWS Environment Using Multiple Accounts* is the reference design, and the Well-Architected Security Pillar makes account separation its first best practice (SEC01-BP01), rating the risk of not doing it as high.[^s2][^s7]

## Why use multiple accounts

The whitepaper lists eight benefits: grouping workloads by business purpose and ownership; applying different security controls per environment; constraining access to sensitive data behind account-level data perimeters; giving builders sandbox and development accounts to experiment in; limiting the blast radius of failures, misconfigurations or malicious activity; supporting different IT operating models; managing costs, since the account is the default unit of cost allocation; and spreading per-account service quotas and API rate limits.[^s1] Well-Architected adds central governance of which services and Regions accounts may use, automated account creation and centralized auditing.[^s7]

## How it works

The strategy is built on **AWS Organizations**: accounts are grouped into a hierarchy of OUs, and policies attached to an OU, such as service control policies (SCPs), are inherited by every account below it.[^s7] AWS recommends one organization for all accounts, because the organization is the boundary for consistent policies and central visibility.[^s5]

**Design principles.** Organize OUs around security and operational needs (shared controls), not the company's reporting chart; attach controls to OUs rather than individual accounts; keep the hierarchy shallow even though Organizations allows five OU levels; start small and grow; separate production from non-production; and give each production account one workload or a few closely related ones.[^s2][^s4] Keep workloads out of the management account, because SCPs do not apply to it, and use delegated administrator accounts so fewer people need to sign in there.[^s2] Humans should sign in through federation with IAM Identity Center and MFA, and break-glass roles should exist for when the identity provider fails.[^s2]

**Recommended OUs.** Start with two foundational OUs: *Security*, for log archive, security tooling, read-only and break-glass accounts, and *Infrastructure*, for shared networking and IT services.[^s3][^s4] A *Workloads* OU, split into production and SDLC (non-production) child OUs, holds business applications, and a *Sandbox* OU holds experimentation accounts that can be cut off from internal networks.[^s3][^s4] Procedural OUs cover *Policy Staging* (testing policy changes), *Suspended* (closed accounts, with a deny-all SCP), *Exceptions* and *Transitional* (for example, accounts from an acquisition); advanced options include *Deployments* for CI/CD and *Individual Business Users*.[^s3][^s4] You do not need every OU on day one.[^s3]

## Quotas and gotchas

As of 2026-11, AWS Organizations quotas are global (accessed through us-east-1 in the Service Quotas console): a new organization defaults to 10 accounts, an adjustable quota AWS may raise as high as 50,000; up to 2,000 OUs; five levels of OU nesting; at most 10 SCPs directly attached per root, OU or account (a hard limit); and five member accounts created concurrently.[^s6] Pending invitations and closed accounts still in their post-closure period count against the account quota.[^s6] Some services cap how many accounts they support separately, for example 7,000 for IAM Identity Center and 10,000 for AWS Control Tower.[^s6]

Operationally, AWS recommends root access management so new member accounts have no root credentials, a group email address and a dedicated, well-kept phone number for each account's recovery, and an organization-wide tagging strategy.[^s5] Common anti-patterns are mixing unrelated workloads of different data sensitivity in one account and a poorly defined OU structure.[^s7]

## When to use it (and when not)

Any environment beyond a single small workload benefits; the right number of accounts ranges from a few to thousands.[^s2] The usual path is to build a landing zone, through AWS Control Tower or a custom solution, which applies mandatory controls and vends baselined accounts through Account Factory.[^s7] Teams that need a structure Control Tower doesn't prescribe can build directly in Organizations.[^s5]

## Connections

The strategy `dependsOn` **AWS Organizations** for accounts, OUs and policy inheritance, and is usually `configuredVia` **AWS Control Tower**, whose landing zone and Account Factory automate it.[^s7] It is `securedBy` **service control policies** applied at the OU level and uses **IAM Identity Center** for federated workforce access.[^s2] Its scale limits are `governedBy` **AWS Organizations quotas**.[^s6] It puts the **Security Pillar** into practice (SEC01-BP01).[^s7] It is `coveredBy` the **Solutions Architect – Associate** exam, whose Task 1.1 asks candidates to design security for multiple AWS accounts, naming Control Tower and SCPs.[^s8]

## Sources

[^s1]: Amazon Web Services, "Benefits of using multiple AWS accounts - Organizing Your AWS Environment Using Multiple Accounts". https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/benefits-of-using-multiple-aws-accounts.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Design principles for your multi-account strategy - Organizing Your AWS Environment Using Multiple Accounts". https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/design-principles-for-your-multi-account-strategy.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "Recommended OUs and accounts - Organizing Your AWS Environment Using Multiple Accounts". https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/recommended-ous-and-accounts (accessed 2026-11-12)
[^s4]: Amazon Web Services, "Best practices for managing organizational units (OUs) with AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_ous_best_practices.html (accessed 2026-11-12)
[^s5]: Amazon Web Services, "Best practices for a multi-account environment - AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_best-practices.md (accessed 2026-11-12)
[^s6]: Amazon Web Services, "Quotas and service limits for AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.md (accessed 2026-11-12)
[^s7]: Amazon Web Services, "SEC01-BP01 Separate workloads using accounts - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/sec_securely_operate_multi_accounts.html (accessed 2026-11-12)
[^s8]: Amazon Web Services, "AWS Certified Solutions Architect - Associate (SAA-C03) Exam Guide" (PDF). https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.pdf (accessed 2026-11-12)
