---
id: kt:api-gateway-quotas
type: Limit
title: Amazon API Gateway quotas
aliases: [API Gateway quotas, API Gateway limits, API Gateway service quotas, Amazon API Gateway limits, API Gateway throttling limits]
domain: serverless-and-integration
summary: >-
  The limits that shape Amazon API Gateway designs: a shared per-Region account throttle with a fixed
  burst bucket, fixed control-plane call rates, and separate per-API limit tables for REST, HTTP and
  WebSocket APIs covering timeouts, payload sizes, routes or resources, stages, authorizers and connections.
verified: 2026-11-20
status: current
broader: [kt:amazon-api-gateway]
related: [kt:aws-lambda, kt:lambda-quotas, kt:amazon-cloudfront, kt:application-load-balancer]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:appsync-quotas}
sources:
  - id: s1
    title: "Amazon API Gateway quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Quotas for configuring and running a REST API in API Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-execution-service-limits-table.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Quotas for configuring and running an HTTP API in API Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-quotas.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "Quotas for configuring and running a WebSocket in API Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-execution-service-websocket-limits-table.html
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "Throttle requests to your REST APIs for better throughput in API Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-request-throttling.html
    type: official-docs
    accessed: 2026-11-20
  - id: s6
    title: "Stream the integration response for your proxy integrations in API Gateway"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/apigateway/latest/developerguide/response-transfer-mode.html
    type: official-docs
    accessed: 2026-11-20
created: 2026-11-20
---

# Amazon API Gateway quotas

Amazon API Gateway enforces two kinds of limits. Account-level quotas apply per account and per Region and are shared by every API type, while per-API quotas are published in separate tables for REST, HTTP and WebSocket APIs.[^s1][^s2] A per-API quota that is raised applies only to the specific APIs named in the request, not to every API in the account.[^s2][^s3] Most designs hit throttling, integration timeouts or payload size first, so those are worth checking before choosing an API type.

## Account-level throttling

As of 2026-11, the default steady-state throttle is 10,000 requests per second per account per Region, shared across HTTP, REST, WebSocket and WebSocket callback APIs. Burst comes from a token bucket holding up to 5,000 requests.[^s1] The RPS figure can be raised through Service Quotas, but AWS sets the burst and customers cannot request a change to it.[^s1] Thirteen newer Regions, including Africa (Cape Town), Europe (Milan), Europe (Zurich), Israel (Tel Aviv) and Mexico (Central), default to 2,500 RPS with a 1,250 burst.[^s1]

For REST APIs, throttling is layered and applied in this order: per-client or per-method limits in a usage plan, per-method limits on a stage, the account limit for the Region, and finally an AWS Regional throttle that customers cannot change. Stage and usage-plan limits cannot exceed the account limit. Requests over the limit get HTTP 429, and AWS describes throttles and quotas as best-effort targets rather than guaranteed ceilings.[^s5] A usage plan can hold at most 20 method-level throttling settings for a stage.[^s2]

The control plane has its own fixed rates that cannot be increased: for example, one CreateDeployment every 5 seconds, one edge-optimized CreateRestApi or DeleteRestApi every 30 seconds, and one CreateDomainName every 30 seconds.[^s1] Pipelines that deploy many stages or APIs in parallel need backoff and serialization.[^s1]

## REST API limits

Defaults as of 2026-11, per account per Region unless noted:[^s2]

- **Fixed:** 600 Regional, 120 edge-optimized and 600 private APIs; 10,000 API keys; a 10 MB payload; 20,480 bytes of combined headers (8,000 bytes for private APIs); a 300 KB mapping template; a 1 MiB cached response; a 310-second idle connection timeout; and a 6 MB API definition for import or put.
- **Adjustable:** 300 resources and 10 stages per API; 120 public and 50 private custom domain names; 300 usage plans; 20 VPC links; 10 authorizers per API (through AWS Support); and an 8,192-character resource policy.
- **Integration timeout:** 50 ms to 29 seconds for all integration types. Regional and private APIs can go above 29 seconds, which may mean a lower Region-level throttle for the account; edge-optimized APIs cannot.[^s2]

Response streaming changes two of these limits. With a proxy integration set to STREAM, any REST endpoint type can stream for up to 15 minutes and go past the 10 MB response limit. The first 10 MB is not bandwidth-limited and anything after is capped at 2 MB/s. Streaming has an idle timeout of 5 minutes on Regional and private endpoints and 30 seconds on edge-optimized ones, and it cannot be used with caching, content encoding or VTL response mapping.[^s6]

## HTTP API limits

HTTP APIs allow 300 routes and 10 stages per API, both adjustable, but cap integrations at 300 per API, and neither the 30-second integration timeout nor the 10 MB payload can be raised.[^s3] The request line plus headers may total 10,240 bytes. Custom domains (120) and VPC links (10) are counted per account per Region, and a Lambda authorizer must respond within 10 seconds.[^s3] JWT authorizers must fetch the JSON Web Key Set within 1,500 ms.[^s3]

## WebSocket API limits

WebSocket APIs accept 500 new connections per second per account per Region (adjustable). There is no separate concurrent-connection quota: the ceiling follows from that rate and the 2-hour maximum connection duration.[^s4] Connections close after 10 idle minutes, and neither timeout can be changed. Frames are limited to 32 KB and messages to 128 KB, so larger messages must be split into frames, and an oversized frame closes the connection with code 1009. Integration timeouts are fixed at 50 ms to 29 seconds.[^s4]

## Gotchas and alternatives

The 29- or 30-second integration timeout, together with the 10 MB payload, is the most common reason to redesign. Options are a timeout increase on Regional REST APIs, response streaming, or an asynchronous pattern that returns at once and delivers the result later.[^s2][^s6] Because the account throttle is shared across API types in a Region, one noisy API can throttle others, so set stage and usage-plan limits for each tenant.[^s1][^s5] Fixed API-count limits also make "one API per customer" designs a poor fit.[^s2]

## Connections

These quotas are **broader** under Amazon API Gateway (`kt:amazon-api-gateway`). Adjustable rows are **configured via** Service Quotas (`kt:service-quotas`), and most are **scoped to** AWS Regions (`kt:aws-regions`).[^s1][^s2] They relate to AWS Lambda (`kt:aws-lambda`) and its own quotas (`kt:lambda-quotas`), because Lambda is the usual integration and its timeouts interact with API Gateway's.[^s2][^s6] Edge-optimized APIs run behind Amazon CloudFront (`kt:amazon-cloudfront`), which accounts for their shorter streaming idle timeout.[^s6] They **contrast with** AWS AppSync quotas (`kt:appsync-quotas`) for GraphQL front ends. Application Load Balancer (`kt:application-load-balancer`) is a related HTTP front end for workloads that outgrow API Gateway's timeout or payload limits.

## Sources

[^s1]: Amazon Web Services, "Amazon API Gateway quotas". https://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "Quotas for configuring and running a REST API in API Gateway". https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-execution-service-limits-table.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "Quotas for configuring and running an HTTP API in API Gateway". https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-quotas.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "Quotas for configuring and running a WebSocket in API Gateway". https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-execution-service-websocket-limits-table.html (accessed 2026-11-20)
[^s5]: Amazon Web Services, "Throttle requests to your REST APIs for better throughput in API Gateway". https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-request-throttling.html (accessed 2026-11-20)
[^s6]: Amazon Web Services, "Stream the integration response for your proxy integrations in API Gateway". https://docs.aws.amazon.com/apigateway/latest/developerguide/response-transfer-mode.html (accessed 2026-11-20)
