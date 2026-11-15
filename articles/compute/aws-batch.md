---
id: kt:aws-batch
type: Service
title: AWS Batch
aliases: [Amazon Batch]
domain: compute
summary: >-
  A fully managed AWS job scheduler that queues containerized batch jobs and provisions EC2, Spot,
  Fargate, ECS Managed Instances or EKS capacity to run them, with no charge beyond the underlying
  resources.
verified: 2026-11-15
status: current
broader: [kt:aws-compute-services]
related: [kt:amazon-ecs, kt:amazon-eks, kt:aws-step-functions, kt:amazon-sagemaker-ai]
relations:
  - {predicate: runsOn, object: kt:amazon-ec2}
  - {predicate: runsOn, object: kt:aws-fargate}
  - {predicate: integratesWith, object: kt:ecs-managed-instances}
  - {predicate: pricedBy, object: kt:spot-instances}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "Components of AWS Batch"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/batch/latest/userguide/batch_components.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Resource: AWS Batch service quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/batch/latest/userguide/service_limits.html
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Instance type allocation strategies for AWS Batch"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/batch/latest/userguide/allocation-strategies.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "AWS Batch pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/batch/pricing
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "AWS Batch FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/batch/faqs
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "AWS Batch now supports Amazon ECS Managed Instances"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/08/aws-batch-on-ecs-managed-instances/
    type: official-blog
    accessed: 2026-11-15
  - id: s7
    title: "AWS Certified Solutions Architect - Associate (SAA-C03): In-scope AWS services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-15
created: 2026-11-15
---

# AWS Batch

AWS Batch is a managed scheduler for running large volumes of batch work: AWS describes it as handling hundreds of thousands of jobs, provisioning compute to match the volume and resource needs of what is queued.[^s5] Jobs are containerized and run on Amazon ECS (on EC2 or Fargate) or Amazon EKS, optionally on Spot capacity.[^s5] AWS Batch itself is free; you pay only for the EC2 instances, Fargate tasks or other resources it launches.[^s4]

## How it works

A Batch deployment has four core pieces.[^s1]

- **Job definitions** are templates that set the container image, vCPU and memory, environment variables, mount points and the IAM role a job assumes; most fields can be overridden at submit time.[^s1]
- **Jobs** are units of work — a shell script, a Linux executable or a container image. A job can depend on other jobs by name or ID, and can also wait on *consumable resources* such as license tokens or third-party API rate limits, so Batch does not start work that would sit idle waiting for them.[^s1] Supported job types are single-node, array and multi-node parallel (MNP) jobs, the last of which suit tightly coupled HPC work spanning several instances.[^s5]
- **Job queues** hold submitted jobs until they can be placed. Each queue maps to one or more compute environments in priority order, and queues themselves carry priorities, so a time-critical queue can outrank a "run when cheap" queue.[^s1] The default scheduler is first-in, first-out; a *fair-share scheduling policy* instead splits capacity among share identifiers such as teams or workloads.[^s1]
- **Compute environments** are the capacity. In a *managed* environment you choose Fargate, EC2 or ECS Managed Instances and set minimum, desired and maximum vCPUs, a Spot price ceiling and VPC subnets; Batch scales the underlying ECS cluster across the Region's Availability Zones. In an *unmanaged* environment you run and scale the instances yourself.[^s1]

Batch can also act as a queue in front of other services: a *service environment* lets it queue, prioritize and cap capacity for SageMaker AI training jobs that then execute on SageMaker infrastructure.[^s1]

For EC2-backed environments the *allocation strategy* decides which instance types to launch. `BEST_FIT` (the default) picks the cheapest type that fits and waits if it is unavailable; `BEST_FIT_PROGRESSIVE` moves on to other types; `SPOT_PRICE_CAPACITY_OPTIMIZED` balances price and interruption risk and is AWS's recommended Spot choice. Ordered variants (`BEST_FIT_PROGRESSIVE_ORDERED` for On-Demand, `SPOT_CAPACITY_OPTIMIZED_PRIORITIZED` for Spot) honour your own ranking of instance types.[^s3] Allocation strategies are not set for Fargate or ECS Managed Instances environments.[^s3]

## Quotas, pricing and gotchas

As of 2026-11 the documented per-Region quotas include 50 job queues, 50 compute environments (across ECS and EKS), 3 compute environments per job queue, 20 dependencies per job, array jobs of up to 10,000 children, 1,000,000 jobs in `SUBMITTED` state and 50 `SubmitJob` calls per second per account. AWS states these quotas cannot be changed, and EC2, ECS and EKS quotas apply on top.[^s2]

There is no AWS Batch fee in any Region; Reserved Instances, Savings Plans, Spot and Fargate pricing are applied to the underlying resources at billing time.[^s4] Gotchas:

- With any strategy other than `BEST_FIT`, Batch may overshoot `maxvCpus` by up to one instance.[^s3]
- A single job queue cannot mix Fargate/Fargate Spot environments with EC2 or Spot environments.[^s5]
- Fargate scales out faster for small bursts, but for large workloads EC2- or EKS-backed environments are often quicker because Batch reuses instances and cached images.[^s5]

## What changed (2026-08)

In August 2026 AWS added Amazon ECS Managed Instances as a Batch compute option. AWS patches the AMIs and manages the instance lifecycle, while you keep instance-type choice, GPUs and On-Demand, Spot or reserved capacity, in every Region that offers Batch.[^s6]

## When to use it (and when not)

Batch fits queued, throughput-oriented work: rendering, genomics, simulations, ETL and ML training sweeps that run for minutes to days and need many vCPUs or GPUs. Choose Fargate environments when you want no instances to manage, EC2/ECS when you need specific instance families, GPUs or very large scale, and EKS when Kubernetes is already your standard.[^s5] Short, event-driven functions are usually a better fit for Lambda, and multi-step pipelines for a workflow orchestrator such as Step Functions, with Batch handling the heavy compute stages.

## Connections

AWS Batch is part of `kt:aws-compute-services`. It `runsOn` `kt:amazon-ec2` and `kt:aws-fargate` and `integratesWith` `kt:ecs-managed-instances`, all through clusters it builds in `kt:amazon-ecs`, or alternatively on `kt:amazon-eks`.[^s1][^s6] It is `pricedBy` `kt:spot-instances` because Spot is the main lever on Batch cost.[^s4][^s5] Quotas and environments are per Region (`scopedTo` `kt:aws-regions`).[^s2] Service environments link it to `kt:amazon-sagemaker-ai`.[^s1] AWS Batch is listed under Compute in the in-scope services for the Solutions Architect – Associate (SAA-C03) exam, hence the `coveredBy` edge to `kt:aws-certified-solutions-architect-associate`.[^s7]

## Sources
[^s1]: Amazon Web Services, "Components of AWS Batch". https://docs.aws.amazon.com/batch/latest/userguide/batch_components.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Resource: AWS Batch service quotas". https://docs.aws.amazon.com/batch/latest/userguide/service_limits.html (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Instance type allocation strategies for AWS Batch". https://docs.aws.amazon.com/batch/latest/userguide/allocation-strategies.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "AWS Batch pricing". https://aws.amazon.com/batch/pricing (accessed 2026-11-15)
[^s5]: Amazon Web Services, "AWS Batch FAQs". https://aws.amazon.com/batch/faqs (accessed 2026-11-15)
[^s6]: Amazon Web Services, "AWS Batch now supports Amazon ECS Managed Instances". https://aws.amazon.com/about-aws/whats-new/2026/08/aws-batch-on-ecs-managed-instances/ (accessed 2026-11-15)
[^s7]: Amazon Web Services, "AWS Certified Solutions Architect - Associate (SAA-C03): In-scope AWS services". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-15)
