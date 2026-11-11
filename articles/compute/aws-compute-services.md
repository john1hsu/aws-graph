---
id: kt:aws-compute-services
type: Concept
title: AWS compute services
aliases: [AWS compute, compute on AWS, AWS compute portfolio, AWS compute category]
domain: compute
summary: >-
  The family of AWS services that run your code: virtual machine instances (Amazon EC2 and
  Lightsail), containers (ECS, EKS, Fargate), serverless functions (Lambda), batch processing
  (AWS Batch) and hybrid/edge compute (Outposts, Local Zones, Wavelength), plus the scaling,
  load-balancing and pricing tools that sit around them.
verified: 2026-11-11
status: concept
broader: [kt:amazon-web-services]
related: [kt:aws-container-services, kt:aws-serverless-services, kt:elastic-load-balancing, kt:amazon-ec2-auto-scaling]
relations:
  - {predicate: hasPart, object: kt:amazon-ec2}
  - {predicate: hasPart, object: kt:aws-lambda}
  - {predicate: hasPart, object: kt:aws-fargate}
  - {predicate: hasPart, object: kt:aws-batch}
  - {predicate: pricedBy, object: kt:savings-plans}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Compute - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/compute-services.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Choosing an AWS compute service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/compute-on-aws-how-to-choose/choosing-aws-compute-service.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "PERF02-BP01 Select the best compute options for your workload - Performance Efficiency Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/performance-efficiency-pillar/perf_compute_hardware_select_best_compute_options.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "AWS Fargate or AWS Lambda?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/fargate-or-lambda/fargate-or-lambda.html
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "AWS App Runner availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ja_jp/apprunner/latest/dg/apprunner-availability-change.html
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# AWS compute services

"Compute" is the AWS service category for running application code. The AWS overview whitepaper groups more than a dozen offerings under it, from Amazon EC2 and EC2 Auto Scaling to Lightsail, Elastic Beanstalk, AWS Batch, Fargate, Lambda, Outposts, Wavelength and VMware Cloud on AWS. Its comparison table also pulls in neighbours such as ECS, EKS, ECR, Local Zones, Spot Instances, Savings Plans, Compute Optimizer and Elastic Load Balancing.[^s1] There is no single "compute service" to provision; this node is the map that ties those services together.

## How the portfolio is organized

AWS's compute decision guide (last updated February 2025) sorts the portfolio into categories by what each one is optimized for:[^s2]

- **Instances** – Amazon EC2 virtual servers, with EC2 Auto Scaling, EC2 Image Builder, Elastic Beanstalk and Lightsail layered on top for scaling, image pipelines, managed web-app deployment and simple fixed-price hosting.
- **Containers** – Amazon ECS and EKS (and their on-premises variants ECS Anywhere and EKS Anywhere), Amazon ECR for images, and AWS Batch for queued jobs.
- **Serverless** – AWS Lambda for functions and AWS Fargate for containers without servers to manage.
- **Hybrid and edge** – Local Zones, Dedicated Local Zones, Outposts and Wavelength for low latency or local data processing.
- **Cost and traffic** – Savings Plans, Spot Instances and Compute Optimizer for price, and Application, Network and Gateway Load Balancers for availability.[^s2]

The Well-Architected Performance Efficiency pillar uses a simpler three-way split (instances, containers and functions), with AWS Batch for batch and HPC work and Lightsail for small, preconfigured sites as special cases.[^s3]

## Choosing among them

The decision guide lists the criteria that drive the choice: workload type, CPU/GPU/memory/I/O needs, vertical versus horizontal scaling, how much patching and operations you want to own, cost, latency, compliance, integrations and reliability.[^s2] The trade-off is mostly control versus operational burden. EC2 gives the widest choice of instance families, processors, accelerators and dedicated-hardware or licensing options, which suits lift-and-shift and enterprise apps. Containers suit microservices that need consistent environments. Lambda runs event-driven code while AWS manages the underlying capacity.[^s3] PERF02-BP01 rates choosing the wrong option as high risk. It warns against copying the on-premises choice by default or over-provisioning one option when a different one fits better, and it notes that one workload can mix several compute types.[^s3]

A common boundary question is Lambda versus Fargate. As of 2026-11, AWS's guide gives Lambda a 15-minute limit per invocation, 128 MB–10 GB of memory and up to 6 vCPU. Fargate has no hard run-time limit and offers 0.25–32 vCPU and up to 244 GiB of memory.[^s4] Lambda bills per request and GB-second, while Fargate bills per second for vCPU and memory with a one-minute minimum. Lambda's default concurrency is 1,000 executions per account per Region, and that quota is adjustable.[^s4] As a rule of thumb, bursty, event-driven or low-volume work favours Lambda, and long-running containers or steady, high-throughput services favour Fargate.[^s4]

## Pricing levers and gotchas

Cost is decided as much by the purchase model as by the service. AWS cites up to 72% savings with Savings Plans and up to 90% with Spot Instances. It also cites up to 40% better price-performance from Graviton-based instances and up to 25% savings from right-sizing with Compute Optimizer (figures as stated by AWS, as of 2026-11).[^s2] Compute Savings Plans also apply to Fargate and Lambda, with up to 66% off.[^s4] Part of the portfolio is also changing: AWS App Runner is no longer open to new customers and will get no new features. AWS points new container web apps to Amazon ECS Express Mode, which provisions a Fargate service, an Application Load Balancer and auto scaling from a single API call.[^s5] Existing App Runner customers can keep creating services.[^s5]

## Connections

This node is the compute branch of `kt:amazon-web-services` and the parent of the major compute services. Its parts include `kt:amazon-ec2`, `kt:aws-lambda`, `kt:aws-fargate` and `kt:aws-batch`, and it shares those last two with the container and serverless overviews (`kt:aws-container-services`, `kt:aws-serverless-services`).[^s1][^s2] Elastic Load Balancing and EC2 Auto Scaling are related nodes because the decision guide treats load balancing and elasticity as part of the compute toolkit.[^s2] It is `pricedBy` `kt:savings-plans`, the cross-service discount for EC2, Fargate and Lambda.[^s2][^s4] The Cloud Practitioner exam (CLF-C02) Task 3.3 asks candidates to identify AWS compute services. That covers EC2 instance types, container options (ECS, EKS), serverless options (Fargate, Lambda), auto scaling for elasticity and the purpose of load balancers, which is the basis for the `coveredBy` edge.[^s6]

## Sources
[^s1]: Amazon Web Services, "Compute - Overview of Amazon Web Services". https://docs.aws.amazon.com/whitepapers/latest/aws-overview/compute-services.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Choosing an AWS compute service". https://docs.aws.amazon.com/decision-guides/latest/compute-on-aws-how-to-choose/choosing-aws-compute-service.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "PERF02-BP01 Select the best compute options for your workload". https://docs.aws.amazon.com/wellarchitected/latest/performance-efficiency-pillar/perf_compute_hardware_select_best_compute_options.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "AWS Fargate or AWS Lambda?". https://docs.aws.amazon.com/decision-guides/latest/fargate-or-lambda/fargate-or-lambda.html (accessed 2026-11-11)
[^s5]: Amazon Web Services, "AWS App Runner availability change". https://docs.aws.amazon.com/ja_jp/apprunner/latest/dg/apprunner-availability-change.html (accessed 2026-11-11)
[^s6]: Amazon Web Services, "Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html (accessed 2026-11-11)
