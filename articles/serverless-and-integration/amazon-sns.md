---
id: kt:amazon-sns
type: Service
title: Amazon SNS
aliases: [Amazon Simple Notification Service, SNS, Simple Notification Service, AWS SNS]
domain: serverless-and-integration
summary: >-
  AWS's fully managed publish/subscribe service: publishers send messages to a topic and SNS pushes
  copies to every subscribed endpoint (SQS queues, Lambda functions, Firehose streams, HTTP/S, email,
  SMS and mobile push), with standard and FIFO topic types, filtering, retries and dead-letter queues.
verified: 2026-11-11
status: current
broader: [kt:aws-serverless-services]
related: [kt:amazon-sqs, kt:aws-lambda, kt:amazon-data-firehose]
relations:
  - {predicate: implements, object: kt:event-driven-architecture}
  - {predicate: alternativeTo, object: kt:amazon-eventbridge}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: governedBy, object: kt:sns-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "Amazon SNS features and capabilities"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/sns/latest/dg/welcome-features.md
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Amazon Simple Notification Service endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/sns.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Amazon SNS FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/sns/faqs
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Amazon SNS message delivery retries"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/sns/latest/dg/sns-message-delivery-retries.md
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Publishing large messages with Amazon SNS and Amazon S3"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/sns/latest/dg/large-message-payloads.md
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "Amazon SNS now supports sending SMS in additional AWS Regions"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/02/amazon-sns-sms-region-expansion
    type: official-blog
    accessed: 2026-11-11
  - id: s7
    title: "Amazon SNS FIFO topics are now available in AWS GovCloud (US) Regions"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2023/12/amazon-sns-fifo-topics-aws-govcloud-us-regions
    type: official-blog
    accessed: 2026-11-11
  - id: s8
    title: "In-Scope AWS Services – AWS Certified Solutions Architect – Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# Amazon SNS

Amazon Simple Notification Service (SNS) is AWS's managed publish/subscribe (pub/sub) service. A producer publishes a message once to a **topic**, and SNS pushes a copy to every subscription on that topic, so subscribers never poll.[^s3] Subscribers fall into two families: application-to-application (A2A) targets such as Amazon SQS queues, AWS Lambda functions, Amazon Data Firehose delivery streams and HTTP/S endpoints, and application-to-person (A2P) targets such as mobile push, SMS phone numbers and email addresses.[^s1] SNS is generally available and is listed among the Application Integration services in scope for the Solutions Architect – Associate (SAA-C03) exam.[^s8]

## How it works

Topics come in two kinds. **Standard topics** support every delivery protocol and suit workloads where ordering and the occasional duplicate don't matter. **FIFO topics** add strict ordering within a message group plus deduplication; they deliver to SQS FIFO or standard queues rather than to every protocol.[^s1][^s3] FIFO topics also offer an in-place archive whose messages subscribers can later replay to their endpoint.[^s1]

By default each subscriber gets every message. To narrow that down, attach a **filter policy** to a subscription; its scope can be message attributes (the default) or the message body.[^s1] Before acknowledging a publish, SNS stores copies of the message on disk across multiple Availability Zones.[^s3] Topic contents can be encrypted server-side with AWS KMS keys, and VPC endpoints keep publisher traffic on a private connection.[^s1]

When delivery fails with a server-side error, a per-protocol **delivery policy** takes over. For AWS-managed endpoints (Lambda, SQS) it makes up to 100,015 attempts over 23 days. For customer-managed endpoints (SMTP, SMS, mobile push) it makes 50 attempts over 6 hours.[^s4] Only HTTP/S delivery policies can be customized, and their total retry window is capped at 3,600 seconds, a hard limit.[^s4] Once the policy runs out, SNS discards the message unless the subscription has a dead-letter queue. Client-side errors, such as a deleted endpoint, skip retries altogether.[^s3][^s4]

## Quotas, pricing and gotchas

Selected quotas as of 2026-11, from the AWS General Reference:[^s2]

- **Topics:** 100,000 standard and 1,000 FIFO per account (adjustable).[^s2]
- **Subscriptions:** 12,500,000 per standard topic and 100 per FIFO topic (adjustable).[^s2]
- **Publish rate in us-east-1:** 30,000 messages per second per account, counting `Publish` and `PublishBatch` together. This is a soft limit.[^s2]
- **FIFO throughput:** 300 messages per second per message group. With topic-scoped throughput, a topic can reach 3,000 messages per second or 20 MB per second.[^s2]
- **Filter policies:** 200 per topic and 10,000 per account (adjustable).[^s2]
- **Email delivery:** 10 messages per second per email subscription, a hard limit that can't be raised.[^s2]

Message size defaults to 256 KiB. You can raise it to 1 MiB with the `MaximumMessageSize` topic attribute.[^s2][^s3] For anything bigger, the Extended Client Libraries for Java and Python store the payload in Amazon S3 and publish a pointer, which supports messages up to 2 GB.[^s5] One gotcha: the large-payload guide still describes the native maximum as 256 KB, so check the quotas page for current figures.[^s2][^s5]

List prices in the SNS FAQ (as of 2026-11; no Region given there, and prices vary by Region) are USD 0.50 per million requests, USD 0.06 per 100,000 HTTP deliveries and USD 2.00 per 100,000 email deliveries.[^s3] SMS is priced by destination country and has no free tier.[^s3] Billing counts each 64 KB chunk of published data as one request, so a 1 MiB message is billed as 16 requests.[^s3] SMS now goes through AWS End User Messaging, which manages origination identities and two-way messaging. The default SMS spend limit is USD 1.00 per month, and you can request an increase.[^s2][^s3] As of February 2026, SNS supports SMS in 32 Regions.[^s6]

## When to use it (and when not)

SNS is the right tool for push-based **fan-out**, where one event must reach many independent consumers, and for simple A2P alerts. A common pattern publishes to SNS and subscribes SQS queues, so each consumer gets its own durable buffer to poll at its own pace.[^s3] FIFO topics give ordered fan-out to as many as 100 subscribers. The FAQ contrasts this with Kinesis Data Streams, which supports up to 5.[^s3] Use a queue on its own when one consumer group pulls work. Consider Amazon EventBridge when you need content-based routing across many sources and targets rather than topic subscriptions. For public-sector work, FIFO topics are available in the AWS GovCloud (US) Regions.[^s7]

## Connections

SNS sits under **AWS serverless services** and typically **implements** an **event-driven architecture**.[^s1] Its most frequent partners are **Amazon SQS** (fan-out to queues), **AWS Lambda** (push invocation) and **Amazon Data Firehose** (archiving to analytics stores).[^s1][^s3] It is an **alternative to Amazon EventBridge** for event distribution. It is **secured by AWS KMS** for server-side encryption[^s1] and **governed by** its Regional service quotas.[^s2] Topics are **scoped to** an AWS Region,[^s2][^s6] and the service is **covered by** the Solutions Architect – Associate exam.[^s8]

## Sources

[^s1]: Amazon Web Services, "Amazon SNS features and capabilities". https://docs.aws.amazon.com/sns/latest/dg/welcome-features.md (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Amazon Simple Notification Service endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/sns.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Amazon SNS FAQs". https://aws.amazon.com/sns/faqs (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Amazon SNS message delivery retries". https://docs.aws.amazon.com/sns/latest/dg/sns-message-delivery-retries.md (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Publishing large messages with Amazon SNS and Amazon S3". https://docs.aws.amazon.com/sns/latest/dg/large-message-payloads.md (accessed 2026-11-11)
[^s6]: Amazon Web Services, "Amazon SNS now supports sending SMS in additional AWS Regions" (What's New, 2026-02-19). https://aws.amazon.com/about-aws/whats-new/2026/02/amazon-sns-sms-region-expansion (accessed 2026-11-11)
[^s7]: Amazon Web Services, "Amazon SNS FIFO topics are now available in AWS GovCloud (US) Regions" (What's New). https://aws.amazon.com/about-aws/whats-new/2023/12/amazon-sns-fifo-topics-aws-govcloud-us-regions (accessed 2026-11-11)
[^s8]: Amazon Web Services, "In-Scope AWS Services – AWS Certified Solutions Architect – Associate (SAA-C03)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-11)
