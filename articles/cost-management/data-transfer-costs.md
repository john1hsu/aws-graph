---
id: kt:data-transfer-costs
type: PricingModel
title: AWS data transfer costs
aliases: [Data transfer costs, AWS data transfer pricing, Data transfer out, DTO, AWS egress fees]
domain: cost-management
summary: >-
  How AWS bills for moving data: inbound internet traffic is free, while outbound internet,
  inter-Region and cross-Availability Zone traffic, plus NAT gateway processing and dedicated
  links, carry per-GB or hourly charges that architecture choices can greatly reduce.
verified: 2026-11-19
status: concept
broader: [kt:aws-pricing-fundamentals]
related: [kt:aws-free-tier, kt:aws-cost-explorer, kt:availability-zones, kt:aws-regions, kt:nat-gateway]
relations:
  - {predicate: governedBy, object: kt:cost-optimization-pillar}
  - {predicate: integratesWith, object: kt:aws-direct-connect}
  - {predicate: integratesWith, object: kt:aws-transit-gateway}
  - {predicate: integratesWith, object: kt:amazon-cloudfront}
  - {predicate: integratesWith, object: kt:aws-privatelink}
sources:
  - id: s1
    title: "Overview of Data Transfer Costs for Common Architectures"
    publisher: Amazon Web Services (AWS Architecture Blog)
    url: https://aws.amazon.com/blogs/architecture/overview-of-data-transfer-costs-for-common-architectures
    type: official-blog
    accessed: 2026-11-19
  - id: s2
    title: "COST08-BP02 Select components to optimize data transfer cost - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/cost_data_transfer_optimized_components.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Amazon VPC Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/vpc/pricing/
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "AWS Direct Connect Pay-as-you-go Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/directconnect/pricing/pay-as-you-go/
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Free data transfer out to internet when moving out of AWS"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/jp/blogs/aws/free-data-transfer-out-to-internet-when-moving-out-of-aws/
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# AWS data transfer costs

Data transfer is the third of AWS's big cost drivers after compute and storage, and the one most often missed in estimates because it depends on how traffic flows rather than on what you provision. The basic rule is asymmetric: AWS charges nothing for data coming in from the internet to any service in any Region, but bills data going out to the internet per service, at rates set by the Region it leaves from.[^s1] Inside AWS, where two resources sit decides whether their traffic is free or billed.[^s1]

## How the charges work

- **Same Availability Zone:** traffic between resources in one AZ carries no transfer charge.[^s1]
- **Across Availability Zones:** traffic between AZs in the same Region is billed, for example EC2-to-EC2 or EC2-to-RDS traffic; VPC peering that crosses AZs is $0.01 per GB in each direction (as of 2026-11).[^s1][^s3] An RDS Multi-AZ standby is a notable exception: its replication traffic is not charged, though clients in other AZs still pay to reach the primary.[^s1]
- **Between Regions:** inter-Region traffic is billed at rates that depend on the source and destination Regions.[^s1]
- **Reaching AWS services in-Region:** calling public service endpoints through an internet gateway has no transfer charge, but routing the same calls through a NAT gateway adds a per-GB processing fee.[^s1]
- **Connectivity services:** Transit Gateway bills an hourly fee per attachment plus a per-GB processing fee for traffic sent into it; Site-to-Site VPN bills hourly plus data out of AWS; Direct Connect bills port-hours plus data out, with data in at $0.00 per GB everywhere.[^s1][^s4]

## Prices, quotas and gotchas (as of 2026-11)

- **NAT gateway:** in US East (Ohio), $0.045 per NAT gateway-hour plus $0.045 per GB processed; the Regional NAT gateway option is $0.045 per hour per AZ plus the same per-GB fee. AWS's worked example adds $0.09 per GB for the resulting internet transfer out, and traffic between the NAT gateway and an instance in a different AZ is also charged.[^s3]
- **Gateway VPC endpoints** for S3 and DynamoDB have no hourly or data processing charge, which makes them the standard fix for NAT-heavy S3 traffic; interface endpoints, by contrast, carry both hourly and per-GB charges.[^s3][^s1]
- **Direct Connect:** from the contiguous United States to a US location, data out is $0.0200 per GB; a dedicated 1 Gbps port is $0.30 per hour and a 10 Gbps port $2.25 per hour (excluding partner charges).[^s4]
- **Public IPv4 addresses**, in use or idle, are $0.005 per address-hour, a network charge that appears alongside transfer on the bill.[^s3]
- **Free allowances:** every account gets 100 GB per month of free transfer to the internet from Regions and 1 TB per month from CloudFront, and AWS says over 90% of customers pay no transfer-out fees.[^s5]
- **Leaving AWS:** since March 2024 AWS waives transfer-out charges for customers migrating data to another provider or on premises. You request it from AWS Support, receive credits for the migrated data, and have 90 days to complete the move; a September 2025 update removed the need to exceed 100 GB a month before asking.[^s5]

## Reducing the bill

The Well-Architected Cost Optimization pillar treats this as a design question (COST08): first model where data flows, then choose components that remove or cheapen the transfer.[^s2] Its recommendations are to serve users from a CDN such as CloudFront, cache repeated reads, use VPC endpoints for private access to AWS services, place each NAT gateway in the same AZ as the busy resources behind it, and use Direct Connect when moving large volumes to and from on premises.[^s2] It also warns against cross-Region transfer unless multi-Region is a deliberate choice, and asks you to weigh the cross-AZ charges of Multi-AZ designs against their resilience benefit.[^s2] The Architecture Blog adds to keep traffic AZ-local where possible and to estimate with the Pricing Calculator before building.[^s1]

To see where money goes, the same best practice points to VPC flow logs and CloudWatch for traffic volumes and to Cost Explorer or the CUDOS dashboards for the cost view.[^s2] Data transfer rarely has a single owner, so tag and break down the line items early.

## Connections

This node expands the outbound-transfer driver from **AWS pricing fundamentals**, and its free allowances belong to the **AWS Free Tier**.[^s5] The **Cost Optimization Pillar** governs how to design around it.[^s2] Location boundaries (**Availability Zones** and **AWS Regions**) decide whether traffic is billed.[^s1] The main levers are networking services: **NAT gateways** are a frequent source of charges, while **AWS PrivateLink** endpoints, **AWS Transit Gateway**, **AWS Direct Connect** and **Amazon CloudFront** each change the path and the price of the bytes.[^s1][^s2][^s3][^s4] **AWS Cost Explorer** is where the resulting charges are analysed.[^s2]

## Sources
[^s1]: Amazon Web Services (AWS Architecture Blog), "Overview of Data Transfer Costs for Common Architectures". https://aws.amazon.com/blogs/architecture/overview-of-data-transfer-costs-for-common-architectures (accessed 2026-11-19)
[^s2]: Amazon Web Services, "COST08-BP02 Select components to optimize data transfer cost - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/cost_data_transfer_optimized_components.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Amazon VPC Pricing". https://aws.amazon.com/vpc/pricing/ (accessed 2026-11-19)
[^s4]: Amazon Web Services, "AWS Direct Connect Pay-as-you-go Pricing". https://aws.amazon.com/directconnect/pricing/pay-as-you-go/ (accessed 2026-11-19)
[^s5]: Amazon Web Services (AWS News Blog), "Free data transfer out to internet when moving out of AWS". https://aws.amazon.com/jp/blogs/aws/free-data-transfer-out-to-internet-when-moving-out-of-aws/ (accessed 2026-11-19)
