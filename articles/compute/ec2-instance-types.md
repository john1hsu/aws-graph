---
id: kt:ec2-instance-types
type: Feature
title: Amazon EC2 instance types
aliases: [EC2 instance types, EC2 instance families, instance type, EC2 instance type naming conventions]
domain: compute
summary: >-
  The catalogue of fixed hardware configurations (CPU, memory, storage, networking and accelerators)
  that every Amazon EC2 instance is launched with, organized into families and generations and named
  by a series-generation-options.size convention such as c7gn.xlarge.
verified: 2026-11-14
status: current
broader: [kt:amazon-ec2]
related: [kt:aws-graviton, kt:ec2-placement-groups, kt:aws-trainium-and-inferentia, kt:spot-instances]
relations:
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: dependsOn, object: kt:aws-nitro-system}
  - {predicate: integratesWith, object: kt:amazon-ec2-auto-scaling}
  - {predicate: configuredVia, object: kt:attribute-based-instance-type-selection}
  - {predicate: integratesWith, object: kt:aws-compute-optimizer}
sources:
  - id: s1
    title: "Amazon EC2 instance type naming conventions"
    publisher: Amazon Web Services
    url: https://docs.aws.eu/ec2/latest/instancetypes/instance-type-names.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "Amazon EC2 instance types"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ec2/latest/instancetypes/instance-types.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Find an Amazon EC2 instance type"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-discovery.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Compatibility for changing the instance type"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/resize-limitations.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "Create mixed instances group using attribute-based instance type selection"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-instance-type-requirements.html
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "Now available: Amazon EC2 M9g and M9gd instances powered by new AWS Graviton5 processors"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/now-available-amazon-ec2-m9g-and-m9gd-instances-powered-by-new-aws-graviton5-processors/
    type: official-blog
    accessed: 2026-11-14
created: 2026-11-14
---

# Amazon EC2 instance types

An **instance type** is the hardware profile an Amazon EC2 instance runs on: it fixes the vCPU count, memory, instance storage, network and EBS bandwidth and any accelerators.[^s2] EC2 dedicates CPU, memory and instance storage to each instance, while network and disk subsystems are shared among instances on a host; a type rated for higher I/O gets a larger share of those shared resources and less variance.[^s2] Choosing the type is the single biggest lever on an instance's performance and its hourly price.[^s2][^s3]

## Naming convention

Names follow `<series><generation><options>.<size>`.[^s1] In AWS's example `c7gn.xlarge`, `c` is the compute-optimized series, `7` the generation, `g` marks AWS Graviton processors, `n` means network and EBS optimized, and `xlarge` is the size; `metal` denotes bare metal.[^s1] Common series letters include M (general purpose), T (burstable), C (compute optimized), R, X, U and Z (memory), I and D (storage), P and G (GPU), Inf and Trn (AWS Inferentia and Trainium), F (FPGA), Mac (macOS) and Hpc (high-performance computing).[^s1] Option letters signal processor vendor (`a` AMD, `g` Graviton, `i` Intel), local NVMe instance store (`d`), extra memory or storage (`e`), high CPU frequency (`z`), block-storage optimization (`b`) and Flex variants (`-flex`).[^s1]

## Families and generations

AWS groups current-generation types into general purpose, compute optimized, memory optimized, storage optimized, accelerated computing and HPC categories.[^s2] As listed on 2026-11-14 the current generation spans from fifth-generation families such as M5 and C5 up to ninth-generation Graviton families (M9g, C9g, R9g).[^s2] The M9g and M9gd launch on 2026-06-10 illustrates the generational cadence: AWS claimed up to 25% better compute performance than Graviton4-based instances, top sizes of 192 vCPUs and 768 GiB memory, and initial availability in only four Regions (N. Virginia, Ohio, Oregon, Frankfurt).[^s6] Older families such as M4, C4, R4 and T1 are **previous generation**: still supported, but AWS encourages moving to current types; some high-memory sizes (U-9tb1 through U-24tb1) are no longer available for new launches, with U7i recommended instead.[^s2]

## Performance models

Most types are **fixed performance**, able to sustain full CPU indefinitely.[^s2] **Burstable** T instances run at a CPU baseline and spend credits earned while idle to burst above it, suiting web servers, small databases and dev/test.[^s2] **Flex** instances (for example M7i-flex and C8i-flex) offer a 40% CPU baseline and can burst to 100% for 95% of a 24-hour window; running above baseline for long periods may gradually lower burst throughput.[^s2]

## Choosing and finding a type

The EC2 console's Instance Types page filters by vCPUs, memory, network performance, local storage and Availability Zone (including Local and Wavelength Zones) and compares types side by side; the CLI equivalents are `describe-instance-types` and `describe-instance-type-offerings`, the latter showing which types a given AZ offers.[^s3] The EC2 instance type finder suggests types for a new workload, AWS Compute Optimizer recommends changes for running ones, and AWS advises load-testing candidates before committing.[^s3] For fleets, **attribute-based instance type selection** lets an Auto Scaling group describe vCPU, memory, CPU vendor and other requirements instead of naming types; matching newer types are picked up automatically, and On-Demand price protection defaults to excluding types priced more than 20% above the cheapest matching current-generation C, M or R type.[^s5]

## Gotchas

- Availability varies by Region and AZ, so check offerings before designing around a type.[^s3][^s6]
- Changing an instance's type requires the same processor architecture (an Arm Graviton instance can move only to other Arm types) and compatible virtualization; Nitro-based targets need an EBS-backed AMI with ENA and NVMe drivers, and Linux `/etc/fstab` should use UUIDs or labels because device names change.[^s4]
- The target size must support as many attached EBS volumes as you already have: AWS's example is an `m7i.4xlarge` with 32 volumes that cannot move to an `m6i.4xlarge`, which allows 27.[^s4]
- If ENA Express or NitroTPM is enabled, the new type must also support it.[^s4]

## Connections

Instance types are a defining part of **Amazon EC2**, and most current types **depend on** the **AWS Nitro System**.[^s2][^s4] The `g` option ties families to **AWS Graviton**, and the Inf and Trn series to **AWS Trainium and Inferentia**.[^s1] Offerings are **scoped to** **Availability Zones**, so a type must be offered in the AZ where you launch it, including when grouping instances with **EC2 placement groups**.[^s3] **Amazon EC2 Auto Scaling** **integrates with** the catalogue through **attribute-based instance type selection**, which also helps diversify **Spot Instances** across many types, and **AWS Compute Optimizer** recommends right-sized types for running workloads.[^s3][^s5]

## Sources
[^s1]: Amazon Web Services, "Amazon EC2 instance type naming conventions". https://docs.aws.eu/ec2/latest/instancetypes/instance-type-names.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "Amazon EC2 instance types". https://docs.aws.amazon.com/ec2/latest/instancetypes/instance-types.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, "Find an Amazon EC2 instance type". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-discovery.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, "Compatibility for changing the instance type". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/resize-limitations.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, "Create mixed instances group using attribute-based instance type selection". https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-instance-type-requirements.html (accessed 2026-11-14)
[^s6]: Amazon Web Services, AWS News Blog, "Now available: Amazon EC2 M9g and M9gd instances powered by new AWS Graviton5 processors" (2026-06-10). https://aws.amazon.com/blogs/aws/now-available-amazon-ec2-m9g-and-m9gd-instances-powered-by-new-aws-graviton5-processors/ (accessed 2026-11-14)
