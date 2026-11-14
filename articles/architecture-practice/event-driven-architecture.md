---
id: kt:event-driven-architecture
type: Pattern
title: Event-driven architecture
aliases: [EDA, event-driven architectures, event-driven design]
domain: architecture-practice
summary: >-
  An architecture style in which decoupled services communicate by publishing, routing and consuming
  events (records of state changes) through an event router or messaging service instead of calling
  each other directly; on AWS it is typically built with EventBridge, SNS, SQS, Lambda and Step Functions.
verified: 2026-11-14
status: concept
broader: [kt:aws-well-architected-framework, kt:aws-serverless-services]
related: [kt:amazon-eventbridge, kt:amazon-sqs, kt:amazon-sns, kt:aws-step-functions, kt:aws-lambda]
relations:
  - {predicate: dependsOn, object: kt:amazon-eventbridge}
  - {predicate: alternativeTo, object: kt:amazon-kinesis-data-streams}
  - {predicate: integratesWith, object: kt:aws-step-functions}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is EDA (Event-Driven Architecture)?"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/what-is/eda/
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "Event-driven architectures - Serverless Applications Lens"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/it_it/wellarchitected/latest/serverless-applications-lens/event-driven-architectures.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Amazon EventBridge - Integrating microservices by using AWS serverless services (AWS Prescriptive Guidance)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-integrating-microservices/eventbridge.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "REL04-BP04 Make mutating operations idempotent - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_prevent_interaction_failure_idempotent.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "How EventBridge retries delivering events"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rule-retry-policy.html
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "Operating Lambda: Design principles in event-driven architectures – Part 2"
    publisher: AWS Compute Blog
    url: https://aws.amazon.com/blogs/compute/operating-lambda-design-principles-in-event-driven-architectures-part-2
    type: official-blog
    accessed: 2026-11-14
  - id: s7
    title: "AWS Certified Solutions Architect - Associate Exam Guide (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.pdf
    type: official-learning
    accessed: 2026-11-14
created: 2026-11-14
---

# Event-driven architecture

Event-driven architecture (EDA) is a way of building systems from small, decoupled services that publish, route and consume **events**. An event is a change in state, such as an item being added to a cart or an order shipping; it can carry the full state or just identifiers that consumers use to look up the rest.[^s1] AWS describes EDA as a popular approach for large, distributed microservice applications because it helps make them scalable, resilient, agile and cost-effective.[^s2]

## How it works

Every event-driven system has three parts. **Producers** (AWS services, your own microservices, or third-party SaaS applications) emit events when something changes. An **event router** ingests, filters and forwards those events. **Consumers** (destinations) receive only the events they care about and act on them.[^s1][^s2] Messaging services such as queues can sit between the router and a consumer to buffer events so each consumer works at its own pace.[^s1][^s3]

On AWS the router is usually Amazon EventBridge: producers put events on an event bus, rules match events by their content, and each rule delivers matches to one or more targets. Producers do not know who consumes their events, and any interested team can add its own rule to subscribe. That is a choreography-style model with no central controller.[^s3] EventBridge Pipes can link a source such as an SQS queue straight to a target such as a Step Functions state machine without glue code.[^s3] Amazon SNS handles high-fanout publish/subscribe, and Amazon SQS gives durable, buffered point-to-point delivery between services.[^s1][^s2]

Producers and consumers should agree on a **data contract**. EventBridge's schema registry stores schemas in OpenAPI 3 or JSONSchema Draft4 format. You can use them to validate events and generate code bindings.[^s2]

## Benefits

- **Loose coupling:** producers and consumers evolve, deploy and scale independently, and one consumer failing does not stop the others.[^s1]
- **Less custom plumbing:** the router filters and pushes events, so teams do not write polling code; push-based flows also cut idle capacity and wasted compute.[^s1]
- **Central control point:** a router gives one place to audit traffic and apply access policies and encryption.[^s1]

Typical uses include communication between microservices, SaaS integration, cross-account and cross-Region replication, parallel fan-out processing, resource monitoring and alerting, and automated business workflows.[^s1][^s2]

## Design rules and gotchas

- **Expect duplicates.** At-least-once delivery is common in event-driven systems built on SQS, Amazon MQ, Kinesis or Amazon MSK, because exactly-once delivery is hard to guarantee. The Well-Architected Reliability Pillar says publishers should attach an idempotency token to each message. Consumers should record the tokens they have processed, skip repeats, and pass the token on to downstream calls.[^s4] A common implementation stores processed event IDs in DynamoDB with a TTL.[^s6]
- **Plan for delivery failure.** By default (as of 2026-11), EventBridge retries a failed delivery for up to 24 hours and up to 185 attempts, using exponential backoff with jitter. You can change both in the target's retry policy. When retries run out, the event is dropped unless the target has a dead-letter queue.[^s5]
- **Ordering is not guaranteed by a bus.** If strict ordering matters, use SQS FIFO queues, SNS FIFO topics, Kinesis Data Streams or Amazon MSK instead of EventBridge.[^s3]
- **Latency.** EventBridge adds some latency. For latency-sensitive paths, the Serverless Lens suggests filtering and routing with SNS and SQS instead.[^s2] EDA is a poor fit for workloads that need consistently very low latency (AWS gives high-frequency trading as an example), and workloads that need strong consistency may need other patterns.[^s1]
- **Observability.** Flows span many services, so enable distributed tracing. EventBridge integrates with AWS X-Ray.[^s2]
- **Don't hide workflows in functions.** If a Lambda function coordinates branching, retries and error handling, the code becomes tightly coupled. Move that logic to a Step Functions state machine, and keep each function small, stateless and idempotent.[^s6]

## When to use it (and when not)

Choose EDA when services don't need a synchronous reply, when traffic is spiky or unpredictable, or when many independent consumers react to the same change.[^s1][^s3] Keep synchronous request/response calls, for example through API Gateway, where the caller needs an immediate answer. Use an orchestrator such as Step Functions where one process has to control a multi-step workflow from end to end.[^s6] Event streaming with Kinesis Data Streams or MSK is the alternative when you need ordered, replayable streams rather than routed discrete events.[^s3]

## Connections

- **Broader:** a design style recommended across the AWS Well-Architected Framework (including the Serverless Applications Lens) and a core pattern for AWS serverless services.[^s2][^s6]
- **Depends on / related:** Amazon EventBridge as the usual router; Amazon SNS for fan-out and Amazon SQS for buffering; AWS Lambda as a typical consumer; AWS Step Functions for orchestrating steps within the overall choreography.[^s2][^s3][^s6]
- **Alternative to:** Amazon Kinesis Data Streams (and MSK) when strict ordering or stream replay is required.[^s3]
- **Covered by:** the AWS Certified Solutions Architect – Associate (SAA-C03) exam guide lists event-driven architectures as knowledge under Task 2.1, "Design scalable and loosely coupled architectures".[^s7]

## Sources
[^s1]: Amazon Web Services, "What is EDA (Event-Driven Architecture)?". https://aws.amazon.com/what-is/eda/ (accessed 2026-11-14)
[^s2]: Amazon Web Services, "Event-driven architectures - Serverless Applications Lens". https://docs.aws.amazon.com/it_it/wellarchitected/latest/serverless-applications-lens/event-driven-architectures.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, "Amazon EventBridge - Integrating microservices by using AWS serverless services" (AWS Prescriptive Guidance). https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-integrating-microservices/eventbridge.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, "REL04-BP04 Make mutating operations idempotent - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_prevent_interaction_failure_idempotent.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, "How EventBridge retries delivering events". https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rule-retry-policy.html (accessed 2026-11-14)
[^s6]: AWS Compute Blog, "Operating Lambda: Design principles in event-driven architectures – Part 2". https://aws.amazon.com/blogs/compute/operating-lambda-design-principles-in-event-driven-architectures-part-2 (accessed 2026-11-14)
[^s7]: Amazon Web Services, "AWS Certified Solutions Architect - Associate Exam Guide (SAA-C03)". https://docs.aws.amazon.com/pdfs/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.pdf (accessed 2026-11-14)
