---
id: kt:fargate-quotas
type: Limit
title: AWS Fargate quotas
aliases: [Fargate quotas, Fargate service quotas, Fargate limits, Fargate vCPU quota, Fargate launch rate limits]
domain: containers
summary: >-
  The per-account, per-Region limits on AWS Fargate: concurrent vCPU quotas for On-Demand and Spot
  capacity, token-bucket launch-rate quotas shared by Amazon ECS tasks and Amazon EKS pods, and a
  separate RunTask API rate. The vCPU and launch-rate quotas are adjustable through Service Quotas.
verified: 2026-11-15
status: current
broader: [kt:aws-fargate]
related: [kt:amazon-ecs, kt:amazon-eks]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:ecs-quotas}
sources:
  - id: s1
    title: "Amazon ECS endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/ecs-service.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "AWS Fargate throttling quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/throttling.html
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Migrating Fargate service quotas to vCPU-based quotas"
    publisher: Amazon Web Services (AWS Containers Blog)
    url: https://aws.amazon.com/blogs/containers/migrating-fargate-service-quotas-to-vcpu-based-quotas/
    type: official-blog
    accessed: 2026-11-15
  - id: s4
    title: "AWS Fargate usage metrics"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/monitoring-fargate-usage.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "Understand Fargate Pod configuration details"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/fargate-pod-configuration.html
    type: official-docs
    accessed: 2026-11-15
created: 2026-11-15
---

# AWS Fargate quotas

AWS Fargate has its own quota namespace in Service Quotas, separate from the Amazon ECS and Amazon EKS namespaces. It caps two things per account and per Region: how much Fargate compute you can run at once, measured in vCPUs, and how fast you can launch new ECS tasks or EKS pods onto it.[^s1] These quotas apply whichever orchestrator places the work, so an account running both ECS on Fargate and EKS on Fargate shares one pool.[^s1][^s2]

## How it works

There are six quotas, three for Fargate On-Demand and three for Fargate Spot. Each pricing class has a concurrent **vCPU resource count**, a **burst launch rate** and a **sustained launch rate**. All six are adjustable.[^s1] The vCPU quotas count the vCPUs that tasks and pods have requested and that are running at the same moment. They do not count tasks.[^s1][^s3]

Fargate used to limit the *number* of concurrent tasks and pods. In 2022 AWS moved every account to vCPU-based quotas. The reason was that a fixed task count meant very different amounts of compute depending on task size. Under the new model, sixteen 0.25-vCPU tasks, four 1-vCPU tasks and one 4-vCPU task each use the same 4 vCPUs.[^s3] Alarms built on the old task and pod count metrics had to be rebuilt on the vCPU metrics.[^s3]

Launch rates use a token bucket. The bucket's size is the burst rate, and its refill speed is the sustained rate. Each task or pod launched removes a token, so with the common defaults an account can start 100 tasks or pods at once and then 20 per second after that.[^s2] Separately, the `RunTask` API has its own bucket of 20 calls per second, burst and sustained. Each call can start up to 10 tasks, so `RunTask` alone tops out at 100 tasks per second.[^s2]

## Default values (as of 2026-11)

- **vCPU resource count:** 6 vCPUs for On-Demand and 6 vCPUs for Spot, in each supported Region.[^s1]
- **Launch rates:** 100 burst and 20 sustained per second for On-Demand and for Spot in most Regions. In 16 Regions the defaults are 25 burst and 5 sustained. Examples include af-south-1, ap-east-2, ca-west-1, il-central-1 and mx-central-1. The same lower defaults apply to any Region introduced after 20 May 2025.[^s1]
- **EKS-only accounts:** on the EKS platform versions AWS lists, accounts that launch only EKS pods get a launch bucket of 20 burst with a 20-per-second refill.[^s2]

These are starting values. The applied quota in an account can differ, and new accounts may start lower. Fargate watches each account's usage per Region and raises its quotas automatically as usage grows. You can also request an increase through Service Quotas.[^s1] The 2022 migration post cited a ceiling of up to 4,000 vCPUs per Region for each quota.[^s3]

## Gotchas

- **Spot launch rates count ECS tasks only.** The On-Demand launch-rate quotas cover both ECS tasks and EKS pods, but the Spot ones are defined only for ECS tasks.[^s1]
- **EKS pods can consume more vCPU than requested.** For pods, Fargate takes the larger of the biggest init-container request and the sum of the long-running containers' requests. It then adds 256 MB of memory for kubelet, kube-proxy and containerd and rounds up to the next supported size. A pod requesting 1 vCPU and 8 GB is therefore provisioned as 2 vCPU and 9 GB. The `CapacityProvisioned` annotation shows the size that is enforced and billed.[^s5]
- **Real launch rates are lower than the quotas.** Image pulls, health checks and load balancer registration slow launches down. Passing `count` to `RunTask` (up to 10 tasks per call) saves API tokens.[^s2]
- **The low default is easy to hit.** At 6 vCPUs, a new account can run only a handful of 1- or 2-vCPU tasks before launches fail. Check the applied value before a load test or a migration.[^s1]

## Monitoring

Fargate publishes usage to CloudWatch in the `AWS/Usage` namespace as the `ResourceCount` metric. The dimensions are `Service=Fargate`, `Type=Resource`, `Resource=vCPU`, and `Class=Standard/OnDemand` or `Class=Standard/Spot`.[^s4] These usage metrics map to the service quotas, so you can alarm when usage nears a limit from the Service Quotas console. Each class has its own count and needs its own alarm.[^s4]

## Connections

This node is the quota side of `kt:aws-fargate` (broader). It is related to `kt:amazon-ecs` and `kt:amazon-eks`, whose tasks and pods both draw on the same vCPU and launch-rate pools.[^s1][^s2] It is `configuredVia` `kt:service-quotas`, where the values are viewed and increases requested, and `monitoredBy` `kt:amazon-cloudwatch` through the `AWS/Usage` metrics.[^s1][^s4] It is `scopedTo` `kt:aws-regions` because every value is per account per Region and the defaults differ by Region.[^s1] It `contrastsWith` `kt:ecs-quotas`. That namespace limits ECS objects (clusters, services, task definitions) and ECS API rates, while this one limits the Fargate compute underneath them.[^s1][^s2]

## Sources
[^s1]: Amazon Web Services, "Amazon ECS endpoints and quotas - AWS General Reference". https://docs.aws.amazon.com/general/latest/gr/ecs-service.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "AWS Fargate throttling quotas". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/throttling.html (accessed 2026-11-15)
[^s3]: Amazon Web Services (AWS Containers Blog), "Migrating Fargate service quotas to vCPU-based quotas". https://aws.amazon.com/blogs/containers/migrating-fargate-service-quotas-to-vcpu-based-quotas/ (accessed 2026-11-15)
[^s4]: Amazon Web Services, "AWS Fargate usage metrics". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/monitoring-fargate-usage.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "Understand Fargate Pod configuration details". https://docs.aws.amazon.com/eks/latest/userguide/fargate-pod-configuration.html (accessed 2026-11-15)
