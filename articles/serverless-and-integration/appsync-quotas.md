---
id: kt:appsync-quotas
type: Limit
title: AWS AppSync quotas
aliases: [AppSync quotas, AWS AppSync limits, AppSync limits, AppSync service quotas, AppSync request token quota]
domain: serverless-and-integration
summary: >-
  The per-account, per-Region limits on AWS AppSync: a request-token rate that meters GraphQL and
  Event API traffic by the compute each request consumes, adjustable real-time message and connection
  rates, and mostly fixed per-API limits on execution time, payload, code and schema size.
verified: 2026-11-20
status: current
broader: [kt:aws-appsync]
related: [kt:aws-lambda, kt:amazon-eventbridge]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:api-gateway-quotas}
sources:
  - id: s1
    title: "AWS AppSync endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/appsync.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Using CloudWatch to monitor and log GraphQL API data - AWS AppSync GraphQL"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/appsync/latest/devguide/monitoring.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Configuring GraphQL run complexity, query depth, and introspection with AWS AppSync - AWS AppSync GraphQL"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/appsync/latest/devguide/configuration-limits.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "AWS AppSync increases existing service quota and adds subscription service quotas"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2024/04/aws-appsync-increases-service-quota-adds-subscription/
    type: official-blog
    accessed: 2026-11-20
created: 2026-11-20
---

# AWS AppSync quotas

AWS AppSync publishes one quota table that is split into three groups: limits that apply to all API types, limits for Event APIs, and limits for GraphQL APIs. Every quota applies per account in each supported Region, and only a minority can be raised through Service Quotas; the rest are hard limits that shape how resolvers, schemas and real-time fan-out have to be designed.[^s1] The central idea is the **request token**: AppSync throttles on tokens consumed per second rather than on raw request counts.[^s1][^s2]

## Request tokens: the throughput quota

A token is a unit of work. A request that uses up to 1,500 KB-seconds of combined memory and vCPU time costs one token, and heavier requests cost more tokens, rounded up; AWS's example is a 3,350 KB-second request costing three tokens.[^s2] Most requests consume a single token.[^s2] As of 2026-11 the default GraphQL token rate is 10,000 per second in us-east-1, us-east-2, us-west-2, ap-northeast-1, ap-northeast-2, ap-south-1, ap-southeast-1, ap-southeast-2, eu-central-1, eu-west-1 and eu-west-2, and 5,000 per second in other supported Regions; it is adjustable.[^s1] Event APIs have a separate, adjustable token quota of 2,000 per second, where tokens also count the number of 5 kB messages published.[^s1] These values date from April 2024, when AWS raised the GraphQL default from 2,000 and added the real-time message quotas described below.[^s4]

Because tokens track resource use, effective requests per second fall as resolvers get heavier: at an average of two tokens per request, a 10,000-token account tops out around 5,000 requests per second.[^s2] The `TokensConsumed` CloudWatch metric, the `x-amzn-appsync-TokensConsumed` response header and verbose request logs show the cost of each operation, which is the evidence to bring to a quota increase request.[^s2]

## Real-time and connection rates

Subscriptions and Event APIs have their own rate quotas, all adjustable, with the same defaults for both API types: 2,000 connection requests per second per API, 10,000 inbound messages (or events) per second per API, and 1,000,000 outbound messages per second per API, where one outbound message is 5 KB of delivered payload.[^s1][^s4] Fixed limits sit alongside them: 240 KB per subscription message, 100 subscription invalidation requests per second per account, and, for Event APIs, 25 publish requests per second per client connection, five events per publish batch and a 1.2 MB publish payload.[^s1] A client connection can hold 200 subscriptions by default (adjustable). An Event API channel can have at most five segments of up to 50 characters each.[^s1] When the inbound or outbound rate is breached, CloudWatch records `InboundMessageDelayed` or `InboundMessageDropped`; throttled connects and subscribes surface as `ConnectClientError` and `SubscribeClientError`.[^s2]

## Fixed per-API limits

Several limits cannot be changed, so they belong in design reviews rather than support tickets (values as of 2026-11, same in every Region):[^s1]

- 30-second maximum request execution time.[^s1]
- 32 KB of APPSYNC_JS code per handler, resolver or function; 10 functions per pipeline; 1,000 loop iterations.[^s1]
- 5 MB evaluated resolver response; 64 KB request and response mapping templates; 1 MB schema document.[^s1]
- Queries of at most 15,000 parsed tokens and at most 10,000 resolvers executed per request; 2,000 items per Lambda BatchInvoke.[^s1]
- 50 API keys and 50 authentication providers per API; 25 caching keys.[^s1]

Adjustable resource counts include 50 GraphQL APIs and 50 Event APIs per Region, 50 channel namespaces per Event API, 50 custom domain names and 10 source APIs per Merged API.[^s1]

## Self-imposed limits and gotchas

Beyond the service quotas, each GraphQL API can set its own guard rails: a maximum query depth from 1 to 75 (unlimited by default), a maximum resolver count from 1 to 10,000 (10,000 by default) and an on/off switch for introspection. Exceeding them returns `QueryDepthLimitReached` or `ResolverExecutionLimitReached` errors, and resolvers past the count limit do not run.[^s3] Setting these protects the shared token budget from deeply nested or abusive queries. The 30-second ceiling is the most common surprise: long-running work should be handed to asynchronous services and the result delivered later, for example through a subscription.[^s1] AppSync has no metric literally named "throttled"; throttling shows up in `4XXError` and the client-error metrics.[^s2]

## Connections

These quotas are the **governing limits** of AWS AppSync, the broader node, and like most per-account limits they are **configured via** Service Quotas, where each adjustable row has a quota code.[^s1] They are **scoped to** AWS Regions: the token default itself differs by Region.[^s1] They are **monitored by** Amazon CloudWatch through token, error and message metrics.[^s2] They **contrast with** Amazon API Gateway quotas, the limits of the other managed API front door in the graph. The 2,000-item BatchInvoke limit ties AppSync's resolvers to AWS Lambda.[^s1] Work that would outrun the 30-second ceiling can be handed to an asynchronous integration such as Amazon EventBridge.

## Sources
[^s1]: Amazon Web Services, "AWS AppSync endpoints and quotas - AWS General Reference". https://docs.aws.amazon.com/general/latest/gr/appsync.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "Using CloudWatch to monitor and log GraphQL API data - AWS AppSync GraphQL". https://docs.aws.amazon.com/appsync/latest/devguide/monitoring.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "Configuring GraphQL run complexity, query depth, and introspection with AWS AppSync - AWS AppSync GraphQL". https://docs.aws.amazon.com/appsync/latest/devguide/configuration-limits.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "AWS AppSync increases existing service quota and adds subscription service quotas" (What's New, 2024-04-02). https://aws.amazon.com/about-aws/whats-new/2024/04/aws-appsync-increases-service-quota-adds-subscription/ (accessed 2026-11-20)
