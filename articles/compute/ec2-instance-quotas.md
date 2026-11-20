---
id: kt:ec2-instance-quotas
type: Limit
title: Amazon EC2 instance quotas
aliases: [EC2 instance quotas, EC2 instance type quotas, EC2 vCPU limits, EC2 vCPU quotas, EC2 instance limits, Spot Instance quotas, EC2 API throttling limits]
domain: compute
summary: >-
  The per-account, per-Region limits on how much Amazon EC2 capacity you can run, expressed as vCPU
  quotas per instance-family group for On-Demand and Spot, host counts for Dedicated Hosts and vCPUs for
  Capacity Blocks, together with the token-bucket API rate limits that cap how fast you can launch.
verified: 2026-11-20
status: current
broader: [kt:amazon-ec2]
related: [kt:ec2-instance-types, kt:spot-instances, kt:on-demand-capacity-reservations, kt:ec2-capacity-blocks-for-ml]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
sources:
  - id: s1
    title: "Amazon EC2 instance type quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ec2/latest/instancetypes/ec2-instance-quotas.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Amazon EC2 service quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Spot Instance quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-limits.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "Request throttling for the Amazon EC2 API"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ec2/latest/devguide/ec2-api-throttling.html
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "How do I request a vCPU service quota increase for my Amazon EC2 On-Demand Instance?"
    publisher: Amazon Web Services (AWS Knowledge Center)
    url: https://aws.amazon.com/premiumsupport/knowledge-center/ec2-on-demand-instance-vcpu-increase/
    type: official-docs
    accessed: 2026-11-20
  - id: s6
    title: "vCPU-based Spot Instance Limits are Now Available in Amazon EC2"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/pt/about-aws/whats-new/2020/10/vcpu-based-spot-instance-limits-available-amazon-ec2/
    type: official-blog
    accessed: 2026-11-20
created: 2026-11-20
---

# Amazon EC2 instance quotas

Amazon EC2 instance quotas cap how much compute an AWS account can run in each Region. Defaults are set per Region, and a launch that would exceed a quota simply fails, so the EC2 guide advises planning increase requests ahead of time.[^s2] Since 2019 for On-Demand and October 2020 for Spot, these quotas count **vCPUs rather than instances**, so a single limit covers any mix of sizes within a group of instance families.[^s6][^s1] A second, separate layer of limits throttles the EC2 API itself, including how many instances you can launch per second.[^s4]

## Capacity quotas

The instance type quotas page lists four families of capacity quota, all adjustable and all managed per Region in the Service Quotas console (as of 2026-11):[^s1]

- **Running On-Demand instances**, in vCPUs, one quota per family group: DL; F; G and VT; HPC; High Memory; Inf; P; Trn; X; and **Standard (A, C, D, H, I, M, R, T, Z)**. Standard defaults to 5 vCPUs and every other group to 0.[^s1]
- **All Spot Instance Requests**, in vCPUs, with the same grouping and the same defaults (Standard 5, others 0).[^s1][^s3]
- **Running Dedicated Hosts**, counted in hosts, not vCPUs, with one quota per host family; each defaults to 0.[^s1]
- **Capacity Blocks**, the vCPUs of concurrently active Capacity Blocks for P4d, P5, P5e, P5en, Trn1 and Trn2, each with an account-level and an organization-level quota defaulting to 0.[^s1]

EC2 also raises On-Demand and Spot quotas automatically as your usage grows, so long-standing accounts often show values well above these defaults.[^s1][^s3]

## Sizing a request

Because requests are made in vCPUs, the AWS Knowledge Center advises adding up the vCPUs of the instances you plan to run and comparing that with the group's quota.[^s5] All standard families share one quota, so vCPUs for M, C, R and T instances accumulate together; in its example, an account at 64 standard vCPUs that adds two 8-vCPU and two 40-vCPU instances needs at least 160.[^s5] The Spot guide gives a similar illustration: a 256-vCPU Standard Spot quota could hold 32 m5.2xlarge or 16 c5.4xlarge instances.[^s3] Quotas are per Region, so each Region you deploy into needs its own request; several increases can be bundled in one Support case.[^s5]

Requests go through the Service Quotas console (choose the Region, Amazon EC2, the quota, then request an increase). The status moves from Pending to Quota requested, when a Support case number appears.[^s2] For Spot, the typical trigger is the error "Max spot instance count exceeded."[^s3]

## API throttling

EC2 throttles API calls per account per Region with a token bucket: each call spends a token, buckets refill at a fixed rate, and a throttled call returns `RequestLimitExceeded`.[^s4] Default buckets as of 2026-11 include non-mutating actions at a capacity of 100 and refill of 20 per second, unfiltered and unpaginated describes (such as DescribeInstances) at 50 and 10, and mutating actions at 50 and 5.[^s4] RunInstances has both a request bucket (5, refilling 2 per second) and a **resource bucket** that limits instances launched: 1,000 tokens refilling at 2 instances per second.[^s4] For M, C and R launches into Availability Zones with spare capacity, dynamic throttling can raise these refill rates up to tenfold, but AWS does not guarantee it.[^s4] Rate limits can be raised through an AWS Support case, by at most three times the current value per request, and refill rates should be raised before bucket capacity.[^s4]

## Gotchas

- Terminated instances whose Spot requests were never cancelled keep counting against the Spot vCPU quota until EC2 closes the request.[^s3]
- Pending Spot requests count too, until they are fulfilled and the running instance counts instead.[^s3]
- New accounts and new Regions start with small Standard quotas and zero for accelerated families, so GPU, Trainium or Inferentia projects should request capacity before launch day.[^s1][^s5]
- CloudWatch usage metrics let you alarm before you reach a quota.[^s3]

## Connections

- **Broader: Amazon EC2** (`kt:amazon-ec2`), whose capacity and API these quotas bound.[^s2]
- **Configured via Service Quotas** (`kt:service-quotas`), where every capacity quota is viewed and increases are requested, and **scoped to AWS Regions** (`kt:aws-regions`), since every value is per account per Region.[^s1][^s2]
- **Monitored by Amazon CloudWatch** (`kt:amazon-cloudwatch`), whose usage metrics support quota alarms.[^s3]
- **Related to EC2 instance types** (`kt:ec2-instance-types`), whose families define the quota groups; to **Spot Instances** (`kt:spot-instances`), which have their own vCPU quotas; to **On-Demand Capacity Reservations** (`kt:on-demand-capacity-reservations`); and to **Capacity Blocks for ML** (`kt:ec2-capacity-blocks-for-ml`), which have dedicated vCPU quotas.[^s1][^s3]

## Sources
[^s1]: Amazon Web Services, Amazon EC2 Instance Types guide, "Amazon EC2 instance type quotas". https://docs.aws.amazon.com/ec2/latest/instancetypes/ec2-instance-quotas.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, Amazon EC2 User Guide, "Amazon EC2 service quotas". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, Amazon EC2 User Guide, "Spot Instance quotas". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-limits.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, Amazon EC2 Developer Guide, "Request throttling for the Amazon EC2 API". https://docs.aws.amazon.com/ec2/latest/devguide/ec2-api-throttling.html (accessed 2026-11-20)
[^s5]: Amazon Web Services, AWS Knowledge Center, "How do I request a vCPU service quota increase for my Amazon EC2 On-Demand Instance?". https://aws.amazon.com/premiumsupport/knowledge-center/ec2-on-demand-instance-vcpu-increase/ (accessed 2026-11-20)
[^s6]: Amazon Web Services, What's New with AWS, "vCPU-based Spot Instance Limits are Now Available in Amazon EC2" (2020-10-01). https://aws.amazon.com/pt/about-aws/whats-new/2020/10/vcpu-based-spot-instance-limits-available-amazon-ec2/ (accessed 2026-11-20)
