---
id: kt:amazon-verified-permissions
type: Service
title: Amazon Verified Permissions
aliases: [Verified Permissions, AVP, Amazon AVP]
domain: identity-and-access
summary: >-
  A managed, fine-grained authorization service for the applications you build: you store Cedar
  policies in a policy store, and your application asks the service at runtime whether a principal
  may perform an action on a resource, receiving an allow or deny decision.
verified: 2026-11-18
status: current
broader: [kt:aws-security-services]
related: [kt:amazon-cognito, kt:attribute-based-access-control, kt:role-based-access-control]
relations:
  - {predicate: integratesWith, object: kt:amazon-api-gateway}
  - {predicate: implements, object: kt:cedar-policy-language}
  - {predicate: contrastsWith, object: kt:aws-iam}
  - {predicate: governedBy, object: kt:verified-permissions-quotas}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is Amazon Verified Permissions? - Amazon Verified Permissions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/verifiedpermissions/latest/userguide/what-is-avp.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Amazon Verified Permissions policy stores - Amazon Verified Permissions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/verifiedpermissions/latest/userguide/policy-stores.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Quotas for Amazon Verified Permissions - Amazon Verified Permissions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/verifiedpermissions/latest/userguide/quotas.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Amazon Verified Permissions Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/verified-permissions/pricing/
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Amazon Verified Permissions reduces authorization request price by up to 97%"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/06/amazon-verified-permissions-reduces-price/
    type: official-blog
    accessed: 2026-11-18
  - id: s6
    title: "Amazon Verified Permissions now supports policy store aliases and named policies and policy templates"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/04/amazon-verified-permissions-policy-store
    type: official-blog
    accessed: 2026-11-18
  - id: s7
    title: "Control access based on an identity's attributes with Verified Permissions - Amazon API Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-lambda-authorizer-verified-permissions.html
    type: official-docs
    accessed: 2026-11-18
  - id: s8
    title: "Amazon Verified Permissions multi-tenant design considerations - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/saas-multitenant-api-access-authorization/avp-design-considerations.html
    type: official-docs
    accessed: 2026-11-18
  - id: s9
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-18
  - id: s10
    title: "Cedar: A New Language for Expressive, Fast, Safe, and Analyzable Authorization (OOPSLA 2024)"
    publisher: arXiv
    url: https://arxiv.org/abs/2403.04651v2
    type: academic
    accessed: 2026-11-18
created: 2026-11-18
---

# Amazon Verified Permissions

Amazon Verified Permissions is a managed authorization service for the custom applications you build. Instead of scattering permission checks through business code, you externalize them: policies written in the **Cedar** policy language live in the service, and before performing an operation your application asks whether the request is authorized, proceeding on allow and returning an error on deny.[^s1] The service only decides authorization; it assumes the principal (a user or a machine) has already been authenticated by Amazon Cognito, an OpenID Connect provider or another solution, and it does not care where principals are managed.[^s1]

## How it works

The unit of organization is the **policy store**, a container for policies and policy templates that every authorization request names.[^s2] Each store has its own **schema** describing entity types, common types and actions; with validation turned on, any policy that references something the schema does not define is rejected.[^s2] Stores can also hold **policy templates**, and an **identity source** lets the store build principals and groups directly from Cognito user pool or OIDC tokens.[^s1][^s7] Applications call `IsAuthorized` (or `IsAuthorizedWithToken` when passing a token from an identity source), or the batch variants, and the service evaluates the store's Cedar policies to return a decision.[^s4] Policies can be managed in the console, through the API and SDKs, or as infrastructure as code with AWS CloudFormation and a CDK L2 construct.[^s1]

Cedar itself is open source, implemented in Rust, and designed to be readable, fast and analyzable; its authors modeled the language in Lean and proved key properties, so tools can, for example, confirm that refactoring a policy set does not change what it permits.[^s10] Its syntax supports role-, attribute- and relationship-based styles of access control.[^s10] As of 2026-11 the service runs Cedar 4.12.[^s1]

Recent additions (April 2026) are **policy store aliases**, so a multi-tenant application can address a store by a tenant-derived name instead of keeping a tenant-to-store-ID lookup table, and **named policies and templates** that replace system-generated IDs in day-to-day management.[^s6]

## Quotas, pricing and gotchas

Default quotas (as of 2026-11, per Region per account unless noted): 30,000 policy stores (adjustable); 40 policy templates per store (adjustable); exactly one identity source per store (not adjustable); 10 aliases per store; a 10,000-byte limit per policy and 100,000 bytes per schema (both adjustable); a 1 MB authorization request size (fixed); and at most 100 transitive parents per principal, action or resource (fixed).[^s3] Throughput defaults are 200 requests per second per policy store for `IsAuthorized` and `IsAuthorizedWithToken` and 30 for the batch APIs, all adjustable; `CreatePolicyStore` is fixed at 1 per second per account, which matters when onboarding many tenants at once.[^s3]

Pricing (us-east-1 page, which states the same prices in all Regions, as of 2026-11): single authorization requests cost $0.000005 each, i.e. $5 per million; batch requests are billed per API call at $0.00015 for the first 40 million a month, falling to $0.000075 for the next 60 million and $0.00004 beyond 100 million; policy management calls cost $0.00004 each.[^s4] The single-request price followed a cut of up to 97% that took effect on 2025-06-12.[^s5]

Gotchas: deletion protection is on by default for stores created in the console but off for stores created through the API or SDK, so automation should set it explicitly.[^s2] The API Gateway integration supports only REST APIs with Regional or edge-optimized endpoints, and API Gateway caches the authorizer's result for 120 seconds by default, so policy changes may take that long to bite for cached callers.[^s7]

## When to use it (and when not)

Use Verified Permissions for authorization inside your own application: which customer can view which document, which clinician can edit which record, which tenant user can call which API route.[^s1] It is not a replacement for IAM, which governs access to AWS resources themselves. For SaaS, AWS Prescriptive Guidance describes two models, a **policy store per tenant** for stronger isolation, or **one shared store** for simpler operations, and notes that the choice affects isolation, resiliency, onboarding effort and observability.[^s8] The user guide recommends one store per application or per tenant, using schema namespaces such as `TenantA::User` to keep types distinct.[^s2]

## Connections

- **Broader: AWS security services** (`kt:aws-security-services`) — the identity, authorization and protection services of AWS.[^s1]
- **Implements the Cedar policy language** (`kt:cedar-policy-language`), whose design supports **RBAC** (`kt:role-based-access-control`) and **ABAC** (`kt:attribute-based-access-control`) patterns.[^s10]
- **Related: Amazon Cognito** (`kt:amazon-cognito`) — user pools can be an identity source that supplies principals and groups.[^s1][^s7]
- **Integrates with Amazon API Gateway** (`kt:amazon-api-gateway`): an API-linked store generates a Lambda authorizer for REST APIs.[^s7]
- **Contrasts with AWS IAM** (`kt:aws-iam`): IAM authorizes calls to AWS; Verified Permissions authorizes calls within your application.[^s1]
- **Governed by** its service quotas (`kt:verified-permissions-quotas`) and **scoped to AWS Regions** (`kt:aws-regions`), since stores and limits are per Region.[^s3]
- **Complies with FedRAMP** (`kt:fedramp-on-aws`): AWS lists the service in scope for both the East/West and GovCloud (US) authorizations.[^s9]

## Sources

[^s1]: Amazon Web Services, "What is Amazon Verified Permissions?". https://docs.aws.amazon.com/verifiedpermissions/latest/userguide/what-is-avp.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Amazon Verified Permissions policy stores". https://docs.aws.amazon.com/verifiedpermissions/latest/userguide/policy-stores.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Quotas for Amazon Verified Permissions". https://docs.aws.amazon.com/verifiedpermissions/latest/userguide/quotas.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Amazon Verified Permissions Pricing". https://aws.amazon.com/verified-permissions/pricing/ (accessed 2026-11-18)
[^s5]: Amazon Web Services, What's New, "Amazon Verified Permissions reduces authorization request price by up to 97%" (2025-06-12). https://aws.amazon.com/about-aws/whats-new/2025/06/amazon-verified-permissions-reduces-price/ (accessed 2026-11-18)
[^s6]: Amazon Web Services, What's New, "Amazon Verified Permissions now supports policy store aliases and named policies and policy templates" (2026-04-06). https://aws.amazon.com/about-aws/whats-new/2026/04/amazon-verified-permissions-policy-store (accessed 2026-11-18)
[^s7]: Amazon Web Services, "Control access based on an identity's attributes with Verified Permissions", Amazon API Gateway Developer Guide. https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-lambda-authorizer-verified-permissions.html (accessed 2026-11-18)
[^s8]: Amazon Web Services, "Amazon Verified Permissions multi-tenant design considerations", AWS Prescriptive Guidance. https://docs.aws.amazon.com/prescriptive-guidance/latest/saas-multitenant-api-access-authorization/avp-design-considerations.html (accessed 2026-11-18)
[^s9]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-18)
[^s10]: arXiv, "Cedar: A New Language for Expressive, Fast, Safe, and Analyzable Authorization", OOPSLA 2024 (arXiv extended version). https://arxiv.org/abs/2403.04651v2 (accessed 2026-11-18)
