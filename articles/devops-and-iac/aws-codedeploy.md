---
id: kt:aws-codedeploy
type: Service
title: AWS CodeDeploy
aliases: [CodeDeploy, Amazon CodeDeploy]
domain: devops-and-iac
summary: >-
  AWS's managed deployment service that rolls application revisions out to EC2 instances,
  on-premises servers, Lambda functions and ECS services, using in-place or blue/green
  deployments with canary, linear or all-at-once traffic shifting and automatic rollback.
verified: 2026-11-14
status: current
broader: [kt:aws-developer-tools, kt:ci-cd]
related: [kt:aws-codepipeline, kt:aws-codebuild, kt:canary-deployments]
relations:
  - {predicate: implements, object: kt:blue-green-deployments}
  - {predicate: integratesWith, object: kt:amazon-ec2-auto-scaling}
  - {predicate: integratesWith, object: kt:elastic-load-balancing}
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: alternativeTo, object: kt:ecs-native-blue-green-deployments}
  - {predicate: governedBy, object: kt:codedeploy-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-developer-associate}
sources:
  - id: s1
    title: "What is CodeDeploy?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/codedeploy/latest/userguide/welcome.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "AWS CodeDeploy FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/codedeploy/faqs
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "CodeDeploy quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/codedeploy/latest/userguide/limits.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Working with deployment configurations in CodeDeploy"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "Choosing between Amazon ECS Blue/Green Native or AWS CodeDeploy in AWS CDK"
    publisher: Amazon Web Services (AWS DevOps & Developer Productivity Blog)
    url: https://aws.amazon.com/blogs/devops/choosing-between-amazon-ecs-blue-green-native-or-aws-codedeploy-in-aws-cdk
    type: official-blog
    accessed: 2026-11-14
  - id: s6
    title: "In-Scope AWS Services - AWS Certified Developer - Associate (DVA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/developer-associate-03/dva-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-14
created: 2026-11-14
---

# AWS CodeDeploy

AWS CodeDeploy automates application deployments to four kinds of target: Amazon EC2 instances, on-premises servers, AWS Lambda functions and Amazon ECS services.[^s1] It can ship almost any content (code, Lambda functions, configuration files, executables, packages, scripts or media) pulled from an Amazon S3 bucket, GitHub or Bitbucket, and it works without changes to existing application code, scaling from a single instance to thousands.[^s1]

## How it works

You describe a deployment with three pieces. An **application revision** is an archive of your content plus an **AppSpec file**, the CodeDeploy-specific file that lists what to install and which lifecycle hooks to run. A **deployment group** is the target set, for example EC2 instances selected by tag, Auto Scaling groups, or both. A **deployment configuration** sets how fast the rollout proceeds and what counts as failure.[^s1][^s4]

The service behaves differently on each **compute platform**:[^s1]

- **EC2/On-Premises** supports **in-place** deployments, where each instance's application is stopped, updated and restarted (optionally deregistered from its load balancer while it updates), and **blue/green** deployments, where CodeDeploy provisions replacement instances, installs the revision, and has Elastic Load Balancing shift traffic to them. Blue/green works only with EC2 instances, not on-premises servers.[^s1] An open-source **CodeDeploy agent** must run on every instance; it polls the service, pulls the revision from S3 or GitHub, and needs to reach AWS public endpoints. AWS tests it on Amazon Linux, RHEL, Ubuntu Server and Windows Server.[^s2][^s1]
- **AWS Lambda** deployments publish a new function version and shift traffic to it.[^s1]
- **Amazon ECS** deployments are always blue/green: CodeDeploy creates a replacement task set, shifts traffic to it and terminates the original task set after success. ECS blue/green can also be driven by a CloudFormation stack update, with no CodeDeploy application or deployment group to create.[^s1]

On EC2/On-Premises, the predefined configurations are `OneAtATime` (the default), `HalfAtATime` and `AllAtOnce`, each defined by a minimum number of healthy hosts; a per-Availability Zone healthy-host rule requires a custom configuration.[^s4] Lambda and ECS use canary, linear or all-at-once traffic shifting, for example `LambdaCanary10Percent5Minutes` (10% first, the rest five minutes later) or `ECSLinear10PercentEvery1Minutes`; custom canary and linear configurations are allowed except for CloudFormation-driven ECS deployments.[^s4] To roll back, CodeDeploy redeploys a previous revision, so earlier revisions must stay available; deployments can also be stopped mid-flight.[^s2][^s1]

## Quotas, pricing and gotchas

Pricing (as of 2026-11; the FAQ does not name a Region): deployments to Amazon EC2 carry no additional CodeDeploy charge, while on-premises instances cost $0.02 per instance update. The FAQ does not state Lambda or ECS pricing.[^s2]

Default quotas per account per Region (as of 2026-11):[^s3]

- 1,000 applications per account and 1,000 deployment groups per application (both adjustable); 1,300 concurrent deployments per account (adjustable), but only **one** concurrent deployment per deployment group (not adjustable).[^s3]
- 2,000 instances per deployment in us-east-1, us-east-2, us-west-2 and eu-central-1, and 1,000 elsewhere (adjustable).[^s3]
- Maximum run times: 12 hours for EC2/On-Premises in-place (8 hours for custom configurations created before June 2023), 109 hours for EC2 blue/green and 50 hours for Lambda; none adjustable.[^s3]
- A lifecycle event fails if it doesn't start within 5 minutes or finish within 3,600 seconds; an ECS service can belong to only one deployment group.[^s3]

Gotchas: `HalfAtATime` counts instances across all Auto Scaling groups in the deployment group, so one group can be fully updated while the other is untouched and the deployment still succeeds.[^s4] For ECS behind a Network Load Balancer, only `ECSAllAtOnce` is supported.[^s4] Deployments are Regional: a multi-Region rollout means copying the bundle to an S3 bucket in each Region and deploying to each.[^s2]

## When to use it (and when not)

CodeDeploy is the natural fit for rolling out software to fleets of EC2 instances or on-premises servers, and for safe, gradual traffic shifting on Lambda.[^s1] For ECS, AWS's DevOps blog (February 2026) calls built-in ECS blue/green the default for most new projects: it now offers canary and linear shifting too, keeps configuration inside the ECS service, and allows lifecycle hooks up to 24 hours per stage against CodeDeploy's one-hour limit. CodeDeploy is not deprecated; the same post keeps it as a sound choice where teams rely on CodePipeline integration, existing governance, or coordinated multi-service, multi-Region or multi-account releases.[^s5]

## Connections

CodeDeploy belongs to the **AWS developer tools** family; the DVA-C03 guide for **AWS Certified Developer – Associate** lists it under Developer Tools beside **AWS CodeBuild** and **AWS CodePipeline**.[^s6] In a **CI/CD** pipeline it is the deploy stage that takes artifacts from CodeBuild.[^s1] It **implements blue/green deployments** and canary releases,[^s1][^s4] **integrates with** Amazon EC2 Auto Scaling, Elastic Load Balancing and AWS Lambda,[^s1] is an **alternative to** ECS-native blue/green deployments for container services,[^s5] and is **governed by** the CodeDeploy quotas above.[^s3]

## Sources
[^s1]: Amazon Web Services, "What is CodeDeploy?". https://docs.aws.amazon.com/codedeploy/latest/userguide/welcome.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "AWS CodeDeploy FAQs". https://aws.amazon.com/codedeploy/faqs (accessed 2026-11-14)
[^s3]: Amazon Web Services, "CodeDeploy quotas". https://docs.aws.amazon.com/codedeploy/latest/userguide/limits.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, "Working with deployment configurations in CodeDeploy". https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, "Choosing between Amazon ECS Blue/Green Native or AWS CodeDeploy in AWS CDK". https://aws.amazon.com/blogs/devops/choosing-between-amazon-ecs-blue-green-native-or-aws-codedeploy-in-aws-cdk (accessed 2026-11-14)
[^s6]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Developer - Associate (DVA-C03)". https://docs.aws.amazon.com/aws-certification/latest/developer-associate-03/dva-03-in-scope-services.html (accessed 2026-11-14)
