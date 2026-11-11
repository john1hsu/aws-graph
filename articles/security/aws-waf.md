---
id: kt:aws-waf
type: Service
title: AWS WAF
aliases: [AWS Web Application Firewall, WAF, AWS WAFV2]
domain: security
summary: >-
  AWS's managed layer 7 web application firewall: a web ACL (shown as a protection pack in the new
  console) of rules and rule groups inspects HTTP(S) requests sent to CloudFront, Application Load
  Balancers, API Gateway and other front doors, and allows, blocks, counts or challenges them.
verified: 2026-11-11
status: current
broader: [kt:aws-security-services]
related: [kt:aws-shield, kt:aws-firewall-manager]
relations:
  - {predicate: integratesWith, object: kt:amazon-cloudfront}
  - {predicate: integratesWith, object: kt:application-load-balancer}
  - {predicate: integratesWith, object: kt:amazon-api-gateway}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "How AWS WAF works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/waf/latest/developerguide/how-aws-waf-works.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Resources that you can protect with AWS WAF"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/waf/latest/developerguide/how-aws-waf-works-resources.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "AWS WAF quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/waf/latest/developerguide/limits.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "AWS WAF Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/waf/pricing/
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Introducing the new console experience for AWS WAF"
    publisher: AWS Security Blog
    url: https://aws.amazon.com/blogs/security/introducing-the-new-console-experience-for-aws-waf
    type: official-blog
    accessed: 2026-11-11
  - id: s6
    title: "In-Scope AWS Services (AWS Certified Cloud Practitioner CLF-C02), French edition"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# AWS WAF

AWS WAF is a web application firewall that controls how protected AWS resources respond to HTTP(S) requests. You build a **web access control list (web ACL)**, associate it with one or more resources, and those resources forward each incoming request to AWS WAF, which evaluates it against the web ACL's rules.[^s1] Because it inspects request contents such as headers, query strings and bodies, it works at the application layer (layer 7) rather than on packets or ports.[^s1][^s3]

## How it works

A web ACL holds an ordered set of **rules**. Each rule pairs a statement (the inspection criteria, such as a source IP range, a header, a query string value or a regex match on the body) with an action. The actions are **Allow**, **Block**, **Count** (record a match without enforcing it, useful for testing new rules) and **CAPTCHA** or **Challenge**, which make the client prove it is a human or a standard browser. Every web ACL also has a **default action**, allow or block, applied to any request no rule has already allowed or blocked.[^s1]

Rules can be written inline or packaged into reusable **rule groups**. You can write your own, subscribe to **AWS Managed Rules**, or buy rule groups from AWS Marketplace sellers.[^s1] Processing cost is measured in **web ACL capacity units (WCUs)**: each rule consumes WCUs according to how complex its inspection is, and the totals cap what a web ACL can hold.[^s1][^s3]

Since June 2025 the console offers a new experience in which a web ACL is presented as a **protection pack**. You describe the application category and traffic sources, choose Recommended, Essentials or "You build it", and AWS WAF creates a web ACL with matching rules.[^s5] Protection packs change only how web ACLs are managed in the console; the API and the standard console still work with web ACLs directly. The new console also adds dashboards, including traffic-based rule recommendations drawn from the previous two weeks of allowed traffic and an AI traffic analysis view of bots and agents.[^s1][^s5] AWS reports that the new flow can cut implementation time by up to 80 percent; treat that as a vendor figure.[^s5]

## What it can protect

Regional web ACLs protect API Gateway REST APIs, Application Load Balancers, AppSync GraphQL APIs, Cognito user pools, App Runner services, Amazon Bedrock AgentCore Gateways and Verified Access instances. The web ACL and its supporting resources (IP sets, regex pattern sets, rule groups) must sit in the same Region as the protected resource.[^s2] CloudFront distributions use the **Global (CloudFront)** scope, which means creating the web ACL and everything it references in US East (N. Virginia), us-east-1; Amplify apps also require a CloudFront-scope web ACL.[^s2] A resource can have only one web ACL, while one web ACL can serve many resources. A web ACL attached to CloudFront cannot also be attached to other resource types.[^s2]

## Quotas, pricing and gotchas

Default quotas per account per Region, as of 2026-11: 100 web ACLs, 100 rule groups, 100 IP sets and 10 regex pattern sets. All of these can be raised through Service Quotas.[^s3] Fixed limits include 5,000 WCUs per web ACL and per rule group, 10,000 CIDR entries per IP set, and 10 rate-based rules per web ACL. Body inspection is fixed at 8 KB for ALB and AppSync. For CloudFront, API Gateway, Cognito, App Runner and Verified Access it defaults to 16 KB and can be raised to 64 KB.[^s3] A rule never sees body content past those limits, so oversize-body handling deserves explicit rules.[^s3]

Pay-as-you-go pricing, as listed on 2026-11 (AWS notes that rates may vary by Region): $5.00 per web ACL per month, $1.00 per rule or rule group per month, and $0.60 per million requests.[^s4] The base price covers 1,500 WCUs. Each extra 500 WCUs adds $0.20 per million requests, and inspecting bodies beyond the default size is also charged per million requests.[^s3][^s4] Intelligent threat features are billed on top. Bot Control at the Common level costs $10.00 per web ACL per month plus $1.00 per million requests after the first 10 million; Fraud Control (account takeover and account creation fraud prevention) is charged per request and costs far more. AWS recommends scope-down statements so these rule groups only evaluate the paths that need them.[^s4] Flat-rate plans are offered as an alternative to pay-as-you-go.[^s4]

## When to use it (and when not)

Use AWS WAF when you need request-level filtering in front of HTTP applications and APIs: OWASP-style injection patterns via managed rules, IP or geo allow and deny lists, per-client rate limiting, and bot or credential-stuffing defenses.[^s1][^s4] It does not replace security groups or network ACLs for network-layer controls. For expanded DDoS protection, AWS offers AWS Shield Advanced, which is documented alongside AWS WAF and AWS Firewall Manager; Shield Advanced subscribers get limited AWS WAF usage included.[^s1][^s4]

## Connections

AWS WAF is part of the AWS security services family (**broader**) and appears in the Security, Identity and Compliance list of in-scope services for the Cloud Practitioner exam (**coveredBy**).[^s6] It **integrates with** the front doors it protects, chiefly Amazon CloudFront at the edge, Application Load Balancers and Amazon API Gateway, and its regional web ACLs are **scoped to** individual AWS Regions, with CloudFront managed from us-east-1.[^s2] Its metrics and optional logs flow to Amazon CloudWatch (**monitoredBy**); the pricing includes an allowance of CloudWatch log volume per million requests.[^s4] It is **related to** AWS Shield (DDoS protection) and AWS Firewall Manager (central policy), which share its developer guide.[^s1]

## Sources
[^s1]: Amazon Web Services, "How AWS WAF works". https://docs.aws.amazon.com/waf/latest/developerguide/how-aws-waf-works.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Resources that you can protect with AWS WAF". https://docs.aws.amazon.com/waf/latest/developerguide/how-aws-waf-works-resources.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "AWS WAF quotas". https://docs.aws.amazon.com/en_us/waf/latest/developerguide/limits.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "AWS WAF Pricing". https://aws.amazon.com/waf/pricing/ (accessed 2026-11-11)
[^s5]: AWS Security Blog, "Introducing the new console experience for AWS WAF" (2025-06-17). https://aws.amazon.com/blogs/security/introducing-the-new-console-experience-for-aws-waf (accessed 2026-11-11)
[^s6]: Amazon Web Services, "In-Scope AWS Services" for AWS Certified Cloud Practitioner (CLF-C02), French edition. https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-11)
