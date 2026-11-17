---
id: kt:amazon-swf
type: Service
title: Amazon Simple Workflow Service
aliases: [Amazon SWF, SWF, Simple Workflow Service]
domain: serverless-and-integration
summary: >-
  AWS's original managed workflow coordination service: it durably tracks the state of long-running,
  multi-step background jobs while your own decider and worker programs poll it for tasks. AWS now
  recommends Step Functions for most new workflows.
verified: 2026-11-17
status: current
broader: [kt:aws-application-integration-services]
related: [kt:aws-step-functions, kt:amazon-sqs, kt:amazon-ec2]
relations:
  - {predicate: alternativeTo, object: kt:aws-step-functions}
  - {predicate: contrastsWith, object: kt:amazon-sqs}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is Amazon Simple Workflow Service?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazonswf/latest/developerguide
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Amazon SWF Quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazonswf/latest/developerguide/swf-dg-limits.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Amazon SWF FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/swf/faqs
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "Amazon SWF pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/swf/pricing
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "Document history - Amazon Simple Workflow Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazonswf/latest/developerguide/WhatsNew.html
    type: official-docs
    accessed: 2026-11-17
  - id: s6
    title: "Services in Maintenance - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/maintenance_services.html
    type: official-docs
    accessed: 2026-11-17
created: 2026-11-17
---

# Amazon Simple Workflow Service

Amazon Simple Workflow Service (Amazon SWF) is a managed service for building, running and scaling background jobs made of parallel or sequential steps. It coordinates work across distributed components and keeps each application's execution state durably, so the application survives the failure of individual components.[^s1] It predates AWS Step Functions, and AWS's own documentation now says that for most use cases you should consider Step Functions for workflow and orchestration needs; the SWF FAQ similarly points customers to Step Functions for new applications.[^s1][^s3]

## How it works

SWF models a unit of work as a **task**, which your own **workers** perform. Workers can run on Amazon EC2 or on premises, and SWF stores tasks, hands them to workers when they are ready, and tracks progress and completion.[^s1] Ordering, concurrency and scheduling are decided by a **decider**, a program you write: it receives a decision task containing the latest execution history and replies with the next steps. SWF keeps at most one decision task outstanding per execution.[^s3] Unlike Step Functions, where a JSON state machine defines the flow, the orchestration logic in SWF lives entirely in your code, which gives full control at the cost of more development effort.[^s3]

Resources are grouped into **domains**, account-level containers that cannot be nested; teams often use separate domains for development, test and production. **Task lists** route tasks to particular workers or deciders and can be created at runtime.[^s3] Workers and deciders use long polling: if no task is waiting, SWF holds the poll open for up to a minute, which cuts empty responses compared with frequent short polls.[^s3] The AWS Flow Framework, part of the AWS SDK for Java, lets developers write SWF programs with ordinary method calls that start activity tasks and wait for their results.[^s3] Execution history is stored across three Availability Zones, and completed executions are retained for a period you choose, up to 90 days.[^s3]

## Quotas, pricing and gotchas

Key quotas from the SWF developer guide (as of 2026-11):[^s2]

- A workflow execution can run for at most **1 year** and record at most **25,000 history events**; both are hard quotas, and AWS's best practice is to stay under 10,000 events.[^s2]
- **100,000** open executions per domain (child executions included), **1,000** open child executions, **1,000** open activity tasks and **1,000** open timers per execution.[^s2]
- Inputs, results and signal inputs are capped at **32,768 characters**; each API request is limited to 1 MB.[^s2]
- **100** registered domains per account and **10,000** workflow and activity types per domain, deprecated ones included.[^s2]
- API throttling uses token buckets that are the same in every Region; for example `StartWorkflowExecution` has a bucket of 2,000 refilling at 200 per second. Account-level throttles can be raised through Service Quotas, but the workflow-level throttles and the hard quotas cannot.

Pricing is pay-per-use. In the US East (Ohio) Region (as of 2026-11), each workflow execution started beyond the first 1,000 per month costs $0.0001, each task, marker, timer or signal beyond 10,000 costs $0.000025, and each workflow-day open or retained beyond a combined 30,000 costs $0.000005; data transferred in is free.[^s4] A common gotcha is that retained history keeps accruing workflow-day charges, so set the retention period no longer than you need.[^s3][^s4]

## When to use it (and when not)

For new orchestration work, AWS steers customers to Step Functions, which offers visual, declarative state machines and direct service integrations instead of hand-written deciders.[^s1][^s3] SWF remains a fit for existing applications built on it, or for teams that need to express coordination logic in general-purpose code and run workers outside AWS.[^s1][^s3] Compared with Amazon SQS, SWF is task-oriented rather than message-oriented: it tracks every task and event, offers visibility APIs and signaling, and passes data between tasks, whereas with SQS you build that bookkeeping yourself.[^s3]

SWF is not closed to new customers: as of 2026-11 it is absent from AWS's list of services in maintenance, and its documentation carries no availability notice.[^s5][^s6] Its documentation history has been quiet since 2023, however, with the last feature additions being EventBridge support in 2020, a quota for tasks scheduled per second in 2021 and a new console in 2022.[^s5]

## Connections

SWF sits under AWS's application integration services. Its closest **alternative** is AWS Step Functions, the service AWS recommends for most workflows, and it is commonly **contrasted** with Amazon SQS, which moves messages but leaves task tracking to you.[^s1][^s3] SWF **integrates with** Amazon EventBridge, which can receive SWF events, and its resources are **monitored** with Amazon CloudWatch.[^s5] Like most AWS services it is **scoped to** a Region, with history replicated across Availability Zones within it.[^s3] Workers can run on Amazon EC2 or on premises, in any language, as long as they poll SWF for tasks.[^s1][^s3]

## Sources

[^s1]: Amazon Web Services, "What is Amazon Simple Workflow Service?". https://docs.aws.amazon.com/amazonswf/latest/developerguide (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Amazon SWF Quotas". https://docs.aws.amazon.com/amazonswf/latest/developerguide/swf-dg-limits.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Amazon SWF FAQs". https://aws.amazon.com/swf/faqs (accessed 2026-11-17)
[^s4]: Amazon Web Services, "Amazon SWF pricing". https://aws.amazon.com/swf/pricing (accessed 2026-11-17)
[^s5]: Amazon Web Services, "Document history - Amazon Simple Workflow Service". https://docs.aws.amazon.com/amazonswf/latest/developerguide/WhatsNew.html (accessed 2026-11-17)
[^s6]: Amazon Web Services, "Services in Maintenance - AWS General Reference". https://docs.aws.amazon.com/general/latest/gr/maintenance_services.html (accessed 2026-11-17)
