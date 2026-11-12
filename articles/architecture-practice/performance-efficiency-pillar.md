---
id: kt:performance-efficiency-pillar
type: Concept
title: Performance Efficiency Pillar
aliases: [AWS Well-Architected Performance Efficiency Pillar, Well-Architected performance efficiency pillar, Performance Efficiency Pillar whitepaper]
domain: architecture-practice
summary: >-
  The pillar of the AWS Well-Architected Framework concerned with using cloud resources efficiently to
  meet performance requirements and keeping that efficiency as demand and technology change, organized
  into five best practice areas and five design principles.
verified: 2026-11-12
status: concept
broader: [kt:aws-well-architected-framework]
related: [kt:reliability-pillar, kt:purpose-built-databases, kt:aws-graviton, kt:amazon-cloudfront, kt:aws-global-accelerator, kt:cost-optimization-pillar]
relations:
  - {predicate: partOf, object: kt:aws-well-architected-framework}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: dependsOn, object: kt:aws-compute-optimizer}
sources:
  - id: s1
    title: "Performance Efficiency Pillar - AWS Well-Architected Framework (introduction)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/performance-efficiency-pillar/welcome.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Design principles - Performance Efficiency Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/performance-efficiency-pillar/design-principles.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Definition (performance efficiency) - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/perf-def.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "Architecture selection - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/wellarchitected/latest/framework/perf-arch.html
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "PERF 2. How do you select and use compute resources in your workload?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/perf-02.html
    type: official-docs
    accessed: 2026-11-12
  - id: s6
    title: "PERF02-BP01 Select the best compute options for your workload - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/perf_compute_hardware_select_best_compute_options.html
    type: official-docs
    accessed: 2026-11-12
  - id: s7
    title: "Networking and content delivery - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/wellarchitected/latest/framework/perf-networking.html
    type: official-docs
    accessed: 2026-11-12
  - id: s8
    title: "PERF04-BP06 Choose your workload's location based on network requirements - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/perf_networking_choose_workload_location_network_requirements.html
    type: official-docs
    accessed: 2026-11-12
  - id: s9
    title: "PERF 5. How do your organizational practices and culture contribute to performance efficiency in your workload?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/perf-05.html
    type: official-docs
    accessed: 2026-11-12
created: 2026-11-12
---

# Performance Efficiency Pillar

The Performance Efficiency Pillar is one of the six pillars of the AWS Well-Architected Framework, next to operational excellence, security, reliability, cost optimization and sustainability. Its whitepaper aims to help teams build AWS architectures that deliver sustained performance over time, and is written for CTOs, architects, developers and operations staff. The current whitepaper is dated November 6, 2024 (as of 2026-11).[^s1] The framework defines the pillar as the ability to use cloud resources efficiently to meet performance requirements and to stay efficient as demand changes and technologies evolve.[^s3]

## Design principles

The pillar lists five design principles.[^s2]

- **Democratize advanced technologies:** consume specialized technology such as NoSQL databases, media transcoding or machine learning as a managed service, so the team builds product instead of operating infrastructure.[^s2]
- **Go global in minutes:** deploy to several AWS Regions to cut latency for users worldwide at low cost.[^s2]
- **Use serverless architectures:** remove server management for common compute tasks; managed services operating at cloud scale can also lower transaction costs.[^s2]
- **Experiment more often:** because resources are virtual and automatable, comparative tests across instance types, storage or configurations are cheap to run.[^s2]
- **Consider mechanical sympathy:** pick the technology that fits the goal, for example by matching a database or storage choice to the workload's data access patterns.[^s2]

## How it works: five best practice areas

The pillar groups its guidance into architecture selection, compute and hardware, data management, networking and content delivery, and process and culture.[^s3] The framework asks you to take a data-driven approach, gathering data from high-level design down to resource configuration, to review choices regularly as AWS evolves, to monitor for deviation from expected performance, and to make explicit trade-offs such as compression, caching or relaxed consistency.[^s3]

- **Architecture selection (PERF 1)** asks how you select cloud resources and architecture patterns; AWS notes that well-architected systems usually combine several approaches rather than relying on one.[^s4]
- **Compute and hardware (PERF 2)** covers choosing compute options, understanding configuration features, collecting compute metrics, right-sizing, dynamic scaling and hardware accelerators, across six best practices.[^s5] PERF02-BP01, rated high risk if not established, compares EC2 instances, ECS/EKS containers, Lambda functions, AWS Batch and Lightsail by use case, and says one workload can mix several compute types.[^s6]
- **Networking and content delivery (PERF 4)** points to features such as enhanced networking, S3 Transfer Acceleration, CloudFront, Route 53 latency routing, VPC endpoints, Direct Connect and Global Accelerator, and frames the choice around latency, throughput, jitter and bandwidth.[^s7]
- **Process and culture (PERF 5)** has seven best practices: set KPIs, use monitoring to find where performance matters most, define an improvement process, load test, automate remediation, keep services up to date and review metrics regularly.[^s9]

## Numbers and gotchas

Placement guidance is concrete. PERF04-BP06 (medium risk) warns against two anti-patterns: putting all resources in one geography, and choosing the Region closest to your own office instead of your users. It notes that cluster placement groups with supported instances and the Elastic Network Adapter give a low-jitter network of up to 25 Gbps between instances, and that AWS claims Global Accelerator can improve performance by up to 60% for TCP/UDP traffic (figures as of 2026-11).[^s8] It also places Local Zones, Wavelength and Outposts as options when users or data sit outside a Region.[^s8]

On compute, the anti-patterns are carrying the on-premises choice over without re-evaluation, not knowing what AWS offers, and over-provisioning one option when a different one would fit better. The suggested loop is to benchmark in non-production, then keep optimizing with CloudWatch metrics and AWS Compute Optimizer recommendations.[^s6]

## When to use it

Use the pillar when choosing between compute, data and network options for a new design, when a workload misses latency or throughput targets, or in a Well-Architected review. It overlaps with cost optimization, since over-provisioning wastes money as well as performance, and with reliability, since dynamic scaling and load testing serve both.[^s5][^s6][^s9]

## Connections

The pillar is **part of** the AWS Well-Architected Framework.[^s1] It is **monitored by** Amazon CloudWatch and **depends on** AWS Compute Optimizer for the continuous right-sizing loop in PERF02-BP01.[^s6] It is **related** to purpose-built databases through the mechanical-sympathy principle and the data management area,[^s2][^s3] to AWS Graviton as one of the instance and processor choices in compute and hardware,[^s5][^s6] and to Amazon CloudFront and AWS Global Accelerator as the main edge tools in PERF 4.[^s7][^s8] It sits beside the reliability and cost optimization pillars, which share its scaling and right-sizing practices.[^s1][^s6]

## Sources
[^s1]: Amazon Web Services, "Performance Efficiency Pillar - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/performance-efficiency-pillar/welcome.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Design principles - Performance Efficiency Pillar". https://docs.aws.amazon.com/wellarchitected/latest/performance-efficiency-pillar/design-principles.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "Definition - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/perf-def.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, "Architecture selection - AWS Well-Architected Framework". https://docs.aws.amazon.com/en_us/wellarchitected/latest/framework/perf-arch.html (accessed 2026-11-12)
[^s5]: Amazon Web Services, "PERF 2. How do you select and use compute resources in your workload?". https://docs.aws.amazon.com/wellarchitected/latest/framework/perf-02.html (accessed 2026-11-12)
[^s6]: Amazon Web Services, "PERF02-BP01 Select the best compute options for your workload". https://docs.aws.amazon.com/wellarchitected/latest/framework/perf_compute_hardware_select_best_compute_options.html (accessed 2026-11-12)
[^s7]: Amazon Web Services, "Networking and content delivery - AWS Well-Architected Framework". https://docs.aws.amazon.com/en_us/wellarchitected/latest/framework/perf-networking.html (accessed 2026-11-12)
[^s8]: Amazon Web Services, "PERF04-BP06 Choose your workload's location based on network requirements". https://docs.aws.amazon.com/wellarchitected/latest/framework/perf_networking_choose_workload_location_network_requirements.html (accessed 2026-11-12)
[^s9]: Amazon Web Services, "PERF 5. How do your organizational practices and culture contribute to performance efficiency in your workload?". https://docs.aws.amazon.com/wellarchitected/latest/framework/perf-05.html (accessed 2026-11-12)
