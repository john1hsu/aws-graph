---
id: kt:aws-lambda
type: Service
title: AWS Lambda
aliases: [Lambda, Amazon Lambda]
domain: serverless-and-integration
summary: >-
  AWS's serverless compute service, which runs code in response to events, API calls or HTTP
  requests while AWS handles servers, capacity, scaling and patching; it offers event functions,
  web functions and MicroVMs, with Managed Instances and durable functions as options.
verified: 2026-11-09
status: current
broader: [kt:aws-compute-services, kt:aws-serverless-services]
related: [kt:amazon-api-gateway, kt:amazon-eventbridge, kt:amazon-sqs, kt:aws-step-functions]
relations:
  - {predicate: governedBy, object: kt:lambda-quotas}
  - {predicate: runsOn, object: kt:amazon-ec2}
  - {predicate: pricedBy, object: kt:savings-plans}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: alternativeTo, object: kt:aws-fargate}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is AWS Lambda?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/welcome.md
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "Lambda quotas - AWS Lambda"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "AWS Lambda pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/lambda/pricing/
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Building fault-tolerant long-running applications with AWS Lambda durable functions"
    publisher: Amazon Web Services (AWS Compute Blog)
    url: https://aws.amazon.com/blogs/compute/building-fault-tolerant-long-running-application-with-aws-lambda-durable-functions
    type: official-blog
    accessed: 2026-11-09
  - id: s5
    title: "Lambda Managed Instances capacity providers"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/lambda-managed-instances-capacity-providers.html
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "Top announcements of AWS re:Invent 2025"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/fr/blogs/aws/top-announcements-of-aws-reinvent-2025
    type: official-blog
    accessed: 2026-11-09
  - id: s7
    title: "AWS Lambda announces Lambda Web Functions"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/10/aws-lambda-web-functions/
    type: official-blog
    accessed: 2026-11-09
  - id: s8
    title: "AWS introduces Lambda MicroVMs for isolated execution of user and AI-generated code"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/aws-lambda-microvms/
    type: official-blog
    accessed: 2026-11-09
  - id: s9
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS Lambda

AWS Lambda is AWS's serverless compute service: you supply code, and AWS manages the servers underneath, including maintenance, capacity provisioning, scaling and patching.[^s1] Since 2026 the developer guide describes three compute primitives. **Event functions** are the original Lambda function type. **Web functions** run HTTP servers. **MicroVMs** are long-lived isolated sandboxes. All three share Firecracker VM-level isolation, managed networking and pay-per-use billing.[^s1]

## How it works

An event function is a handler you connect to a trigger such as Amazon API Gateway, Amazon S3, Amazon SQS or Amazon EventBridge. AWS says more than 200 services can trigger Lambda. Each invocation runs independently with no shared state, and Lambda scales by creating and destroying execution environments as traffic changes. Environments can be reused (warm starts), but state is not guaranteed to persist between invocations.[^s1] By default an environment handles one request at a time, and an invocation can run for up to 15 minutes.[^s1][^s2]

Two re:Invent 2025 launches extended that model.[^s6] **Lambda Managed Instances** runs functions on EC2 instances that Lambda manages inside your VPC. You create a *capacity provider* that sets subnets, an operator IAM role, the architecture and optionally allowed or excluded instance types; it scales automatically up to a vCPU cap (400 by default). The capacity provider replaces Firecracker as the isolation boundary, so AWS advises putting untrusted workloads in separate providers.[^s5] Managed Instances can serve several concurrent requests per environment and allows up to 90 minutes for asynchronous and event-source-mapping invocations.[^s1][^s2] **Durable functions** checkpoint each `step` and replay the handler after a failure or resume, skipping completed steps. `wait` and `waitForCallback` suspend execution, with no compute charge for on-demand functions, for up to a year. Each individual invocation is still capped at 15 minutes. The Compute Blog lists Python and Node.js as the supported runtimes.[^s4]

Newer primitives (as of 2026-11): **Web Functions** (announced 2026-10-12) run Node.js web frameworks such as Express directly behind a Lambda HTTPS endpoint, and one warm environment serves many concurrent requests.[^s7][^s1] **MicroVMs** (announced 2026-06-22) give each user or job its own Firecracker-isolated environment for user-supplied or AI-generated code. A MicroVM can be suspended and resumed, for a total lifespan of up to 8 hours.[^s8][^s1] Both launched in five Regions: N. Virginia, Ohio, Oregon, Tokyo and Ireland.[^s7][^s8]

## Quotas, pricing and gotchas

Quotas per Region (as of 2026-11): 1,000 concurrent executions by default, adjustable to tens of thousands (new accounts start lower and rise automatically), with each function adding up to 1,000 execution environments every 10 seconds. Memory runs from 128 MB to 10,240 MB (1,769 MB equals one vCPU) and the timeout is fixed at 900 seconds. Payloads are 6 MB each way for synchronous calls (200 MB streamed) and 1 MB for asynchronous ones. A .zip package is limited to 50 MB zipped and 250 MB unzipped with layers, a container image to 10 GB, and `/tmp` to 512–10,240 MB. The 300 GB of Lambda-managed code storage is not adjustable, and apart from concurrency, VPC network interfaces and non-VPC bandwidth most function limits are fixed.[^s2]

Pricing in US East (N. Virginia), as of 2026-11: $0.20 per million requests and $0.0000166667 per GB-second (x86, first tier), with a free tier of 1 million requests and 400,000 GB-seconds a month that excludes Provisioned Concurrency functions. Provisioned Concurrency costs $0.0000041667 per GB-second provisioned plus $0.0000097222 per GB-second of compute; `/tmp` beyond 512 MB is $0.0000000309 per GB-second. Durable functions add $8.00 per million operations, $0.25 per GB written and $0.15 per GB-month retained. Managed Instances bills EC2 prices plus a 15% fee on the On-Demand price, and the EC2 portion can use Compute Savings Plans or Reserved Instances.[^s3]

Gotcha: synchronous throughput is capped at 10 requests per second per execution environment, so concurrency limits request rate as well as parallelism.[^s2]

## When to use it (and when not)

AWS recommends event functions for request-response and event-driven work such as APIs, data processing and automation. Web functions suit HTTP backends, and MicroVMs suit persistent sandboxes for untrusted code.[^s1] For jobs longer than 15 minutes, durable functions (for multi-step waits) or Managed Instances (up to 90 minutes for asynchronous work) can help. Because the 15-minute timeout and most function limits are fixed, workloads that need longer runs, specific hosts or more control are usually better served by containers on AWS Fargate or by EC2.[^s4][^s2][^s5]

## Connections

- **Broader:** AWS compute services and AWS serverless services. The CLF-C02 exam guide lists Lambda under Serverless.[^s9]
- **Related:** API Gateway, EventBridge and SQS are common triggers.[^s1] AWS Step Functions is listed with EventBridge and SQS under Application Integration in the exam guide.[^s9]
- **Integrates with Amazon S3**, one of the documented trigger sources.[^s1]
- **Runs on Amazon EC2** when you use Managed Instances, and **priced by Savings Plans** for that EC2 capacity.[^s5][^s3]
- **Alternative to AWS Fargate**: containers are the usual next step when Lambda's fixed limits don't fit.[^s2]
- **Governed by Lambda quotas** (`kt:lambda-quotas`).[^s2]
- **Covered by AWS Certified Cloud Practitioner.**[^s9]

## Sources
[^s1]: Amazon Web Services, "What is AWS Lambda?". https://docs.aws.amazon.com/lambda/latest/dg/welcome.md (accessed 2026-11-09)
[^s2]: Amazon Web Services, "Lambda quotas". https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "AWS Lambda pricing". https://aws.amazon.com/lambda/pricing/ (accessed 2026-11-09)
[^s4]: Amazon Web Services, AWS Compute Blog, "Building fault-tolerant long-running applications with AWS Lambda durable functions" (2026-02-06). https://aws.amazon.com/blogs/compute/building-fault-tolerant-long-running-application-with-aws-lambda-durable-functions (accessed 2026-11-09)
[^s5]: Amazon Web Services, "Lambda Managed Instances capacity providers". https://docs.aws.amazon.com/lambda/latest/dg/lambda-managed-instances-capacity-providers.html (accessed 2026-11-09)
[^s6]: Amazon Web Services, AWS News Blog, "Top announcements of AWS re:Invent 2025". https://aws.amazon.com/fr/blogs/aws/top-announcements-of-aws-reinvent-2025 (accessed 2026-11-09)
[^s7]: Amazon Web Services, What's New, "AWS Lambda announces Lambda Web Functions" (2026-10-12). https://aws.amazon.com/about-aws/whats-new/2026/10/aws-lambda-web-functions/ (accessed 2026-11-09)
[^s8]: Amazon Web Services, What's New, "AWS introduces Lambda MicroVMs for isolated execution of user and AI-generated code" (2026-06-22). https://aws.amazon.com/about-aws/whats-new/2026/06/aws-lambda-microvms/ (accessed 2026-11-09)
[^s9]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-09)
