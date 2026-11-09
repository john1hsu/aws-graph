---
id: kt:aws-iam
type: Service
title: AWS Identity and Access Management
aliases: [IAM, AWS IAM]
domain: identity-and-access
summary: >-
  AWS's free service for controlling who can sign in (authentication) and what they can do
  (authorization) in an AWS account, using users, groups, roles and JSON policies that AWS
  evaluates on every request.
verified: 2026-11-09
status: current
broader: [kt:aws-security-services]
related: [kt:iam-identity-center, kt:aws-sts, kt:iam-policy-evaluation-logic, kt:permissions-boundaries]
relations:
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: governedBy, object: kt:iam-quotas}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: scopedTo, object: kt:aws-partitions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is IAM? - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "Policy evaluation logic - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/AccessPolicyLanguage_EvaluationLogic.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "IAM and AWS STS quotas - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "AWS Identity and Access Management endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/iam-service.html
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "Security best practices in IAM"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/IAMBestPractices.html
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "SEC02-BP02 Use temporary credentials (AWS Well-Architected Framework)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/sec_identities_unique.html
    type: official-docs
    accessed: 2026-11-09
  - id: s7
    title: "AWS IAM increases quotas (What's New with AWS, 2026-05-05)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/05/aws-iam-increased-quotas/
    type: official-blog
    accessed: 2026-11-09
  - id: s8
    title: "AWS introduces additional policy details to access denied error messages (What's New with AWS, 2026-01-21)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/01/additional-policy-details-access-denied-error
    type: official-blog
    accessed: 2026-11-09
  - id: s9
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02) exam guide (French edition)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-09
  - id: s10
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03) exam guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS Identity and Access Management

AWS Identity and Access Management (IAM) is the web service that controls access to AWS resources: it decides who is authenticated (signed in) and who is authorized (has permission) to use what.[^s1] Almost every AWS API call is checked against IAM, so it sits underneath every other service in the graph. IAM itself costs nothing; you pay only for the other services that IAM identities use, and AWS STS and IAM Identity Center are also free.[^s1]

## How it works

Every account starts with a **root user** that has complete access; AWS recommends not using it for everyday work and creating other identities instead.[^s1] A **principal** is an IAM user, an IAM role, an AWS STS federated principal or an application the account trusts. **Authentication** matches presented credentials to a trusted principal; **authorization** then checks the request against every policy that applies.[^s1] Users can be collected into **groups** that share attached policies, while **roles** are assumed to obtain temporary credentials.[^s5][^s6]

Permissions are JSON **policies**. On each request AWS gathers the request context, finds the applicable policies and evaluates them.[^s2] Within one account, identity-based and resource-based policies combine as a union (either may allow), while a permissions boundary combines with identity policies as an intersection, and for a member of AWS Organizations the identity policy, service control policy (SCP) and resource control policy (RCP) must all allow the action. In every combination an explicit `Deny` wins over any `Allow`, and an action that nothing allows is denied.[^s2] SCPs, RCPs and permissions boundaries set ceilings but grant nothing by themselves.[^s5]

IAM is **eventually consistent**: a successful change is committed but takes time to replicate, so AWS advises keeping IAM changes out of critical, high-availability code paths and verifying propagation before production depends on them.[^s1] IAM has one service endpoint per partition (for example `iam.amazonaws.com` across commercial Regions and `iam.us-gov.amazonaws.com` in AWS GovCloud (US)), rather than one per Region.[^s4]

## Quotas and gotchas

Default quotas, per account (as of 2026-11, from the IAM User Guide and AWS General Reference): 1,000 roles, 1,500 customer managed policies, 300 groups and 1,000 instance profiles, all adjustable through Service Quotas (requested in US East (N. Virginia)); 20 managed policies per role and 10 per user, also adjustable; and a 2,048-character role trust policy, adjustable up to 8,192.[^s3][^s4] On 2026-05-05 AWS raised the ceilings for automatically approved increases, for example to 10,000 roles, 10,000 customer managed policies, 25 managed policies per role and 700 OIDC providers.[^s7][^s3]

Several limits cannot be raised: 5,000 IAM users per account, 10 groups per user, 2 access keys per user, 8 MFA devices per user and 100 SAML providers.[^s4] A customer managed policy is capped at 6,144 characters (whitespace not counted), and aggregate inline policy size is 2,048 characters for a user, 5,120 for a group and 10,240 for a role. A role's maximum session duration can be set from 1 to 12 hours; if a caller does not request a duration the session lasts 1 hour.[^s3]

Since January 2026 access-denied errors in same-account and same-organization cases include the ARN of the responsible policy (SCP, RCP, identity-based, session policy or permissions boundary), not just the policy type, rolling out gradually across services and Regions.[^s8]

## Practices and when to use what

AWS's best practices start with temporary credentials: humans should federate through an identity provider, ideally managed in IAM Identity Center, and workloads should use IAM roles rather than long-lived IAM user keys.[^s5] The Well-Architected security pillar rates failing to use temporary credentials as high risk and lists anti-patterns such as access keys committed to public Git repositories or embedded in mobile apps; EC2 instance profiles, Lambda execution roles and IAM Roles Anywhere (for on-premises systems) supply temporary credentials to workloads.[^s6] Other practices: require MFA (preferably phishing-resistant passkeys or security keys), apply least privilege starting from AWS managed policies, use IAM Access Analyzer to generate and validate policies and review public or cross-account access, remove unused credentials, and add policy conditions.[^s5] IAM user access keys remain a fallback for cases that cannot use temporary credentials, with rotation, tight permissions and monitoring.[^s6]

## Connections

- **Broader: AWS security services** (`kt:aws-security-services`): IAM is the identity and authorization layer of AWS's security, identity and compliance category.[^s9][^s10]
- **Related to IAM Identity Center** (`kt:iam-identity-center`) for workforce federation, **AWS STS** (`kt:aws-sts`) which issues temporary credentials, **policy evaluation logic** (`kt:iam-policy-evaluation-logic`) and **permissions boundaries** (`kt:permissions-boundaries`).[^s1][^s2][^s5]
- **Integrates with AWS Organizations** (`kt:aws-organizations`), whose SCPs and RCPs cap member-account permissions.[^s2][^s5]
- **Governed by IAM quotas** (`kt:iam-quotas`); **scoped to AWS partitions** (`kt:aws-partitions`) through its per-partition endpoint.[^s3][^s4]
- **Monitored by AWS CloudTrail** (`kt:aws-cloudtrail`), whose activity data IAM Access Analyzer uses to generate least-privilege policies.[^s5]
- **Covered by** the AWS Certified Cloud Practitioner and Solutions Architect – Associate exams, which both list IAM as in scope.[^s9][^s10]

## Sources
[^s1]: Amazon Web Services, "What is IAM?". https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction (accessed 2026-11-09)
[^s2]: Amazon Web Services, "Policy evaluation logic". https://docs.aws.amazon.com/IAM/latest/UserGuide/AccessPolicyLanguage_EvaluationLogic.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "IAM and AWS STS quotas". https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "AWS Identity and Access Management endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/iam-service.html (accessed 2026-11-09)
[^s5]: Amazon Web Services, "Security best practices in IAM". https://docs.aws.amazon.com/IAM/latest/UserGuide/IAMBestPractices.html (accessed 2026-11-09)
[^s6]: Amazon Web Services, AWS Well-Architected Framework, "SEC02-BP02 Use temporary credentials". https://docs.aws.amazon.com/wellarchitected/latest/framework/sec_identities_unique.html (accessed 2026-11-09)
[^s7]: Amazon Web Services, What's New, "AWS IAM increases quotas" (2026-05-05). https://aws.amazon.com/about-aws/whats-new/2026/05/aws-iam-increased-quotas/ (accessed 2026-11-09)
[^s8]: Amazon Web Services, What's New, "AWS introduces additional policy details to access denied error messages" (2026-01-21). https://aws.amazon.com/about-aws/whats-new/2026/01/additional-policy-details-access-denied-error (accessed 2026-11-09)
[^s9]: Amazon Web Services, AWS Certification, "In-Scope AWS Services" for AWS Certified Cloud Practitioner (CLF-C02), French edition. https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-09)
[^s10]: Amazon Web Services, AWS Certification, "In-Scope AWS Services" for AWS Certified Solutions Architect – Associate (SAA-C03). https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-09)
