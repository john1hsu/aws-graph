---
id: kt:amazon-api-gateway
type: Service
title: Amazon API Gateway
aliases: [API Gateway, AWS API Gateway, Amazon API GW]
domain: serverless-and-integration
summary: >-
  AWS's managed service for creating, publishing, securing and monitoring REST, HTTP and WebSocket
  APIs that front Lambda functions, other AWS services or HTTP backends, handling throttling,
  authorization and versioning for you.
verified: 2026-11-11
status: current
broader: [kt:aws-serverless-services, kt:aws-networking-and-content-delivery]
relations:
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: alternativeTo, object: kt:application-load-balancer}
  - {predicate: securedBy, object: kt:aws-waf}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: governedBy, object: kt:api-gateway-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-developer-associate}
sources:
  - id: s1
    title: "What is Amazon API Gateway?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Choose between REST APIs and HTTP APIs"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-vs-rest.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Amazon API Gateway quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/us_en/apigateway/latest/developerguide/limits.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Quotas for configuring and running a REST API in API Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-execution-service-limits-table
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Quotas for configuring and running an HTTP API in API Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-quotas.html
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "Amazon API Gateway pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/api-gateway/pricing/
    type: official-docs
    accessed: 2026-11-11
  - id: s7
    title: "Amazon API Gateway - Choosing the right AWS service for your microservice endpoints"
    publisher: Amazon Web Services (AWS Prescriptive Guidance)
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/choosing-the-right-aws-service-for-your-microservice-endpoints/api-gateway.html
    type: official-docs
    accessed: 2026-11-11
  - id: s8
    title: "Amazon API Gateway adds MCP proxy support"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/api-gateway-mcp-proxy-support
    type: official-blog
    accessed: 2026-11-11
  - id: s9
    title: "Amazon API Gateway now supports mutual TLS for backend integrations"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-api-gateway-mutual-tls-backend/
    type: official-blog
    accessed: 2026-11-11
  - id: s10
    title: "In-Scope AWS Services - AWS Certified Developer - Associate"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/dva-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# Amazon API Gateway

Amazon API Gateway is AWS's managed service for creating, publishing, maintaining, monitoring and securing APIs at any scale. AWS describes it as a front door through which client applications reach business logic or data running on Amazon EC2, AWS Lambda or any web application. The service takes on traffic management, authorization and access control, monitoring and API version management for up to hundreds of thousands of concurrent calls.[^s1]

## How it works

API Gateway offers three API types. **REST APIs** and **HTTP APIs** are both stateless, HTTP-based APIs that implement standard methods such as GET, POST, PUT, PATCH and DELETE. **WebSocket APIs** follow RFC 6455 and keep a stateful, full-duplex connection, routing each incoming message by its content.[^s1] Each API connects routes or methods to *integrations*: Lambda functions (including streamlined proxy integrations), other AWS services, public HTTP endpoints, or private resources reached through a VPC link to a Network or Application Load Balancer.[^s1][^s2] Callers can be authenticated with IAM, Lambda authorizers or Amazon Cognito user pools. Changes can be rolled out with canary deployments, APIs can be defined as CloudFormation resources, and calls are logged to CloudTrail and CloudWatch, with AWS WAF and AWS X-Ray integration available.[^s1]

REST APIs have more features and HTTP APIs cost less. Only REST APIs offer edge-optimized and private endpoints, API keys and per-client usage throttling, caching, request validation, body transformation, mock integrations, response streaming, resource policies, AWS WAF, X-Ray tracing and execution logs. Only HTTP APIs offer native JWT authorizers, automatic deployments and AWS Cloud Map private integrations. Both support Regional endpoints, mutual TLS on the client side, custom domains and CORS.[^s2]

Recent changes (as of 2026-11): on 2025-12-02 API Gateway added an MCP proxy that exposes existing REST APIs to AI agents through Amazon Bedrock AgentCore Gateway. It launched in nine Regions where AgentCore is available.[^s8] On 2026-09-08 REST APIs gained mutual TLS toward backends: API Gateway can present an ACM-managed certificate, imported or issued by AWS Private CA, instead of only a self-generated one. That feature covers all commercial Regions and the AWS GovCloud (US) Regions.[^s9]

## Quotas, pricing and gotchas

Account-level throttling (per account, per Region, as of 2026-11) is 10,000 requests per second shared by HTTP, REST and WebSocket APIs. Burst comes from a token bucket holding up to 5,000 requests. The RPS quota is adjustable through Service Quotas, but AWS sets the burst. Thirteen newer Regions, including Cape Town, Milan, Zurich and Mexico (Central), default to 2,500 RPS with a 1,250 burst.[^s3] Control-plane calls are also rate-limited and cannot be raised, for example one CreateDeployment every 5 seconds.[^s3]

For REST APIs, the integration timeout ranges from 50 ms to 29 seconds. You can raise it for Regional and private APIs (possibly at the cost of a lower Region throttle) but not for edge-optimized ones. Payloads are capped at 10 MB. An account gets 600 Regional and 120 edge-optimized APIs per Region and 600 private APIs, all fixed, plus 300 resources per API (adjustable).[^s4] HTTP APIs have a fixed 30-second integration timeout and a 10 MB payload limit. They default to 300 routes and 10 stages per API, both adjustable.[^s5]

Pricing (as of 2026-11, US East example rates): HTTP APIs cost $1.00 per million requests for the first 300 million and $0.90 after that, metered in 512 KB increments. REST APIs cost $3.50 per million for the first 333 million, falling to $2.80 and then $2.38 at higher volume. WebSocket APIs cost $1.00 per million messages (32 KB increments) plus $0.25 per million connection minutes. A 1.6 GB REST cache costs $0.038 per hour. The developer portal has a $125 monthly charge. New customers get a 12-month free tier of 1 million REST calls, 1 million HTTP calls, 1 million WebSocket messages and 750,000 connection minutes a month.[^s6]

Gotcha: a single client can exhaust the shared Region throttle for every API in the account unless you use REST API usage plans to throttle per client.[^s3][^s2]

## When to use it (and when not)

AWS Prescriptive Guidance recommends API Gateway when you need full API management: a single governed entry point, partner access with API keys or OAuth, throttling to protect backends from surges, WebSocket links to IoT devices, or webhook receivers. It points to Application Load Balancer and Lambda function URLs as the simpler alternatives.[^s7] Within API Gateway, choose a REST API when you need API keys, per-client throttling, request validation, AWS WAF or private endpoints. Otherwise a cheaper HTTP API is usually enough.[^s2] Long-running requests suit neither type, because integrations time out at about 30 seconds unless you raise the REST timeout.[^s4][^s5]

## Connections

- **Broader:** AWS serverless services, where API Gateway is the app-facing part paired with Lambda.[^s1] The DVA-C02 exam guide lists it under Networking and Content Delivery.[^s10]
- **Integrates with AWS Lambda**, through Lambda proxy integrations and Lambda authorizers.[^s1]
- **Alternative to Application Load Balancer**, which Prescriptive Guidance names as a lighter endpoint option. ALB can also sit behind API Gateway as a private integration.[^s7][^s2]
- **Secured by AWS WAF** (REST APIs) and **monitored by Amazon CloudWatch** metrics and logs.[^s1][^s2]
- **Governed by API Gateway quotas** (`kt:api-gateway-quotas`).[^s3]
- **Covered by AWS Certified Developer – Associate** (DVA-C02).[^s10]

## Sources
[^s1]: Amazon Web Services, "What is Amazon API Gateway?". https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Choose between REST APIs and HTTP APIs". https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-vs-rest.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Amazon API Gateway quotas". https://docs.aws.amazon.com/us_en/apigateway/latest/developerguide/limits.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Quotas for configuring and running a REST API in API Gateway". https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-execution-service-limits-table (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Quotas for configuring and running an HTTP API in API Gateway". https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-quotas.html (accessed 2026-11-11)
[^s6]: Amazon Web Services, "Amazon API Gateway pricing". https://aws.amazon.com/api-gateway/pricing/ (accessed 2026-11-11)
[^s7]: Amazon Web Services, AWS Prescriptive Guidance, "Amazon API Gateway" (Choosing the right AWS service for your microservice endpoints). https://docs.aws.amazon.com/prescriptive-guidance/latest/choosing-the-right-aws-service-for-your-microservice-endpoints/api-gateway.html (accessed 2026-11-11)
[^s8]: Amazon Web Services, What's New, "Amazon API Gateway adds MCP proxy support" (2025-12-02). https://aws.amazon.com/about-aws/whats-new/2025/12/api-gateway-mcp-proxy-support (accessed 2026-11-11)
[^s9]: Amazon Web Services, What's New, "Amazon API Gateway now supports mutual TLS for backend integrations" (2026-09-08). https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-api-gateway-mutual-tls-backend/ (accessed 2026-11-11)
[^s10]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Developer - Associate". https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/dva-02-in-scope-services.html (accessed 2026-11-11)
