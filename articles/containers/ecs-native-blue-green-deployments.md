---
id: kt:ecs-native-blue-green-deployments
type: Feature
title: Amazon ECS blue/green deployments
aliases: [ECS native blue/green deployments, ECS built-in blue/green deployments, ECS-native blue/green]
domain: containers
summary: >-
  A deployment strategy built into the Amazon ECS deployment controller that starts a new (green)
  service revision beside the running (blue) one, shifts test and then production traffic to it in
  managed lifecycle stages, and keeps blue running for a bake period so rollback is near-instant; it
  also offers linear and canary traffic shifting.
verified: 2026-11-18
status: current
broader: [kt:amazon-ecs, kt:blue-green-deployments]
related: [kt:canary-deployments, kt:application-load-balancer, kt:aws-lambda, kt:aws-cdk, kt:aws-european-sovereign-cloud]
relations:
  - {predicate: partOf, object: kt:amazon-ecs}
  - {predicate: implements, object: kt:blue-green-deployments}
  - {predicate: implements, object: kt:canary-deployments}
  - {predicate: alternativeTo, object: kt:aws-codedeploy}
  - {predicate: dependsOn, object: kt:elastic-load-balancing}
  - {predicate: dependsOn, object: kt:amazon-vpc-lattice}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: configuredVia, object: kt:aws-cloudformation}
sources:
  - id: s1
    title: "Amazon ECS blue/green deployments"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-blue-green.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Amazon ECS blue/green service deployments workflow"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/blue-green-deployment-how-it-works.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Required resources for Amazon ECS blue/green deployments"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/blue-green-deployment-implementation.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Accelerate safe software releases with new built-in blue/green deployments in Amazon ECS"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/aws/accelerate-safe-software-releases-with-new-built-in-blue-green-deployments-in-amazon-ecs/
    type: official-blog
    accessed: 2026-11-18
  - id: s5
    title: "Amazon ECS canary deployments"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/canary-deployment.html
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "Amazon ECS now supports built-in Linear and Canary deployments"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/10/amazon-ecs-built-in-linear-canary-deployments/
    type: official-blog
    accessed: 2026-11-18
  - id: s7
    title: "Migrating from AWS CodeDeploy to Amazon ECS for blue/green deployments"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/containers/migrating-from-aws-codedeploy-to-amazon-ecs-for-blue-green-deployments/
    type: official-blog
    accessed: 2026-11-18
  - id: s8
    title: "Amazon ECS adds Amazon VPC Lattice support for blue/green, linear, and canary deployments"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/10/amazon-ecs-vpc-lattice-blue-green-deployments/
    type: official-blog
    accessed: 2026-11-18
  - id: s9
    title: "Choosing between Amazon ECS Blue/Green Native or AWS CodeDeploy in AWS CDK"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/devops/choosing-between-amazon-ecs-blue-green-native-or-aws-codedeploy-in-aws-cdk/
    type: official-blog
    accessed: 2026-11-18
  - id: s10
    title: "Amazon ECS advanced deployment strategies now available in AWS European Sovereign Cloud"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/07/ecs-adv-deployments-eu-sovereign-cloud/
    type: official-blog
    accessed: 2026-11-18
created: 2026-11-18
---

# Amazon ECS blue/green deployments

Amazon ECS blue/green deployments are a strategy of the ECS deployment controller itself, launched on 17 July 2025, that lets a service roll out a new revision without handing the release to a separate tool.[^s4] ECS starts the new ("green") service revision alongside the current ("blue") one, moves traffic to green only after it has been validated, and keeps blue alive for a bake period so a rollback is close to instant.[^s1][^s4] There is no extra charge; you pay for the compute both revisions consume during the rollout, and the feature launched in all commercial Regions.[^s4] In October 2025 ECS added **linear** and **canary** strategies on the same machinery,[^s6] and in July 2026 the three strategies reached the AWS European Sovereign Cloud.[^s10]

## How it works

You set the service's deployment controller to `ECS` and choose the blue/green strategy, then give ECS a way to steer traffic: an Application Load Balancer, a Network Load Balancer, ECS Service Connect, or Amazon VPC Lattice.[^s3] VPC Lattice support arrived on 2 October 2026 for new and existing services.[^s8] A service with none of these (a headless service) still gets green tasks replacing blue ones, but ECS does not shift any traffic.[^s1][^s3] With a load balancer, ECS flips weights between two target groups behind a listener rule, rather than swapping task sets as CodeDeploy does.[^s7]

A deployment walks through fixed lifecycle stages: an optional reconcile stage, pre-scale-up, scale-up (green launches at full size but takes no traffic), post-scale-up, test traffic shift, post-test-traffic shift, pre-production shift, production traffic shift, post-production shift, bake time, and clean-up, where blue scales to zero.[^s2] Test traffic reaches green through the same listener as production, matched by listener rules on request attributes such as a test header, so you can exercise the new version in the real environment while users still hit blue.[^s2]

Lifecycle hooks attach to most stages. A **Lambda hook** runs your code (synthetic tests, metric checks) and answers `SUCCEEDED`, `FAILED` (which rolls back) or `IN_PROGRESS`, which makes ECS call it again after 30 seconds by default.[^s4][^s7] A **pause hook** holds the deployment until someone calls `ContinueServiceDeployment`; the two traffic-shift stages accept only Lambda hooks.[^s1][^s5] CloudWatch alarms can also trigger automatic rollback.[^s3]

**Linear** moves production traffic in equal steps with a wait after each; **canary** sends a small slice first, waits a canary bake time, then moves the rest. Both still end with a deployment bake time before blue is removed.[^s6][^s5] Hooks bound to the production-shift stages run at every step.[^s5]

## Limits and gotchas (as of 2026-11)

- Each lifecycle stage may last up to 24 hours; reaching that fails and rolls back the deployment. The whole deployment can run 30 days, but 36 hours when driven by CloudFormation.[^s2]
- A Lambda hook invocation must return within 15 minutes; pause hooks can wait up to 20,160 minutes (14 days).[^s2]
- Behind a Network Load Balancer, the test and production shift stages each add about 10 minutes while ECS checks the shift is safe.[^s1]
- Both revisions run together, so capacity can roughly double during a rollout; plan cluster capacity accordingly.[^s1][^s3]
- There is no console button to force or stop a traffic shift, and the CodePipeline ECS standard action only handles image changes.[^s7]

## When to use it (and when not)

AWS's DevOps blog (February 2026, updated March 2026) calls the ECS-native strategy the recommended option for most teams and new projects: configuration lives in the ECS service, it supports Service Connect, headless services and multiple target groups, and AWS CDK has L2 support for it.[^s9] AWS CodeDeploy remains a reasonable choice where CodePipeline workflows, existing governance, or one release coordinated across services, Regions and accounts depend on it; CodeDeploy hooks, by contrast, have a one-hour limit.[^s9] AWS describes three migration paths from CodeDeploy, from an in-place controller switch (simplest, but no parallel test) to a new service behind a new load balancer with traffic moved at Route 53 or CloudFront (safest, costliest).[^s7]

## Connections

This feature is **part of Amazon ECS** and **implements** the general **blue/green deployment** pattern and, through its canary strategy, **canary deployments**.[^s1][^s6] It is an **alternative to AWS CodeDeploy** for ECS services.[^s9] It **depends on** a traffic layer — **Elastic Load Balancing** (Application or Network Load Balancer), Service Connect or **Amazon VPC Lattice**.[^s3][^s8] Rollbacks can be **monitored by Amazon CloudWatch** alarms, hooks run on **AWS Lambda**, and it can be **configured via AWS CloudFormation** or the AWS CDK.[^s2][^s9]

## Sources

[^s1]: Amazon Web Services, "Amazon ECS blue/green deployments". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-blue-green.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Amazon ECS blue/green service deployments workflow". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/blue-green-deployment-how-it-works.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Required resources for Amazon ECS blue/green deployments". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/blue-green-deployment-implementation.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Accelerate safe software releases with new built-in blue/green deployments in Amazon ECS". https://aws.amazon.com/blogs/aws/accelerate-safe-software-releases-with-new-built-in-blue-green-deployments-in-amazon-ecs/ (accessed 2026-11-18)
[^s5]: Amazon Web Services, "Amazon ECS canary deployments". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/canary-deployment.html (accessed 2026-11-18)
[^s6]: Amazon Web Services, "Amazon ECS now supports built-in Linear and Canary deployments". https://aws.amazon.com/about-aws/whats-new/2025/10/amazon-ecs-built-in-linear-canary-deployments/ (accessed 2026-11-18)
[^s7]: Amazon Web Services, "Migrating from AWS CodeDeploy to Amazon ECS for blue/green deployments". https://aws.amazon.com/blogs/containers/migrating-from-aws-codedeploy-to-amazon-ecs-for-blue-green-deployments/ (accessed 2026-11-18)
[^s8]: Amazon Web Services, "Amazon ECS adds Amazon VPC Lattice support for blue/green, linear, and canary deployments". https://aws.amazon.com/about-aws/whats-new/2026/10/amazon-ecs-vpc-lattice-blue-green-deployments/ (accessed 2026-11-18)
[^s9]: Amazon Web Services, "Choosing between Amazon ECS Blue/Green Native or AWS CodeDeploy in AWS CDK". https://aws.amazon.com/blogs/devops/choosing-between-amazon-ecs-blue-green-native-or-aws-codedeploy-in-aws-cdk/ (accessed 2026-11-18)
[^s10]: Amazon Web Services, "Amazon ECS advanced deployment strategies now available in AWS European Sovereign Cloud". https://aws.amazon.com/about-aws/whats-new/2026/07/ecs-adv-deployments-eu-sovereign-cloud/ (accessed 2026-11-18)
