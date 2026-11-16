---
id: kt:amazon-mq
type: Service
title: Amazon MQ
aliases: [AWS MQ, Amazon MQ for ActiveMQ, Amazon MQ for RabbitMQ]
domain: serverless-and-integration
summary: >-
  AWS's managed message broker service for Apache ActiveMQ Classic and RabbitMQ, which lets
  applications that speak standard messaging protocols such as JMS, AMQP, MQTT and STOMP move to AWS
  without rewriting their messaging code, while AWS runs provisioning, patching and failover.
verified: 2026-11-16
status: current
broader: [kt:aws-application-integration-services]
related: [kt:amazon-sqs, kt:amazon-sns, kt:amazon-eventbridge, kt:seven-rs-migration-strategies]
relations:
  - {predicate: alternativeTo, object: kt:amazon-sqs}
  - {predicate: dependsOn, object: kt:amazon-efs}
  - {predicate: dependsOn, object: kt:amazon-ebs}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: securedBy, object: kt:amazon-vpc}
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is Amazon MQ?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/amazon-mq/latest/developer-guide/welcome.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Deployment options for Amazon MQ for ActiveMQ brokers"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/amazon-mq/latest/developer-guide/amazon-mq-broker-architecture.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Amazon MQ release notes"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-release-notes.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Quotas in Amazon MQ"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/amazon-mq/latest/developer-guide/amazon-mq-limits.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "Amazon MQ pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/amazon-mq/pricing/
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "Amazon MQ now supports RabbitMQ version 4.2"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/amazon-mq-rabbitmq-42
    type: official-blog
    accessed: 2026-11-16
  - id: s7
    title: "What's the Difference Between Amazon MQ and Amazon SQS?"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compare/mq-and-sqs/
    type: official-docs
    accessed: 2026-11-16
created: 2026-11-16
---

# Amazon MQ

Amazon MQ is AWS's managed message broker service. It runs brokers on two open-source engines, Apache ActiveMQ Classic and RabbitMQ, and takes over setup, maintenance and version upgrades, which it applies during a scheduled maintenance window.[^s1] Its main selling point is compatibility: you can stand up a new broker, or move an existing one, without rewriting the messaging code that talks to it.[^s1] AWS positions it for application migration, hybrid messaging and enterprise integration, and describes it as a drop-in replacement for on-premises ActiveMQ or RabbitMQ brokers.[^s7]

## How it works

A **broker** is the unit you provision: a broker environment that applications reach over industry-standard protocols such as JMS, AMQP, MQTT and STOMP.[^s1][^s7] Messages are encrypted at rest and in transit, connections use TLS, a broker can be limited to a private endpoint inside your VPC, and IAM controls who can manage it. Broker metrics go to Amazon CloudWatch every minute.[^s1]

**ActiveMQ** brokers come as a single instance in one Availability Zone, or as an **active/standby** pair spread over two AZs. A single-instance broker can store data on Amazon EFS, which keeps it redundantly across several AZs, or on Amazon EBS, which favours low latency and throughput within one AZ. In an active/standby pair, both brokers use shared storage, and the standby takes over when the active broker fails or is under maintenance. Clients should connect through the ActiveMQ failover transport so they can reach either endpoint.[^s2] ActiveMQ also offers asynchronous cross-Region data replication, with an API call that promotes the replica broker in the other Region.[^s1]

**RabbitMQ** brokers are a single instance or a three-node cluster. On RabbitMQ 4.x, quorum queues are the only replicated, durable queue type. Classic queue mirroring is no longer supported, though non-replicated classic queues still are.[^s6] Each quorum queue has a leader and followers in different AZs. If a node fails, a new leader is elected and delivery continues.[^s1]

## Lifecycle and recent changes

Amazon MQ is generally available. Engine versions are retired on a published schedule. For example, RabbitMQ 3.11 and 3.12 and ActiveMQ 5.17 all reached end of support in 2025. The `mq.t2.micro` and `mq.m4.large` ActiveMQ instance types reached end of support on 12 May 2025.[^s3] RabbitMQ 4.2 arrived on 20 November 2025 on Graviton-based `m7g` instances. It added native AMQP 1.0, the Raft-based Khepri metadata store, local shovels and quorum-queue message priorities.[^s6] In 2026 AWS added an in-place upgrade from 3.13 to 4.2, which blocks all connections and so causes downtime. It also shipped ActiveMQ 5.19 and RabbitMQ 4.3, which removes transient queues, global QoS and classic queue v1.[^s3]

## Quotas, pricing and gotchas

As of 2026-11, an account can have 200 brokers per Region. ActiveMQ allows 2,000 wire-level connections per protocol on `large` instance types and 300 on `micro` types. RabbitMQ connection limits per node are set by instance type and cannot be overridden. A broker can have at most 5 security groups and 50 tags. CloudWatch monitors only the first 1,000 ActiveMQ destinations or the first 500 RabbitMQ queues. AWS says many of these limits can be raised through Service Quotas, but the page doesn't say which ones.[^s4]

Billing is per broker-instance hour, at one-second resolution, plus storage in GB-months and standard data transfer. Traffic between brokers in different AZs costs $0.01/GB in each direction. Here are the US East (N. Virginia) example rates as of 2026-11:[^s5]

- `mq.m5.large` ActiveMQ active/standby: $0.576/hour.
- `mq.m7g.large` RabbitMQ cluster: $0.2734/hour per broker node.
- ActiveMQ storage: $0.30/GB-month on EFS.
- EBS storage (both engines): $0.10/GB-month.[^s5]

Unlike SQS, a broker costs money while it sits idle. Size and count brokers on purpose.[^s7]

## When to use it (and when not)

Choose Amazon MQ when existing applications depend on JMS, AMQP, MQTT or STOMP semantics, or when you are rehosting an on-premises broker. Choose Amazon SQS for new cloud-native work such as decoupling microservices, work queues and buffering. SQS has no brokers to size and charges per request.[^s7] For fan-out or event routing between AWS services, SNS and EventBridge are the serverless counterparts.

## Connections

Amazon MQ sits with SQS, SNS and EventBridge among AWS's application integration services. It is the **alternative to** SQS when you need protocol compatibility rather than a native AWS API.[^s7] ActiveMQ brokers **depend on** EFS or EBS for message storage, and high-availability designs are **scoped to** multiple Availability Zones.[^s2] Brokers are **secured by** VPC private endpoints and **monitored by** CloudWatch.[^s1] Moving a broker unchanged is a rehost in the 7 Rs migration strategies.[^s7] The service is in scope for the AWS Certified Solutions Architect – Associate exam.

## Sources
[^s1]: Amazon Web Services, "What is Amazon MQ?". https://docs.aws.amazon.com/en_us/amazon-mq/latest/developer-guide/welcome.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Deployment options for Amazon MQ for ActiveMQ brokers". https://docs.aws.amazon.com/en_us/amazon-mq/latest/developer-guide/amazon-mq-broker-architecture.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Amazon MQ release notes". https://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-release-notes.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Quotas in Amazon MQ". https://docs.aws.amazon.com/en_us/amazon-mq/latest/developer-guide/amazon-mq-limits.html (accessed 2026-11-16)
[^s5]: Amazon Web Services, "Amazon MQ pricing". https://aws.amazon.com/amazon-mq/pricing/ (accessed 2026-11-16)
[^s6]: Amazon Web Services, "Amazon MQ now supports RabbitMQ version 4.2". https://aws.amazon.com/about-aws/whats-new/2025/11/amazon-mq-rabbitmq-42 (accessed 2026-11-16)
[^s7]: Amazon Web Services, "What's the Difference Between Amazon MQ and Amazon SQS?". https://aws.amazon.com/compare/mq-and-sqs/ (accessed 2026-11-16)
