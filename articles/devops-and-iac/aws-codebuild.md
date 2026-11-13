---
id: kt:aws-codebuild
type: Service
title: AWS CodeBuild
aliases: [CodeBuild, Amazon CodeBuild]
domain: devops-and-iac
summary: >-
  AWS's fully managed build service that compiles source, runs tests and produces deployable
  artifacts in fresh, isolated environments on EC2 or Lambda compute, billed by build minute
  (or second) with no build servers to operate.
verified: 2026-11-13
status: current
broader: [kt:aws-developer-tools, kt:ci-cd]
related: [kt:aws-codepipeline, kt:aws-codedeploy, kt:infrastructure-as-code]
relations:
  - {predicate: runsOn, object: kt:amazon-ec2}
  - {predicate: runsOn, object: kt:aws-lambda}
  - {predicate: integratesWith, object: kt:amazon-ecr}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: coveredBy, object: kt:aws-certified-developer-associate}
sources:
  - id: s1
    title: "AWS CodeBuild FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/codebuild/faqs/
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "AWS CodeBuild pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/codebuild/pricing/
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "Quotas for AWS CodeBuild"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/codebuild/latest/userguide/limits.html
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "Build environment compute modes and types"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-compute-types.html
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "In-Scope AWS Services - AWS Certified Developer - Associate (DVA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/developer-associate-03/dva-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-13
created: 2026-11-13
---

# AWS CodeBuild

AWS CodeBuild is a fully managed build service: it takes source code, compiles it, runs tests and produces artifacts ready to deploy, without you provisioning, patching or scaling build servers.[^s1] Every build runs in a fresh, isolated environment that is thrown away when the build ends, and build output can be encrypted with an AWS KMS key while IAM resource-level permissions control who may start or view builds.[^s1]

## How it works

A **build project** tells CodeBuild where the source lives, which build environment to use and what to run. Build commands normally live in a `buildspec.yml` file in the repository, though they can also be set directly on the project.[^s1] The environment is a Docker image: AWS supplies preconfigured images for common languages and runtimes, or you can build your own image and pull it from Amazon ECR or Docker Hub (a custom image is required for .NET Framework builds on Windows).[^s1] Logs and test output stream to the console and to Amazon CloudWatch.[^s1]

CodeBuild has two **compute modes**. **EC2** mode is the more flexible one and runs either on-demand (predefined compute types such as `BUILD_GENERAL1_SMALL`) or on **reserved capacity fleets**, where you specify vCPUs, memory and disk and CodeBuild picks a matching instance type.[^s4] **Lambda** mode starts faster and does not queue builds, but offers only 1–10 GiB of memory with 10 GB of disk, and as of 2026-11 is offered in a subset of 11 commercial Regions.[^s4] Compute type names are not hardware specs on their own: a Linux small container gets 2 vCPUs, 4 GiB and 64 GB of disk, while the same `BUILD_GENERAL1_SMALL` on a Linux GPU environment gets 4 vCPUs, 16 GiB and 235 GB of SSD.[^s4]

## Quotas, pricing and gotchas

Pricing (as of 2026-11; the page's worked examples name no Region, so treat them as illustrative and check the rate table for your Region):[^s2]

- **On-demand EC2** is billed per build minute, rounded up; the example prices `general1.small` at $0.005 per minute. **On-demand Lambda** is billed per second, rounded up; the example uses $0.00001 per second for `lambda.arm.1GB`.[^s2]
- **Reserved capacity** is billed per instance minute whether or not builds run, with a 60-minute minimum per instance (24 hours for Mac); one `reserved.x86-64.g1.small` instance for 30 days comes to $129.60 in the example.[^s2]
- The free tier gives 100 build minutes a month on `general1.small` or `arm1.small` plus 6,000 build seconds on the 1 GB Lambda types, and it does not expire after 12 months; reserved capacity has no free tier. CloudWatch Logs, S3 artifact storage, KMS and data transfer are charged separately.[^s2]

Default quotas per Region per account (as of 2026-11):[^s3]

- 5,000 build projects (adjustable); separately, 5,000 report groups per account, with at most 5 reports per project and 500 test cases per report.[^s3]
- Build timeout from 5 to 2,160 minutes (36 hours); neither bound is adjustable. Build history is kept for one year.[^s3]
- Concurrent builds are set **per compute type**, and many start very low: 1 for most Linux, ARM, Windows and Lambda sizes, and 0 for Linux 2XLarge and GPU types. All are adjustable. A "Cannot have more than X active builds" error means you have hit this quota.[^s3]
- At most 10 compute fleets, and 5 security groups and 16 subnets per VPC configuration.[^s3]

The low concurrency defaults are the classic surprise: a team that moves its CI to a fresh account often sees builds queue until it requests an increase for the compute types it actually uses.[^s3]

## When to use it (and when not)

Use CodeBuild when you want pay-per-use build capacity that scales with demand and never sits idle, and when builds need to run inside your AWS network boundary with IAM and KMS controls.[^s1][^s2] It also suits teams that keep Jenkins as the orchestrator: the CodeBuild plugin for Jenkins sends jobs to CodeBuild so you don't run Jenkins worker nodes.[^s1] Pick Lambda compute for short, frequent builds where start-up latency matters; pick EC2 for larger, longer builds or when you need GPU or Windows environments; and consider reserved capacity only when steady utilization outweighs paying for idle instances.[^s4][^s2]

## Connections

CodeBuild is part of the **AWS developer tools** family, which AWS's DVA-C03 exam guide groups alongside **AWS CodePipeline** and **AWS CodeDeploy**.[^s6] Within **CI/CD** it supplies the compile-and-test step, producing the artifacts that a pipeline then hands to a deployment service or to **infrastructure-as-code** tooling.[^s1] Builds **run on** Amazon EC2 or AWS Lambda compute,[^s4] pull custom images from **Amazon ECR**,[^s1] and are access-controlled with **AWS IAM**.[^s1] AWS lists CodeBuild in scope for FedRAMP Class C (formerly the Moderate baseline, US East/West) and Class D (formerly High, GovCloud), linking it to **FedRAMP on AWS**.[^s5] The DVA-C03 exam guide for **AWS Certified Developer – Associate** lists CodeBuild among its in-scope Developer Tools services.[^s6]

## Sources
[^s1]: Amazon Web Services, "AWS CodeBuild FAQs". https://aws.amazon.com/codebuild/faqs/ (accessed 2026-11-13)
[^s2]: Amazon Web Services, "AWS CodeBuild pricing". https://aws.amazon.com/codebuild/pricing/ (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Quotas for AWS CodeBuild". https://docs.aws.amazon.com/codebuild/latest/userguide/limits.html (accessed 2026-11-13)
[^s4]: Amazon Web Services, "Build environment compute modes and types". https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-compute-types.html (accessed 2026-11-13)
[^s5]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-13)
[^s6]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Developer - Associate (DVA-C03)". https://docs.aws.amazon.com/aws-certification/latest/developer-associate-03/dva-03-in-scope-services.html (accessed 2026-11-13)
