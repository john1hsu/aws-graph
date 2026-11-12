---
id: kt:aws-sts
type: Service
title: AWS Security Token Service
aliases: [AWS STS, STS, Security Token Service]
domain: identity-and-access
summary: >-
  The AWS web service that issues short-lived, scoped temporary security credentials (access key,
  secret key and session token) for assumed IAM roles, federated users and MFA-protected sessions,
  so people and workloads can reach AWS without long-term access keys.
verified: 2026-11-12
status: current
broader: [kt:aws-iam]
related: [kt:iam-roles, kt:iam-identity-center, kt:iam-policy-evaluation-logic, kt:permissions-boundaries]
relations:
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: governedBy, object: kt:iam-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "Temporary security credentials in IAM - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Compare AWS STS credentials - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_sts-comparison.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "AssumeRole - AWS Security Token Service API Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "AWS STS Regions and endpoints - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_region-endpoints.html
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "Manage AWS STS in an AWS Region - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html
    type: official-docs
    accessed: 2026-11-12
  - id: s6
    title: "How to use Regional AWS STS endpoints"
    publisher: AWS Security Blog
    url: https://aws.amazon.com/blogs/security/how-to-use-regional-aws-sts-endpoints/
    type: official-blog
    accessed: 2026-11-12
  - id: s7
    title: "AssumeRoot - AWS Security Token Service API Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoot.html
    type: official-docs
    accessed: 2026-11-12
  - id: s8
    title: "Simplify access to external services using AWS IAM Outbound Identity Federation"
    publisher: AWS News Blog
    url: https://aws.amazon.com/blogs/aws/simplify-access-to-external-services-using-aws-iam-outbound-identity-federation/
    type: official-blog
    accessed: 2026-11-12
  - id: s9
    title: "AWS STS now supports cross-partition federation for AWS identities"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/11/aws-sts-cross-partition/
    type: official-blog
    accessed: 2026-11-12
  - id: s10
    title: "AWS Certified Solutions Architect - Associate (SAA-C03) Exam Guide"
    publisher: Amazon Web Services
    url: https://d1.awsstatic.com/training-and-certification/docs-sa-assoc/AWS-Certified-Solutions-Architect-Associate_Exam-Guide_C03.pdf
    type: official-learning
    accessed: 2026-11-12
  - id: s11
    title: "What is IAM? - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html
    type: official-docs
    accessed: 2026-11-12
created: 2026-11-12
---

# AWS Security Token Service

AWS Security Token Service (AWS STS) is the service that generates **temporary security credentials**: an access key ID, a secret access key and a session token that work almost exactly like long-term IAM access keys but expire after minutes to hours.[^s1] Because they are generated on request rather than stored with an identity, applications do not need embedded keys, credentials never need explicit revocation, and people can reach AWS without an IAM user of their own. STS is the mechanism underneath IAM roles, cross-account access, SAML and OIDC federation, and the credentials that EC2 instances receive from instance profiles.[^s1] Like IAM itself, STS is offered at no additional charge; you pay only for the services the credentials are used with.[^s11]

## How it works

Each STS API issues credentials under different rules:

- **AssumeRole** — called by an IAM user or role with existing credentials, usually for cross-account access or MFA-protected elevation. Sessions last 15 minutes up to the role's maximum session duration (1 hour by default).[^s2]
- **AssumeRoleWithSAML / AssumeRoleWithWebIdentity** — unsigned calls that exchange a SAML 2.0 assertion or an OIDC JSON Web Token from a trusted identity provider for role credentials, with the same 15-minute minimum and 1-hour default.[^s2]
- **GetSessionToken** — lets an IAM user obtain MFA-authenticated credentials (15 minutes to 36 hours, default 12 hours; the root user is capped at 1 hour); it does not accept session policies.[^s2]
- **GetFederationToken** — lets an IAM user broker custom federated sessions; the resulting credentials cannot call IAM through the API or other STS operations except GetCallerIdentity.[^s2]

On AssumeRole, `DurationSeconds` ranges from 900 to 43,200 seconds, and a request above the role's configured maximum (settable from 1 to 12 hours) fails.[^s3] Callers can pass **session policies** to narrow a session: the effective permissions are the intersection of the role's identity-based policies and the session policies, so they can restrict but never expand access.[^s3] **Session tags**, **source identity** and an **external ID** (which guards cross-account roles against the confused-deputy problem) are also set at assume time, and transitive tags and source identity persist through a chain of role sessions.[^s3]

Newer STS operations extend the model. **AssumeRoot** gives an AWS Organizations management or delegated administrator account a 15-minute (900-second maximum) session on a member account, scoped by one of five AWS managed task policies such as deleting root credentials or unlocking a misconfigured S3 bucket policy; it requires centralized root access and a Regional endpoint.[^s7] **GetWebIdentityToken** (outbound identity federation, launched November 2025) issues signed JWTs that let AWS workloads authenticate to external services, with a lifetime of 60 to 3,600 seconds and a 300-second default.[^s8] Since November 2026 the same tokens can be exchanged through AssumeRoleWithWebIdentity for a role in a different AWS partition, at no additional charge.[^s9]

## Quotas, endpoints and gotchas

Limits worth knowing (as of 2026-11, from the API reference): up to 10 managed session policy ARNs plus one inline policy, together no more than 2,048 plaintext characters; up to 50 session tags with keys up to 128 and values up to 256 characters.[^s3] Policies and tags are packed into the session token, so a request can fail with `PackedPolicyTooLarge` even when each individual limit is met; the `SessionTokenUtilization` response field shows how close you are.[^s3] **Role chaining** — using one role's credentials to assume another — caps the new session at 1 hour regardless of the role's maximum.[^s3]

The legacy global endpoint, `sts.amazonaws.com`, historically ran only in US East (N. Virginia). Since April 2025, requests to it from Regions enabled by default are served locally when DNS is resolved by the Amazon DNS server, but requests from opt-in Regions or through other resolvers still go to N. Virginia, and the condition key `aws:RequestedRegion` always reads `us-east-1` for them.[^s4] By default the global endpoint issues version 1 tokens that do not work in opt-in Regions such as Asia Pacific (Hong Kong); Regional endpoints issue tokens valid in every Region.[^s5] AWS therefore recommends Regional endpoints: set `sts_regional_endpoints = regional` (newer SDKs and AWS CLI v2 already default to it), call the endpoint in the Region where the workload runs without cross-Region fallback, and use an interface VPC endpoint to keep STS traffic off the internet.[^s6] STS can be deactivated per Region in default Regions but is always on in opt-in Regions once they are enabled.[^s5]

## When to use it (and when not)

Prefer STS-issued credentials to IAM user access keys: roles for workloads and cross-account access, federation for people; most teams consume them indirectly through instance profiles, execution roles or IAM Identity Center.[^s1] Call the APIs directly when you need session policies, session tags, an external ID, or MFA-gated elevation.[^s3]

## Connections

- **Broader: AWS IAM** (`kt:aws-iam`) — STS is the credential-issuing half of IAM's identity model.[^s1]
- **Related:** **IAM roles** (`kt:iam-roles`) are what AssumeRole* calls assume; **IAM Identity Center** (`kt:iam-identity-center`) federates the workforce onto role sessions; **policy evaluation logic** (`kt:iam-policy-evaluation-logic`) and **permissions boundaries** (`kt:permissions-boundaries`) define how session policies intersect with other policy types.[^s2][^s3]
- **Integrates with AWS Organizations** (`kt:aws-organizations`) through AssumeRoot for centralized root access.[^s7]
- **Monitored by AWS CloudTrail** (`kt:aws-cloudtrail`): Regional requests log in their own Region, global-endpoint requests in us-east-1.[^s4][^s6]
- **Scoped to AWS Regions** (`kt:aws-regions`) through per-Region endpoints and activation; **governed by** the IAM and STS quotas (`kt:iam-quotas`).[^s5][^s3]
- **Covered by** the SAA-C03 exam, whose Domain 1 lists STS for role-based access, role switching and cross-account access.[^s10]

## Sources

[^s1]: Amazon Web Services, "Temporary security credentials in IAM". https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Compare AWS STS credentials". https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_sts-comparison.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "AssumeRole", AWS STS API Reference. https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, "AWS STS Regions and endpoints". https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_region-endpoints.html (accessed 2026-11-12)
[^s5]: Amazon Web Services, "Manage AWS STS in an AWS Region". https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html (accessed 2026-11-12)
[^s6]: AWS Security Blog, "How to use Regional AWS STS endpoints" (updated 2025-07-10). https://aws.amazon.com/blogs/security/how-to-use-regional-aws-sts-endpoints/ (accessed 2026-11-12)
[^s7]: Amazon Web Services, "AssumeRoot", AWS STS API Reference. https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoot.html (accessed 2026-11-12)
[^s8]: AWS News Blog, "Simplify access to external services using AWS IAM Outbound Identity Federation" (2025-11-19). https://aws.amazon.com/blogs/aws/simplify-access-to-external-services-using-aws-iam-outbound-identity-federation/ (accessed 2026-11-12)
[^s9]: Amazon Web Services, What's New, "AWS STS now supports cross-partition federation for AWS identities" (2026-11-09). https://aws.amazon.com/about-aws/whats-new/2026/11/aws-sts-cross-partition/ (accessed 2026-11-12)
[^s10]: Amazon Web Services, "AWS Certified Solutions Architect - Associate (SAA-C03) Exam Guide". https://d1.awsstatic.com/training-and-certification/docs-sa-assoc/AWS-Certified-Solutions-Architect-Associate_Exam-Guide_C03.pdf (accessed 2026-11-12)
[^s11]: Amazon Web Services, "What is IAM?". https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html (accessed 2026-11-12)
