---
id: kt:aws-step-functions
type: Service
title: AWS Step Functions
aliases: [Step Functions, Amazon Step Functions, SFN]
domain: serverless-and-integration
summary: >-
  AWS's serverless workflow orchestration service: you define state machines in the Amazon States
  Language, and Step Functions runs each step, calling AWS services, retrying failures and passing
  data between steps, as durable Standard or high-volume Express workflows.
verified: 2026-11-13
status: current
broader: [kt:aws-serverless-services]
related: [kt:aws-lambda, kt:amazon-eventbridge, kt:amazon-sqs]
relations:
  - {predicate: alternativeTo, object: kt:amazon-swf}
  - {predicate: governedBy, object: kt:step-functions-quotas}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is Step Functions?"
    publisher: Amazon Web Services
    url: https://docs.amazonaws.cn/en_us/step-functions/latest/dg/welcome.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Step Functions service quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/step-functions/latest/dg/limits.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "AWS Step Functions pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/step-functions/pricing
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "AWS Step Functions FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/pt/step-functions/faqs/
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "AWS Step Functions Express Workflows - Serverless Applications Lens"
    publisher: Amazon Web Services (AWS Well-Architected)
    url: https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/step-functions-workflows.html
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "AWS Step Functions now supports Service Quotas"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/09/aws-step-functions-service-quotas
    type: official-blog
    accessed: 2026-11-13
  - id: s7
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-13
created: 2026-11-13
---

# AWS Step Functions

AWS Step Functions is a serverless orchestration service for coordinating distributed applications, automating business processes, orchestrating microservices and building data and machine learning pipelines.[^s1] A workflow is a **state machine** whose steps are **states**, and each run of it is an **execution**.[^s1] Definitions are written in JSON using the Amazon States Language (ASL), and Step Functions retries failed steps and records the state of every step.[^s4]

## How it works

Task states do the work, usually by calling another AWS service. Flow states such as `Choice`, `Parallel` and `Map` add branching, concurrency and iteration, and `Retry` and `Catch` handle errors declaratively.[^s1] Work can also be handed to **activities**, which are your own workers running on EC2, ECS, on-premises servers or elsewhere. They poll Step Functions for tasks, which makes them easy to run behind a firewall.[^s4] Variables and JSONata data transformation can be added to existing workflows without moving to a new ASL version.[^s4]

Integrations come in two kinds. **AWS SDK integrations** can call over 200 services and thousands of API actions directly. A smaller set of **optimized integrations**, including Lambda, ECS/Fargate, Batch, Glue, SageMaker AI, DynamoDB, SNS, SQS and EventBridge, adds custom handling.[^s1] Three patterns apply. Request Response returns once the call is accepted, Run a Job (`.sync`) waits for the job to finish, and Wait for Callback (`.waitForTaskToken`) pauses until an external system returns a task token.[^s1]

The `Map` state runs the same steps over a collection. Inline mode allows up to 40 parallel branches. Distributed mode allows up to 10,000 and is optimized for S3 inputs such as a bucket, manifest, or JSON or CSV file. Each iteration runs as its own child execution, so it avoids the parent's payload and history limits.[^s4]

## Standard vs. Express workflows

| | Standard | Express |
|---|---|---|
| Max duration | 1 year | 5 minutes |
| Semantics | Exactly-once | At-least-once (async) |
| Execution history | Kept by Step Functions for 90 days | Sent to CloudWatch Logs |
| Integration patterns | Request Response, `.sync`, callback | Request Response only |
| Billing | Per state transition | Per request plus duration and memory |[^s1][^s2]

The Well-Architected Serverless Lens recommends Standard for long-running, auditable work and for non-idempotent actions such as payments. It recommends Express for high-volume event processing such as IoT ingestion and streaming transforms, which must be idempotent because an execution may run more than once.[^s5] Synchronous Express runs are at-most-once.[^s5]

## Quotas, pricing and gotchas

As of 2026-11, these limits are hard: 256 KiB maximum input or output per task, state or execution, a 1 MB definition, and 25,000 history events per Standard execution, after which the execution fails.[^s2] Standard history is retained 90 days, and you can reduce that to 30.[^s2] Open executions default to 1,000,000 per account per Region and can be raised. StartExecution for Standard uses a bucket of 1,300 with a refill of 300 per second in N. Virginia, Oregon and Ireland, and 800 and 150 elsewhere. Express uses 6,000 and 6,000 in all Regions. All of these are adjustable.[^s2] New accounts start with reduced state-transition quotas that AWS raises automatically with use.[^s2] Since September 2025, Step Functions quotas can be viewed and increased in the Service Quotas console.[^s6]

Pricing (us-east-1, as of 2026-11): Standard costs $0.000025 per state transition, retries included. The first 4,000 transitions a month are free, and that allowance does not expire after the 12-month Free Tier.[^s3] Express costs $1.00 per million requests plus duration, which is rounded up to 100 ms and billed in 64 MB memory increments. The services a workflow calls are billed separately.[^s3]

A common gotcha is polling loops in Standard workflows. Every loop iteration costs transitions, so the Serverless Lens suggests callbacks or `.sync` integrations instead.[^s5] A large payload should be stored in S3 and passed by reference, because the 256 KiB limit cannot be raised.[^s2]

## When to use it (and when not)

Use Step Functions when you need visible, retryable, multi-step orchestration across services. With SQS you would build that task tracking and workflow logic yourself.[^s4] For new applications AWS recommends Step Functions over Amazon SWF. SWF still fits cases that need external signals or child processes that return results to a parent.[^s4] A hybrid design is common: a Standard workflow handles human approval and long waits, then starts Express workflows for high-volume backend updates.[^s5]

## Connections

- **Broader:** one of the AWS serverless services, listed under Application Integration in the SAA-C03 in-scope services.[^s7]
- **Related:** AWS Lambda, Amazon SQS and Amazon EventBridge are all optimized integrations that a workflow can call directly.[^s1]
- **Alternative to:** Amazon SWF for workflow coordination.[^s4]
- **Governed by** the Step Functions quotas above, and **scoped to** a Region (quotas apply per account per Region).[^s2]
- **Monitored by:** Express execution history is delivered to CloudWatch Logs, and quota usage appears as CloudWatch metrics.[^s1][^s6]
- **Covered by:** AWS Certified Solutions Architect - Associate (SAA-C03).[^s7]

## Sources
[^s1]: Amazon Web Services, "What is Step Functions?". https://docs.amazonaws.cn/en_us/step-functions/latest/dg/welcome.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Step Functions service quotas". https://docs.aws.amazon.com/step-functions/latest/dg/limits.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "AWS Step Functions pricing". https://aws.amazon.com/step-functions/pricing (accessed 2026-11-13)
[^s4]: Amazon Web Services, "AWS Step Functions FAQs". https://aws.amazon.com/pt/step-functions/faqs/ (accessed 2026-11-13)
[^s5]: Amazon Web Services, "AWS Step Functions Express Workflows - Serverless Applications Lens". https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/step-functions-workflows.html (accessed 2026-11-13)
[^s6]: Amazon Web Services, "AWS Step Functions now supports Service Quotas". https://aws.amazon.com/about-aws/whats-new/2025/09/aws-step-functions-service-quotas (accessed 2026-11-13)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-13)
