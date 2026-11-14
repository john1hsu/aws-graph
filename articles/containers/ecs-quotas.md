---
id: kt:ecs-quotas
type: Limit
title: Amazon ECS quotas
aliases: [ECS quotas, ECS service quotas, ECS limits, Amazon ECS service limits, ECS API throttling limits]
domain: containers
summary: >-
  The per-account, per-Region resource limits (clusters, services, tasks, container instances,
  task definition size) and token-bucket API rate limits that constrain Amazon ECS; resource counts
  are mostly fixed, while API burst and sustained rates can be raised.
verified: 2026-11-14
status: current
broader: [kt:amazon-ecs]
related: [kt:aws-fargate, kt:ecs-managed-instances, kt:aws-cloud-map, kt:amazon-eventbridge]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:fargate-quotas}
sources:
  - id: s1
    title: "Amazon ECS endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/ecs-service.md
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "Request throttling for the Amazon ECS API"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/request-throttling.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Handle Amazon ECS service quotas and API throttling limits"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/operating-at-scale-service-quotas.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Amazon ECS service quotas - Amazon Elastic Container Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service_limits.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "AWS Fargate throttling quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/throttling.html
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "AWS Cloud Map endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/cloud_map.html
    type: official-docs
    accessed: 2026-11-14
created: 2026-11-14
---

# Amazon ECS quotas

Amazon ECS quotas are the limits AWS applies to the Elastic Container Service in each account and Region. AWS sets default values per Region when an account is created, and a request that would exceed one simply fails, so the ECS guide advises requesting increases before you need them.[^s4] The quotas split into two families that behave very differently: fixed resource counts that shape how you lay out clusters and services, and adjustable API rate limits that shape how fast automation can drive ECS.[^s1] Most, but not all, ECS quotas appear under the Amazon ECS namespace in the Service Quotas console.[^s1]

## Resource quotas

As of 2026-11 the General Reference lists the same defaults in every supported Region.[^s1] Only **clusters per account** (10,000) is adjustable. The rest are fixed:[^s1]

| Quota | Default |
|---|---|
| Services per cluster | 5,000 |
| Tasks per service (desired count) | 5,000 |
| Container instances per cluster | 5,000 |
| Capacity providers per cluster | 20 |
| Containers per task definition | 10 |
| Task definition size | 64 KiB |
| Revisions per task definition family | 1,000,000 |
| Tasks launched per RunTask call | 10 |
| Target groups per service (ALB or NLB) | 5 |
| Subnets / security groups per awsvpcConfiguration | 16 / 5 |
| Tags per resource | 50 |
| Services per namespace | 300 |
| Tasks in PROVISIONING state per cluster | 500[^s1] |

Three details catch people out. Deregistered or deleted task definition revisions still count toward the one-million revision limit.[^s1] The PROVISIONING quota applies only to tasks launched through an EC2 Auto Scaling group capacity provider; when a big deployment hits it, the scheduler backs off with exponential retries and the rollout slows, so AWS suggests deploying in batches or keeping spare capacity.[^s1][^s3] And the service scheduler launches at most 500 tasks per service per minute on EC2 or external instances; on Fargate the figure is also 500 in most Regions but 125 in sixteen listed Regions, among them Africa (Cape Town), Asia Pacific (Taipei) and Mexico (Central), and in any Region introduced after 20 May 2025.[^s1] Real launch rates are often lower because of image pulls, health checks and load balancer registration.[^s1]

ECS Managed Instances is not bounded by a separate ECS instance quota; the ECS guide says it follows the Amazon EC2 service quotas.[^s4]

## API throttling

ECS throttles API calls per account per Region, whether they come from the console, the CLI or a third-party tool, and returns `ThrottlingException` ("Rate exceeded") when a limit is hit.[^s2] Each API category has a token bucket: the **burst** quota is the bucket size and the **sustained** quota is its refill rate per second, and actions in the same category share one bucket.[^s2] Some defaults as of 2026-11:[^s1][^s2]

- **Service modify** (CreateService, UpdateService, DeleteService, and the Express Mode and daemon equivalents): burst 50, sustained 5.[^s2]
- **Cluster resource modify** (RunTask, StartTask, StopTask, ExecuteCommand): burst 100, sustained 40.[^s2]
- **Cluster read** (DescribeClusters, ListClusters): burst 50, sustained 20.[^s2]
- **Task definition modify** (RegisterTaskDefinition, DeregisterTaskDefinition) and **cluster modify**: burst 20, sustained 1.[^s2]
- **Agent modify** (state-change submissions from the container agent): burst 200, sustained 120.[^s2]

The low sustained rate for service modify is the one most likely to bite pipelines that update hundreds of services at once.[^s2] The General Reference marks these rate quotas as adjustable, and the throttling guide says to request increases through AWS Support only after reducing call volume.[^s1][^s2] It recommends SDK retries with exponential backoff, jitter on scheduled jobs, caching DescribeTasks and ListTasks results, batching resource IDs, reading the task metadata endpoint from inside a task, and subscribing to ECS state-change events in Amazon EventBridge instead of polling.[^s2]

Fargate adds its own limits on top: a separate RunTask rate of 20 calls per second (burst and sustained) per account per Region, plus launch-rate and concurrent-vCPU quotas listed under the AWS Fargate namespace in Service Quotas.[^s5][^s1]

## Limits inherited from other services

At scale, ECS deployments often stall on neighbouring services rather than on ECS itself.[^s3] In awsvpc mode every task needs an elastic network interface, so EC2's Regional network interface quota, the fixed per-instance-type ENI limit and EC2 API throttling all apply; ENI trunking raises task density, for example from 2 to 10 awsvpc tasks on a c5.large.[^s3] Elastic Load Balancing target-registration APIs can also be throttled, which shows up in service event messages.[^s3] Services that use ECS service discovery are capped at 1,000 tasks because AWS Cloud Map allows only 1,000 instances per service, a quota that cannot be raised; Cloud Map also defaults to 2,000 instances per namespace (adjustable).[^s1][^s6]

## Connections

- **Broader: Amazon ECS** (`kt:amazon-ecs`), whose resources and APIs these quotas bound.[^s4]
- **Configured via Service Quotas** (`kt:service-quotas`), where most ECS quotas and the adjustable API rates are listed.[^s1]
- **Scoped to AWS Regions** (`kt:aws-regions`): every value is per account per Region, and some launch-rate defaults differ by Region.[^s1][^s2]
- **Contrasts with Fargate quotas** (`kt:fargate-quotas`), a separate namespace covering vCPU concurrency and launch rates for ECS tasks and EKS pods, and **related to AWS Fargate** (`kt:aws-fargate`).[^s1][^s5]
- **Related to ECS Managed Instances** (`kt:ecs-managed-instances`), which follows EC2 quotas, to **AWS Cloud Map** (`kt:aws-cloud-map`), whose per-service limit caps service-discovery tasks, and to **Amazon EventBridge** (`kt:amazon-eventbridge`), the recommended alternative to polling.[^s4][^s6][^s2]

## Sources
[^s1]: Amazon Web Services, AWS General Reference, "Amazon ECS endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/ecs-service.md (accessed 2026-11-14)
[^s2]: Amazon Web Services, Amazon ECS Developer Guide, "Request throttling for the Amazon ECS API". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/request-throttling.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, Amazon ECS Developer Guide, "Handle Amazon ECS service quotas and API throttling limits". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/operating-at-scale-service-quotas.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, Amazon ECS Developer Guide, "Amazon ECS service quotas". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service_limits.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, Amazon ECS Developer Guide, "AWS Fargate throttling quotas". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/throttling.html (accessed 2026-11-14)
[^s6]: Amazon Web Services, AWS General Reference, "AWS Cloud Map endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/cloud_map.html (accessed 2026-11-14)
