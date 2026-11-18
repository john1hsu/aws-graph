---
id: kt:canary-deployments
type: Pattern
title: Canary deployments
aliases: [Canary deployment, Canary release, Canary releases, Canary traffic shifting]
domain: devops-and-iac
summary: >-
  A release pattern that exposes a new version to a small, randomly chosen share of production
  traffic first, watches its health, and only then shifts the rest of the traffic to it, rolling
  back if the canary misbehaves.
verified: 2026-11-18
status: concept
broader: [kt:ci-cd]
related: [kt:immutable-infrastructure, kt:operational-excellence-pillar, kt:aws-lambda, kt:aws-sam]
relations:
  - {predicate: contrastsWith, object: kt:blue-green-deployments}
  - {predicate: alternativeTo, object: kt:rolling-deployments}
  - {predicate: usedBy, object: kt:aws-codedeploy}
  - {predicate: usedBy, object: kt:amazon-ecs}
  - {predicate: usedBy, object: kt:amazon-api-gateway}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
sources:
  - id: s1
    title: "Canary deployments - Overview of Deployment Options on AWS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/canary-deployments.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Amazon ECS canary deployments"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/canary-deployment.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Working with deployment configurations in CodeDeploy"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Implement Lambda canary deployments using a weighted alias"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/configuring-alias-routing.md
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Set up an API Gateway canary release deployment"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/apigateway/latest/developerguide/canary-release.md
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "OPS06-BP03 Employ safe deployment strategies - Operational Excellence Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/operational-excellence-pillar/ops_mit_deploy_risks_deploy_mgmt_sys.html
    type: official-docs
    accessed: 2026-11-18
created: 2026-11-18
---

# Canary deployments

A canary deployment releases a new version gradually: a small slice of production traffic, the **canary group**, reaches the new code first, and the rest follows only if that slice stays healthy.[^s1] AWS's deployment-options whitepaper frames it as a more risk-averse variant of the blue/green family, because exposure is limited to a few users before any full cutover.[^s1] The Well-Architected operational excellence pillar lists canary releases among the safe deployment strategies it expects (best practice OPS06-BP03), alongside feature flags, one-box, immutable, traffic-splitting and blue/green releases, and rates the risk of skipping such strategies as medium.[^s6]

## How it works

The whitepaper describes two shapes. In the **two-step** form, the new version takes a first small increment of traffic for trial and, once accepted, receives everything else. In the **linear** form, the share grows in equal steps until the new version carries all traffic.[^s1] Either way the loop is the same: shift a slice, compare the canary's error rates and latency with the baseline, then promote or roll back. Selection is random, so no single user is stuck on a faulty build for long and most users never see it.[^s5]

The pattern's main anti-pattern target is pushing a failing change to all of production at once, which hits every customer simultaneously and can force days of emergency fixes.[^s6]

## AWS implementations

**AWS CodeDeploy.** For Lambda and ECS, CodeDeploy ships predefined canary configurations that send 10% of traffic first and the remaining 90% after a fixed wait: `LambdaCanary10Percent5Minutes`, `…10Minutes`, `…15Minutes` and `…30Minutes`, and `ECSCanary10Percent5Minutes` and `…15Minutes` (as of 2026-11).[^s3] You can define custom canary percentages and intervals, except for ECS blue/green deployments managed through CloudFormation, and an ECS service behind a Network Load Balancer is limited to all-at-once.[^s3] EC2/On-Premises deployment groups have no canary configurations; they use minimum-healthy-host rules such as `OneAtATime` instead.[^s3]

**Amazon ECS native canary.** ECS can run a canary strategy itself, without CodeDeploy, routing between target groups through Elastic Load Balancing or VPC Lattice. You set a canary percentage and a canary bake time; ECS shifts that percentage to the new revision, waits, then moves 100%.[^s2] AWS suggests starting at 5–10% and evaluating for 10–30 minutes. Lifecycle hooks (Lambda functions or pause hooks resumed with `ContinueServiceDeployment`) can gate each stage, and CloudWatch alarms can trigger automatic rollback to the original revision.[^s2] Each lifecycle stage may last up to 24 hours, and a CloudFormation-driven deployment is capped at 36 hours overall (as of 2026-11).[^s2]

**AWS Lambda weighted aliases.** An alias can point at two published versions with a weight on the additional one, for example 3% to version 2 and 97% to version 1. Both versions must share an execution role and dead-letter-queue settings, and `$LATEST` is not allowed.[^s4] The invoked version appears in the `START` log line, in the `ExecutedVersion` metric dimension and, for synchronous calls, in the `x-amz-executed-version` response header. AWS SAM's `DeploymentPreference` drives these weights through CodeDeploy.[^s4]

**Amazon API Gateway canary release.** On a REST API stage you add `canarySettings`: a separate deployment ID, a traffic percentage from 0.0 to 100.0, optional stage-variable overrides and whether canary requests may use the stage cache.[^s5] Canary requests get their own execution and access log groups with a `/Canary` suffix, which makes comparison easy. While a canary is enabled, the stage cannot move to another non-canary deployment until you promote or remove the canary.[^s5]

## Gotchas

- **Low traffic, noisy signal.** Lambda splits traffic probabilistically, so at low volumes the observed share can differ widely from the configured weight; ECS likewise warns that the canary slice needs enough traffic to be meaningful.[^s2][^s4]
- **Two versions live at once.** Both revisions run during evaluation, which raises capacity use and means database changes must be backward compatible.[^s2]
- **Provisioned concurrency.** With Lambda, raise provisioned concurrency while alias routing is active to avoid spillover invocations.[^s4]
- **Longer releases.** Bake times add directly to deployment duration.[^s2]

## When to use it

Pick a canary when real-user validation matters more than release speed and the workload has enough traffic to reveal problems in a small slice. Prefer a straight blue/green cutover when you need a clean, all-at-once switch, and rolling updates when duplicate capacity is too expensive.[^s1][^s2]

## Connections

Canary releases are a technique within **CI/CD** and a concrete answer to the **operational excellence pillar**'s safe-deployment practice.[^s6] They **contrast with** **blue/green deployments**, which the whitepaper treats as the parent pattern, and are an **alternative to** **rolling deployments**.[^s1] The pattern is **used by** **AWS CodeDeploy**, **Amazon ECS** and **Amazon API Gateway**, while **AWS Lambda** aliases and **AWS SAM** provide the serverless mechanics.[^s3][^s4][^s5] **Amazon CloudWatch** alarms and logs **monitor** the canary and drive rollback, and the approach fits **immutable infrastructure**, since the new version runs on fresh resources rather than patched ones.[^s2][^s6]

## Sources
[^s1]: Amazon Web Services, "Canary deployments - Overview of Deployment Options on AWS". https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/canary-deployments.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Amazon ECS canary deployments". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/canary-deployment.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Working with deployment configurations in CodeDeploy". https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Implement Lambda canary deployments using a weighted alias". https://docs.aws.amazon.com/lambda/latest/dg/configuring-alias-routing.md (accessed 2026-11-18)
[^s5]: Amazon Web Services, "Set up an API Gateway canary release deployment". https://docs.aws.amazon.com/apigateway/latest/developerguide/canary-release.md (accessed 2026-11-18)
[^s6]: Amazon Web Services, "OPS06-BP03 Employ safe deployment strategies - Operational Excellence Pillar". https://docs.aws.amazon.com/wellarchitected/latest/operational-excellence-pillar/ops_mit_deploy_risks_deploy_mgmt_sys.html (accessed 2026-11-18)
