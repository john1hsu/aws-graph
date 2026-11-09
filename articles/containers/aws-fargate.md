---
id: kt:aws-fargate
type: Service
title: AWS Fargate
aliases: [Fargate, Amazon ECS on Fargate, EKS on Fargate]
domain: containers
summary: >-
  AWS's serverless compute engine for containers: it runs Amazon ECS tasks and Amazon EKS pods
  in their own isolated environments, sized and billed by requested vCPU and memory, so you never
  provision or patch the underlying servers.
verified: 2026-11-09
status: current
broader: [kt:aws-container-services, kt:aws-serverless-services]
related: [kt:amazon-ecs, kt:amazon-eks]
relations:
  - {predicate: integratesWith, object: kt:amazon-ecs}
  - {predicate: integratesWith, object: kt:amazon-eks}
  - {predicate: alternativeTo, object: kt:amazon-ec2}
  - {predicate: pricedBy, object: kt:savings-plans}
  - {predicate: governedBy, object: kt:fargate-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "Architect for AWS Fargate for Amazon ECS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.md
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "Amazon ECS task definition differences for Fargate"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/fargate-tasks-services.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "AWS Fargate Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/fargate/pricing/
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Simplify compute management with AWS Fargate - Amazon EKS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/fargate.html
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "Amazon ECS endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/ecs-service.html
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "Amazon ECS with AWS Fargate now supports 32vCPU compute configurations"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-ecs-fargate-32vcpu/
    type: official-blog
    accessed: 2026-11-09
  - id: s7
    title: "Amazon ECS now supports tmpfs mounts on AWS Fargate and ECS Managed Instances"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/01/amazon-ecs-tmpfs-mounts-aws-fargate-managed-instances
    type: official-blog
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS Fargate

AWS Fargate runs containers without servers to manage: you don't provision, configure or scale clusters of EC2 instances, choose instance types or worry about bin-packing. You package the application as a container image, state its CPU and memory, attach networking and IAM settings, and launch it.[^s1] Fargate is not an orchestrator of its own. It is a compute option used by Amazon ECS (tasks) and Amazon EKS (pods).[^s1][^s4]

## How it works

Each Fargate task or pod gets its own isolation boundary and shares no kernel, CPU, memory or elastic network interface with any other task. On ECS you mark a task definition as Fargate-compatible with `requiresCompatibilities: FARGATE`.[^s1][^s4] The runtime underneath is a **platform version**, a pairing of kernel and container runtime. Linux uses platform versions 1.3.0 and 1.4.0, and Windows Server versions are offered as well. A new task always starts on the newest revision of its platform version. When a revision has a security problem, AWS publishes a patched one and retires tasks that are still on the old revision, so long-running services need to tolerate task replacement.[^s1]

On ECS, CPU and memory are set at task level from fixed combinations. These run from 0.25 vCPU with 512 MiB up to 16 vCPU with 32–120 GB. Since June 2026 Linux tasks can also use 32 vCPU with 60, 120 or 244 GiB, on x86 or ARM, in all commercial and AWS GovCloud (US) Regions. Sizes of 8 vCPU and above need Linux platform version 1.4.0 or later.[^s2][^s6] Linux tasks can run on X86_64 or ARM64. Windows tasks (Windows Server 2019 and 2022, Full and Core) need X86_64, at least 1 vCPU and one of the 1, 2 or 4 vCPU sizes.[^s2]

Every Fargate task uses the `awsvpc` network mode, which gives it its own elastic network interface, subnets and security groups. To pull images, a task in a public subnet needs a public IP, and a task in a private subnet needs a NAT gateway or an ECR interface VPC endpoint.[^s2] Load-balancer target groups have to use the `ip` target type, since a task has no EC2 instance to register.[^s1] For storage, tasks can use bind mounts on ephemeral storage, Amazon EFS volumes and Amazon EBS volumes.[^s2] Linux tasks gained memory-backed tmpfs mounts in January 2026.[^s7]

## Quotas, pricing and gotchas

Pricing (US East (N. Virginia), as of 2026-11): Linux/x86 costs $0.000011244 per vCPU-second and $0.000001235 per GB-second of memory. Linux/ARM costs $0.0000089944 and $0.0000009889. Windows costs $0.0000254167 per vCPU-second plus an OS license fee of $0.0000127778 per vCPU-second. Each task gets 20 GB of ephemeral storage, and anything above that is billed. Linux is billed per second with a one-minute minimum, Windows with a five-minute minimum, measured from the start of the image download until the task ends. **Fargate Spot**, available only on Linux, is up to 70% cheaper, and Compute Savings Plans cover Fargate on both ECS and EKS.[^s3] Spot tasks can be reclaimed with a two-minute warning, so use them only for work that tolerates interruption.[^s1]

Default quotas per Region (as of 2026-11): 6 vCPUs each for Fargate On-Demand and Fargate Spot. Launch rates are 100 burst and 20 sustained in most Regions, and 25 and 5 in a set of newer Regions. All of these are adjustable, and new accounts may start lower and be raised automatically as usage grows. A single ECS service can launch up to 500 Fargate tasks per minute in most Regions, and that limit is fixed.[^s5]

Gotchas on ECS: `privileged`, `gpu`, `links`, `ipcMode`, placement constraints and custom DNS servers are all invalid in Fargate task definitions. The only Linux capability you can add is `CAP_SYS_PTRACE`.[^s2] On EKS the list is longer. Pods are only scheduled if they match a Fargate profile, run in private subnets, and can't use DaemonSets, privileged containers, GPUs, Arm, EBS volumes, Fargate Spot or the EC2 instance metadata service. Completed Job pods keep billing until they are cleaned up.[^s4]

## When to use it (and when not)

Choose Fargate when per-task isolation and no host maintenance matter more than control over the host, and when paying per second for each task's requested vCPU and memory beats keeping a cluster of instances warm.[^s1][^s3] Choose EC2-backed capacity when you need GPUs, privileged containers or host-level agents, or when dense bin-packing on Reserved or Spot instances costs less than paying per task. On EKS, also choose EC2 when you need DaemonSets or EBS-backed pods.[^s2][^s4] Fargate's 32 vCPU size is aimed at compute-heavy work such as data processing and AI inference that used to require EC2.[^s6]

## Connections

- **Broader:** AWS container services (`kt:aws-container-services`) and AWS serverless services (`kt:aws-serverless-services`). Fargate is the serverless way to run ECS tasks and EKS pods.[^s1][^s4]
- **Integrates with Amazon ECS** (`kt:amazon-ecs`) through the FARGATE and FARGATE_SPOT capacity providers, and with **Amazon EKS** (`kt:amazon-eks`) through Fargate profiles.[^s1][^s4]
- **Alternative to Amazon EC2** (`kt:amazon-ec2`) capacity that you manage yourself.[^s1]
- **Priced by Savings Plans** (`kt:savings-plans`). Compute Savings Plans apply to Fargate usage.[^s3]
- **Governed by Fargate quotas** (`kt:fargate-quotas`) and **scoped to AWS Regions** (`kt:aws-regions`). vCPU and launch-rate quotas apply per Region.[^s5]

## Sources
[^s1]: Amazon Web Services, "Architect for AWS Fargate for Amazon ECS". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.md (accessed 2026-11-09)
[^s2]: Amazon Web Services, "Amazon ECS task definition differences for Fargate". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/fargate-tasks-services.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "AWS Fargate Pricing". https://aws.amazon.com/fargate/pricing/ (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Simplify compute management with AWS Fargate - Amazon EKS". https://docs.aws.amazon.com/eks/latest/userguide/fargate.html (accessed 2026-11-09)
[^s5]: Amazon Web Services, "Amazon ECS endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/ecs-service.html (accessed 2026-11-09)
[^s6]: Amazon Web Services, What's New with AWS, "Amazon ECS with AWS Fargate now supports 32vCPU compute configurations" (2026-06-05). https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-ecs-fargate-32vcpu/ (accessed 2026-11-09)
[^s7]: Amazon Web Services, What's New with AWS, "Amazon ECS now supports tmpfs mounts on AWS Fargate and ECS Managed Instances" (2026-01-06). https://aws.amazon.com/about-aws/whats-new/2026/01/amazon-ecs-tmpfs-mounts-aws-fargate-managed-instances (accessed 2026-11-09)
