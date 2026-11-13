---
id: kt:application-load-balancer
type: Service
title: Application Load Balancer
aliases: [ALB, AWS Application Load Balancer, Elastic Load Balancing Application Load Balancer]
domain: networking-and-content-delivery
summary: >-
  The layer-7 load balancer type in Elastic Load Balancing: it terminates HTTP, HTTPS and gRPC traffic,
  evaluates prioritized listener rules on hosts, paths, headers and other request attributes, and forwards
  each request to a target group of instances, IP addresses, containers or a Lambda function.
verified: 2026-11-13
status: current
broader: [kt:elastic-load-balancing, kt:aws-networking-and-content-delivery]
related: [kt:network-load-balancer]
relations:
  - {predicate: securedBy, object: kt:aws-waf}
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: alternativeTo, object: kt:amazon-api-gateway}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: governedBy, object: kt:elb-quotas}
sources:
  - id: s1
    title: "What is an Application Load Balancer? - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Quotas for your Application Load Balancers - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-limits.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "Elastic Load Balancing pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/elasticloadbalancing/classicloadbalancer/pricing
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "Authenticate users using an Application Load Balancer - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "Application Load Balancer - Choosing the right AWS service for your microservice endpoints (AWS Prescriptive Guidance)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/choosing-the-right-aws-service-for-your-microservice-endpoints/app-load-balancer.html
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "AWS Application Load Balancer launches Target Optimizer"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/aws-application-load-balancer-target-optimizer
    type: official-blog
    accessed: 2026-11-13
created: 2026-11-13
---

# Application Load Balancer

An Application Load Balancer (ALB) is the Elastic Load Balancing product that operates at the application layer, layer 7 of the OSI model.[^s1] It receives client requests, decides where each one should go by inspecting the request itself, and spreads traffic across healthy targets such as EC2 instances, containers and IP addresses in one or more Availability Zones.[^s1] Because routing decisions see the full HTTP request, one ALB can front many services and domains at once, which is why it is the usual entry point for microservice and container estates.[^s5]

## How it works

An ALB is built from four pieces. The **load balancer** is the client-facing endpoint; **listeners** wait for connections on a protocol and port; each listener holds a mandatory **default rule** plus optional **rules**; and **target groups** hold the registered targets that receive requests.[^s1] A rule has a priority, one or more conditions and one or more actions, and the listener evaluates rules in priority order until one matches.[^s1]

Conditions can match the host header (so one ALB serves several domains), the URL path, other HTTP headers, the HTTP method, query parameters and the client source IP.[^s1] Actions can forward to a target group, redirect to another URL, return a fixed custom response, or authenticate the user first.[^s1] Health checks are defined per target group, a single instance can sit in several target groups on different ports, and Amazon ECS can register a task on a dynamically chosen port.[^s1] Each target group picks its own routing algorithm, round robin by default or least outstanding requests.[^s1]

Forward actions can split traffic between weighted target groups. AWS Prescriptive Guidance uses this for blue/green and canary releases, for example sending 90% of a service's traffic to an EC2 fleet and 10% to a new Fargate version.[^s5] The same rule engine supports A/B tests, bot-versus-human segmentation and routing selected HTTP methods to new microservices while a monolith keeps the rest.[^s5]

## Built-in authentication

An ALB can sign users in before a request reaches your code. Listener rules support `authenticate-oidc` (any OpenID Connect provider) and `authenticate-cognito` (an Amazon Cognito user pool, which can federate social and SAML identity providers), but only on HTTPS listeners.[^s4] After login the load balancer keeps a session cookie (default session timeout 7 days) and passes user claims to targets in the `x-amzn-oidc-data` header, a JWT signed with ES256 that applications should verify, along with the load balancer ARN in its signer field.[^s4] Behaviour for unauthenticated requests is configurable: redirect to the IdP, allow without claims, or deny with HTTP 401 for single-page apps.[^s4]

Gotchas: users must finish login within 15 minutes, a fixed limit; claims plus access token over 11 KB produce an HTTP 500; and the load balancer reaches the IdP's token and user-info endpoints over IPv4 only, so internal ALBs may need a NAT gateway.[^s4]

## Quotas and pricing

As of 2026-11 the defaults per Region are 50 ALBs, 50 listeners and 100 rules (excluding defaults) per ALB, 25 certificates per ALB (excluding the default), and 1,000 targets per ALB, all adjustable.[^s2] Fixed limits include 100 target groups per ALB, 5 target groups per action, 5 condition values and 6 wildcards per rule, and a 64 KB ceiling on the entire request header.[^s2] For mutual TLS, an account gets 20 trust stores (adjustable), and at most 2 listeners per load balancer can run mTLS in verify mode.[^s2]

Pricing in US East (N. Virginia), as of 2026-11, is $0.0225 per ALB-hour plus $0.008 per LCU-hour.[^s3] One LCU covers 25 new connections per second, 3,000 active connections per minute (1,500 with mutual TLS), 1 GB per hour of processed bytes for EC2, container and IP targets (0.4 GB for Lambda targets), or 1,000 rule evaluations per second, and you pay on whichever dimension is highest; the first 10 rules are free.[^s3] Teams expecting a traffic spike can reserve LCUs ahead of time at the same $0.008 per reserved LCU-hour, with usage above the reservation billed normally.[^s3] The default quota for reserved LCUs per Region is 0, so a quota increase is needed first.[^s2]

## Recent changes

In November 2025 AWS added Target Optimizer, which caps how many concurrent requests each target receives (as low as one) using an AWS-provided agent on the target and a target control port on a new target group.[^s6] It suits compute-heavy work such as inference, is available in commercial, GovCloud (US) and China Regions, and drives up LCU usage.[^s6]

## When to use it (and when not)

Choose an ALB for HTTP and gRPC workloads that need content-based routing, TLS offload, user sign-in or weighted deployments across EC2, containers, Lambda or on-premises IP targets.[^s5][^s1] Because it works at layer 7, traffic that is not HTTP belongs on one of the other Elastic Load Balancing types, such as the layer-4 Network Load Balancer.[^s1] For APIs that need per-client throttling, API keys or request validation, compare it with Amazon API Gateway; an ALB charges by capacity rather than per request.[^s3][^s5]

## Connections

The ALB is one of the four load balancer types **within** Elastic Load Balancing.[^s1] It is **secured by** AWS WAF web ACLs attached to the load balancer and **monitored by** Amazon CloudWatch, which reports many metrics per target group.[^s1][^s5] It **integrates with** AWS Lambda, which can be the single target of a target group.[^s2] It is an **alternative to** Amazon API Gateway for HTTP entry points, and is **governed by** the Elastic Load Balancing quotas above.[^s5][^s2] Its sibling, the Network Load Balancer, is the Elastic Load Balancing option for traffic below layer 7.[^s1]

## Sources
[^s1]: Amazon Web Services, "What is an Application Load Balancer?". https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Quotas for your Application Load Balancers". https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-limits.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Elastic Load Balancing pricing". https://aws.amazon.com/elasticloadbalancing/classicloadbalancer/pricing (accessed 2026-11-13)
[^s4]: Amazon Web Services, "Authenticate users using an Application Load Balancer". https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html (accessed 2026-11-13)
[^s5]: Amazon Web Services, "Application Load Balancer", AWS Prescriptive Guidance: Choosing the right AWS service for your microservice endpoints. https://docs.aws.amazon.com/prescriptive-guidance/latest/choosing-the-right-aws-service-for-your-microservice-endpoints/app-load-balancer.html (accessed 2026-11-13)
[^s6]: Amazon Web Services, "AWS Application Load Balancer launches Target Optimizer" (What's New, 2025-11-20). https://aws.amazon.com/about-aws/whats-new/2025/11/aws-application-load-balancer-target-optimizer (accessed 2026-11-13)
