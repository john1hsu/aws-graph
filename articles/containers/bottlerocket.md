---
id: kt:bottlerocket
type: Tool
title: Bottlerocket
aliases: [Bottlerocket OS, AWS Bottlerocket]
domain: containers
summary: >-
  An open source, AWS-sponsored and AWS-supported Linux distribution built only to host containers,
  with a minimal immutable image, API-driven configuration and single-step, rollback-capable updates;
  used for Amazon EKS and Amazon ECS worker nodes on EC2.
verified: 2026-11-17
status: current
broader: [kt:aws-container-services]
related: [kt:amazon-machine-images, kt:eks-auto-mode, kt:ecs-managed-instances, kt:karpenter]
relations:
  - {predicate: usedBy, object: kt:amazon-eks}
  - {predicate: usedBy, object: kt:amazon-ecs}
  - {predicate: runsOn, object: kt:amazon-ec2}
  - {predicate: configuredVia, object: kt:aws-systems-manager}
  - {predicate: monitoredBy, object: kt:amazon-inspector}
sources:
  - id: s1
    title: "Bottlerocket FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/bottlerocket/faqs
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Create nodes with optimized Bottlerocket AMIs"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami-bottlerocket.md
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Amazon ECS-optimized Bottlerocket AMIs"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-bottlerocket.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "Bottlerocket: a special-purpose container operating system"
    publisher: Amazon Web Services (AWS Containers Blog)
    url: https://aws.amazon.com/blogs/containers/bottlerocket-a-special-purpose-container-operating-system
    type: official-blog
    accessed: 2026-11-17
created: 2026-11-17
---

# Bottlerocket

Bottlerocket is a Linux distribution that AWS sponsors and supports, designed for one job: running containers. It ships only what that job needs — the Linux kernel, a small set of system software and containerd — rather than a general-purpose userland.[^s1] It is open source, licensed under Apache 2.0 or MIT (third-party components such as the kernel keep their own licenses).[^s1] AWS publishes optimized Bottlerocket AMIs for Amazon EKS and Amazon ECS.[^s2][^s3]

## How it works

**Image-based and immutable.** There is no package manager; Bottlerocket is delivered as a prebuilt disk image. The root filesystem is read-only, and container images and volumes live in a separate writable area, so hosts don't drift from the tested image.[^s4][^s3] dm-verity checks the image's integrity at boot, so tampering such as a rootkit causes the boot to fail rather than run silently.[^s1][^s4]

**Updates as a unit.** A new release is written to a second partition; on reboot the bootloader switches to it and keeps the previous image as a one-step rollback target.[^s4] AWS describes updates as applied whole and reversible, avoiding the half-patched states a per-package update can leave behind.[^s2] By default a node updates to the latest secure version on boot, and the reboot can be coordinated by the orchestrator, which drains workloads first.[^s1] Updates come from a repository built on The Update Framework (TUF) specification, with signed metadata and wave-based rollouts.[^s4]

**API-driven configuration.** Instead of editing files in `/etc`, you change settings through an on-host API (supplied as TOML user data at launch or via the `apiclient` tool), which supports structured settings and transactions.[^s4] The API can also be called through AWS Systems Manager.[^s1]

**Host containers for access.** Bottlerocket images include no SSH server or shell.[^s2] A *control container*, on by default, runs the Systems Manager agent for commands and Session Manager shells; it must stay enabled for Amazon Inspector to scan the host.[^s2][^s1] An *admin container* — Amazon Linux-based, privileged but SELinux-constrained, with debugging tools like `strace` and `tcpdump` — is off by default and is enabled if you supply an SSH key when creating an EKS node group; AWS recommends it only for development and testing.[^s1][^s2][^s4] Host containers run on a separate containerd instance from orchestrated workloads, isolating their fault and security domains.[^s4]

**Security features.** SELinux runs in enforcing mode, alongside seccomp, cgroup and namespace isolation, and eBPF for tracing.[^s1] First-party components are written mainly in memory-safe Rust and Go.[^s4] For compliance, AWS lists a CIS Benchmark (Levels 1 and 2), FIPS 140-3 validated variants from version 1.27.0, HIPAA eligibility and PCI DSS guidance.[^s1]

## Variants, support and gotchas

Bottlerocket comes in variants tied to an orchestrator version — Kubernetes worker nodes (EC2, VMware, bare metal), ECS on EC2, and NVIDIA GPU builds for both EKS and ECS — and a variant is deprecated when its orchestrator version is.[^s1] For ECS, as of 2026-11, the `aws-ecs-2` and `aws-ecs-2-nvidia` variants use kernel 6.1 and the `aws-ecs-1` variants use kernel 5.10.[^s3] Minor releases arrive roughly every 6–8 weeks, with patch releases as severity warrants.[^s1] It supports x86_64 and arm64 EC2 instances.[^s2][^s3]

Pricing: AWS-provided builds carry no extra charge — you pay for the EC2 instances, billed per second — and are covered by AWS Support plans; Marketplace products built on Bottlerocket may add an hourly fee.[^s1] AWS lists availability in all commercial Regions, AWS GovCloud (US) and the China Regions (as of 2026-11).[^s1]

ECS limitations (as of 2026-11): Bottlerocket doesn't support ECS Anywhere, Service Connect, EFS in encrypted mode or with `awsvpc` networking, EBS volume mounts, or Elastic Inference; AWS doesn't recommend it for Inferentia instances; and some task features need minimum versions (`awsvpc` 1.1.0, `initProcessEnabled` 1.19.0).[^s3] Because there's no package manager or shell, extra host tooling has to come in as a container (such as the admin or control container) rather than as installed packages.[^s3][^s4]

## When to use it (and when not)

Choose Bottlerocket for EKS or ECS fleets where you want a smaller attack surface, faster boots, consistent hosts and hands-off patching; AWS cites lower resource use and reduced management overhead.[^s2] It is supported with EKS managed node groups, self-managed nodes and Karpenter.[^s2] Prefer Amazon Linux or another general-purpose AMI when nodes need arbitrary host software, Windows, or an ECS feature Bottlerocket lacks.[^s3] Logging in to individual Bottlerocket hosts is meant only for infrequent, advanced troubleshooting, so teams that rely on hands-on host administration will find it restrictive.[^s3]

## Connections

Bottlerocket sits within **AWS container services**, is **used by** Amazon EKS and Amazon ECS, and **runs on** Amazon EC2 as an AMI.[^s2][^s3] It is **configured via** AWS Systems Manager through its control container and **monitored by** Amazon Inspector when that container is enabled.[^s1] It also works with the Karpenter node autoscaler, and is related in this graph to ECS Managed Instances and EKS Auto Mode, AWS's managed-node options for ECS and EKS.[^s2]

## Sources

[^s1]: Amazon Web Services, "Bottlerocket FAQs". https://aws.amazon.com/bottlerocket/faqs (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Create nodes with optimized Bottlerocket AMIs", Amazon EKS User Guide. https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami-bottlerocket.md (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Amazon ECS-optimized Bottlerocket AMIs", Amazon ECS Developer Guide. https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-bottlerocket.html (accessed 2026-11-17)
[^s4]: Samuel Karp, "Bottlerocket: a special-purpose container operating system", AWS Containers Blog, 2020-03-30. https://aws.amazon.com/blogs/containers/bottlerocket-a-special-purpose-container-operating-system (accessed 2026-11-17)
