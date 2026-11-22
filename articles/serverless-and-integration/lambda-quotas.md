---
id: kt:lambda-quotas
type: Limit
title: AWS Lambda quotas
aliases: [Lambda quotas, Lambda limits, AWS Lambda limits, Lambda service quotas, Lambda concurrency limits]
domain: serverless-and-integration
summary: >-
  The limits that shape AWS Lambda designs: a per-Region account concurrency pool (with a
  100-unit unreserved floor), a per-function scaling rate, request-rate rules tied to concurrency,
  mostly fixed per-function limits on memory, timeout, payloads and packages, and fixed
  control-plane API rates.
verified: 2026-11-22
status: current
broader: [kt:aws-lambda]
related: [kt:api-gateway-quotas, kt:lambda-cold-starts, kt:aws-lambda-durable-functions, kt:amazon-sqs]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "Lambda quotas - AWS Lambda"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html
    type: official-docs
    accessed: 2026-11-22
  - id: s2
    title: "Event Function scaling behavior - AWS Lambda"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/burst-concurrency.html
    type: official-docs
    accessed: 2026-11-22
  - id: s3
    title: "Configuring reserved concurrency for an Event Function - AWS Lambda"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html
    type: official-docs
    accessed: 2026-11-22
  - id: s4
    title: "Understanding Lambda Event Function scaling - AWS Lambda"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/lambda-concurrency
    type: official-docs
    accessed: 2026-11-22
  - id: s5
    title: "Announcing 30-minute timeout for on-demand AWS Lambda Event Functions"
    publisher: Amazon Web Services (AWS Compute Blog)
    url: https://aws.amazon.com/blogs/compute/announcing-30-minute-timeout-for-on-demand-aws-lambda-event-functions/
    type: official-blog
    accessed: 2026-11-22
  - id: s6
    title: "AWS Lambda functions now scale up to 12X faster"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2023/12/aws-lambda-functions-scale-up
    type: official-blog
    accessed: 2026-11-22
created: 2026-11-22
---

# AWS Lambda quotas

Lambda's limits fall into three groups: account-level compute and storage quotas per Region, mostly adjustable; per-function configuration, deployment and execution limits, mostly fixed; and API request rates for invocations and the control plane.[^s1] New accounts start with reduced concurrency and memory quotas, which AWS raises automatically as usage grows.[^s1] The figures below are as of 2026-11 and are the same in every commercial Region unless noted.

## Concurrency: the account pool

Concurrency is the number of in-flight requests, and AWS sizes it as average requests per second multiplied by average duration in seconds.[^s3][^s4] Each account gets 1,000 concurrent executions per Region by default, and a quota increase can take it to tens of thousands.[^s1] The pool is shared by every function in the Region. Reserved concurrency carves a guaranteed slice out of it for one function and also caps that function at the same number, at no extra charge.[^s3] You can reserve at most the unreserved total minus 100, because Lambda always keeps 100 units for functions without a reservation; with the default 1,000 that leaves 900 to reserve.[^s3][^s4] Setting reserved concurrency to zero stops a function from processing events until the limit is removed.[^s3]

Provisioned concurrency is a separate, billed setting that pre-initializes environments. It cannot exceed the function's reserved concurrency, and once its environments are busy, extra traffic spills into on-demand concurrency.[^s4] Lambda can provision up to 6,000 environments per minute in any Region.[^s4]

## Scaling rate and request rate

Each function in each Region can add 1,000 execution environments every 10 seconds (equivalently 10,000 requests per second every 10 seconds), independently of other functions.[^s1][^s2] Unused scaling capacity does not bank up, and once a function reaches the account or reserved maximum, further requests are throttled with HTTP 429.[^s2] This per-function rate arrived in December 2023. Before that, scaling was set at account level: up to 3,000 concurrent executions in the first minute, then 500 more per minute.[^s6]

Request rate is tied to concurrency. A synchronous environment can serve up to 10 requests per second, so the account's synchronous request limit is ten times its concurrency quota (10,000 RPS by default). Asynchronous invokes have no per-environment request cap and are limited only by available concurrency.[^s1][^s4] Provisioned concurrency allows ten times its allocation in requests per second for each version or alias.[^s1]

## Per-function limits

These cannot be changed unless noted:[^s1]

- **Memory** 128–10,240 MB in 1 MB steps; one vCPU's worth arrives at 1,769 MB. Ephemeral `/tmp` storage is configurable over the same range.[^s1]
- **Timeout** 1,800 seconds (30 minutes) for on-demand event functions. File descriptors and processes/threads are each capped at 1,024 (Lambda Managed Instances allows 4,096 file descriptors).[^s1]
- **Payloads** 6 MB request and 6 MB response for synchronous calls, up to 200 MB for a streamed response (first 6 MB uncapped, then 2 MBps), and 1 MB for asynchronous events.[^s1]
- **Packaging** 50 MB zipped for direct upload, 250 MB unzipped including all layers, five layers, 10 GB for an uncompressed container image, and 4 KB of environment variables in total. The function's resource-based policy is limited to 20 KB.[^s1]
- **Storage and networking** 300 GB of Lambda-managed .zip and layer storage per Region (not increasable); 3,000 elastic network interfaces per VPC (adjustable, and shared with services such as Amazon EFS); 625 Mbps of bandwidth per environment, which non-VPC functions can ask to raise.[^s1]

Control-plane calls are rate-limited and none of these can be raised: GetFunction at 100 requests per second, GetPolicy at 15, and all other control-plane APIs at 15 combined.[^s1] Pipelines that update many functions need backoff.

## What changed (2026-11)

On 19 November 2026 AWS doubled the on-demand event-function timeout from 15 to 30 minutes for synchronous, asynchronous and event source mapping invocations, in all commercial Regions, at standard pricing.[^s5] Several limits did not move. Init stays at 10 seconds on demand. Web Functions stay at 15 minutes. Amazon MQ and Amazon DocumentDB event source mappings remain capped at 14 minutes. Managed Instances keep 90 minutes for asynchronous and mapping-based work.[^s5] AWS also warns that a longer timeout multiplies concurrency: one request per second running 30 minutes each needs 1,800 concurrent executions, nearly double the default pool.[^s5] For SQS triggers, the queue's visibility timeout should be at least six times the function timeout.[^s5]

## Gotchas and alternatives

Synchronous callers impose their own ceilings. API Gateway's integration timeout and payload limits usually bind before Lambda's, and clients must raise their own read timeouts to benefit from 30 minutes.[^s5] One runaway function can drain the shared pool and throttle everything else in the Region, so reserve concurrency for critical functions and cap noisy ones.[^s3] Work that outgrows the fixed limits fits durable functions, Managed Instances or containers better.[^s5][^s1]

## Connections

These quotas are **broader** under AWS Lambda (`kt:aws-lambda`). The adjustable rows are **configured via** Service Quotas (`kt:service-quotas`), and the concurrency pool and storage limits are **scoped to** AWS Regions (`kt:aws-regions`).[^s1] They relate to Amazon API Gateway quotas (`kt:api-gateway-quotas`) because API Gateway is the usual synchronous front end, to Lambda cold starts (`kt:lambda-cold-starts`) because provisioned concurrency and the 10-second init limit both shape start-up time, to AWS Lambda durable functions (`kt:aws-lambda-durable-functions`) with their own execution quotas, and to Amazon SQS (`kt:amazon-sqs`), whose visibility timeout must track the function timeout.[^s1][^s4][^s5]

## Sources

[^s1]: Amazon Web Services, "Lambda quotas - AWS Lambda". https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html (accessed 2026-11-22)
[^s2]: Amazon Web Services, "Event Function scaling behavior - AWS Lambda". https://docs.aws.amazon.com/lambda/latest/dg/burst-concurrency.html (accessed 2026-11-22)
[^s3]: Amazon Web Services, "Configuring reserved concurrency for an Event Function - AWS Lambda". https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html (accessed 2026-11-22)
[^s4]: Amazon Web Services, "Understanding Lambda Event Function scaling - AWS Lambda". https://docs.aws.amazon.com/lambda/latest/dg/lambda-concurrency (accessed 2026-11-22)
[^s5]: Amazon Web Services, AWS Compute Blog, "Announcing 30-minute timeout for on-demand AWS Lambda Event Functions" (2026-11-19). https://aws.amazon.com/blogs/compute/announcing-30-minute-timeout-for-on-demand-aws-lambda-event-functions/ (accessed 2026-11-22)
[^s6]: Amazon Web Services, What's New, "AWS Lambda functions now scale up to 12X faster" (2023-12-06). https://aws.amazon.com/about-aws/whats-new/2023/12/aws-lambda-functions-scale-up (accessed 2026-11-22)
