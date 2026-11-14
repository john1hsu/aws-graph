---
id: kt:aws-appsync
type: Service
title: AWS AppSync
aliases: [AppSync, Amazon AppSync, AWS AppSync GraphQL, AWS AppSync Events, AppSync Events]
domain: serverless-and-integration
summary: >-
  AWS's serverless API service for GraphQL and real-time Pub/Sub: GraphQL APIs expose many data
  sources behind one endpoint with resolvers and WebSocket subscriptions, while AppSync Events
  provides managed WebSocket channels for broadcasting events to large numbers of subscribers.
verified: 2026-11-14
status: current
broader: [kt:aws-serverless-services]
related: [kt:amazon-dynamodb, kt:aws-lambda, kt:amazon-eventbridge, kt:aws-amplify]
relations:
  - {predicate: alternativeTo, object: kt:amazon-api-gateway}
  - {predicate: securedBy, object: kt:amazon-cognito}
  - {predicate: securedBy, object: kt:aws-waf}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: governedBy, object: kt:appsync-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-developer-associate}
sources:
  - id: s1
    title: "What is AWS AppSync?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/appsync/latest/devguide/what-is-appsync.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "What is AWS AppSync Events?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/appsync/latest/eventapi/event-api-welcome.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Attaching a data source in AWS AppSync"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/appsync/latest/devguide/attaching-a-data-source.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "AWS AppSync endpoints and quotas"
    publisher: Amazon Web Services (AWS General Reference)
    url: https://docs.aws.amazon.com/general/latest/gr/appsync.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "AWS AppSync Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/appsync/pricing/
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "AWS AppSync FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/appsync/faqs/
    type: official-docs
    accessed: 2026-11-14
  - id: s7
    title: "Announcing AWS AppSync Events: serverless WebSocket APIs to power real-time web and mobile experiences at any scale"
    publisher: Amazon Web Services (Front-End Web & Mobile Blog)
    url: https://aws.amazon.com/blogs/mobile/announcing-aws-appsync-events-serverless-websocket-apis/
    type: official-blog
    accessed: 2026-11-14
  - id: s8
    title: "Enhance real-time applications with AWS AppSync Events data source integrations"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/enhance-real-time-applications-with-aws-appsync-events-data-source-integrations/
    type: official-blog
    accessed: 2026-11-14
  - id: s9
    title: "In-Scope AWS Services - AWS Certified Developer - Associate (DVA-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/dva-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-14
created: 2026-11-14
---

# AWS AppSync

AWS AppSync is a serverless service for connecting applications to data and events through two kinds of API: **GraphQL APIs**, which let clients query and modify data from one or more data sources through a single endpoint, and **Pub/Sub APIs** built with **AppSync Events**, which deliver real-time messages over WebSockets.[^s1] AWS runs the endpoints, scaling and WebSocket connection management, and bills per request and per connection minute rather than per server.[^s1][^s5]

## How it works

**GraphQL APIs.** You define a GraphQL schema and attach resolvers that map each field to a data source. Resolver business logic can be written in JavaScript or TypeScript, and pipeline resolvers chain several functions together.[^s1][^s4] The supported data source types are Amazon DynamoDB, AWS Lambda, Amazon RDS (for example Aurora Serverless), Amazon EventBridge, OpenSearch, any HTTP endpoint, and a "None" source for logic that needs no backend call.[^s3] Clients receive live changes through GraphQL subscriptions carried over serverless WebSockets.[^s1] Optional server-side caching keeps resolver results in in-memory caches for lower latency, at an extra hourly charge.[^s1][^s5] **Merged APIs** combine several source GraphQL APIs into one schema, so separate teams can evolve their own sub-schemas behind a single consumer endpoint, and **Private APIs** restrict an API to callers inside your Amazon VPC.[^s6]

**AppSync Events.** Announced in October 2024, Events provides managed WebSocket APIs for broadcasting to anything from a handful to millions of subscribers.[^s7] Events are published to channels grouped into **namespaces**. A namespace sets the authorization rules and handler code for all of its channels, and clients can subscribe to a whole group of channels with a wildcard path.[^s2] Clients publish over HTTP or WebSocket and subscribe over WebSocket.[^s2] Optional `onPublish` and `onSubscribe` handlers can transform or filter events, or reject a publish or subscription.[^s2][^s6] Since April 2025, namespace handlers can also call data sources such as Lambda, DynamoDB and Aurora, for example to save events in batches to a DynamoDB table, at no extra AppSync charge.[^s8]

**Security and operations.** Both API types support API keys, IAM, Amazon Cognito user pools, OpenID Connect providers and Lambda authorizers. AWS WAF can protect the endpoints, and metrics and logs go to Amazon CloudWatch.[^s1][^s2]

## Quotas, pricing and gotchas

Default quotas as of 2026-11, per Region:[^s4]

- **GraphQL request tokens:** 10,000 per second in 11 listed Regions including us-east-1, us-west-2 and eu-west-1, and 5,000 per second in other supported Regions (adjustable).[^s4]
- **Request execution time:** 30 seconds (not adjustable).[^s4]
- **Schema document size:** 1 MB. **Resolver and function code size (APPSYNC_JS):** 32 KB. **Resolver response size:** 5 MB. **Functions per pipeline resolver:** 10.[^s4]
- **GraphQL APIs per Region:** 50 (adjustable). **Source APIs per Merged API:** 10 (adjustable).[^s4]
- **Subscriptions:** 240 KB maximum payload (not adjustable) and 200 subscriptions per client connection (adjustable).[^s4]
- **Events APIs:** maximum publish payload of 1.2 MB, at most 5 events per publish batch, and a limit of 25 publish requests per second on each client connection. None of these three is adjustable.[^s4]

List prices as of 2026-11 (the pricing page gives one rate rather than per-Region rates):[^s5]

- **GraphQL:** $4.00 per million queries and data modifications, $2.00 per million real-time updates, and $0.08 per million connection minutes.[^s5]
- **Events:** $1.00 per million operations and $0.08 per million connection minutes.[^s5]
- **Caching:** billed hourly, from $0.044 per hour for a `cache.small` instance.[^s5]
- **Free tier:** for GraphQL, 250,000 operations, 250,000 real-time updates and 600,000 connection minutes per month; for Events, 250,000 real-time updates and 600,000 connection minutes. The free tier expires after 12 months.[^s5]

Two pricing details are easy to miss. Real-time messages are metered in 5 KB units, so an 8 KB message counts as two operations. Requests that fail authentication or authorization are not charged.[^s5][^s1]

The 30-second execution limit is fixed, so a long-running operation should start asynchronous work (for example through Lambda or EventBridge) and return straight away rather than block the resolver.[^s4][^s3]

## When to use it (and when not)

Choose AppSync GraphQL when clients need to pull exactly the fields they want from several backends in one request, or need live subscriptions. For offline or sync-heavy mobile apps, AWS recommends pairing it with Amplify clients and, for DynamoDB-backed data, Amplify DataStore's conflict detection and resolution.[^s6] Choose AppSync Events for fan-out real-time features such as chat, live scores, in-app alerts and activity feeds, where the main job is pushing messages to many subscribers.[^s2] AppSync's model is schema- and channel-centric, so a plain REST or HTTP API without a GraphQL schema or Pub/Sub channels belongs in Amazon API Gateway instead, which this graph covers separately.[^s1][^s2]

## Connections

AppSync sits under the AWS serverless services API layer. It is an alternative to Amazon API Gateway when the contract is GraphQL or managed Pub/Sub.[^s1] Its most common resolver targets are Amazon DynamoDB and AWS Lambda, and Amazon EventBridge is both a GraphQL data source and a publishing integration for Events.[^s3][^s2] Access is controlled by Amazon Cognito user pools and other authorization modes, AWS WAF protects the endpoints, and Amazon CloudWatch carries metrics and logs.[^s1][^s2] AWS Amplify builds on AppSync for web and mobile front ends.[^s1] AppSync APIs and their quotas are per Region (AWS Regions).[^s4] AWS AppSync is listed under Application Integration among the in-scope services for the AWS Certified Developer – Associate (DVA-C02) exam.[^s9]

## Sources
[^s1]: Amazon Web Services, "What is AWS AppSync?". https://docs.aws.amazon.com/appsync/latest/devguide/what-is-appsync.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "What is AWS AppSync Events?". https://docs.aws.amazon.com/appsync/latest/eventapi/event-api-welcome.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, "Attaching a data source in AWS AppSync". https://docs.aws.amazon.com/appsync/latest/devguide/attaching-a-data-source.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, "AWS AppSync endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/appsync.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, "AWS AppSync Pricing". https://aws.amazon.com/appsync/pricing/ (accessed 2026-11-14)
[^s6]: Amazon Web Services, "AWS AppSync FAQs". https://aws.amazon.com/appsync/faqs/ (accessed 2026-11-14)
[^s7]: Amazon Web Services, "Announcing AWS AppSync Events: serverless WebSocket APIs to power real-time web and mobile experiences at any scale". https://aws.amazon.com/blogs/mobile/announcing-aws-appsync-events-serverless-websocket-apis/ (accessed 2026-11-14)
[^s8]: Amazon Web Services, "Enhance real-time applications with AWS AppSync Events data source integrations". https://aws.amazon.com/blogs/aws/enhance-real-time-applications-with-aws-appsync-events-data-source-integrations/ (accessed 2026-11-14)
[^s9]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Developer - Associate". https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/dva-02-in-scope-services.html (accessed 2026-11-14)
