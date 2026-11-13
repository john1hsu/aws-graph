---
id: kt:aws-nitro-system
type: Feature
title: AWS Nitro System
aliases: [Nitro System, AWS Nitro, Nitro Hypervisor, Nitro Cards, Nitro Security Chip]
domain: compute
summary: >-
  The hardware and software platform underneath modern Amazon EC2 instances: dedicated Nitro Cards
  for networking, storage and management, a Nitro Security Chip, and a minimal hypervisor, designed
  so instances get nearly all host resources and AWS operators have no access to customer data.
verified: 2026-11-13
status: current
broader: [kt:amazon-ec2, kt:aws-compute-services]
related: [kt:aws-graviton, kt:ec2-instance-types, kt:aws-trainium-and-inferentia, kt:shared-responsibility-model, kt:ec2-placement-groups]
relations:
  - {predicate: enables, object: kt:aws-nitro-enclaves}
  - {predicate: integratesWith, object: kt:amazon-ebs}
  - {predicate: integratesWith, object: kt:amazon-vpc}
  - {predicate: integratesWith, object: kt:aws-kms}
sources:
  - id: s1
    title: "The components of the Nitro System - The Security Design of the AWS Nitro System"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/security-design-of-aws-nitro-system/the-components-of-the-nitro-system.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "The Nitro System journey - The Security Design of the AWS Nitro System"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/security-design-of-aws-nitro-system/the-nitro-system-journey.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "Instances built on the AWS Nitro System"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ec2/latest/instancetypes/ec2-nitro-instances.html
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "AWS Nitro System"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ec2/nitro/
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "What is Nitro Enclaves?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/enclaves/latest/user/nitro-enclave.html
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "AWS Nitro System gets independent affirmation of its confidential compute capabilities"
    publisher: Amazon Web Services (AWS Compute Blog)
    url: https://aws.amazon.com/blogs/compute/aws-nitro-system-gets-independent-affirmation-of-its-confidential-compute-capabilities
    type: official-blog
    accessed: 2026-11-13
  - id: s7
    title: "AWS Nitro Isolation Engine: Formally verifying the hypervisor in the AWS Nitro System"
    publisher: Amazon Web Services (AWS Compute Blog)
    url: https://aws.amazon.com/blogs/compute/aws-nitro-isolation-engine-formally-verifying-the-hypervisor-in-the-aws-nitro-system/
    type: official-blog
    accessed: 2026-11-13
created: 2026-11-13
---

# AWS Nitro System

The AWS Nitro System is the platform that current-generation Amazon EC2 instances run on. AWS took the jobs a traditional hypervisor does (CPU and memory partitioning, storage, networking and management) and split them apart. Most of them moved onto dedicated hardware, so the instance gets nearly all of the host's resources.[^s4] It is not a separate service: you use it by launching an instance type built on Nitro.[^s3] The C5 instance type, launched in 2017, was the first to remove the need for a Dom0 management domain entirely.[^s2]

## How it works

The system has three main components.[^s1]

- **Nitro Cards** are separate hardware with their own processors and ASICs, designed by Annapurna Labs. They handle all I/O: VPC networking, Amazon EBS and local NVMe instance storage. They connect over PCIe but stay logically isolated from the main board where customer code runs.[^s1] The primary card, the **Nitro Controller**, is the hardware root of trust and the only path between the server and the EC2, EBS and VPC control planes.[^s1] If the controller's boot-time firmware measurements don't match, the host is taken out of service.[^s1]
- **The Nitro Security Chip** sits on the main board and extends that chain of trust to it. It controls access to firmware storage and management buses. It also stops the host CPUs from rewriting firmware, which is what makes bare metal instances safe to offer.[^s1]
- **The Nitro Hypervisor** is deliberately minimal and based on KVM. It partitions CPU and memory, assigns SR-IOV virtual functions from the Nitro Cards to VMs, and carries out lifecycle commands from the controller.[^s1][^s2] It has no networking stack, no general-purpose file system, no peripheral drivers and no shell or interactive mode. Bare metal instances run without it.[^s1]

Encryption keys for EBS, instance storage and VPC traffic exist in plaintext only in the Nitro Cards' protected volatile memory. AWS operators can't reach them, and neither can code on the host CPUs.[^s1] Newer VPC cards encrypt traffic between compatible instances without slowing it down.[^s1] AWS also lists the **Nitro Isolation Engine** and **NitroTPM**, a TPM 2.0 module for key storage and attestation, as parts of the system.[^s4] The Isolation Engine is a small Rust component inside the hypervisor that controls access to VM memory, CPU state and devices. AWS formally verified its core VM-lifecycle hypercalls. It was announced at re:Invent 2025 and became generally available on all Graviton5-based instances on 11 June 2026.[^s7]

## Versions, limits and gotchas

The EC2 documentation groups instance families by Nitro version, v2 to v6. As of 2026-11, M5 and T3 are on v2, while M9g and C8i are on v6.[^s3] Maximum bandwidth per network card goes up with each version: up to 100 Gbps on v3, 170 Gbps on v4 (100 Gbps for GPU and Trainium types), 200 Gbps on v5 and 400 Gbps on v6.[^s3] Things to check before you migrate:

- Nitro instances use ENA networking and present EBS volumes as NVMe block devices, so the AMI needs current drivers. On Linux, ENA 2.2.9g or later is required on v5 and later. Older drivers fail to attach network interfaces.[^s3]
- Traffic Mirroring isn't supported on v5 or v6.[^s3]
- Nitro v6 cuts the default idle timeout for established TCP connections from 432,000 seconds to 350 seconds. Long-lived idle connections need keepalives.[^s3]
- Bare metal instances can take 20 minutes or more after reaching `running` before they're reachable, because the host checks all hardware and firmware during boot.[^s3]

There's no separate charge for Nitro. **Nitro Enclaves** are also free: you pay only for the parent instance.[^s5]

## Security model and Nitro Enclaves

AWS designed Nitro so that no administrative access to hosts exists, Amazon employees included.[^s4] In 2023, as part of its Digital Sovereignty Pledge, AWS hired NCC Group to review this design. NCC found no design gaps that would undermine seven of AWS's claims, among them that no operator can log in to a host and no administrative API can reach customer content.[^s6]

Nitro Enclaves uses the same hypervisor isolation to carve a hardened VM out of an instance's vCPUs and memory. An enclave has no persistent storage, no interactive access and no external networking, and it talks only to its parent over a local socket. Root on the parent can't read inside it.[^s5] AWS KMS key policies can require that an enclave's attestation measurements match before a key operation succeeds. Each parent instance can run up to four enclaves, the enclave must run Linux, and enclaves aren't supported on Outposts, Local Zones or Wavelength Zones.[^s5]

## When it matters

You rarely choose Nitro directly. Pick a Nitro-based family when you need high network or EBS performance, bare metal access, a TPM, enclaves, or the published no-operator-access design for sovereignty or confidential-computing reviews.[^s3][^s4][^s6] Moving older workloads onto Nitro families means checking ENA and NVMe drivers first.[^s3]

## Connections

Nitro is the platform under **Amazon EC2** and most **AWS compute services**, and **EC2 instance types** are grouped by Nitro version.[^s3] **AWS Graviton** and **AWS Trainium and Inferentia** instances run on it.[^s3][^s7] Its cards provide the data path for **Amazon EBS** and **Amazon VPC** networking.[^s1] Nitro **enables** **Nitro Enclaves**, whose attestation **integrates with** **AWS KMS**.[^s5] Its no-operator-access design affects how AWS's side of the **shared responsibility model** works, and the network performance it provides is what **EC2 placement groups** are built to use.[^s3][^s6]

## Sources
[^s1]: Amazon Web Services, "The components of the Nitro System", The Security Design of the AWS Nitro System whitepaper. https://docs.aws.amazon.com/whitepapers/latest/security-design-of-aws-nitro-system/the-components-of-the-nitro-system.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "The Nitro System journey", The Security Design of the AWS Nitro System whitepaper. https://docs.aws.amazon.com/whitepapers/latest/security-design-of-aws-nitro-system/the-nitro-system-journey.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Instances built on the AWS Nitro System", Amazon EC2 Instance Types. https://docs.aws.amazon.com/ec2/latest/instancetypes/ec2-nitro-instances.html (accessed 2026-11-13)
[^s4]: Amazon Web Services, "AWS Nitro System". https://aws.amazon.com/ec2/nitro/ (accessed 2026-11-13)
[^s5]: Amazon Web Services, "What is Nitro Enclaves?", AWS Nitro Enclaves User Guide. https://docs.aws.amazon.com/enclaves/latest/user/nitro-enclave.html (accessed 2026-11-13)
[^s6]: Amazon Web Services, AWS Compute Blog, "AWS Nitro System gets independent affirmation of its confidential compute capabilities" (2023-05-09). https://aws.amazon.com/blogs/compute/aws-nitro-system-gets-independent-affirmation-of-its-confidential-compute-capabilities (accessed 2026-11-13)
[^s7]: Amazon Web Services, AWS Compute Blog, "AWS Nitro Isolation Engine: Formally verifying the hypervisor in the AWS Nitro System" (2026-06-11). https://aws.amazon.com/blogs/compute/aws-nitro-isolation-engine-formally-verifying-the-hypervisor-in-the-aws-nitro-system/ (accessed 2026-11-13)
