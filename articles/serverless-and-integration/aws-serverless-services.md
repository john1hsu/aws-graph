---
id: kt:aws-serverless-services
type: Concept
title: AWS serverless services
aliases: [AWS serverless, serverless on AWS, AWS serverless portfolio]
domain: serverless-and-integration
summary: >-
  The family of AWS services you use without provisioning or managing servers, billed for what you
  consume and scaled automatically: compute (Lambda, Fargate), API and integration (API Gateway,
  SQS, SNS, EventBridge, Step Functions) and data (DynamoDB, S3, Aurora Serverless).
verified: 2026-11-09
status: concept
broader: [kt:cloud-architecture]
related: [kt:amazon-api-gateway, kt:amazon-sqs, kt:aws-step-functions, kt:amazon-eventbridge, kt:aws-appsync]
relations:
  - {predicate: hasPart, object: kt:aws-lambda}
  - {predicate: hasPart, object: kt:aws-fargate}
  - {predicate: hasPart, object: kt:amazon-dynamodb}
  - {predicate: implements, object: kt:event-driven-architecture}
  - {predicate: configuredVia, object: kt:aws-sam}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Choosing an AWS serverless service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/serverless/latest/devguide/
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "AWS Fargate or AWS Lambda?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/decision-guides/fargate-or-lambda.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Amazon SQS, Amazon SNS, or Amazon EventBridge?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/decision-guides/sns-or-sqs-or-eventbridge.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "General design principles - Serverless Applications Lens"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/general-design-principles.html
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "AWS serverless capabilities - Optimizing Enterprise Economics with Serverless Architectures"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/optimizing-enterprise-economics-with-serverless/aws-serverless-capabilities.html
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "Top announcements of AWS re:Invent 2025"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/aws/top-announcements-of-aws-reinvent-2025
    type: official-blog
    accessed: 2026-11-09
  - id: s7
    title: "Content Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS serverless services

"Serverless" on AWS is not a single product but a family of managed services that share three traits: you build and run applications without provisioning or managing servers, you pay only for the resources you consume, and capacity scales automatically with demand.[^s1] AWS's own whitepaper frames the same idea as compute, storage and messaging delivered as managed services rather than as software installed on virtual machines, with AWS taking on patching, scaling and infrastructure management.[^s5]

## What is in the family

AWS's serverless decision guide groups the portfolio by job:[^s1]

- **Compute:** AWS Lambda for event-driven functions, and AWS Fargate for containers without servers or clusters to manage.[^s1]
- **API layer:** Amazon API Gateway (HTTP, REST and WebSocket APIs), AWS AppSync for managed GraphQL, and Lambda function URLs.[^s1]
- **Application integration:** Amazon SQS queues, Amazon SNS publish/subscribe, and Amazon EventBridge event buses with content-based routing.[^s1]
- **Orchestration:** AWS Step Functions, with Standard Workflows (exactly-once, up to one year) and Express Workflows (high volume, up to five minutes, at-least-once).[^s1]
- **Data:** Amazon DynamoDB, Amazon S3 and Amazon Aurora Serverless.[^s1]
- **Tooling:** AWS SAM and AWS CDK for deployment, Amazon CloudWatch and AWS X-Ray for observability.[^s1]

The older AWS whitepaper on serverless economics lists a similar set and adds Amazon Kinesis for streaming, plus AWS WAF and AWS CloudTrail on the security side; AWS marks that page as kept for historical reference.[^s5]

## How the pieces fit together

Serverless applications are usually event-driven: producers emit events about a change in state and do not need to know which consumers react, so components deploy and scale independently. The canonical example is an S3 upload that triggers a Lambda function to make a thumbnail.[^s1] Each messaging pattern maps to a service: queue (SQS), event bus (EventBridge), fan-out (SNS), orchestration (Step Functions), synchronous API (API Gateway) and streams (Kinesis).[^s1]

The Well-Architected Serverless Applications Lens adds seven design principles. Keep functions short and single-purpose; reason about concurrent rather than total requests; share nothing locally; assume no hardware affinity; orchestrate with state machines instead of chaining functions in code; trigger transactions from events; and design for failures and duplicates by making operations idempotent.[^s4]

## Quotas, pricing and gotchas

Limits differ sharply between the two compute options (AWS decision guide, as of 2026-11). A Lambda invocation runs for at most 15 minutes with 128 MB to 10 GB of memory and up to 6 vCPU. The default is 1,000 concurrent executions per account per Region, and that quota is adjustable. A Fargate task has no hard duration limit and can use 0.25 to 32 vCPU and up to 244 GB of memory.[^s2] Lambda bills per request and per GB-second, with a monthly free tier of 1 million requests and 400,000 GB-seconds. Fargate bills vCPU and memory per second with a one-minute minimum. Both can use Compute Savings Plans, which AWS says can cut costs by up to 66%.[^s2]

Lambda is usually cheaper at low or spiky traffic because its cost tracks requests, while Fargate tends to win for sustained high throughput.[^s2] On the integration side, SQS keeps messages for up to 14 days. EventBridge does not persist events, retries delivery for up to 24 hours, and does not guarantee ordering, so use SQS FIFO when sequence matters.[^s3]

A common gotcha is cold starts: Lambda initialization adds latency. AWS points to SnapStart (Java, Python, .NET) or Provisioned Concurrency to reduce it.[^s1]

## What changed (2025-12)

At re:Invent 2025 (30 November to 4 December 2025), AWS launched Lambda durable functions and Lambda Managed Instances, which blur the old boundaries of serverless compute. Durable functions run multi-step workflows for up to a year without charging for idle waits. Managed Instances run Lambda functions on EC2 capacity with EC2 pricing options.[^s6] AWS's decision guide now recommends Managed Instances for steady, predictable workloads where EC2 pricing pays off.[^s2]

## When to use it (and when not)

Pick serverless when traffic is unpredictable or event-driven and you want to avoid operating servers. Choose Lambda for short event handlers and wait-heavy workflows, and Fargate for long-running processes, persistent connections or specific CPU and memory needs.[^s2] AWS lists selection factors that include execution duration, concurrency, cost model, operational complexity, cold-start behaviour, integration depth and portability.[^s1] Workloads that need host-level control, special hardware or steady full utilisation are often better on Amazon EC2 or Lambda Managed Instances.[^s2][^s6]

## Connections

- **Broader:** cloud architecture (`kt:cloud-architecture`); serverless is an architectural approach as much as a product set.[^s5]
- **Has part:** AWS Lambda, AWS Fargate and Amazon DynamoDB, the compute and data pillars of the family.[^s1]
- **Related:** Amazon API Gateway, Amazon SQS, AWS Step Functions and Amazon EventBridge, the API, integration and orchestration layers.[^s1][^s3]
- **Implements** event-driven architecture, the pattern most serverless applications follow.[^s1][^s4]
- **Configured via** AWS SAM (with AWS CDK as the other deployment option named by AWS).[^s1]
- **Covered by AWS Certified Cloud Practitioner:** Task Statement 3.3 of CLF-C02 asks candidates to recognise when to use serverless compute options such as Fargate and Lambda.[^s7]

## Sources
[^s1]: Amazon Web Services, "Choosing an AWS serverless service". https://docs.aws.amazon.com/serverless/latest/devguide/ (accessed 2026-11-09)
[^s2]: Amazon Web Services, "AWS Fargate or AWS Lambda?". https://docs.aws.amazon.com/decision-guides/latest/decision-guides/fargate-or-lambda.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Amazon SQS, Amazon SNS, or Amazon EventBridge?". https://docs.aws.amazon.com/decision-guides/latest/decision-guides/sns-or-sqs-or-eventbridge.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "General design principles - Serverless Applications Lens". https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/general-design-principles.html (accessed 2026-11-09)
[^s5]: Amazon Web Services, "AWS serverless capabilities - Optimizing Enterprise Economics with Serverless Architectures" (whitepaper, historical reference). https://docs.aws.amazon.com/whitepapers/latest/optimizing-enterprise-economics-with-serverless/aws-serverless-capabilities.html (accessed 2026-11-09)
[^s6]: Amazon Web Services, AWS News Blog, "Top announcements of AWS re:Invent 2025". https://aws.amazon.com/blogs/aws/top-announcements-of-aws-reinvent-2025 (accessed 2026-11-09)
[^s7]: Amazon Web Services, "Content Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html (accessed 2026-11-09)
