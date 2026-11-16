---
id: kt:sqs-vs-sns-vs-eventbridge
type: Concept
title: SQS vs SNS vs EventBridge
aliases: [Choosing between SQS, SNS and EventBridge, SNS vs SQS vs EventBridge, AWS messaging service decision guide]
domain: serverless-and-integration
summary: >-
  A decision framework for choosing among AWS's three core serverless messaging services: Amazon SQS
  (pull-based queue that persists work until consumed), Amazon SNS (push-based pub/sub fan-out) and
  Amazon EventBridge (rule-based event bus with content filtering and many AWS and SaaS targets).
verified: 2026-11-16
status: concept
broader: [kt:event-driven-architecture, kt:aws-serverless-services]
related: [kt:amazon-sqs, kt:amazon-sns, kt:amazon-eventbridge, kt:aws-lambda]
relations:
  - {predicate: contrastsWith, object: kt:amazon-kinesis-data-streams}
  - {predicate: contrastsWith, object: kt:amazon-mq}
sources:
  - id: s1
    title: "Amazon SQS, Amazon SNS, or Amazon EventBridge? (AWS Decision Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/sns-or-sqs-or-eventbridge/sns-or-sqs-or-eventbridge.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Messaging - Integrating microservices by using AWS serverless services (AWS Prescriptive Guidance)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-integrating-microservices/messaging.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Amazon EventBridge quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Publishing large messages with Amazon SNS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/sns/latest/dg/large-message-payloads.md
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "More room to build: serverless services now support payloads up to 1 MB"
    publisher: AWS Compute Blog
    url: https://aws.amazon.com/blogs/compute/more-room-to-build-serverless-services-now-support-payloads-up-to-1-mb
    type: official-blog
    accessed: 2026-11-16
  - id: s6
    title: "Amazon SQS increases maximum message payload size to 1 MiB"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/08/amazon-sqs-max-payload-size-1mib
    type: official-blog
    accessed: 2026-11-16
created: 2026-11-16
---

# SQS vs SNS vs EventBridge

Amazon SQS, Amazon SNS and Amazon EventBridge all decouple the parts of a distributed or serverless application, and they overlap enough that picking one is a recurring design question. AWS publishes a dedicated decision guide that frames the choice around communication pattern, persistence, delivery guarantees, ordering, filtering and the kind of consumer on the other end.[^s1] This node summarizes that framework; the individual service articles cover each service in depth.

## The three models in one sentence each

- **SQS is a queue (pull).** Producers send messages; consumers such as EC2 workers or Lambda poll and process them at their own pace. Messages persist until a consumer deletes them or they expire, up to 14 days.[^s1]
- **SNS is a topic (push).** A publish immediately pushes a copy to every subscriber: HTTP/S endpoints, email, SMS, mobile push, Lambda functions or SQS queues. SNS does not store messages for later retrieval.[^s1]
- **EventBridge is an event bus (route).** Rules match incoming events by pattern and route them to targets such as Lambda, Step Functions, SQS, SNS, Kinesis, API destinations or buses in other accounts. Events are processed as they arrive rather than queued for consumers.[^s1]

## Decision criteria

**Who controls the pace?** If downstream workers must absorb bursts and process at their own rate, a queue is the natural buffer, and long polling waits of up to 20 seconds keep idle polling cheap.[^s1] If every interested party must hear about something right away, push via SNS or EventBridge.[^s1]

**How much routing logic?** SQS cannot choose consumers by message content. SNS adds subscription filter policies on message attributes, and content filtering on FIFO topics. EventBridge offers the richest content-based pattern matching, which suits many producers and consumers that evolve independently.[^s1]

**Does order matter?** SQS FIFO queues and SNS FIFO topics both preserve order; EventBridge makes no ordering guarantee. AWS Prescriptive Guidance calls this out as the deciding factor when ordered delivery is required.[^s1][^s2]

**What happens on failure?** All three deliver at least once, so consumers should be idempotent. EventBridge retries a failed target for 24 hours and up to 185 times with exponential backoff and jitter by default, and SQS keeps undelivered work on the queue with dead-letter queues for poison messages.[^s1]

## Quotas and gotchas (as of 2026-11)

- **Payload size.** SQS raised its maximum message size from 256 KiB to 1 MiB in August 2025, with Lambda's SQS event source mapping updated to match.[^s6] EventBridge and asynchronous Lambda invocations followed in January 2026, also moving from 256 KB to 1 MB; the EventBridge increase excluded a handful of newer Regions at launch.[^s5] SNS topics still default to 256 KiB but accept up to 1 MiB if you raise the `MaximumMessageSize` attribute; such topics are limited to 100 subscriptions and to Firehose, SQS and Lambda endpoints only.[^s4]
- **EventBridge fan-out width.** A rule can have at most 5 targets (not adjustable); a custom bus defaults to 300 rules in most Regions (adjustable).[^s3] Wider fan-out means more rules, or a rule that targets an SNS topic.
- **EventBridge throughput.** In us-east-1, PutEvents defaults to 10,000 transactions per second and target invocations to 18,750 per second, both adjustable; throttled invocations are delayed rather than dropped.[^s3]
- **Pricing shape.** SQS charges per API request, SNS per request plus per delivery, and EventBridge per event published; all three have free tiers.[^s1]

## Common combinations

The services are often composed rather than chosen exclusively. The classic fan-out pattern publishes once to an SNS topic subscribed by several SQS queues, giving each consumer its own durable buffer; this also works around SQS's inability to route by content.[^s1] Likewise an EventBridge rule can target an SQS queue to add buffering and retry control behind content-based routing.[^s1] For point-to-point integrations between a source and a single target, AWS points to EventBridge Pipes.[^s1]

## Connections

This comparison sits under **event-driven architecture** and the broader set of **AWS serverless services**. Its three subjects, **Amazon SQS**, **Amazon SNS** and **Amazon EventBridge**, are related nodes, and **AWS Lambda** appears as a consumer or target of all three in the decision guide.[^s1] Ordering requirements push designs toward the FIFO variants of SQS and SNS rather than EventBridge.[^s2] In the graph it **contrasts with** Amazon Kinesis Data Streams and Amazon MQ, two other integration services that are often confused with these three but sit outside the AWS guide's comparison.[^s1]

## Sources
[^s1]: Amazon Web Services, "Amazon SQS, Amazon SNS, or Amazon EventBridge?" (AWS Decision Guide). https://docs.aws.amazon.com/decision-guides/latest/sns-or-sqs-or-eventbridge/sns-or-sqs-or-eventbridge.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Messaging" (AWS Prescriptive Guidance, Integrating microservices by using AWS serverless services). https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-integrating-microservices/messaging.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Amazon EventBridge quotas". https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Publishing large messages with Amazon SNS". https://docs.aws.amazon.com/sns/latest/dg/large-message-payloads.md (accessed 2026-11-16)
[^s5]: AWS Compute Blog, "More room to build: serverless services now support payloads up to 1 MB" (2026-01-29). https://aws.amazon.com/blogs/compute/more-room-to-build-serverless-services-now-support-payloads-up-to-1-mb (accessed 2026-11-16)
[^s6]: Amazon Web Services, "Amazon SQS increases maximum message payload size to 1 MiB" (What's New, 2025-08-04). https://aws.amazon.com/about-aws/whats-new/2025/08/amazon-sqs-max-payload-size-1mib (accessed 2026-11-16)
