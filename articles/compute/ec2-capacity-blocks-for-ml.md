---
id: kt:ec2-capacity-blocks-for-ml
type: Feature
title: Amazon EC2 Capacity Blocks for ML
aliases: [EC2 Capacity Blocks, Capacity Blocks for ML, Capacity Blocks, Capacity Block reservations]
domain: compute
summary: >-
  An Amazon EC2 purchasing option that reserves GPU or Trainium accelerated instances (or
  UltraServers) inside EC2 UltraClusters for a fixed, time-boxed window of 1 to 182 days, starting
  as soon as minutes later or up to eight weeks ahead, paid up front at a supply-and-demand price.
verified: 2026-11-20
status: current
broader: [kt:amazon-ec2]
related: [kt:on-demand-capacity-reservations, kt:aws-trainium-and-inferentia, kt:ec2-instance-types, kt:amazon-sagemaker-ai, kt:ec2-instance-quotas]
relations:
  - {predicate: contrastsWith, object: kt:on-demand-capacity-reservations}
  - {predicate: alternativeTo, object: kt:spot-instances}
  - {predicate: contrastsWith, object: kt:savings-plans}
  - {predicate: integratesWith, object: kt:amazon-eks}
  - {predicate: monitoredBy, object: kt:amazon-eventbridge}
  - {predicate: scopedTo, object: kt:availability-zones}
sources:
  - id: s1
    title: "Capacity Blocks for ML - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-capacity-blocks.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "How Amazon EC2 Capacity Blocks work - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/capacity-blocks-how.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Extend Capacity Blocks - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/capacity-blocks-extend.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "Amazon EC2 Capacity Blocks for ML pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ec2/capacityblocks/pricing/
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "Choosing a purchasing option for Amazon EC2 - AWS Decision Guides"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/decision-guides/ec2-purchasing-options-aws-how-to-choose.html
    type: official-docs
    accessed: 2026-11-20
  - id: s6
    title: "Secure short-term GPU capacity for ML workloads with EC2 Capacity Blocks for ML and SageMaker training plans"
    publisher: AWS Machine Learning Blog
    url: https://aws.amazon.com/blogs/machine-learning/secure-short-term-gpu-capacity-for-ml-workloads-with-ec2-capacity-blocks-for-ml-and-sagemaker-training-plans/
    type: official-blog
    accessed: 2026-11-20
created: 2026-11-20
---

# Amazon EC2 Capacity Blocks for ML

Amazon EC2 Capacity Blocks for ML let you reserve accelerated instances for a defined future window, so a training run, fine-tuning job or experiment has GPUs or Trainium chips waiting when it starts. The reserved instances are placed in EC2 UltraClusters, and Capacity Blocks can also reserve whole EC2 UltraServers.[^s1]

## How it works

You specify the instance (or UltraServer) type, the instance count, the duration, and a window of earliest start and latest end. EC2 returns an offering with a start time, an Availability Zone and a price; buying it creates a Capacity Reservation whose ID your launches must target once it becomes active.[^s2] Start times can be up to eight weeks out, and offerings can begin as soon as 30 minutes after you search.[^s1] Durations run from one to 14 days in 1-day steps, then in 7-day steps up to 182 days.[^s2][^s5]

Blocks end at 11:30 AM UTC, and EC2 starts terminating any running instances at 11:00 AM UTC on the final day; for UltraServer blocks the cut-over is 60 minutes before the end. EventBridge emits a warning event 10 minutes before termination starts.[^s1][^s2] A block that is active or scheduled can be extended from 57 days down to 1 hour before it expires, keeping the same reservation ID and running instances, but only if capacity is available; the extension is paid up front and fails if payment isn't processed within 12 hours or 35 minutes before the block ends, whichever is first.[^s3]

Supported types (as of 2026-11) include P6-B300, P6-B200, P5en, P5e, P5, P4d and P4de GPU instances and Trn1/Trn2 Trainium instances, plus Trn2 and P6e-GB200 UltraServers. Availability is per type and Region: the docs table lists commercial Regions, some Local Zones (which you must opt in to) and, for P6-B200, the AWS GovCloud (US) Regions. Only default tenancy is supported.[^s1]

## Quotas, pricing and gotchas

- **Size limits:** up to 64 instances per block and 256 across blocks; across all accounts in an AWS Organization, no more than 256 instances in blocks on any one date. A size of 64 isn't offered for every type in every Region. These are fixed rules in the docs rather than adjustable quotas.[^s1]
- **Quota interaction:** instances in a block don't count against your On-Demand Instance limits.[^s1]
- **No cancellation, no moving:** blocks can't be cancelled, moved or split; UltraServer blocks can't be shared with other accounts, even within an Organization.[^s1]
- **No placement groups:** you can't specify a placement group when launching into a block.[^s1]
- **Pricing:** a reservation fee charged up front when the block is scheduled, plus an operating-system fee while instances run (Linux at $0; RHEL, SLES and Ubuntu Pro extra). Prices are updated regularly on supply and demand; a block keeps the rate in effect when it was bought. Example as of 2026-11: p5.48xlarge in US East (N. Virginia) at $47.757 per instance-hour (about $5.970 per H100); the page says the next price update is scheduled for January 2027.[^s4]
- **Not a discount:** AWS's decision guide says Capacity Block rates can sit at, above or below On-Demand (popular GPU types often at a premium), and they can't be combined with Savings Plans or Reserved Instance discounts.[^s5]

## When to use it (and when not)

Use Capacity Blocks when you need a known number of high-end accelerators for a bounded period: a multi-day training run, a benchmark, or a launch-week inference burst. For open-ended capacity in an AZ, On-Demand Capacity Reservations hold capacity at the On-Demand rate and can be combined with Savings Plans; for interruptible, fault-tolerant work, Spot is far cheaper but offers no assurance.[^s5] If the work runs on Amazon SageMaker AI, use SageMaker training plans instead: AWS notes that they aren't interchangeable with Capacity Blocks, and EC2 Capacity Blocks can't be used with SageMaker.[^s6]

## Connections

- **Broader: Amazon EC2** (`kt:amazon-ec2`) — Capacity Blocks are an EC2 purchasing and reservation option.[^s5]
- **Contrasts with On-Demand Capacity Reservations** (`kt:on-demand-capacity-reservations`) and **Savings Plans** (`kt:savings-plans`): both other options are open-ended or discount-based, whereas a block is a time-boxed, prepaid reservation.[^s5]
- **Alternative to Spot Instances** (`kt:spot-instances`) for cost-sensitive ML that can tolerate interruption.[^s5]
- **Related to AWS Trainium and Inferentia** (`kt:aws-trainium-and-inferentia`) and **EC2 instance types** (`kt:ec2-instance-types`), which define what you can reserve; and to **EC2 instance quotas** (`kt:ec2-instance-quotas`), which blocks sit outside of.[^s1]
- **Integrates with Amazon EKS** (`kt:amazon-eks`) managed and self-managed node groups, and with EC2 Auto Scaling scheduled actions timed to the block start.[^s1]
- **Monitored by Amazon EventBridge** (`kt:amazon-eventbridge`) for end-of-block warnings; **scoped to Availability Zones** (`kt:availability-zones`), since each offering lands in one AZ.[^s2]
- **Related to Amazon SageMaker AI** (`kt:amazon-sagemaker-ai`), whose training plans are the separate SageMaker-side reservation path.[^s6]

## Sources
[^s1]: Amazon Web Services, "Capacity Blocks for ML - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-capacity-blocks.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "How Amazon EC2 Capacity Blocks work - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/capacity-blocks-how.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "Extend Capacity Blocks - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/capacity-blocks-extend.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "Amazon EC2 Capacity Blocks for ML pricing". https://aws.amazon.com/ec2/capacityblocks/pricing/ (accessed 2026-11-20)
[^s5]: Amazon Web Services, "Choosing a purchasing option for Amazon EC2 - AWS Decision Guides". https://docs.aws.amazon.com/decision-guides/latest/decision-guides/ec2-purchasing-options-aws-how-to-choose.html (accessed 2026-11-20)
[^s6]: Vanessa Ji, Alvaro Sanchez Martin and Yati Agarwal, AWS Machine Learning Blog, "Secure short-term GPU capacity for ML workloads with EC2 Capacity Blocks for ML and SageMaker training plans", 7 May 2026. https://aws.amazon.com/blogs/machine-learning/secure-short-term-gpu-capacity-for-ml-workloads-with-ec2-capacity-blocks-for-ml-and-sagemaker-training-plans/ (accessed 2026-11-20)
