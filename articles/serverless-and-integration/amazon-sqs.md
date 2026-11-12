---
id: kt:amazon-sqs
type: Service
title: Amazon SQS
aliases: [Amazon Simple Queue Service, SQS, Simple Queue Service, AWS SQS]
domain: serverless-and-integration
summary: >-
  AWS's fully managed, pull-based message queue for decoupling producers from consumers, offering
  standard queues (at-least-once, best-effort ordering), FIFO queues (ordered, exactly-once processing)
  and fair queues for multi-tenant workloads, with dead-letter queues and KMS encryption.
verified: 2026-11-12
status: current
broader: [kt:aws-serverless-services]
related: [kt:amazon-sns, kt:aws-lambda, kt:amazon-eventbridge]
relations:
  - {predicate: implements, object: kt:event-driven-architecture}
  - {predicate: alternativeTo, object: kt:amazon-kinesis-data-streams}
  - {predicate: alternativeTo, object: kt:amazon-mq}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: governedBy, object: kt:sqs-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
  - {predicate: coveredBy, object: kt:aws-certified-developer-associate}
sources:
  - id: s1
    title: "Amazon SQS FAQs"
    publisher: Amazon Web Services
    url: https://www.aws.amazon.com/sqs/faqs
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Amazon SQS queue types"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-queue-types.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Amazon SQS message quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/quotas-messages.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "Amazon SQS pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/sqs/pricing/
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "Amazon SQS increases maximum message payload size to 1 MiB"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/08/amazon-sqs-max-payload-size-1mib
    type: official-blog
    accessed: 2026-11-12
  - id: s6
    title: "Amazon SQS introduces fair queues for multi-tenant workloads"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-sqs-introduces-fair
    type: official-blog
    accessed: 2026-11-12
  - id: s7
    title: "In-Scope AWS Services – AWS Certified Solutions Architect – Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-12
  - id: s8
    title: "In-Scope AWS Services – AWS Certified Developer – Associate (DVA-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/dva-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-12
created: 2026-11-12
---

# Amazon SQS

Amazon Simple Queue Service (SQS) is AWS's fully managed message queue. Producers send messages to a queue, and consumers poll the queue, process what they receive and then delete it, so the two sides never have to be online or scaled together.[^s1] SQS stores messages within one Region across multiple redundant Availability Zones, so a single machine, network or AZ failure should not make messages unavailable.[^s1] The service is generally available, and both the Solutions Architect – Associate (SAA-C03) and Developer – Associate (DVA-C02) exams list it under Application Integration.[^s7][^s8]

## How it works

SQS offers two queue types. **Standard queues** provide at-least-once delivery and best-effort ordering. A message can occasionally arrive twice or out of order, so consumers should be idempotent. In return, throughput per API action is described as nearly unlimited.[^s2] **FIFO queues** preserve first-in-first-out order within each *message group ID* and provide exactly-once processing. Duplicates are suppressed using a deduplication ID or content-based deduplication.[^s2] Spreading messages across many groups lets consumers work in parallel while each group keeps its order.[^s2]

**Fair queues** (launched July 2025) extend standard queues for multi-tenant systems. When one tenant builds a backlog, SQS reorders delivery so other tenants' messages keep a consistent dwell time. The noisy tenant still gets its messages, but later.[^s6] You turn this on by adding a message group ID to messages sent to a standard queue. Consumers need no changes, and the feature is available in all commercial and AWS GovCloud (US) Regions.[^s6][^s1]

Once a consumer receives a message, the message is hidden for a **visibility timeout**: 30 seconds by default, configurable from 0 seconds to 12 hours. If the consumer doesn't delete it in time, the message reappears for another attempt.[^s3] **Long polling** keeps a `ReceiveMessage` call open until a message arrives or the wait ends. This cuts down on empty responses, and AWS recommends a wait of no more than 20 seconds.[^s1] A **redrive policy** moves a message to a **dead-letter queue** once its receive count exceeds `maxReceiveCount`. You can inspect messages there and redrive them to the source queue at a controlled rate. A FIFO queue must use a FIFO dead-letter queue.[^s1] Server-side encryption protects message bodies using AWS KMS keys. Queue metadata, message metadata and metrics are not encrypted.[^s1]

## Quotas, pricing and gotchas

Selected quotas as of 2026-11, from the SQS Developer Guide and FAQ:[^s3][^s1]

- **Message size:** 1 byte to 1 MiB (1,048,576 bytes). The Extended Client Libraries can store a larger payload of up to 2 GB in Amazon S3 and send a reference to it.[^s3] AWS raised the limit from 256 KiB to 1 MiB in August 2025 for standard and FIFO queues, in commercial and GovCloud (US) Regions. The Lambda event source mapping was updated to match.[^s5]
- **Retention:** 4 days by default, configurable from 60 seconds to 14 days. **Delay** ranges from 0 to 15 minutes.[^s3]
- **Batching:** up to 10 messages per batch request, and up to 10 attributes per message.[^s3]
- **In-flight messages:** up to 120,000 per queue for both standard and FIFO queues.[^s1]
- **FIFO throughput:** 300 transactions per second per API action, or 3,000 messages per second with batching. High-throughput FIFO limits vary by Region. In US East (N. Virginia), US West (Oregon) and Europe (Ireland) the limit is 70,000 non-batched TPS (700,000 messages per second batched), and most other Regions default to 2,400 TPS. You can request an increase through Service Quotas.[^s3]

One gotcha: the queue-types page still quotes 30,000 TPS for high-throughput FIFO, so plan against the Regional quotas table instead.[^s2][^s3]

On pricing, every customer gets 1 million requests per month free. The free tier counts usage across all Regions except GovCloud, and unused requests don't roll over.[^s4] Standard, FIFO and fair-queue requests are billed at different rates. Fair-queue requests on a standard queue incur both the fair and the standard rate.[^s4] Each 64 KB chunk of payload counts as one request, so a 1 MiB message is billed as 16 requests.[^s4] Batch calls cost the same as single calls, long polls cost the same as short polls, and data transfer to EC2 or Lambda in the same Region is free.[^s1] Per-request rates vary by Region, so check the pricing page for your Region.[^s4]

## When to use it (and when not)

Use SQS to buffer work between components, absorb traffic spikes and spread jobs across a fleet of workers. Typical examples include media encoding, payment validations and batched database writes on standard queues, and ordered commands or price updates on FIFO queues.[^s2] It is a pull model with one logical consumer group per queue. When many consumers each need every event, put Amazon SNS in front for push-based fan-out. Choose Amazon Kinesis Data Streams when several applications must read and replay the same ordered stream.[^s1] For existing applications built on standard protocols such as JMS or AMQP, AWS points to Amazon MQ, and it recommends SQS and SNS for new cloud-native designs.[^s1]

## Connections

SQS sits under **AWS serverless services** and is a core building block that **implements** an **event-driven architecture**.[^s1] It is closely **related to Amazon SNS** (topics fan out into queues) and **AWS Lambda**, which polls queues through an event source mapping.[^s5] It is also related to **Amazon EventBridge**, which the SAA-C03 guide lists beside it under Application Integration.[^s7] It is an **alternative to Amazon Kinesis Data Streams** for streaming with replay and an **alternative to Amazon MQ** for protocol-compatible brokers.[^s1] Encryption **is secured by AWS KMS**.[^s1] The service is **governed by** its message and throughput quotas[^s3] and **scoped to** a single AWS Region.[^s1] It is **covered by** the Solutions Architect – Associate and Developer – Associate exams.[^s7][^s8]

## Sources

[^s1]: Amazon Web Services, "Amazon SQS FAQs". https://www.aws.amazon.com/sqs/faqs (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Amazon SQS queue types". https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-queue-types.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "Amazon SQS message quotas". https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/quotas-messages.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, "Amazon SQS pricing". https://aws.amazon.com/sqs/pricing/ (accessed 2026-11-12)
[^s5]: Amazon Web Services, "Amazon SQS increases maximum message payload size to 1 MiB" (What's New, 2025-08-04). https://aws.amazon.com/about-aws/whats-new/2025/08/amazon-sqs-max-payload-size-1mib (accessed 2026-11-12)
[^s6]: Amazon Web Services, "Amazon SQS introduces fair queues for multi-tenant workloads" (What's New, 2025-07-21). https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-sqs-introduces-fair (accessed 2026-11-12)
[^s7]: Amazon Web Services, "In-Scope AWS Services – AWS Certified Solutions Architect – Associate (SAA-C03)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-12)
[^s8]: Amazon Web Services, "In-Scope AWS Services – AWS Certified Developer – Associate (DVA-C02)". https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/dva-02-in-scope-services.html (accessed 2026-11-12)
