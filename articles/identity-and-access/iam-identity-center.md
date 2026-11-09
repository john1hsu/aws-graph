---
id: kt:iam-identity-center
type: Service
title: AWS IAM Identity Center
aliases: [IAM Identity Center, AWS Single Sign-On, AWS SSO, Identity Center]
domain: identity-and-access
summary: >-
  AWS's workforce identity service (formerly AWS Single Sign-On) that connects an existing identity
  provider or its own user directory to AWS managed applications and, from an organization instance,
  to AWS accounts across AWS Organizations through permission sets and the AWS access portal.
verified: 2026-11-09
status: current
broader: [kt:aws-security-services]
related: [kt:aws-iam, kt:aws-organizations]
relations:
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: governedBy, object: kt:iam-identity-center-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is IAM Identity Center? - AWS IAM Identity Center"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/singlesignon/latest/userguide/what-is.md
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "Organization and account instances of IAM Identity Center - AWS IAM Identity Center"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/singlesignon/latest/userguide/identity-center-instances.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Create, manage, and delete permission sets - AWS IAM Identity Center"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/singlesignon/latest/userguide/permissionsets.md
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Quotas and limits in IAM Identity Center"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/singlesignon/latest/userguide/limits.html
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "AWS IAM Identity Center enables account access and application use in multiple AWS Regions (What's New with AWS, 2026-02-03)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/02/aws-iam-identity-center-multi-region-aws-account-access-and-application-deployment
    type: official-blog
    accessed: 2026-11-09
  - id: s6
    title: "AWS IAM Identity Center now supports separate quotas for AWS accounts and applications (What's New with AWS, 2026-06-22)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/aws-identity-center-separate-quotas/
    type: official-blog
    accessed: 2026-11-09
  - id: s7
    title: "AWS IAM Identity Center makes management of AWS account access optional for new organization instances (What's New with AWS, 2026-08-05)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/08/aws-identity-center-accounts-optional/
    type: official-blog
    accessed: 2026-11-09
  - id: s8
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03) exam guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-09
  - id: s9
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02) exam guide (French edition)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS IAM Identity Center

AWS IAM Identity Center is AWS's service for connecting workforce users to AWS managed applications and to AWS accounts. You either connect an existing identity provider and synchronize its users and groups, or create users directly in Identity Center, and then grant those users access to applications, to accounts, or both.[^s1] It was launched as AWS Single Sign-On and renamed on July 26, 2022; for backward compatibility the `sso`, `sso-admin`, `sso-oidc` and `identitystore` API and CLI namespaces, `AWS::SSO` CloudFormation resources and the `AWSServiceRoleForSSO` service-linked role kept their old names.[^s1] AWS states that Identity Center is provided at no additional cost; customer managed AWS KMS keys used with it incur normal KMS charges.[^s5]

## How it works

An **instance** is one deployment of Identity Center, and there are two kinds. An **organization instance** is enabled in the AWS Organizations management account, supports every feature and is AWS's recommended setup; an **account instance** is bound to a single account and Region and is meant for isolated deployments of selected AWS managed applications.[^s1][^s2] Only an organization instance offers multi-account permissions, the access portal for AWS accounts, SAML 2.0 customer managed applications, a delegated administrator, encryption with a customer managed KMS key and replication to additional Regions.[^s2]

For account access, administrators define **permission sets**: stored templates of AWS managed, customer managed and inline policies that Identity Center provisions into member accounts as IAM roles and assigns to users or groups. Each assigned permission set shows up as a role users can pick in the **AWS access portal**, the web page from which they open accounts and applications.[^s3][^s1] Teams can alternatively keep their own IAM roles in accounts and assign them centrally through IAM's account access manager feature.[^s1] Since August 2026, a new organization instance can be created with account-access management switched off, so it serves only applications and does not provision its service-linked role into member accounts; it can be turned on later.[^s7]

On the application side, Identity Center gives AWS managed applications (AWS names Kiro, Amazon Quick and Amazon Redshift as examples) a common view of users and groups. **Trusted identity propagation** lets one such application pass the signed-in user's identity to another, so access is authorized per user and CloudTrail events record which user initiated each action.[^s1]

## Quotas, sessions and gotchas

Default quotas as of 2026-11, from the IAM Identity Center User Guide: one Identity Center instance per account (not adjustable); 7,000 AWS accounts and, separately, 7,000 applications per instance; 3,500 permission sets per instance; 500 provisioned permission sets per account; 1,000,000 users and 100,000 groups; and 6 Regions enabled for one instance, all adjustable.[^s4] AWS split the account and application quotas in June 2026 so that using one no longer consumes the other.[^s6] Fixed limits include one inline policy and 251 managed policies per permission set, one connected Active Directory, and 100 groups assignable to a permission set per account.[^s4] A gotcha: IAM still caps a role at 10 managed policies by default, so a permission set with more needs that IAM quota raised in every target account.[^s4]

Permission-set sessions default to 1 hour and can be set up to 12 hours, while an AWS access portal session defaults to 8 hours and can be extended to 90 days; AWS advises keeping both no longer than the work requires and assigning the most restrictive permission set that does the job.[^s3]

**Multi-Region (February 2026):** an organization instance can be replicated from its primary Region to other Regions, so users keep reaching accounts through already provisioned entitlements if the primary Region is disrupted, and applications can be deployed closer to users. It requires an external identity provider and a multi-Region customer managed KMS key, and administration stays in the primary Region.[^s5]

## When to use it

AWS's guidance is to use an organization instance for workforce access across a multi-account organization and for all production application use, reserving account instances for isolated application deployments.[^s1][^s2] Customers already federating into accounts directly with IAM can keep doing so and still use Identity Center only for AWS managed applications.[^s1]

## Connections

- **Broader: AWS security services** (`kt:aws-security-services`): both the SAA-C03 and CLF-C02 exam guides list AWS IAM Identity Center under Security, Identity, and Compliance.[^s8][^s9]
- **Related to AWS IAM** (`kt:aws-iam`), which supplies the roles that permission sets become and the alternative direct-federation path.[^s1][^s3]
- **Integrates with AWS Organizations** (`kt:aws-organizations`): the organization instance lives in the management account and manages access across member accounts.[^s2]
- **Secured by AWS KMS** (`kt:aws-kms`) when an organization instance uses a customer managed key, which multi-Region replication requires.[^s2][^s5]
- **Monitored by AWS CloudTrail** (`kt:aws-cloudtrail`), whose events are attributed to individual users under trusted identity propagation.[^s1]
- **Governed by IAM Identity Center quotas** (`kt:iam-identity-center-quotas`).[^s4][^s6]
- **Covered by** the AWS Certified Cloud Practitioner and Solutions Architect – Associate exams, which list it as in scope.[^s8][^s9]

## Sources
[^s1]: Amazon Web Services, "What is IAM Identity Center?". https://docs.aws.amazon.com/singlesignon/latest/userguide/what-is.md (accessed 2026-11-09)
[^s2]: Amazon Web Services, "Organization and account instances of IAM Identity Center". https://docs.aws.amazon.com/singlesignon/latest/userguide/identity-center-instances.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Create, manage, and delete permission sets". https://docs.aws.amazon.com/singlesignon/latest/userguide/permissionsets.md (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Quotas and limits in IAM Identity Center". https://docs.aws.amazon.com/singlesignon/latest/userguide/limits.html (accessed 2026-11-09)
[^s5]: Amazon Web Services, What's New, "AWS IAM Identity Center enables account access and application use in multiple AWS Regions" (2026-02-03). https://aws.amazon.com/about-aws/whats-new/2026/02/aws-iam-identity-center-multi-region-aws-account-access-and-application-deployment (accessed 2026-11-09)
[^s6]: Amazon Web Services, What's New, "AWS IAM Identity Center now supports separate quotas for AWS accounts and applications" (2026-06-22). https://aws.amazon.com/about-aws/whats-new/2026/06/aws-identity-center-separate-quotas/ (accessed 2026-11-09)
[^s7]: Amazon Web Services, What's New, "AWS IAM Identity Center makes management of AWS account access optional for new organization instances" (2026-08-05). https://aws.amazon.com/about-aws/whats-new/2026/08/aws-identity-center-accounts-optional/ (accessed 2026-11-09)
[^s8]: Amazon Web Services, AWS Certification, "In-Scope AWS Services" for AWS Certified Solutions Architect – Associate (SAA-C03). https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-09)
[^s9]: Amazon Web Services, AWS Certification, "In-Scope AWS Services" for AWS Certified Cloud Practitioner (CLF-C02), French edition. https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-09)
