---
id: kt:aws-lambda-durable-functions
type: Feature
title: AWS Lambda durable functions
aliases: [Lambda durable functions, Lambda durable execution, AWS Durable Execution SDK]
domain: serverless-and-integration
summary: >-
  A Lambda capability that lets an ordinary function run a multi-step workflow for up to one year by
  checkpointing each durable operation and replaying the handler after a pause or failure, skipping
  work that already completed.
verified: 2026-11-19
status: current
broader: [kt:aws-lambda]
related: [kt:event-driven-architecture]
relations:
  - {predicate: alternativeTo, object: kt:aws-step-functions}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: configuredVia, object: kt:aws-sam}
  - {predicate: integratesWith, object: kt:amazon-sqs}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "Lambda durable functions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/durable-functions.md
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Basic concepts - Lambda durable functions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/durable-basic-concepts.md
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Invoking Lambda durable functions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/durable-invoking.html
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "Durable functions or Step Functions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/durable-step-functions.md
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Security and permissions for Lambda durable functions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/durable-security.html
    type: official-docs
    accessed: 2026-11-19
  - id: s6
    title: "AWS Lambda announces durable functions for multi-step applications and AI workflows"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/lambda-durable-multi-step-applications-ai-workflows
    type: official-blog
    accessed: 2026-11-19
  - id: s7
    title: "AWS Lambda durable functions are now available in 16 additional AWS Regions"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/04/lambda-durable-functions-16-new-regions
    type: official-blog
    accessed: 2026-11-19
  - id: s8
    title: "Building fault-tolerant applications with AWS Lambda durable functions"
    publisher: Amazon Web Services (AWS Compute Blog)
    url: https://aws.amazon.com/blogs/compute/building-fault-tolerant-long-running-application-with-aws-lambda-durable-functions/
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# AWS Lambda durable functions

AWS Lambda durable functions let a regular Lambda function act as a long-running, fault-tolerant workflow. The full lifecycle of one run is called a *durable execution*. It can last up to one year, and it uses checkpoints to record progress and replay to recover from failures without redoing finished work.[^s1][^s2] AWS announced the feature on 2 December 2025, during re:Invent, as generally available in US East (Ohio).[^s6] Later announcements widened the Region list, including 16 more Regions in April 2026, so check AWS Capabilities by Region for current coverage.[^s7][^s6]

## How it works

You wrap your handler with the AWS Durable Execution SDK, which hands your code a `DurableContext` alongside the event. You write ordinary sequential code, but wrap calls to other systems in durable operations.[^s1][^s2] **Steps** run business logic with built-in retries and save their result as a checkpoint. **Waits** suspend the function with no compute charge, for a set time, an external callback or a polled condition. **Invoke** calls another Lambda function, durable or not, and checkpoints the result.[^s2] The Compute Blog also covers `waitForCallback` for human approvals, `waitForCondition` for polling, and `parallel` and `map` for concurrent branches and per-item checkpoints.[^s8]

When a function resumes after a wait or an interruption, Lambda invokes it again from the top and replays the checkpoint log. Completed operations return their stored values instead of running again.[^s2] So the handler must be deterministic outside of steps. Random numbers, the current time and other side effects belong inside steps, or replay can take a different path.[^s2] A failed step retries on its own without repeating earlier checkpoints, but every invocation, replays included, is billed as compute.[^s8]

The SDK is available for JavaScript and TypeScript, Python, Java, Go and C# (.NET).[^s1][^s2] The launch supported Python 3.13 and 3.14 and Node.js 22 and 24. By the April 2026 expansion, Java 17 and later was supported too, and functions could be deployed with CloudFormation, AWS SAM or the AWS CDK.[^s6][^s7]

## Limits, configuration and gotchas

These limits are from the developer guide as of 2026-11 and apply in every Region that offers the feature. A `DurableConfig` adds two settings that cover the whole execution, not one invocation. The **execution timeout** defaults to 86,400 seconds (24 hours), with a minimum of 60 seconds and a maximum of 31,536,000 seconds (one year). The **retention period** for execution history and checkpoint data defaults to 14 days, with a range of 1 to 90 days, counted from when the execution reaches a terminal state.[^s2] The ordinary function timeout still limits each invocation: 15 minutes, or 90 minutes for asynchronous and event-source-mapping invocations on Lambda Managed Instances. Waits count toward the execution timeout but not the function timeout.[^s2]

How you invoke a durable function matters:

- **Use a qualified identifier.** Durable functions accept only a version, an alias or `$LATEST`; unqualified names and ARNs are rejected.[^s3]
- **Pin a version.** Each execution stays on the version that started it, but `$LATEST` executions resume on whatever code is current, which can break replay. AWS advises keeping `$LATEST` for prototyping only.[^s3][^s8]
- **Pick the invocation type.** Synchronous calls are capped at 15 minutes because the caller waits for the whole execution, waits included. Use asynchronous invocation for runs of up to a year.[^s3]
- **Know what's supported.** Dead-letter queues work, but Lambda destinations and cross-account chained invokes don't. An execution name makes starts idempotent.[^s3]
- **Durable from the start.** You can't add a durable configuration to an existing non-durable function.[^s8]

## Security

The execution role needs `lambda:CheckpointDurableExecution` and `lambda:GetDurableExecutionState`. The `AWSLambdaBasicDurableExecutionRolePolicy` managed policy grants these plus basic CloudWatch Logs access. The console adds them for you, but with the CLI or CloudFormation you add them yourself.[^s5] Separate actions cover reading, listing and stopping executions and sending callback results. Because durable execution ARNs carry a qualifier, resource patterns must include one, such as `function:<name>:*`.[^s5] Checkpoint data is encrypted at rest and in transit, and you can supply a customer managed AWS KMS key.[^s5]

## When to use it (and when not)

AWS positions durable functions for application logic that lives in Lambda. Good fits include distributed transactions, order and payment flows, multi-step AI workflows, and processes that wait days for a human.[^s1][^s4] Choose AWS Step Functions when you need a visual workflow others can review, native integrations with more than 220 AWS services, or orchestration that doesn't depend on your runtime. AWS also describes hybrid designs, with Step Functions coordinating services and durable functions handling the logic inside Lambda.[^s4]

## Connections

- **Broader:** AWS Lambda; durable functions are regular Lambda functions with checkpoint and replay added.[^s1]
- **Alternative to AWS Step Functions** for orchestration; AWS publishes a decision framework comparing them.[^s4]
- **Secured by AWS IAM** through the execution-role actions and managed policy above.[^s5]
- **Configured via AWS SAM**, which supports local invokes, callbacks and execution history for testing.[^s8][^s7]
- **Integrates with Amazon SQS** and other event sources through event source mappings, which deliver batches of records to the function.[^s3]
- **Scoped to AWS Regions:** availability is listed per Region, and the list has grown since launch.[^s6][^s7]
- **Related to event-driven architecture:** event sources, callbacks and waits fit the event-driven model.[^s3][^s8]

## Sources
[^s1]: Amazon Web Services, "Lambda durable functions". https://docs.aws.amazon.com/lambda/latest/dg/durable-functions.md (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Basic concepts - Lambda durable functions". https://docs.aws.amazon.com/lambda/latest/dg/durable-basic-concepts.md (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Invoking Lambda durable functions". https://docs.aws.amazon.com/lambda/latest/dg/durable-invoking.html (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Durable functions or Step Functions". https://docs.aws.amazon.com/lambda/latest/dg/durable-step-functions.md (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Security and permissions for Lambda durable functions". https://docs.aws.amazon.com/lambda/latest/dg/durable-security.html (accessed 2026-11-19)
[^s6]: Amazon Web Services, What's New, "AWS Lambda announces durable functions for multi-step applications and AI workflows" (2025-12-02). https://aws.amazon.com/about-aws/whats-new/2025/12/lambda-durable-multi-step-applications-ai-workflows (accessed 2026-11-19)
[^s7]: Amazon Web Services, What's New, "AWS Lambda durable functions are now available in 16 additional AWS Regions" (2026-04-22). https://aws.amazon.com/about-aws/whats-new/2026/04/lambda-durable-functions-16-new-regions (accessed 2026-11-19)
[^s8]: Amazon Web Services, AWS Compute Blog, "Building fault-tolerant applications with AWS Lambda durable functions" (2026-02-06). https://aws.amazon.com/blogs/compute/building-fault-tolerant-long-running-application-with-aws-lambda-durable-functions/ (accessed 2026-11-19)
