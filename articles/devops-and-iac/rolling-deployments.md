---
id: kt:rolling-deployments
type: Pattern
title: Rolling deployments
aliases: [Rolling deployment, Rolling update, Rolling updates, Rolling release]
domain: devops-and-iac
summary: >-
  A release pattern that replaces the old version with the new one in batches across an existing
  fleet of instances or tasks, so part of the fleet keeps serving traffic while each batch is updated.
verified: 2026-11-24
status: concept
broader: [kt:ci-cd]
related: [kt:immutable-infrastructure, kt:amazon-ec2-auto-scaling]
relations:
  - {predicate: alternativeTo, object: kt:canary-deployments}
  - {predicate: contrastsWith, object: kt:blue-green-deployments}
  - {predicate: usedBy, object: kt:amazon-ecs}
  - {predicate: usedBy, object: kt:aws-elastic-beanstalk}
  - {predicate: usedBy, object: kt:aws-cloudformation}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
sources:
  - id: s1
    title: "Deployment policies and settings - AWS Elastic Beanstalk"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html
    type: official-docs
    accessed: 2026-11-24
  - id: s2
    title: "Deploy Amazon ECS services by replacing tasks - Amazon Elastic Container Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-ecs.html
    type: official-docs
    accessed: 2026-11-24
  - id: s3
    title: "How the Amazon ECS deployment circuit breaker detects failures - Amazon Elastic Container Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-circuit-breaker.html
    type: official-docs
    accessed: 2026-11-24
  - id: s4
    title: "How CloudWatch alarms detect Amazon ECS deployment failures - Amazon Elastic Container Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-alarm-failure.html
    type: official-docs
    accessed: 2026-11-24
  - id: s5
    title: "UpdatePolicy attribute - AWS CloudFormation"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-attribute-updatepolicy.html
    type: official-docs
    accessed: 2026-11-24
created: 2026-11-24
---

# Rolling deployments

A rolling deployment updates an application in place across the fleet it already runs on, one batch at a time. Each batch is taken out of service, moved to the new version and returned to service once it passes health checks, while the remaining batches keep serving the old version.[^s1] Unlike blue/green, it does not need a full second environment; unlike all-at-once, the whole fleet is never down simultaneously. The cost is that for the length of the rollout two versions serve traffic side by side.[^s1][^s2]

## How it works

The core controls are the **batch size** (how much of the fleet changes at once) and the **health gate** that decides whether the next batch may start. On AWS these show up under different names per service.[^s1][^s2][^s5]

**AWS Elastic Beanstalk.** Beanstalk offers five deployment policies: All at once (the default), Rolling, Rolling with additional batch, Immutable and Traffic splitting.[^s1] Batch size is set with `BatchSizeType` (`Percentage` of the Auto Scaling group, up to 100 percent, or `Fixed`, up to the group's maximum) and `BatchSize`. With plain **Rolling**, capacity drops by one batch during each step; **Rolling with additional batch** first launches an extra batch so full capacity is kept, then terminates it when the deployment finishes.[^s1] Traffic returns to an updated instance only after it passes the healthy check threshold; with enhanced health reporting a web server instance must pass 12 consecutive OK checks within two minutes (18 checks in three minutes for worker environments).[^s1]

**Amazon ECS.** The default ECS (rolling update) deployment type replaces tasks according to two percentages of the service's desired count. `minimumHealthyPercent` (rounded up) is the floor of healthy tasks that must stay running, and `maximumPercent` (rounded down) is the ceiling of tasks allowed during the deployment. With a desired count of 4, a minimum of 50 lets the scheduler stop two tasks before starting two replacements, and a maximum of 200 lets it start four new tasks before stopping the old four.[^s2] The values must leave room to stop or start at least one task, or the service emits an event saying it could not proceed.[^s2]

**AWS CloudFormation and EC2 Auto Scaling.** An `AutoScalingRollingUpdate` update policy on an Auto Scaling group replaces instances in batches when properties such as the launch template, launch configuration, `VPCZoneIdentifier` or placement group change.[^s5] `MaxBatchSize` defaults to 1 (maximum 100), `MinInstancesInService` defaults to 0 and should be set at least to the group's `MinSize`, and `MinSuccessfulInstancesPercent` defaults to 100. When `WaitOnResourceSignals` is true, `PauseTime` (ISO 8601, at most `PT1H`) defaults to `PT5M` and acts as the signal timeout (values as of 2026-11).[^s5]

## Failure handling and gotchas

- **No automatic rollback in Beanstalk rolling.** If a batch does not turn healthy within the command timeout, the deployment fails; completed batches stay on the new version, pending ones stay on the old, and AWS says to redeploy a known-good version to roll back.[^s1] Immutable and traffic-splitting policies, by contrast, leave the old instances untouched on failure.[^s1]
- **ECS failure detection.** The deployment circuit breaker counts tasks that fail to reach RUNNING, then load balancer, Cloud Map and container health check failures. Its default threshold type, `BOUNDED_PERCENT`, uses 50% of desired count, clamped between 3 and 200, and `rollback=true` returns the service to the last COMPLETED deployment.[^s3] CloudWatch alarms can fail and optionally roll back a deployment too, after a bake time; if an alarm is already in ALARM when a deployment starts, ECS ignores alarms for that deployment.[^s4]
- **Rollback uses the old policy.** When a CloudFormation stack update fails, the rollback runs with the previous template's `UpdatePolicy`, and too short a `PauseTime` can make the rollback itself fail.[^s5]
- **Maintenance policy is not honored.** CloudFormation rolling updates ignore the Auto Scaling instance maintenance policy; AWS points to `AutoScalingInstanceRefresh`, which cannot be combined with `AutoScalingRollingUpdate` on one group.[^s5]
- **Mixed versions.** Because old and new versions overlap, APIs, schemas and session handling must be backward compatible for the length of the rollout.[^s1][^s2]

## When to use it (and when not)

Choose rolling when you cannot afford a duplicate environment, the fleet has enough instances that losing one batch of capacity is tolerable (or you add a batch), and the release is backward compatible. Prefer **blue/green** or Beanstalk's immutable policy when you need a clean, fast rollback without redeploying, and a **canary** when you want a small share of real traffic to validate the release before anything else changes.[^s1][^s4]

## Connections

Rolling deployments are a release technique within **CI/CD**. They are an **alternative to canary deployments** and **contrast with blue/green deployments**, which keep the old environment intact instead of updating it in place.[^s1] The pattern is **used by Amazon ECS** (its default rolling update controller), **AWS Elastic Beanstalk** (the Rolling policies) and **AWS CloudFormation** (`AutoScalingRollingUpdate` on **Amazon EC2 Auto Scaling** groups).[^s1][^s2][^s5] **Amazon CloudWatch** alarms **monitor** ECS rollouts and can trigger rollback.[^s4] It relates to **immutable infrastructure**: Beanstalk's immutable policy is the replace-instead-of-update counterpart to its rolling policies.[^s1]

## Sources
[^s1]: Amazon Web Services, "Deployment policies and settings - AWS Elastic Beanstalk". https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html (accessed 2026-11-24)
[^s2]: Amazon Web Services, "Deploy Amazon ECS services by replacing tasks - Amazon Elastic Container Service". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-ecs.html (accessed 2026-11-24)
[^s3]: Amazon Web Services, "How the Amazon ECS deployment circuit breaker detects failures - Amazon Elastic Container Service". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-circuit-breaker.html (accessed 2026-11-24)
[^s4]: Amazon Web Services, "How CloudWatch alarms detect Amazon ECS deployment failures - Amazon Elastic Container Service". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-alarm-failure.html (accessed 2026-11-24)
[^s5]: Amazon Web Services, "UpdatePolicy attribute - AWS CloudFormation". https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-attribute-updatepolicy.html (accessed 2026-11-24)
