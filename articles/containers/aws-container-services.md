---
id: kt:aws-container-services
type: Concept
title: AWS container services
aliases: [AWS containers, containers on AWS, AWS container portfolio, AWS containers category]
domain: containers
summary: >-
  The family of AWS services for building, storing, orchestrating and running container images:
  a registry (Amazon ECR), orchestrators (Amazon ECS, Amazon EKS, Red Hat OpenShift Service on AWS),
  compute capacity (AWS Fargate, Amazon EC2, Outposts) and higher-level options such as ECS Express Mode,
  Lightsail, AWS Batch and Lambda container images.
verified: 2026-11-12
status: concept
broader: [kt:aws-compute-services]
related: [kt:aws-serverless-services, kt:aws-app-runner, kt:red-hat-openshift-service-on-aws]
relations:
  - {predicate: hasPart, object: kt:amazon-ecs}
  - {predicate: hasPart, object: kt:amazon-eks}
  - {predicate: hasPart, object: kt:aws-fargate}
  - {predicate: hasPart, object: kt:amazon-ecr}
  - {predicate: pricedBy, object: kt:savings-plans}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Choosing an AWS container service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/containers-on-aws-how-to-choose/choosing-aws-container-service.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Containers - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/containers.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Amazon ECS vs Amazon EKS: making sense of AWS container services"
    publisher: Amazon Web Services (AWS Containers Blog)
    url: https://aws.amazon.com/blogs/containers/amazon-ecs-vs-amazon-eks-making-sense-of-aws-container-services
    type: official-blog
    accessed: 2026-11-12
  - id: s4
    title: "Amazon ECS Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ecs/pricing/
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "Amazon EKS pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/eks/pricing/
    type: official-docs
    accessed: 2026-11-12
  - id: s6
    title: "AWS App Runner availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ja_jp/apprunner/latest/dg/apprunner-availability-change.html
    type: official-docs
    accessed: 2026-11-12
  - id: s7
    title: "Announcing Amazon ECS Express Mode"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/announcing-amazon-ecs-express-mode
    type: official-blog
    accessed: 2026-11-12
  - id: s8
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-12
created: 2026-11-12
---

# AWS container services

"Containers" is the AWS service category for packaging applications as container images and running them at scale. The AWS overview whitepaper lists five offerings under it: Amazon ECR (the image registry), Amazon ECS (AWS's own orchestrator), Amazon EKS (managed Kubernetes), AWS App2Container (a command-line tool that containerizes existing .NET and Java applications) and Red Hat OpenShift Service on AWS (ROSA), which AWS and Red Hat support jointly.[^s2] In practice, running a container also involves compute services from other categories, so this node is the map that ties the registry, orchestrators and capacity options together rather than a single thing you provision.

## How the portfolio is organized

AWS's container decision guide (last updated May 2025) describes three layers:[^s1]

- **Compute capacity** – where containers actually run: Amazon EC2 instances, AWS Fargate (serverless capacity for both ECS and EKS, with no instances to manage) and AWS Outposts for on-premises hardware.
- **Orchestration** – scheduling, scaling and lifecycle management: Amazon ECS, Amazon EKS with its highly available managed control plane, and ROSA.
- **Vertical solutions** – bundled, higher-level services for teams with less container experience: AWS App Runner, AWS Lambda (functions packaged as container images), Amazon Lightsail (preconfigured servers and a container service) and AWS Batch.[^s1]

The guide also places Amazon ECR under "tools and services with container support", and it groups Amazon ECS Anywhere, Amazon EKS Anywhere and Amazon EKS Hybrid Nodes as the on-premises options.[^s1]

## Choosing among them

The decision guide's criteria are how much operational overhead the team wants to own, workload type (web apps, APIs, event-driven, streaming, data pipelines), portability between compute services or between on-premises and cloud, and team size and skills.[^s1] It flags that Kubernetes needs real DevOps/SRE investment, partly because Kubernetes ships three major releases a year and clusters must keep upgrading.[^s1] AWS's Containers Blog frames the main fork as simplicity versus flexibility. ECS is AWS-opinionated and cuts the number of compute, network and security decisions, while EKS gives you Kubernetes APIs and its ecosystem with AWS running the control plane.[^s3] The blog argues the choice is not a one-way door. Both share IAM, security and management tooling, container images are portable, and either orchestrator can run on EC2 or Fargate.[^s3]

## Pricing and gotchas

The orchestrators are billed differently, which matters for many small clusters. As of 2026-11, ECS itself has no separate charge; you pay for the Fargate, EC2 or other resources your tasks use.[^s4] Fargate bills vCPU, memory and ephemeral storage per second with a one-minute minimum. In US East (N. Virginia), Linux/x86 Fargate is $0.000011244 per vCPU-second and $0.000001235 per GB-second, and Fargate Spot is up to 70% cheaper for interruption-tolerant tasks.[^s4] ECS Anywhere costs $0.01025 per registered on-premises instance per hour.[^s4] EKS charges $0.10 per cluster per hour while a cluster's Kubernetes version is in standard support. That rises to $0.60 per hour in extended support, so stale clusters cost six times as much.[^s5] Compute Savings Plans cover Fargate and EC2 usage under ECS.[^s4]

The simplest tier is changing. AWS App Runner is no longer open to new customers. Existing customers can still create services, but no new features are planned.[^s6] AWS points new web apps and APIs to Amazon ECS Express Mode. One API call provisions a Fargate-backed ECS service with an Application Load Balancer, auto scaling, networking and a default URL.[^s6] Express Mode launched in November 2025 as available in all AWS Regions, at no extra charge beyond the resources it creates, and up to 25 Express Mode services can share one ALB.[^s7] Source-code App Runner deployments need a Dockerfile and image build to move over, because Express Mode needs a container image.[^s6]

## Connections

This node is the containers branch of `kt:aws-compute-services` and the parent of the container services in the graph. Its parts are `kt:amazon-ecs` and `kt:amazon-eks` (orchestration), `kt:aws-fargate` (serverless capacity, also part of `kt:aws-serverless-services`) and `kt:amazon-ecr` (the registry).[^s1][^s2] It is related to `kt:aws-app-runner`, the vertical-solution tier now closed to new customers,[^s6] and to `kt:red-hat-openshift-service-on-aws`, the third orchestrator option.[^s2] It is `pricedBy` `kt:savings-plans`, since Compute Savings Plans discount Fargate and EC2 container capacity.[^s4] The current Cloud Practitioner (CLF-C02) in-scope services list has a Containers category naming Amazon ECR, Amazon ECS and Amazon EKS, which is the basis for the `coveredBy` edge.[^s8]

## Sources
[^s1]: Amazon Web Services, "Choosing an AWS container service". https://docs.aws.amazon.com/decision-guides/latest/containers-on-aws-how-to-choose/choosing-aws-container-service.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Containers - Overview of Amazon Web Services". https://docs.aws.amazon.com/whitepapers/latest/aws-overview/containers.html (accessed 2026-11-12)
[^s3]: Amazon Web Services (AWS Containers Blog), "Amazon ECS vs Amazon EKS: making sense of AWS container services". https://aws.amazon.com/blogs/containers/amazon-ecs-vs-amazon-eks-making-sense-of-aws-container-services (accessed 2026-11-12)
[^s4]: Amazon Web Services, "Amazon ECS Pricing". https://aws.amazon.com/ecs/pricing/ (accessed 2026-11-12)
[^s5]: Amazon Web Services, "Amazon EKS pricing". https://aws.amazon.com/eks/pricing/ (accessed 2026-11-12)
[^s6]: Amazon Web Services, "AWS App Runner availability change". https://docs.aws.amazon.com/ja_jp/apprunner/latest/dg/apprunner-availability-change.html (accessed 2026-11-12)
[^s7]: Amazon Web Services, "Announcing Amazon ECS Express Mode". https://aws.amazon.com/about-aws/whats-new/2025/11/announcing-amazon-ecs-express-mode (accessed 2026-11-12)
[^s8]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-12)
