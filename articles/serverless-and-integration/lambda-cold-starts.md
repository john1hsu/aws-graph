---
id: kt:lambda-cold-starts
type: Concept
title: Lambda cold starts
aliases: [AWS Lambda cold start, Lambda cold start latency, Lambda Init phase latency]
domain: serverless-and-integration
summary: >-
  The extra latency, and billed time, an AWS Lambda invocation incurs when Lambda must create
  and initialize a new execution environment (download code, start the runtime and extensions,
  run static init code) before the handler runs; mitigated by lean initialization, SnapStart and
  provisioned concurrency.
verified: 2026-11-15
status: concept
broader: [kt:aws-lambda]
related: [kt:aws-serverless-services, kt:amazon-api-gateway, kt:lambda-snapstart, kt:lambda-provisioned-concurrency]
relations:
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: contrastsWith, object: kt:aws-fargate}
sources:
  - id: s1
    title: "Understanding the Lambda execution environment lifecycle - AWS Lambda"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/operatorguide/static-initialization.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Improving startup performance with Lambda SnapStart"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/snapstart.md
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Configuring provisioned concurrency for an Event Function - AWS Lambda"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/provisioned-concurrency.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "AWS Lambda standardizes billing for INIT Phase"
    publisher: AWS Compute Blog
    url: https://aws.amazon.com/blogs/compute/aws-lambda-standardizes-billing-for-init-phase
    type: official-blog
    accessed: 2026-11-15
  - id: s5
    title: "AWS Lambda now supports SnapStart for container image functions"
    publisher: What's New with AWS
    url: https://aws.amazon.com/about-aws/whats-new/2026/07/aws-lambda-snapstart-container/
    type: official-blog
    accessed: 2026-11-15
created: 2026-11-15
---

# Lambda cold starts

A cold start is the time AWS Lambda spends downloading a function's code and setting up a fresh execution environment before the handler can run. It adds latency to that one request, and it is billed. AWS reports that cold starts happen in under 1% of invocations and last anywhere from under 100 ms to more than a second. They are most noticeable in development and test functions, which are invoked rarely and so seldom find a warm environment.[^s1]

## How it works

Every execution environment goes through three phases. In **Init**, Lambda starts extensions, bootstraps the runtime and runs the function's static code (everything outside the handler); the phase ends when the runtime and each extension call the `Next` API. **Invoke** runs the handler under the function timeout. **Shutdown** gives extensions up to 0 ms, 500 ms or 2,000 ms to clean up, depending on whether none, an internal or an external extension is registered.[^s1]

After an invocation Lambda freezes the environment and keeps it around for reuse. A warm start skips Init: objects created outside the handler (SDK clients, database connections) are still there, and `/tmp` keeps its contents. Lambda still recycles environments every few hours for maintenance and runtime updates, so even busy functions see occasional cold starts, and every scale-out to a new concurrent environment is one too.[^s1]

On-demand Init is capped at 10 seconds; if it overruns, Lambda retries initialization on the first invocation under the function timeout. Functions using provisioned concurrency, SnapStart or Lambda Managed Instances get a longer limit: 130 seconds or the function timeout (up to 900 seconds), whichever is higher.[^s1]

## Billing and monitoring

Since 1 August 2025, Init time is part of Billed Duration for on-demand invocations of ZIP-packaged functions on managed runtimes, charged at the function's normal duration rate. Before then that configuration did not bill Init, while custom runtimes, container (OCI) images and provisioned concurrency already did. AWS expected little bill impact because cold starts are rare.[^s4] Init time shows as `Init Duration` in each invocation's CloudWatch Logs `REPORT` line, as the `init_duration` metric when Lambda Insights is on, and in `platform-initReport` events in JSON logs at INFO level or above.[^s4][^s3]

## Reducing cold starts

- **Lean initialization.** Static init is the biggest source of startup latency. Ship only needed dependencies, keep packages and layers small, import individual SDK clients rather than a whole SDK, split oversized functions, and lazy-load objects only some code paths use. Do create reusable clients and connections outside the handler so warm invocations reuse them.[^s1][^s4]
- **SnapStart.** Lambda initializes a published version once, takes an encrypted Firecracker microVM snapshot of memory and disk, and resumes new environments from it, cutting multi-second startups to as low as sub-second. It supports Java 11+, Python 3.12+ and .NET 8+, on published versions and aliases only (not `$LATEST`), and cannot be combined with provisioned concurrency, Amazon EFS, Amazon S3 Files or more than 512 MB of ephemeral storage. It is offered in all commercial Regions except Asia Pacific (New Zealand) and Asia Pacific (Taipei) (as of 2026-11). Java has no SnapStart surcharge; Python and .NET pay snapshot caching (minimum 3 hours) and per-restore charges.[^s2] Since September 2026 it also works for container-image functions.[^s5]
- **Provisioned concurrency.** Pre-initialized environments answer in double-digit milliseconds. It also requires a version or alias, spills excess traffic to on-demand environments, can scale with Application Auto Scaling, and bills for initialized environments even when idle. A single function can be given at most unreserved account concurrency minus 100.[^s3] AWS suggests it pays off for sustained utilization above roughly 60%.[^s4]

## Gotchas

SnapStart reuses one snapshot across many environments, so IDs, secrets or random seeds generated during Init stop being unique, and non-SDK network connections may need re-establishing after restore.[^s2] Code can read `AWS_LAMBDA_INITIALIZATION_TYPE` to see whether it runs on provisioned or on-demand capacity.[^s3] SnapStart helps least for rarely invoked functions.[^s2]

## When it matters (and when not)

Asynchronous pipelines and batch processing rarely care about a sub-1% latency tail; latency-sensitive APIs and user flows do.[^s3][^s2] Try lean init first, then SnapStart, then provisioned concurrency for strict latency targets SnapStart cannot meet.[^s2]

## Connections

Cold starts are a property of **AWS Lambda**, its broader node, and a frequent selection factor in **AWS serverless services**; synchronous front doors such as **Amazon API Gateway** expose them directly to users. The two managed mitigations, **Lambda SnapStart** and **Lambda provisioned concurrency**, are related nodes.[^s2][^s3] Init time is **monitored by Amazon CloudWatch** logs and Lambda Insights.[^s4] In the graph they also **contrast with** long-running containers on **AWS Fargate**, a common comparison when choosing serverless compute; within Lambda itself, warm reuse of environments is what keeps cold starts to a small share of invocations.[^s1]

## Sources

[^s1]: Amazon Web Services, "Understanding the Lambda execution environment lifecycle - AWS Lambda". https://docs.aws.amazon.com/lambda/latest/operatorguide/static-initialization.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Improving startup performance with Lambda SnapStart". https://docs.aws.amazon.com/lambda/latest/dg/snapstart.md (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Configuring provisioned concurrency for an Event Function - AWS Lambda". https://docs.aws.amazon.com/lambda/latest/dg/provisioned-concurrency.html (accessed 2026-11-15)
[^s4]: AWS Compute Blog, "AWS Lambda standardizes billing for INIT Phase" (2025-04-29). https://aws.amazon.com/blogs/compute/aws-lambda-standardizes-billing-for-init-phase (accessed 2026-11-15)
[^s5]: What's New with AWS, "AWS Lambda now supports SnapStart for container image functions" (2026-09-02). https://aws.amazon.com/about-aws/whats-new/2026/07/aws-lambda-snapstart-container/ (accessed 2026-11-15)
