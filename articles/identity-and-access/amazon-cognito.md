---
id: kt:amazon-cognito
type: Service
title: Amazon Cognito
aliases: [Cognito, Cognito user pools, Cognito identity pools, Amazon Cognito Federated Identities]
domain: identity-and-access
summary: >-
  AWS's customer identity (CIAM) service for web and mobile apps: user pools are an OIDC/OAuth 2.0
  user directory and token issuer, and identity pools exchange trusted tokens for temporary,
  IAM-scoped AWS credentials.
verified: 2026-11-17
status: current
broader: [kt:aws-security-services]
related: [kt:aws-appsync, kt:amazon-api-gateway, kt:attribute-based-access-control, kt:multi-region-architecture]
relations:
  - {predicate: dependsOn, object: kt:aws-sts}
  - {predicate: contrastsWith, object: kt:iam-identity-center}
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: securedBy, object: kt:aws-waf}
  - {predicate: governedBy, object: kt:cognito-quotas}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is Amazon Cognito? - Amazon Cognito"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "User pool feature plans - Amazon Cognito"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-sign-in-feature-plans.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Quotas in Amazon Cognito - Amazon Cognito"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cognito/latest/developerguide/quotas.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "Amazon Cognito - Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/cognito/pricing/
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "Amazon Cognito identity pools - Amazon Cognito"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html
    type: official-docs
    accessed: 2026-11-17
  - id: s6
    title: "What's new in Amazon Cognito - Amazon Cognito"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-whats-new.html
    type: official-docs
    accessed: 2026-11-17
  - id: s7
    title: "Amazon Cognito now supports multi-Region replication"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-cognito-multi-region/
    type: official-blog
    accessed: 2026-11-17
  - id: s8
    title: "Federal Risk and Authorization Management Program (FedRAMP) - Services in Scope"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-17
created: 2026-11-17
---

# Amazon Cognito

Amazon Cognito is AWS's identity platform for the end users of web and mobile applications. It combines a user directory, an authentication server and an authorization service that issues both OAuth 2.0 access tokens and temporary AWS credentials, and it can sign users in from its own directory, from enterprise SAML or OIDC directories, or from consumer providers such as Google, Apple, Facebook and Amazon.[^s1] It is the customer-facing counterpart to workforce identity services: Cognito holds your app's customers, not your employees' AWS console access.

## How it works

Cognito has two independent components that are often combined.[^s1]

- **User pools** answer "who is this user?". A user pool is a directory that issues JSON Web Tokens (ID, access and refresh tokens) to your app or API. It acts as an OIDC provider and OAuth 2.0 authorization server, as a relying party for social and OIDC providers, and as a SAML 2.0 service provider that turns assertions into tokens. It also supports MFA, custom authentication flows, user migration and machine-to-machine authorization through the `client_credentials` grant.[^s1] **Managed login** is the hosted sign-up and sign-in front end; alternatively you build your own UI against the user pool API.[^s1]
- **Identity pools** answer "which AWS resources may this user reach?". An identity pool exchanges a trusted token — from a user pool, a social provider, a SAML or OIDC provider, or your own developer-authenticated scheme — for short-lived credentials, which Cognito obtains by calling AWS STS `AssumeRoleWithWebIdentity`.[^s5] It can also give **unauthenticated guests** narrowly scoped credentials and elevate them after sign-in. Roles can be fixed for all authenticated users or chosen from token claims, and claims can be mapped to IAM session tags for attribute-based access control, for example limiting S3 access to objects tagged with the user's attribute.[^s5]

A common pattern is to authenticate with a user pool and then trade its token at an identity pool for AWS credentials; either component also works alone.[^s1]

User pools come in three **feature plans**. **Lite** covers basic sign-up and sign-in, federation, Lambda triggers and the classic hosted UI. **Essentials** — the default for new pools — adds managed login, passwordless one-time codes, passkey (FIDO2) sign-in, email MFA, access-token customization and session management. **Plus** adds threat protection (compromised-credential checks and risk-based sign-in evaluation) plus exportable user-activity and risk logs. The plan is set with the `UserPoolTier` parameter.[^s2]

## Quotas, pricing and gotchas

Quotas apply per account per Region and API-rate categories are shared across all user pools in that Region (as of 2026-11). Defaults include `UserAuthentication` 120 RPS (adjustable), `UserCreation` 50 RPS (adjustable), `UserFederation` 25 RPS (adjustable) and `ClientAuthentication` (client-credentials token requests) 150 RPS, while `UserAccountRecovery` (30 RPS) and `UserUpdate` (25 RPS) are not adjustable.[^s3] Resource defaults include 1,000 user pools per Region, 1,000 app clients per pool, 300 identity providers per pool and 40 million users per pool (all adjustable), plus fixed limits of 50 custom attributes per pool and 4 custom domains per Region.[^s3] ID and access tokens can be valid for 5 minutes to 1 day and refresh tokens for 1 hour to 3,650 days.[^s3] Custom attributes cannot be added beyond the fixed cap, so plan the schema early.

Pricing is per monthly active user (as of 2026-11, list prices on the Cognito pricing page): Lite and Essentials include a free tier of 10,000 MAUs per month per account or organization that does not expire after 12 months, Essentials is $0.015 per MAU above that, and Plus is $0.020 per MAU with no free tier. Users who sign in through SAML or OIDC federation get 50 free MAUs, then $0.015 each.[^s4] Machine-to-machine token requests have no free tier (for example $0.00225 per request in US East (N. Virginia)), identity pools are free, and SMS and email messages are billed by Amazon SNS and Amazon SES.[^s4] The pricing free tier does not apply in AWS GovCloud (US).[^s4] Some user pool features, such as MFA and custom authentication flows, do not apply to federated users.[^s1]

## Recent changes

In June 2026 AWS launched **multi-Region replication**, an Essentials and Plus add-on that keeps a near-real-time standby copy of a user pool — users, credentials, configuration and federation settings — in another Region, so signed-in users keep their sessions and registered users can sign in after a failover.[^s7] It reached the AWS GovCloud (US) Regions in October 2026 and requires a multi-Region AWS KMS customer managed key there.[^s6] From November 2026 the Plus plan carries a 99.99% monthly uptime SLA (Essentials and Lite remain 99.9%), and managed login gained admin session listing, revocation and caps.[^s6] Earlier additions include refresh-token rotation (April 2025) and AWS WAF rules for managed-login app clients (June 2025).[^s6]

## When to use it (and when not)

Use Cognito when your application needs its own sign-up, sign-in and token issuance for customers or partners, when an API needs OAuth 2.0 scopes, or when mobile and browser clients need direct, least-privilege access to AWS services.[^s1][^s5] For employees signing in to AWS accounts and business applications, use IAM Identity Center instead. For back-end workloads, use IAM roles.

## Connections

- **Broader: AWS security, identity and compliance services** (`kt:aws-security-services`).
- **Related:** AWS AppSync (`kt:aws-appsync`) and Amazon API Gateway (`kt:amazon-api-gateway`) commonly authorize requests with user pool tokens; identity pool session tags implement **attribute-based access control** (`kt:attribute-based-access-control`); multi-Region replication supports **multi-Region architecture** (`kt:multi-region-architecture`).[^s5][^s7]
- **Depends on AWS STS** (`kt:aws-sts`), which issues identity pool credentials.[^s5]
- **Contrasts with IAM Identity Center** (`kt:iam-identity-center`): customer identity versus workforce identity.[^s1]
- **Integrates with AWS Lambda** (`kt:aws-lambda`) through user pool triggers and token customization, and is **secured by AWS WAF** (`kt:aws-waf`) web ACLs on managed login.[^s2][^s6]
- **Governed by** Cognito quotas (`kt:cognito-quotas`) and **scoped to AWS Regions** (`kt:aws-regions`), since quotas and pools are Regional.[^s3]
- **Complies with FedRAMP** (`kt:fedramp-on-aws`): Amazon Cognito is listed in scope for US East/West and AWS GovCloud (US).[^s8]

## Sources

[^s1]: Amazon Web Services, "What is Amazon Cognito?". https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "User pool feature plans". https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-sign-in-feature-plans.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Quotas in Amazon Cognito". https://docs.aws.amazon.com/cognito/latest/developerguide/quotas.html (accessed 2026-11-17)
[^s4]: Amazon Web Services, "Amazon Cognito - Pricing". https://aws.amazon.com/cognito/pricing/ (accessed 2026-11-17)
[^s5]: Amazon Web Services, "Amazon Cognito identity pools". https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html (accessed 2026-11-17)
[^s6]: Amazon Web Services, "What's new in Amazon Cognito". https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-whats-new.html (accessed 2026-11-17)
[^s7]: Amazon Web Services, What's New, "Amazon Cognito now supports multi-Region replication" (2026-06-04). https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-cognito-multi-region/ (accessed 2026-11-17)
[^s8]: Amazon Web Services, "FedRAMP - Services in Scope". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-17)
