---
id: kt:amazon-eventbridge
type: Service
title: Amazon EventBridge
aliases: [EventBridge, Amazon CloudWatch Events, CloudWatch Events, AWS EventBridge]
domain: serverless-and-integration
summary: >-
  AWS's serverless event router, the evolution of CloudWatch Events: event buses receive events from
  AWS services, SaaS partners and your own applications and route them to targets, while Pipes
  connect one source to one target and Scheduler runs one-time or recurring invocations.
verified: 2026-11-10
status: current
broader: [kt:aws-serverless-services]
related: [kt:aws-lambda, kt:amazon-sqs, kt:amazon-sns, kt:aws-step-functions]
relations:
  - {predicate: implements, object: kt:event-driven-architecture}
  - {predicate: alternativeTo, object: kt:amazon-sns}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: governedBy, object: kt:eventbridge-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What Is Amazon EventBridge?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.md
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "EventBridge is the evolution of Amazon CloudWatch Events"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-cwe-now-eb.md
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Amazon EventBridge quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Quotas for Amazon EventBridge Scheduler"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/scheduler/latest/UserGuide/scheduler-quotas.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Amazon EventBridge pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/eventbridge/pricing/?nc1=h_ls
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "Introducing enhanced custom event buses in Amazon EventBridge for enterprise-scale event-driven applications"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/introducing-enhanced-custom-event-buses-in-amazon-eventbridge-for-enterprise-scale-event-driven-applications/
    type: official-blog
    accessed: 2026-11-10
  - id: s7
    title: "Amazon SQS, Amazon SNS, or Amazon EventBridge?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/sns-or-sqs-or-eventbridge/sns-or-sqs-or-eventbridge.html
    type: official-docs
    accessed: 2026-11-10
  - id: s8
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-10
  - id: s9
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# Amazon EventBridge

Amazon EventBridge is a serverless service that connects application components through events, so you can build loosely coupled, event-driven applications. It ingests, filters, transforms and delivers events from AWS services, SaaS software and your own code.[^s1] It is the evolution of Amazon CloudWatch Events. It uses the same API, so existing CloudWatch Events code keeps working, and the default event bus and old rules appear in the EventBridge console. New features (SaaS partner events, Pipes, the Schema Registry and Scheduler) are added only to EventBridge.[^s2]

## How it works

EventBridge has three building blocks.[^s1]

- **Event buses** are routers for many sources and many targets. A bus receives events and delivers each one to zero or more targets, optionally transforming it first.[^s1]
- **Pipes** are point-to-point: each pipe reads from one source, can filter and enrich events, and delivers them to one target. A common pattern pairs them, with a pipe reading a DynamoDB stream and sending to a bus that fans events out by rule.[^s1]
- **EventBridge Scheduler** creates one-time schedules or recurring ones with cron and rate expressions. It also offers flexible delivery windows, retry limits and a maximum retention time for failed invocations.[^s1]

There are now two kinds of custom bus. The **Custom Event Bus - Classic** routes events with rules and targets written by the bus owner. The newer **Custom Event Bus** routes to subscribers that consumers create themselves. It keeps events for a retention period you set and supports ordered delivery, replay and non-JSON payloads. AWS recommends it for new applications, while Classic stays available.[^s1]

## What changed (2026-09)

AWS announced the enhanced custom event bus on 2026-09-24. One bus can be shared across an organization through AWS Resource Access Manager, which removes cross-account rules and bus-to-bus routing.[^s6] A **subscriber** bundles filtering, target, retry policy and dead-letter destination. Publishers set an `EventGroupId`, and subscribers that opt in get events with the same ID in order. Content-based deduplication drops repeats within five minutes, and subscribers can start from a chosen point in time to replay history.[^s6] At launch it was offered in 14 Regions, including US East (N. Virginia, Ohio), US West (Oregon), Europe (Ireland, Frankfurt) and Asia Pacific (Tokyo, Sydney).[^s6] Existing buses continue unchanged and are now labeled "Custom event bus – classic".[^s6]

## Quotas, pricing and gotchas

Quotas are per account per Region (as of 2026-11).[^s3]

- **Classic buses:** 100 event buses and 300 rules per bus by default (100 in af-south-1 and eu-south-1), both adjustable. Each rule allows 5 targets, which is not adjustable.[^s3]
- **PutEvents throughput** varies by Region and is adjustable. It is 10,000 TPS in us-east-1, us-west-2 and eu-west-1, 2,400 in us-east-2 and eu-central-1, and 400 in Regions not listed separately. Target invocations are 18,750 TPS in the largest Regions.[^s3]
- **Enhanced buses:** 5 buses per Region and 10,000 subscribers per bus by default, both adjustable. Retention is 1–365 days, and a PutEvents request carries 1–100 entries.[^s3]
- **Pipes:** 1,000 pipes per account. Concurrent executions are 3,000 in N. Virginia, Oregon and Ireland and 1,000 elsewhere.[^s3]
- **Scheduler:** 10 million schedules per Region (adjustable) and a 256 KB target input limit. Invocations throttle at 1,000 TPS in 11 larger Regions and 500 elsewhere. Throttled invocations are delayed rather than dropped, and completed one-time schedules still count toward the quota until deleted.[^s4]

Pricing is in USD on the public pricing page, which does not name a Region (as of 2026-11).[^s5] On Classic buses, AWS management events are free to ingest. Custom and partner events cost $1.00 per million, and delivery to another event bus also costs $1.00 per million. Each 64 KB chunk of a payload bills as one event.[^s5] Scheduler includes 14 million free invocations a month, and API destinations cost $0.20 per million events plus data transfer.[^s5] Enhanced buses instead bill publishers for ingress and subscribers for egress by data volume, with events rounded up to the nearest 1 KB.[^s5][^s6]

Gotchas: Classic delivery is at-least-once and unordered. By default EventBridge retries for 24 hours, up to 185 times, and it does not persist events the way a queue does.[^s7] Consumers should therefore be idempotent, or use an enhanced bus with ordering and deduplication.[^s6]

## When to use it (and when not)

AWS's decision guide points to EventBridge for content-based routing with rich event patterns. It also fits integrating AWS services, SaaS partners and other accounts, and near real-time processing where order does not matter.[^s7] Choose Amazon SQS when consumers must pull at their own pace and messages must persist (up to 14 days). Choose Amazon SNS for simple broadcast to many subscribers such as email, SMS or mobile push, or FIFO topics for strict ordering.[^s7] Use Scheduler instead of cron on an instance for timed invocations.[^s1]

## Connections

- **Broader:** part of the AWS serverless services family and a core service for building event-driven architectures on AWS.[^s1]
- **Related / integrates with:** AWS Lambda, Step Functions, SQS and SNS are common targets.[^s7] Events from AWS services arrive on the default event bus, as they did under CloudWatch Events.[^s2] Enhanced buses are shared across AWS Organizations accounts and OUs.[^s6]
- **Alternative to:** Amazon SNS for fan-out.[^s7]
- **Governed by** the EventBridge and Scheduler quotas above, and **scoped to** a Region.[^s3][^s4]
- **Covered by:** listed under Application Integration in the in-scope services for AWS Certified Cloud Practitioner (CLF-C02)[^s8] and AWS Certified Solutions Architect - Associate (SAA-C03).[^s9]

## Sources
[^s1]: Amazon Web Services, "What Is Amazon EventBridge?". https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.md (accessed 2026-11-10)
[^s2]: Amazon Web Services, "EventBridge is the evolution of Amazon CloudWatch Events". https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-cwe-now-eb.md (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Amazon EventBridge quotas". https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Quotas for Amazon EventBridge Scheduler". https://docs.aws.amazon.com/scheduler/latest/UserGuide/scheduler-quotas.html (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Amazon EventBridge pricing". https://aws.amazon.com/eventbridge/pricing/?nc1=h_ls (accessed 2026-11-10)
[^s6]: Amazon Web Services (AWS News Blog), "Introducing enhanced custom event buses in Amazon EventBridge for enterprise-scale event-driven applications". https://aws.amazon.com/blogs/aws/introducing-enhanced-custom-event-buses-in-amazon-eventbridge-for-enterprise-scale-event-driven-applications/ (accessed 2026-11-10)
[^s7]: Amazon Web Services, "Amazon SQS, Amazon SNS, or Amazon EventBridge?". https://docs.aws.amazon.com/decision-guides/latest/sns-or-sqs-or-eventbridge/sns-or-sqs-or-eventbridge.html (accessed 2026-11-10)
[^s8]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-10)
[^s9]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-10)
