---
id: kt:aws-codepipeline
type: Service
title: AWS CodePipeline
aliases: [CodePipeline, Amazon CodePipeline]
domain: devops-and-iac
summary: >-
  AWS's managed continuous delivery service that models a software release as a pipeline of
  stages and actions (source, build, test, approval, deploy) and runs it automatically on each
  change, billed per active pipeline (V1) or per action execution minute (V2).
verified: 2026-11-11
status: current
broader: [kt:aws-developer-tools, kt:ci-cd]
related: [kt:aws-codebuild, kt:aws-codedeploy, kt:infrastructure-as-code]
relations:
  - {predicate: integratesWith, object: kt:aws-cloudformation}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-developer-associate}
sources:
  - id: s1
    title: "What type of pipeline is right for me?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/codepipeline/latest/userguide/pipeline-types-planning.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "AWS CodePipeline pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/codepipeline/pricing
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Quotas in AWS CodePipeline"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/codepipeline/latest/userguide/limits.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "AWS CodePipeline now available in Asia Pacific (New Zealand) region"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/07/aws-codepipeline-new-zealand/
    type: official-blog
    accessed: 2026-11-11
  - id: s5
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "AWS Certified Developer - Associate (DVA-C03) Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/developer-associate-03/developer-associate-03.pdf
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# AWS CodePipeline

AWS CodePipeline is a managed continuous delivery service: you model the steps that take a code change from source through build and test to production, and the service runs that workflow every time the code changes.[^s4] It integrates natively with AWS CodeBuild, AWS CodeDeploy and AWS CloudFormation, and with third-party tools such as GitHub, and it supports manual approval gates, IAM-based access control and encryption of artifacts at rest and in transit.[^s4] As of July 2026 AWS lists it as generally available in 30 commercial Regions plus AWS GovCloud (US) and China Regions.[^s4]

## How it works

A pipeline is made of **stages** (for example Source, Build, Deploy), and each stage contains one or more **actions** that run sequentially or in parallel. Actions pass **artifacts** to each other through an artifact store in Amazon S3, which is why S3 storage and requests show up as a separate cost.[^s2] A pipeline can have 2 to 50 stages and up to 1,000 actions in total.[^s3]

CodePipeline has two pipeline types. **V1** is the original model. **V2** adds the features most teams now want: pipeline-level variables supplied at run time, triggers on Git tags, trigger filters on branches, pull requests and file paths, source revision overrides, the Commands action, stage conditions, stage rollback and automatic stage retry on failure, and the PARALLEL and QUEUED execution modes.[^s1] Action-level variables work in both types.[^s1] In QUEUED or PARALLEL mode a single pipeline can hold up to 50 concurrent executions.[^s3]

## Quotas, pricing and gotchas

Pricing (as of 2026-11, from the AWS pricing page, which lists no per-Region differences):[^s2]

- **V1:** $1.00 per active pipeline per month. A pipeline counts as active only once it is more than 30 days old and has had at least one code change run through it that month; there is no proration, and V1 pipelines are free for their first 30 days. The free tier covers one active V1 pipeline per month.[^s2]
- **V2:** $0.002 per action execution minute, measured from action start to completion and rounded up to the next minute. Manual approval and custom actions are not charged. The free tier gives 100 action minutes per month, shared across all V2 pipelines in the account, with no carry-over.[^s2]

AWS's planning guide includes a sample script that estimates the cost of moving an existing V1 pipeline to V2 from its execution history, counting only completed, billable actions.[^s1] Because V1 is flat and V2 is metered, the arithmetic is simple: $1.00 buys 500 V2 action minutes, so a pipeline whose billable actions run longer than that each month costs more on V2.[^s2]

Default quotas (as of 2026-11):[^s3]

- 1,000 pipelines per Region per account (adjustable; requests can take up to two weeks). Pipelines that **poll** their source are capped at 300 per Region and that cap cannot be raised, so prefer event-based change detection.[^s3]
- Source artifacts are limited to 7 GB from S3 and 1 GB from CodeCommit or GitHub; a CloudFormation deploy action accepts at most 256 MB and Elastic Beanstalk 512 MB. These sizes are fixed.[^s3]
- Manual approvals time out after 7 days by default (configurable up to 60 days); CodeBuild actions after 36 hours; most other actions after 1 hour.[^s3]
- Execution history is kept for 12 months.[^s3]

A common gotcha is that CodePipeline orchestrates but does not build or deploy by itself: compile and test work runs in CodeBuild (or the Commands action), and deployments are handed to CodeDeploy, CloudFormation, ECS, Elastic Beanstalk and similar targets.[^s4][^s3]

## When to use it (and when not)

Use CodePipeline when you want a managed, IAM-integrated release orchestrator that sits next to the rest of your AWS estate and hands builds to CodeBuild and deployments to CodeDeploy or CloudFormation without you operating a CI server.[^s4] Choose V2 for new pipelines unless you have a reason to stay on the flat monthly V1 price.[^s1][^s2] It is most attractive when governance matters: manual approval gates and IAM-controlled access are built in,[^s4] and AWS lists CodePipeline in scope for both FedRAMP Class C (formerly the Moderate baseline, US East/West) and Class D (formerly High, GovCloud).[^s5]

## Connections

CodePipeline belongs to the **AWS developer tools** family and implements **CI/CD**; it typically pairs with **AWS CodeBuild** for build and test and **AWS CodeDeploy** for deployments.[^s4] It deploys **infrastructure as code** through **AWS CloudFormation** actions and stores artifacts in **Amazon S3**.[^s3] Access is controlled by **AWS IAM** service and action roles, and it is a regional service scoped to **AWS Regions**, authorized under **FedRAMP on AWS**.[^s4][^s5] The DVA-C03 exam guide for **AWS Certified Developer – Associate** lists CodePipeline under Developer Tools and has a skill on using it to orchestrate CI/CD across environments.[^s6]

## Sources
[^s1]: Amazon Web Services, "What type of pipeline is right for me?". https://docs.aws.amazon.com/codepipeline/latest/userguide/pipeline-types-planning.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "AWS CodePipeline pricing". https://aws.amazon.com/codepipeline/pricing (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Quotas in AWS CodePipeline". https://docs.aws.amazon.com/codepipeline/latest/userguide/limits.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "AWS CodePipeline now available in Asia Pacific (New Zealand) region" (July 6, 2026). https://aws.amazon.com/about-aws/whats-new/2026/07/aws-codepipeline-new-zealand/ (accessed 2026-11-11)
[^s5]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-11)
[^s6]: Amazon Web Services, "AWS Certified Developer - Associate (DVA-C03) Exam Guide". https://docs.aws.amazon.com/pdfs/aws-certification/latest/developer-associate-03/developer-associate-03.pdf (accessed 2026-11-11)
