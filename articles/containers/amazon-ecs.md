---
id: kt:amazon-ecs
type: Service
title: Amazon Elastic Container Service
aliases: [Amazon ECS, ECS, AWS ECS]
domain: containers
summary: >-
  AWS's fully managed, AWS-native container orchestration service, which runs and maintains
  containerized tasks and long-running services on AWS Fargate, Amazon ECS Managed Instances,
  self-managed Amazon EC2 capacity or on-premises servers (ECS Anywhere).
verified: 2026-11-08
status: current
broader: [kt:aws-container-services]
related: [kt:amazon-ecr, kt:ecs-managed-instances]
relations:
  - {predicate: runsOn, object: kt:aws-fargate}
  - {predicate: runsOn, object: kt:amazon-ec2}
  - {predicate: alternativeTo, object: kt:amazon-eks}
  - {predicate: governedBy, object: kt:ecs-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Amazon ECS launch types and capacity providers"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/capacity-launch-type-comparison.html
    type: official-docs
    accessed: 2026-11-08
  - id: s2
    title: "Amazon ECS services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html
    type: official-docs
    accessed: 2026-11-08
  - id: s3
    title: "Amazon ECS endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/ecs-service.html
    type: official-docs
    accessed: 2026-11-08
  - id: s4
    title: "Amazon ECS Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ecs/pricing/
    type: official-docs
    accessed: 2026-11-08
  - id: s5
    title: "Announcing Amazon ECS Managed Instances for containerized applications"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/announcing-amazon-ecs-managed-instances-for-containerized-applications/
    type: official-blog
    accessed: 2026-11-08
  - id: s6
    title: "Amazon ECS and AWS Fargate - AWS Product Comparison"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compare/ecs-and-fargate/
    type: official-docs
    accessed: 2026-11-08
  - id: s7
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-08
created: 2026-11-08
---

# Amazon Elastic Container Service

Amazon Elastic Container Service (Amazon ECS) is AWS's own container orchestrator: AWS describes it as a fully managed, AWS-native orchestration service, as opposed to a Kubernetes distribution.[^s6] You describe your containers in a task definition, ECS places the resulting tasks onto compute capacity in a cluster, and an ECS service keeps a chosen number of those tasks running. The orchestration layer itself is not billed; you pay for the compute and storage your tasks consume.[^s4]

## How it works

A **service** runs and maintains a specified number of tasks from one task definition in a cluster. When a task stops or fails its container or load-balancer health checks, the service scheduler launches a replacement; it also throttles relaunches of tasks that repeatedly fail to reach the RUNNING state and records service events. AWS recommends services for long-running stateless applications, with placement strategies and constraints steering where tasks land.[^s2] Services can sit behind an Application Load Balancer (HTTP/HTTPS), Network Load Balancer (TCP/UDP) or Gateway Load Balancer (virtual appliances), scale their desired task count with Application Auto Scaling, and talk to each other through Service Connect, Cloud Map-based service discovery or Amazon VPC Lattice.[^s2]

ECS offers four compute options. **AWS Fargate** is serverless: AWS manages the hosts. **Amazon ECS Managed Instances** gives EC2-style choice of instance types while AWS handles the instances. The **EC2** option uses instances you manage yourself (AMIs, host configuration), and **External** (ECS Anywhere) registers your own on-premises servers.[^s1] AWS now recommends launching workloads through **capacity providers** (FARGATE and FARGATE_SPOT exist on every cluster, plus Auto Scaling group and Managed Instances capacity providers) and treating launch types mainly as task-definition compatibility declarations. A cluster can mix provider types, but one capacity provider strategy cannot. You can move a service from a launch type to a capacity provider, but not directly from one launch type to another (say, EC2 to Fargate).[^s1]

ECS Managed Instances launched on 30 September 2025. It runs tasks on Bottlerocket, applies security patches every 14 days, consolidates tasks onto fewer instances and terminates idle ones. By default it chooses cost-optimized general-purpose instance types, and you can filter by attributes such as GPU acceleration or CPU architecture.[^s5]

## Quotas, pricing and gotchas

Default quotas per Region (as of 2026-11): 10,000 clusters per account (adjustable), 5,000 services per cluster, 5,000 tasks per service, 5,000 container instances per cluster, 20 capacity providers per cluster, 10 containers per task definition, a 64 KiB task definition size and 10 tasks per RunTask call. All of these except clusters per account are fixed. A service that uses ECS service discovery is held to 1,000 tasks by the AWS Cloud Map quota.[^s3]

Pricing (as of 2026-11): the EC2 option adds no ECS charge; you pay for instances, EBS volumes and public IPv4 addresses. Fargate bills requested vCPU, memory and ephemeral storage per second with a one-minute minimum; in US East (N. Virginia), Linux/x86 is $0.000011244 per vCPU-second and $0.000001235 per GB-second, and each task gets 20 GB of ephemeral storage at no extra cost. Managed Instances adds a per-instance management fee that varies by instance type (in US West (Oregon), for example, $0.021 per hour for an m5a.xlarge) on top of the EC2 price, whatever purchase option you use. ECS Anywhere costs $0.01025 per registered instance per hour.[^s4]

Gotchas: Fargate Spot tasks run at up to 70% off On-Demand but can be interrupted, and Spot Fleet is available only as a capacity provider, not as a launch type.[^s4][^s1] The Managed Instances fee still applies when the underlying EC2 capacity is covered by Savings Plans, Reserved Instances or Spot.[^s4]

## When to use it (and when not)

AWS positions ECS for microservices, batch jobs, application migration and hybrid deployments, especially when you want to choose the compute underneath. It positions Fargate for per-task isolation with no host management.[^s6] ECS suits teams that want AWS-native APIs without running Kubernetes. Teams standardised on Kubernetes tooling would look at Amazon EKS instead; both appear in the Cloud Practitioner exam's Containers category.[^s6][^s7] Choose the EC2 option or Managed Instances over Fargate when you need specific instance types, GPUs or denser bin-packing. Managed Instances lets AWS handle patching and scaling of those hosts, and self-managed EC2 gives you full control.[^s1][^s5]

## Connections

- **Broader: AWS container services** (`kt:aws-container-services`), listed with Amazon ECR and Amazon EKS in the exam guide's Containers category.[^s7]
- **Related to Amazon ECR** (`kt:amazon-ecr`), the container registry in the same category, and **ECS Managed Instances** (`kt:ecs-managed-instances`), the newest compute option.[^s7][^s5]
- **Runs on AWS Fargate** (`kt:aws-fargate`) and **Amazon EC2** (`kt:amazon-ec2`) through launch types and capacity providers.[^s1]
- **Alternative to Amazon EKS** (`kt:amazon-eks`), AWS's managed Kubernetes offering.[^s6][^s7]
- **Governed by ECS quotas** (`kt:ecs-quotas`), mostly fixed per Region.[^s3]
- **Covered by AWS Certified Cloud Practitioner** (`kt:aws-certified-cloud-practitioner`); the CLF-C02 in-scope list names Amazon ECS under Containers.[^s7]

## Sources
[^s1]: Amazon Web Services, "Amazon ECS launch types and capacity providers". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/capacity-launch-type-comparison.html (accessed 2026-11-08)
[^s2]: Amazon Web Services, "Amazon ECS services". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html (accessed 2026-11-08)
[^s3]: Amazon Web Services, "Amazon ECS endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/ecs-service.html (accessed 2026-11-08)
[^s4]: Amazon Web Services, "Amazon ECS Pricing". https://aws.amazon.com/ecs/pricing/ (accessed 2026-11-08)
[^s5]: Amazon Web Services, AWS News Blog, "Announcing Amazon ECS Managed Instances for containerized applications". https://aws.amazon.com/blogs/aws/announcing-amazon-ecs-managed-instances-for-containerized-applications/ (accessed 2026-11-08)
[^s6]: Amazon Web Services, "Amazon ECS and AWS Fargate - AWS Product Comparison". https://aws.amazon.com/compare/ecs-and-fargate/ (accessed 2026-11-08)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html (accessed 2026-11-08)
