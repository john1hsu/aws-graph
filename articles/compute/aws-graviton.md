---
id: kt:aws-graviton
type: Feature
title: AWS Graviton
aliases: [Graviton, AWS Graviton processors, Graviton2, Graviton3, Graviton4, Graviton5]
domain: compute
summary: >-
  AWS's own family of Arm64 server processors, used in Amazon EC2 instance families marked with a
  "g" (such as M8g, C9g and T4g) and behind arm64 Lambda functions, aimed at better price
  performance than comparable x86 instances for Linux workloads.
verified: 2026-11-11
status: current
broader: [kt:amazon-ec2, kt:aws-compute-services]
related: [kt:ec2-instance-types, kt:aws-lambda, kt:amazon-ecs, kt:amazon-eks, kt:aws-nitro-system]
relations:
  - {predicate: pricedBy, object: kt:savings-plans}
  - {predicate: pricedBy, object: kt:spot-instances}
  - {predicate: dependsOn, object: kt:aws-nitro-system}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "AWS Graviton Technical Guide"
    publisher: Amazon Web Services
    url: https://aws.github.io/graviton/
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Getting started with AWS Graviton"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ec2/graviton/getting-started
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Now available: Amazon EC2 M9g and M9gd instances powered by new AWS Graviton5 processors"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/now-available-amazon-ec2-m9g-and-m9gd-instances-powered-by-new-aws-graviton5-processors/
    type: official-blog
    accessed: 2026-11-11
  - id: s4
    title: "Amazon EC2 C9g and C9gd compute optimized instances are now available"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/ec2-c9g-c9gd-instances-graviton5-processors-available/
    type: official-blog
    accessed: 2026-11-11
  - id: s5
    title: "Selecting and configuring an instruction set architecture for your Lambda function"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lambda/latest/dg/foundation-arch.html
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "Use Graviton instances and containers - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/optimize-costs-microsoft-workloads/net-graviton.html
    type: official-docs
    accessed: 2026-11-11
created: 2026-11-11
---

# AWS Graviton

AWS Graviton is a family of server processors that AWS designs itself for Amazon EC2, built on the Arm64 architecture.[^s2] AWS markets it on price performance, claiming up to 40% better price performance than comparable x86-based instances across a wide range of workloads.[^s2] Graviton is not a service you call: you get it by choosing a Graviton-based instance type, an arm64 Lambda function, or a container image built for arm64.[^s2][^s5] The best candidates run on Linux or BSD and are built from open-source components or source code you control, so they can be recompiled for Arm.[^s2]

## Generations

AWS's Graviton Technical Guide tracks four generations in current use.[^s1] Graviton2 (Arm Neoverse-N1, 64 cores) powers families such as M6g, C6g, R6g and T4g; Graviton3 (Neoverse-V1, 64 cores) powers C7g, M7g, R7g and Hpc7g; Graviton4 (Neoverse-V2, 96 cores per socket) powers C8g, M8g, R8g, X8g and I8g; and Graviton5 (Neoverse-V3, 192 cores at 3.3 GHz) powers the ninth-generation families.[^s1] In EC2 instance names a "g" after the generation number marks Graviton, and a "d" adds local NVMe storage.[^s3]

Graviton5 reached general availability with M9g and M9gd general-purpose instances in June 2026.[^s3] AWS states up to 25% better compute performance than Graviton4, a five-times-larger L3 cache, DDR5-8800 memory and PCIe Gen6, with up to 192 vCPUs and 768 GiB of memory on the largest size.[^s3] The instances run on the sixth-generation Nitro System, whose Nitro Isolation Engine mediates access to virtual machine memory, CPU state and devices.[^s3] Compute-optimized C9g and C9gd followed on 30 June 2026.[^s4] At launch both were offered in US East (N. Virginia), US East (Ohio), US West (Oregon) and Europe (Frankfurt), so check Regional availability before planning a migration.[^s3][^s4]

## How it works for your workloads

Because Graviton uses a different instruction set from x86, binaries must be built for arm64, and every dependency needs an Arm build.[^s6] The technical guide has per-language guides (C/C++, Go, Java, Python, Rust, Node.js, .NET and others) and recommends recent runtimes such as .NET 8+, PHP 8.2+ and PostgreSQL 15+, which include Arm64 improvements.[^s1] For containers, build multi-architecture images so one tag runs on both x86 and Arm; Docker, Kubernetes, Amazon ECS and Amazon EKS all run on Graviton and Amazon ECR stores multi-arch images.[^s1][^s6] On AWS Lambda you set the function's architecture to arm64; Lambda does not guarantee a particular Graviton generation, so code should target generic AArch64.[^s5]

A Graviton vCPU is a full physical core rather than a hyperthread, which is one reason AWS gives for its efficiency.[^s6]

## Pricing and gotchas

Graviton instances use the normal EC2 purchase options; the ninth-generation families launched with On-Demand, Savings Plans, Spot, Dedicated Instances and Dedicated Hosts.[^s3][^s4] As an example of the price gap, AWS Prescriptive Guidance compares a t4g.xlarge at $0.1344 per hour with a t3.xlarge at $0.1664, about 19% less (On-Demand, figures as published in that guide; check current Regional pricing).[^s6] As of 2026-11, T4g (Graviton2) instances are free for up to 750 hours a month through 31 December 2026.[^s2] The technical guide states arm64 Lambda duration charges are 20% lower than x86.[^s1]

Gotchas: Graviton EC2 instances run Linux, not Windows, and .NET Framework applications must be ported to modern .NET first.[^s6] Graviton4 and Graviton5 do not run 32-bit code, and no generation supports turbo frequencies.[^s1] Some distribution packages are not built with Arm-specific optimizations; the guide flags PostgreSQL builds without LSE support, old pip versions, and stock zlib.[^s1] On Lambda, every layer and extension must also be arm64-compatible.[^s5]

## When to use it (and when not)

Graviton suits Linux workloads you can rebuild for Arm: AWS reports the largest Graviton5 gains for web applications, ML inference and databases, and recommends arm64 Lambda for compute-heavy work such as video encoding and simulation.[^s3][^s5] AWS recommends benchmarking before and after, and staging Lambda rollouts with alias traffic splitting.[^s5][^s6] Stay on x86 for Windows workloads, closed-source software without Arm builds, or code that relies on x86-specific instructions.[^s6][^s1]

## Connections

Graviton sits under **Amazon EC2** and the broader AWS compute services as a processor choice within **EC2 instance types**, and it is the hardware behind arm64 **AWS Lambda** functions.[^s1][^s5] **Amazon ECS** and **Amazon EKS** run Graviton capacity alongside x86.[^s1] Graviton5 instances **depend on** the **AWS Nitro System** for virtualization and isolation.[^s3] They are **priced by** the usual discount models, **Savings Plans** and **Spot Instances**, and each instance family's availability is **scoped to** individual **AWS Regions**.[^s3][^s4]

## Sources
[^s1]: Amazon Web Services, "AWS Graviton Technical Guide". https://aws.github.io/graviton/ (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Getting started with AWS Graviton". https://aws.amazon.com/ec2/graviton/getting-started (accessed 2026-11-11)
[^s3]: Amazon Web Services, AWS News Blog, "Now available: Amazon EC2 M9g and M9gd instances powered by new AWS Graviton5 processors" (2026-06-10). https://aws.amazon.com/blogs/aws/now-available-amazon-ec2-m9g-and-m9gd-instances-powered-by-new-aws-graviton5-processors/ (accessed 2026-11-11)
[^s4]: Amazon Web Services, What's New with AWS, "Amazon EC2 C9g and C9gd compute optimized instances are now available" (2026-06-30). https://aws.amazon.com/about-aws/whats-new/2026/06/ec2-c9g-c9gd-instances-graviton5-processors-available/ (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Selecting and configuring an instruction set architecture for your Lambda function". https://docs.aws.amazon.com/lambda/latest/dg/foundation-arch.html (accessed 2026-11-11)
[^s6]: Amazon Web Services, "Use Graviton instances and containers", AWS Prescriptive Guidance. https://docs.aws.amazon.com/prescriptive-guidance/latest/optimize-costs-microsoft-workloads/net-graviton.html (accessed 2026-11-11)
