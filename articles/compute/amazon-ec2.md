---
id: kt:amazon-ec2
type: Service
title: Amazon EC2
aliases: [Amazon Elastic Compute Cloud, EC2, AWS EC2]
domain: compute
summary: >-
  AWS's core infrastructure-as-a-service offering for launching and managing virtual servers
  (instances) with a chosen instance type, machine image, storage and network configuration,
  billed per second or discounted through commitments and spare-capacity pricing.
verified: 2026-11-08
status: current
broader: [kt:aws-compute-services]
related: [kt:ec2-instance-types, kt:amazon-ec2-auto-scaling]
relations:
  - {predicate: pricedBy, object: kt:savings-plans}
  - {predicate: pricedBy, object: kt:spot-instances}
  - {predicate: governedBy, object: kt:ec2-instance-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: alternativeTo, object: kt:aws-lambda}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is Amazon EC2? - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html
    type: official-docs
    accessed: 2026-11-08
  - id: s2
    title: "Amazon EC2 billing and purchasing options"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-purchasing-options.html
    type: official-docs
    accessed: 2026-11-08
  - id: s3
    title: "Amazon EC2 Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ec2/pricing/
    type: official-docs
    accessed: 2026-11-08
  - id: s4
    title: "Amazon EC2 instance type quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ec2/latest/instancetypes/ec2-instance-quotas.html
    type: official-docs
    accessed: 2026-11-08
  - id: s5
    title: "Amazon EC2 instance types - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html
    type: official-docs
    accessed: 2026-11-08
  - id: s6
    title: "Amazon EC2 FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ec2/faqs/
    type: official-docs
    accessed: 2026-11-08
  - id: s7
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-08
  - id: s8
    title: "Top announcements of AWS re:Invent 2025"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/top-announcements-of-aws-reinvent-2025/
    type: official-blog
    accessed: 2026-11-08
created: 2026-11-08
---

# Amazon EC2

Amazon Elastic Compute Cloud (EC2) gives you on-demand, scalable compute capacity in the AWS Cloud: you launch as many or as few virtual servers as you need, configure their networking and security, attach storage, and scale back down when demand drops.[^s1] It is the foundation many higher-level AWS services build on, and the service most people mean when they talk about "servers in the cloud".

## How it works

An **instance** is a virtual server. You launch it from an **Amazon Machine Image (AMI)**, a template holding the operating system and any preinstalled software, and pick an **instance type**, which fixes its CPU, memory, storage, networking and graphics capacity.[^s1] A **key pair** provides login credentials (AWS keeps the public key, you keep the private key), and **security groups** act as virtual firewalls that allow specific protocols, ports and address ranges in and out.[^s1]

Storage comes in two forms. **Amazon EBS volumes** are persistent network block storage, so an EBS-backed instance can be stopped and started again without losing its root data. **Instance store** volumes are local and temporary: their data is lost when the instance is stopped, hibernated or terminated.[^s1][^s6]

Instance type names encode family, generation, processor and size; in `m7g.large`, for example, `m` is the family, `7` the generation, `g` marks an AWS Graviton processor and `large` the size. Families are grouped as general purpose (M, T), compute optimized (C), memory optimized (R, X, U), storage optimized (D, I), accelerated computing (P, G, Inf, Trn and others) and high-performance computing (Hpc).[^s5] Most current-generation types run on the **AWS Nitro System**, while older Xen-based families such as M4, C4 and T2 are listed as previous generation; T-series burstable instances provide a CPU baseline that can burst when needed.[^s5][^s6]

You can manage EC2 through the console, AWS CLI, CloudFormation templates, the AWS SDKs, Tools for PowerShell or the Query API.[^s1] AWS states that instances typically begin booting within 10 minutes of a RunInstances call.[^s6]

## Quotas, pricing and gotchas

**Purchasing options** (as of 2026-11): On-Demand (no commitment), Savings Plans (a USD-per-hour spend commitment for 1 or 3 years), Reserved Instances (a specific instance configuration in a Region for 1 or 3 years), Spot Instances (spare capacity at a steep discount for interruption-tolerant work), Dedicated Hosts, Dedicated Instances, On-Demand Capacity Reservations in a chosen Availability Zone, and Capacity Blocks for reserving GPU clusters.[^s2][^s3] AWS's pricing page cites savings of up to 72% with Savings Plans and up to 90% with Spot, both compared with On-Demand.[^s3]

**Per-second billing** meters usage in one-second increments with a 60-second minimum per instance. It applies across all purchase options, Regions and Availability Zones for Amazon Linux, Windows, Red Hat Enterprise Linux, Ubuntu and Ubuntu Pro, and also to provisioned EBS storage.[^s3] Remember incidental costs such as EBS snapshots when estimating; AWS points to the Pricing Calculator, Cost Explorer and Trusted Advisor for this.[^s1]

**Quotas** are per Region and measured in vCPUs, not instance counts. As of 2026-11, a new account's default for "Running On-Demand Standard (A, C, D, H, I, M, R, T, Z) instances" is 5 vCPUs, and the default for GPU, accelerator, HPC, high-memory and X families is 0; Spot quotas mirror this. All are adjustable, and EC2 also raises them automatically based on usage.[^s4] A common gotcha is a launch failing for a P or G instance simply because its quota is still 0. Dedicated Host and Capacity Block quotas also default to 0.[^s4]

The EC2 SLA commits to a Monthly Uptime Percentage of at least 99.99% for EC2 and EBS within a Region, which in practice means designing across multiple Availability Zones.[^s6]

## When to use it (and when not)

Choose EC2 when you need control over the operating system, long-running processes, specific hardware (GPUs, high memory, local NVMe) or software licensing tied to physical servers.[^s1][^s2] AWS lists Amazon Lightsail for simple, fixed-price websites, and Amazon ECS or EKS for containers, as alternatives; fully serverless options such as AWS Lambda trade that control for AWS-managed infrastructure (Lambda Managed Instances even run functions on EC2 capacity that AWS manages).[^s1][^s8] EC2 is often paired with EC2 Auto Scaling, Elastic Load Balancing, AWS Backup, Systems Manager and GuardDuty.[^s1]

## What changed (2025-12)

At re:Invent 2025 (30 November to 4 December 2025) AWS announced Graviton5 processors for EC2, Trn3 UltraServers on its 3 nm Trainium3 chip, memory-optimized X8aedz instances, and Lambda Managed Instances, which run Lambda functions on EC2 capacity.[^s8]

## Connections

- **Broader: AWS compute services** (`kt:aws-compute-services`); EC2 sits alongside Lightsail, Batch and Elastic Beanstalk in AWS's compute category.[^s7]
- **Related to EC2 instance types** (`kt:ec2-instance-types`) and **EC2 Auto Scaling** (`kt:amazon-ec2-auto-scaling`), which keeps the right number of instances running.[^s5][^s1]
- **Priced by Savings Plans** (`kt:savings-plans`) and **Spot Instances** (`kt:spot-instances`) as alternatives to On-Demand rates.[^s2][^s3]
- **Governed by EC2 instance quotas** (`kt:ec2-instance-quotas`), which are **scoped to AWS Regions** (`kt:aws-regions`).[^s4]
- **Alternative to AWS Lambda** (`kt:aws-lambda`) for workloads that need server-level control.[^s1][^s8]
- **Monitored by Amazon CloudWatch** (`kt:amazon-cloudwatch`), which tracks instances and EBS volumes.[^s1]
- **Covered by the AWS Certified Cloud Practitioner** exam (`kt:aws-certified-cloud-practitioner`), whose CLF-C02 guide lists Amazon EC2 under Compute.[^s7]

## Sources
[^s1]: Amazon Web Services, "What is Amazon EC2?". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html (accessed 2026-11-08)
[^s2]: Amazon Web Services, "Amazon EC2 billing and purchasing options". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-purchasing-options.html (accessed 2026-11-08)
[^s3]: Amazon Web Services, "Amazon EC2 Pricing". https://aws.amazon.com/ec2/pricing/ (accessed 2026-11-08)
[^s4]: Amazon Web Services, "Amazon EC2 instance type quotas". https://docs.aws.amazon.com/ec2/latest/instancetypes/ec2-instance-quotas.html (accessed 2026-11-08)
[^s5]: Amazon Web Services, "Amazon EC2 instance types". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html (accessed 2026-11-08)
[^s6]: Amazon Web Services, "Amazon EC2 FAQs". https://aws.amazon.com/ec2/faqs/ (accessed 2026-11-08)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html (accessed 2026-11-08)
[^s8]: Amazon Web Services, AWS News Blog, "Top announcements of AWS re:Invent 2025". https://aws.amazon.com/blogs/aws/top-announcements-of-aws-reinvent-2025/ (accessed 2026-11-08)
