---
id: kt:aws-x-ray
type: Service
title: AWS X-Ray
aliases: [X-Ray, Amazon X-Ray]
domain: devops-and-iac
summary: >-
  AWS's distributed tracing service, which collects segments from instrumented applications and
  AWS services, stitches them into end-to-end traces and a service graph, and samples requests so
  you can find latency, errors and faults across microservices.
verified: 2026-11-21
status: current
broader: [kt:aws-developer-tools]
related: [kt:amazon-cloudwatch, kt:microservices-architecture, kt:aws-lambda]
relations:
  - {predicate: partOf, object: kt:aws-developer-tools}
  - {predicate: integratesWith, object: kt:amazon-cloudwatch}
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: integratesWith, object: kt:amazon-dynamodb}
  - {predicate: dependsOn, object: kt:opentelemetry}
  - {predicate: governedBy, object: kt:service-quotas}
sources:
  - id: s1
    title: "Announcing AWS X-Ray SDKs/Daemon End-of-Support and OpenTelemetry Migration"
    publisher: Amazon Web Services (AWS Cloud Operations Blog)
    url: https://aws.amazon.com/blogs/mt/announcing-aws-x-ray-sdks-daemon-end-of-support-and-opentelemetry-migration
    type: official-blog
    accessed: 2026-11-21
  - id: s2
    title: "AWS X-Ray endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/xray.html
    type: official-docs
    accessed: 2026-11-21
  - id: s3
    title: "Transaction Search - Amazon CloudWatch"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Transaction-Search.html
    type: official-docs
    accessed: 2026-11-21
  - id: s4
    title: "AWS X-Ray concepts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.md
    type: official-docs
    accessed: 2026-11-21
  - id: s5
    title: "Configuring sampling rules - AWS X-Ray"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.md
    type: official-docs
    accessed: 2026-11-21
created: 2026-11-21
---

# AWS X-Ray

AWS X-Ray is AWS's distributed tracing service. Instrumented applications and AWS services send records of the work they do for each request; X-Ray groups those records by trace ID into a trace that follows one request end to end, and aggregates many traces into a service graph showing how components call each other and where errors and latency occur.[^s4] It is the tracing pillar of AWS observability alongside CloudWatch metrics and logs, and since 2026 AWS has been moving its instrumentation story from the proprietary X-Ray SDKs to OpenTelemetry.[^s1]

## How it works

The unit of data is the **segment**: the compute resource that handles a request (a host, container or function) sends a segment document describing the request, the response, the work done and any problems, up to 64 kB per document.[^s4] **Subsegments** break that work down into finer timings and record downstream calls; for AWS services that don't emit their own data, such as DynamoDB, X-Ray builds an **inferred segment** from the caller's subsegment, and replaces it with the real one if the downstream service is itself instrumented.[^s4] Requests are correlated through the `X-Amzn-Trace-Id` HTTP header, which carries the root trace ID, the parent segment ID and the sampling decision; Lambda and other AWS services may append a `Lineage` field that applications should not rely on.[^s4]

You can attach **annotations** (key-value pairs that are indexed, so you can search them with filter expressions) and **metadata** (values of any type, including objects, that are not indexed).[^s4] **Groups** are saved filter expressions; each gets its own service graph, trace summaries and CloudWatch metrics, and changing a group's filter doesn't alter data already recorded.[^s4] X-Ray classifies problems as errors (4xx), faults (5xx) and throttles (429).[^s4]

**Sampling** keeps volume and cost under control. By default the SDK records the first request each second (the *reservoir*) plus 5% of additional requests (the *rate*).[^s5] Custom rules match on service name and type, host, HTTP method and URL path, and are evaluated in ascending priority (1–9999), first match wins; reservoirs are shared by all services using a rule, which avoids the per-instance reservoirs you get with local JSON rule files.[^s5] Sampling is parent-based: a downstream service honours the upstream decision, so custom rules only matter where no decision has been made yet, and X-Ray applies rules on a best-effort basis.[^s5] Rules also support an optional anomaly-driven sampling boost (`SamplingRateBoost`).[^s5]

## Quotas, pricing and gotchas

Default quotas, per Region and account (as of 2026-11):[^s2]

- Segment document size: 64 KB; trace document size: 100 KB lower limit, 500 KB dynamic upper limit (not adjustable).[^s2]
- Segments per second: 2,600 (not adjustable).[^s2]
- Indexed annotations per trace: 50; groups per account: 25 (not adjustable).[^s2]
- Custom sampling rules: 25 (**adjustable**); tags per rule or group: 50.[^s2]
- Trace and service graph retention: 30 days; recorded trace data can be modified for 7 days.[^s2]

Groups are billed by the number of retrieved traces that match their filter expression, so broad groups add cost.[^s4] This run did not verify X-Ray's per-trace prices; check the X-Ray and CloudWatch pricing pages for your Region before estimating.

The major lifecycle gotcha: AWS announced that the **X-Ray SDKs and X-Ray daemon** entered maintenance mode on 25 February 2026 (critical bug and security fixes only, no new features or instrumentations) and reach end of support on 25 February 2027, after which no further releases ship.[^s1] The X-Ray service and console experience continue, and existing instrumented applications keep working, but AWS recommends migrating to OpenTelemetry-based instrumentation through AWS Distro for OpenTelemetry (ADOT) or native OpenTelemetry with CloudWatch.[^s1] Some sampling-rule options (Resource ARN and attributes) are not supported by the legacy SDK at all.[^s5]

## When to use it (and when not)

Use X-Ray when a request crosses several services (API front ends, Lambda functions, containers, databases) and you need to see where time goes or which hop fails; that is where per-service logs and metrics stop being enough.[^s4] For new instrumentation, use OpenTelemetry/ADOT or the CloudWatch agent rather than the X-Ray SDK, given its end-of-support date.[^s1][^s5] If you need to search every span rather than a sample, CloudWatch **Transaction Search** can ingest 100% of spans as structured logs in the `aws/spans` log group and index a chosen percentage as X-Ray trace summaries, with traces of up to 10,000 spans viewable; it is billed under CloudWatch pricing.[^s3]

## Connections

X-Ray sits in the **AWS developer tools** family as the tracing service for debugging distributed applications, and it **integrates with Amazon CloudWatch**: groups publish CloudWatch metrics, Transaction Search stores spans in CloudWatch Logs, and migrating to OpenTelemetry also unlocks CloudWatch Application Signals.[^s4][^s3][^s1] It **integrates with AWS Lambda**, which participates in trace-header propagation, and with **Amazon DynamoDB** through inferred segments.[^s4] Going forward it **depends on OpenTelemetry** as its recommended instrumentation path.[^s1] Its limits are documented as Service Quotas, so it is **governed by Service Quotas**.[^s2] In a **microservices architecture** it provides the request-level view across service boundaries that a service graph gives.[^s4]

## Sources
[^s1]: Amazon Web Services, "Announcing AWS X-Ray SDKs/Daemon End-of-Support and OpenTelemetry Migration". https://aws.amazon.com/blogs/mt/announcing-aws-x-ray-sdks-daemon-end-of-support-and-opentelemetry-migration (accessed 2026-11-21)
[^s2]: Amazon Web Services, "AWS X-Ray endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/xray.html (accessed 2026-11-21)
[^s3]: Amazon Web Services, "Transaction Search - Amazon CloudWatch". https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Transaction-Search.html (accessed 2026-11-21)
[^s4]: Amazon Web Services, "AWS X-Ray concepts". https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.md (accessed 2026-11-21)
[^s5]: Amazon Web Services, "Configuring sampling rules - AWS X-Ray". https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.md (accessed 2026-11-21)
