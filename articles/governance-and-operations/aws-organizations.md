---
id: kt:aws-organizations
type: Service
title: AWS Organizations
aliases: [Organizations, AWS Orgs]
domain: governance-and-operations
summary: >-
  AWS's free, global account-management service that groups many AWS accounts into one
  organization under a management account, consolidates their billing, arranges them in
  organizational units, and applies central policies such as service control policies.
verified: 2026-11-09
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:aws-control-tower, kt:multi-account-strategy, kt:iam-identity-center, kt:aws-govcloud-us]
relations:
  - {predicate: hasPart, object: kt:service-control-policies}
  - {predicate: integratesWith, object: kt:aws-iam}
  - {predicate: integratesWith, object: kt:aws-cloudtrail}
  - {predicate: integratesWith, object: kt:aws-cost-explorer}
  - {predicate: governedBy, object: kt:organizations-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is AWS Organizations?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "AWS Organizations FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/organizations/faqs/
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Quotas and service limits for AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Terminology and concepts for AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "Service control policies (SCPs) - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "AWS Organizations in AWS GovCloud (US)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-organizations.html
    type: official-docs
    accessed: 2026-11-09
  - id: s7
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS Organizations

AWS Organizations is the service you use to run many AWS accounts as one managed estate. You create or invite accounts, group them, apply governance policies to the groups, and pay for everything through a single payment method, while integrated AWS services read the organization's structure to deploy central security, audit and resource-sharing settings.[^s1] It is generally available, costs nothing extra (you pay only for the resources the accounts use), and appears under Management and Governance in the in-scope service list for the AWS Certified Cloud Practitioner (CLF-C02) exam.[^s2][^s7]

## How it works

An **organization** is a tree: one **root**, zero or more **organizational units (OUs)**, and AWS accounts as the leaves. The **management account** creates the organization, pays all charges, creates or invites accounts and attaches policies; every other account is a **member account**, and an account can belong to only one organization at a time. You cannot later change which account is the management account.[^s4] OUs can nest up to five levels below the root, and policies attached higher in the tree are inherited by everything beneath them.[^s4] Invitations and feature upgrades are exchanged as **handshakes** between accounts.[^s4]

An organization runs with one of two feature sets. **Consolidated billing** only merges bills. **All features**, the default and AWS's recommendation, adds central policies, delegated administration and service integrations; an organization that started with billing only can upgrade, but every invited member must approve the change.[^s4] Organization entities are global, like IAM, so you do not pick a Region; the exception is the China Regions, which need a separate organization.[^s2]

**Policies.** Service control policies (SCPs) cap what IAM principals in member accounts may do, and resource control policies (RCPs) cap what can be done to their resources. Neither grants anything: effective access is the intersection of these guardrails with identity- and resource-based policies.[^s5][^s1] Other management policy types include declarative, backup, tag, chat applications, AI services opt-out and Security Hub policies.[^s3] SCPs require all features, never restrict service-linked roles, and do not apply to users or roles in the management account.[^s5]

**Integrations and delegation.** With all features on, services such as CloudTrail (organization trails), AWS Config, GuardDuty, IAM Access Analyzer, Macie, IAM Identity Center, AWS Backup, Resource Access Manager and Control Tower operate across the whole organization.[^s1] The management account can name a member as **delegated administrator** either for Organizations policies or for an individual integrated service, which keeps day-to-day work out of the management account.[^s4]

## Quotas and gotchas (as of 2026-11)

Quotas are global; request changes through Service Quotas in us-east-1, where Organizations is hosted.[^s3]

- **Accounts:** 10 by default, adjustable by the management account up to 50,000 depending on qualifications; pending invitations and not-yet-permanently-closed accounts count.[^s3]
- **Structure:** 1 root, 2,000 OUs, nesting 5 levels deep, 50 tags per root, OU or account.[^s3]
- **SCPs:** up to 10,000 per organization, 10,240 characters each, and at most 10 attached directly to any root, OU or account (a hard limit). When SCPs are enabled, every entity must keep at least one attached.[^s3]
- **RCPs:** up to 2,000 per organization, 5,120 characters each, at most 5 attached directly per target.[^s3]
- **Operations:** 5 concurrent account creations; a created account must be at least 4 days old before removal; invitations expire after 15 days; closures within a rolling 30 days are capped at 250 for organizations under 1,250 accounts (not adjustable).[^s3]

Common traps: detaching the default `FullAWSAccess` SCP without a replacement allow policy makes every action in affected member accounts fail.[^s5] And because SCPs do not bind the management account, workloads placed there escape your guardrails, which is why AWS advises keeping it free of them.[^s4][^s5]

## GovCloud (US)

Organizations works in both GovCloud (US) Regions but as a separate organization with all features required. You call `CreateGovCloudAccount` from the commercial management account, which creates a paired commercial account for billing and a standalone GovCloud account that must then be invited into the GovCloud organization. Backup, chat applications and AI services opt-out policies are not supported there, and some operations run only in GovCloud (US-West).[^s6]

## When to use it

Use Organizations as soon as you have more than one account: separating development, production, security and logging into accounts grouped by OU gives blast-radius isolation and one bill.[^s1][^s4] Group OUs by shared policy needs rather than by your org chart. For a pre-built landing zone with guardrails and account vending on top of Organizations, AWS Control Tower is the higher-level option.[^s1]

## Connections

Organizations sits within AWS management and governance services and is the base of a **multi-account strategy**, with **AWS Control Tower** building on it.[^s1] It has **service control policies** as a component and integrates with **IAM** (SCPs and RCPs bound IAM permissions), **AWS CloudTrail** (organization trails) and **AWS Cost Explorer** (consolidated cost tracking).[^s1][^s5] Workforce access across accounts commonly runs through **IAM Identity Center**, and **AWS GovCloud (US)** needs its own paired organization.[^s1][^s6] Limits are `governedBy` **AWS Organizations quotas**, and the service is `coveredBy` the **Cloud Practitioner** exam.[^s3][^s7]

## Sources
[^s1]: Amazon Web Services, "What is AWS Organizations?". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html (accessed 2026-11-09)
[^s2]: Amazon Web Services, "AWS Organizations FAQs". https://aws.amazon.com/organizations/faqs/ (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Quotas and service limits for AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Terminology and concepts for AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html (accessed 2026-11-09)
[^s5]: Amazon Web Services, "Service control policies (SCPs) - AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html (accessed 2026-11-09)
[^s6]: Amazon Web Services, "AWS Organizations in AWS GovCloud (US)". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-organizations.html (accessed 2026-11-09)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html (accessed 2026-11-09)
