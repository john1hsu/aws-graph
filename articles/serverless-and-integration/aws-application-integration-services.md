---
id: kt:aws-application-integration-services
type: Concept
title: AWS application integration services
aliases: [AWS Application Integration, application integration on AWS, AWS integration services]
domain: serverless-and-integration
summary: >-
  The AWS service category for connecting decoupled application components: queues and pub/sub
  (SQS, SNS, Amazon MQ), event routing and scheduling (EventBridge), workflow orchestration (Step
  Functions, MWAA, SWF) and data or document integration (AppFlow, B2B Data Interchange).
verified: 2026-11-18
status: concept
broader: [kt:amazon-web-services]
related: [kt:amazon-sqs, kt:amazon-sns, kt:amazon-eventbridge, kt:aws-step-functions, kt:sqs-vs-sns-vs-eventbridge]
relations:
  - {predicate: hasPart, object: kt:amazon-mq}
  - {predicate: hasPart, object: kt:amazon-swf}
  - {predicate: hasPart, object: kt:amazon-appflow}
  - {predicate: implements, object: kt:event-driven-architecture}
  - {predicate: enables, object: kt:reliability-pillar}
  - {predicate: coveredBy, object: kt:aws-certified-developer-associate}
sources:
  - id: s1
    title: "Application integration - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/application-integration
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Choosing an AWS application integration service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/application-integration-on-aws-how-to-choose/application-integration-on-aws-how-to-choose.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "REL04-BP02 Implement loosely coupled dependencies - Reliability Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/rel_prevent_interaction_failure_loosely_coupled_system.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "In-Scope AWS Services - AWS Certified Developer - Associate (DVA-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/dva-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-18
  - id: s5
    title: "AWS Service Availability Updates (June 2026)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/aws-service-availability/
    type: official-blog
    accessed: 2026-11-18
created: 2026-11-18
---

# AWS application integration services

"Application integration" is one of the service categories AWS uses to organise its portfolio. It groups the managed building blocks that let separately built and separately scaled components talk to each other without direct, synchronous calls. AWS's overview whitepaper lists nine services under the heading: AWS Step Functions, Amazon AppFlow, AWS B2B Data Interchange, Amazon EventBridge, Amazon Managed Workflows for Apache Airflow (MWAA), Amazon MQ, Amazon SNS, Amazon SQS and Amazon Simple Workflow Service (SWF).[^s1] This article is a map of that category; the individual services have their own nodes.

## What is in the category

The services fall into a few families by the job they do:[^s1][^s2]

- **Message queues and brokers.** Amazon SQS is a fully managed queue for point-to-point, pull-based delivery. Amazon MQ runs managed Apache ActiveMQ Classic and RabbitMQ brokers for applications that already speak standard broker protocols.[^s1]
- **Publish/subscribe and event routing.** Amazon SNS pushes each message to many subscribers. Amazon EventBridge is a serverless event bus that routes events from your applications, SaaS partners and AWS services by rule, and its Scheduler covers time-based jobs.[^s1][^s2]
- **Workflow orchestration.** AWS Step Functions coordinates distributed components as visual state machines. Amazon MWAA runs Apache Airflow for data pipelines. Amazon SWF is the older, poll-based way to coordinate background jobs with parallel or sequential steps.[^s1][^s2]
- **Data and document integration.** Amazon AppFlow moves data between SaaS applications and AWS stores such as Amazon S3 and Amazon Redshift. AWS B2B Data Interchange turns EDI documents into JSON or XML for downstream processing.[^s1]

AWS's decision guide draws the boundary a little differently. It leaves out AppFlow, B2B Data Interchange and SWF, but adds the streaming services Amazon Kinesis Data Streams and Amazon MSK. It sorts the result into five needs: event capacity, messaging, streaming, workflows and scheduling.[^s2]

## Why it exists

The Well-Architected Reliability Pillar treats loose coupling as a high-risk item if it is missing (REL04-BP02). It recommends putting a durable intermediate layer, such as a queue or stream, between producers and consumers. That way one component's failure or slowdown stays contained, and each side can change and scale on its own.[^s3] The pillar names EventBridge, SQS, SNS, Step Functions and Kinesis as the AWS tools for doing this.[^s3] It lists as anti-patterns direct API calls between tiers with no asynchronous fallback, integration through a shared database, and ignoring back pressure when a downstream component cannot keep up.[^s3]

## How to choose within the category

The decision guide's main distinctions (last updated April 2025) are:[^s2]

- **Push or pull.** EventBridge and SNS push to targets, while SQS and Kinesis hold messages until consumers fetch them.[^s2]
- **Ordering and replay.** Ordering comes from the FIFO variants of SNS and SQS. Replay is available with Kinesis and some SNS FIFO setups, but not with Amazon MQ.[^s2]
- **Payload and protocol.** Amazon MQ suits larger text or binary messages and standard protocols such as AMQP and MQTT. The guide describes its retention as unlimited.[^s2]
- **Operating model.** Amazon MQ and MSK are managed open-source engines that are compatible with on-premises tooling. SQS, SNS, EventBridge and Step Functions are serverless and billed by usage.[^s2]

The guide also weighs operational overhead, open-source standardisation, portability of applications and automation, feature velocity, and team skills. Teams that already run a self-managed broker often move to its managed equivalent with the least disruption.[^s2] AWS points out that the services are often combined. For example, EventBridge or SNS can fan events out to an SQS buffer, EventBridge Pipes can pull from SQS, Amazon MQ, Kinesis or MSK onto an event bus, and events can be sent to a stream for analytics.[^s2]

## Quotas, pricing and gotchas

There is no category-level quota or price. Each service has its own limits and billing model, so check the service node or its documentation, and the decision guide itself gives no per-service quotas.[^s2] Category membership also differs between AWS sources. The DVA-C02 exam guide files AWS AppSync under Application Integration, for instance, while the overview whitepaper does not.[^s1][^s4] On lifecycle, AWS's June 2026 service-availability update moved several services into maintenance or sunset. None of them was in this category.[^s5]

## Connections

- **Broader:** Amazon Web Services as a whole; application integration is one of the portfolio's top-level categories.[^s1]
- **Has part:** Amazon MQ, Amazon SWF and Amazon AppFlow. **Related:** Amazon SQS, Amazon SNS, Amazon EventBridge and AWS Step Functions, plus the comparison node `kt:sqs-vs-sns-vs-eventbridge`.[^s1][^s2]
- **Implements** event-driven architecture: producers emit events and consumers react without knowing about each other.[^s2][^s3]
- **Enables** the Reliability Pillar's loose-coupling best practice, REL04-BP02.[^s3]
- **Covered by AWS Certified Developer – Associate:** DVA-C02 has an Application Integration category of in-scope services (AppSync, EventBridge, SNS, SQS and Step Functions).[^s4]

## Sources
[^s1]: Amazon Web Services, "Application integration - Overview of Amazon Web Services". https://docs.aws.amazon.com/whitepapers/latest/aws-overview/application-integration (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Choosing an AWS application integration service" (AWS Decision Guide). https://docs.aws.amazon.com/decision-guides/latest/application-integration-on-aws-how-to-choose/application-integration-on-aws-how-to-choose.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "REL04-BP02 Implement loosely coupled dependencies - Reliability Pillar". https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/rel_prevent_interaction_failure_loosely_coupled_system.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Developer - Associate (DVA-C02)". https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/dva-02-in-scope-services.html (accessed 2026-11-18)
[^s5]: Amazon Web Services, What's New with AWS, "AWS Service Availability Updates" (June 2026). https://aws.amazon.com/about-aws/whats-new/2026/06/aws-service-availability/ (accessed 2026-11-18)
