---
id: kt:aws-wavelength
type: InfrastructureConcept
title: AWS Wavelength
aliases: [Wavelength, Wavelength Zones, AWS Wavelength Zone, Wavelength Zone]
domain: global-infrastructure
summary: >-
  AWS Wavelength places standard AWS compute and storage racks (Wavelength Zones) inside
  telecommunications providers' data centers at the edge of their 5G networks, extending a VPC from
  its parent Region so applications can serve mobile and carrier-connected devices with low latency.
verified: 2026-11-16
status: current
broader: [kt:aws-global-infrastructure]
related: [kt:amazon-vpc, kt:aws-transit-gateway, kt:amazon-eks, kt:edge-locations]
relations:
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: alternativeTo, object: kt:local-zones}
  - {predicate: alternativeTo, object: kt:aws-outposts}
  - {predicate: integratesWith, object: kt:amazon-ec2}
  - {predicate: pricedBy, object: kt:savings-plans}
sources:
  - id: s1
    title: "How AWS Wavelength works - AWS Wavelength Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wavelength/latest/developerguide/how-wavelengths-work.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Carrier gateway for AWS Wavelength - AWS Wavelength Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wavelength/latest/developerguide/carrier-gateways.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Quotas and considerations for Wavelength Zones - AWS Wavelength Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wavelength/latest/developerguide/wavelength-quotas.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Subnets in AWS Wavelength - Amazon Virtual Private Cloud User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/userguide/subnet-wavelength.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "AWS Wavelength FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/wavelength/faqs/
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "AWS Wavelength pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/wavelength/pricing/
    type: official-docs
    accessed: 2026-11-16
  - id: s7
    title: "AWS Wavelength features"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/wavelength/features/
    type: official-docs
    accessed: 2026-11-16
  - id: s8
    title: "Announcing new AWS Wavelength Zone in Lenexa, Kansas"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/05/aws-wavelength-zone-lenexa-kansas/
    type: official-blog
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS Wavelength

AWS Wavelength extends AWS infrastructure into the data centers of communications service providers (CSPs), at the edge of their 5G networks. Each deployment, a **Wavelength Zone**, is a set of standard AWS racks (hardware and software) installed in a carrier facility and used for applications with low-latency, data-residency or security requirements.[^s1] A Wavelength Zone is an extension of an AWS Region and appears as a subnet location of a VPC in that Region.[^s1][^s5] AWS pitches it for IoT, game streaming, connected and autonomous vehicles, live media and AI/ML inference at the edge.[^s5] Zones are operated with telco partners; for example, the Lenexa, Kansas zone announced in May 2025 is run with Verizon and belongs to US East (N. Virginia).[^s8]

## How it works

You opt in to a Wavelength Zone, then create a subnet in it; creating that subnet is what extends an existing VPC into the zone.[^s1][^s4] The subnet inherits the VPC's main route table, so its local route connects it to subnets in the Region's Availability Zones and Local Zones.[^s1] Wavelength subnets have the same components as Availability Zone subnets, including IPv4 addressing, DHCP option sets and network ACLs.[^s4]

Traffic to devices and the outside world goes through a **carrier gateway**, a VPC component that exists only in VPCs with Wavelength subnets.[^s1][^s2] It accepts inbound traffic from the carrier network in that location and lets instances send traffic out to the carrier network and the internet, but it does not accept inbound connections from the internet (apart from select partners using multi-access Wavelength).[^s2] Like an internet gateway, it performs NAT, translating an instance's private address to a **Carrier IP address** allocated from the zone's **network border group**, the set of zones from which AWS advertises those addresses.[^s1][^s2] A typical Wavelength route table keeps the local route for the VPC CIDR and sends `0.0.0.0/0` to the carrier gateway.[^s1] Inbound routing from the carrier is optimized for devices in the zone's metro area.[^s3]

## Services, quotas and gotchas

- **Service subset.** AWS lists EC2, EBS, VPC subnets and carrier gateways, plus EC2 Auto Scaling, EKS, ECS, Systems Manager, CloudWatch, CloudTrail and CloudFormation; Application Load Balancer exists only in select zones, and third-party marketplace products stand in for NLB, S3 and RDS (as of 2026-11).[^s7]
- **Narrow instance and volume choice.** Only gp2 EBS volumes are supported, and snapshots and AMIs live in the parent Region.[^s3] Instance types differ by geography: t3.medium, r5.2xlarge and g4dn.2xlarge are offered in every listed area, while some c5, m5 and r5.large sizes appear only in African zones (as of 2026-11).[^s3] Dedicated Instances and Dedicated Hosts are not supported.[^s3]
- **Quotas come from the parent Region.** EC2 quotas are the home Region's, and Wavelength VPCs and subnets count against normal VPC quotas; the guide gives no separate per-zone values.[^s3]
- **No cross-zone traffic in one VPC.** Instances in two different Wavelength Zones of the same VPC cannot talk to each other; AWS recommends one VPC per zone joined by a transit gateway, which must attach through a subnet in the parent Availability Zone, not the Wavelength subnet.[^s3][^s4]
- **IPv4 only and smaller MTU.** Carrier gateways carry IPv4, and IPv6 cannot be assigned to Wavelength subnets.[^s2][^s3] MTU is 9001 bytes within a zone but 1500 bytes through the carrier gateway and 1300 bytes for private-IP traffic back to the Region.[^s1] VPC endpoints must be created in an Availability Zone.[^s3]
- **Default protocol filters.** From the internet, UDP is denied by default except in African zones whose parent Region is eu-west-3; traffic from carrier devices is allowed.[^s3]

## Pricing

Wavelength EC2 capacity is billed On-Demand at prices that differ from the parent Region, and EBS and other resources are also priced per zone; the pricing page gives no rates, so use the EC2 and EBS pricing pages or the Pricing Calculator (as of 2026-11).[^s6] Instance Savings Plans apply, Reserved Instances are not offered, and On-Demand Capacity Reservations can hold capacity.[^s5][^s6] Cost Explorer can filter usage by Wavelength Zone.[^s5]

## When to use it (and when not)

Use Wavelength when the users are devices on a specific carrier's mobile network and the round trip to a Region is too slow.[^s5] AWS distinguishes it from Local Zones, which put AWS infrastructure near metro populations for workloads such as rendering and virtual desktops, and from Outposts, which put racks in your own facility.[^s5] Each Wavelength Zone is a single zone, so AWS suggests combining it with Local Zones, Outposts or on-premises sites for resilience.[^s5] If you need the full service catalog, Multi-AZ resilience or internet-originated inbound traffic, a Region or Local Zone is the better fit.[^s3][^s7]

## Connections

Wavelength is part of the **AWS global infrastructure** and is scoped to a parent **AWS Region**, whose **Amazon VPC** it extends with Wavelength subnets.[^s1][^s4] It is an alternative to **AWS Local Zones** and **AWS Outposts** for edge placement.[^s5] Workloads run mainly on **Amazon EC2** (with **Amazon EKS** for containers), and multi-zone designs connect per-zone VPCs through **AWS Transit Gateway**.[^s3][^s4] EC2 usage can be discounted with **Savings Plans**.[^s6] Unlike **edge locations**, which serve CloudFront and other edge services, a Wavelength Zone runs your own compute inside a carrier network.[^s1][^s7]

## Sources
[^s1]: Amazon Web Services, "How AWS Wavelength works - AWS Wavelength Developer Guide". https://docs.aws.amazon.com/wavelength/latest/developerguide/how-wavelengths-work.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Carrier gateway for AWS Wavelength". https://docs.aws.amazon.com/wavelength/latest/developerguide/carrier-gateways.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Quotas and considerations for Wavelength Zones". https://docs.aws.amazon.com/wavelength/latest/developerguide/wavelength-quotas.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Subnets in AWS Wavelength - Amazon VPC User Guide". https://docs.aws.amazon.com/vpc/latest/userguide/subnet-wavelength.html (accessed 2026-11-16)
[^s5]: Amazon Web Services, "AWS Wavelength FAQs". https://aws.amazon.com/wavelength/faqs/ (accessed 2026-11-16)
[^s6]: Amazon Web Services, "AWS Wavelength pricing". https://aws.amazon.com/wavelength/pricing/ (accessed 2026-11-16)
[^s7]: Amazon Web Services, "AWS Wavelength features". https://aws.amazon.com/wavelength/features/ (accessed 2026-11-16)
[^s8]: Amazon Web Services, "Announcing new AWS Wavelength Zone in Lenexa, Kansas" (What's New, 2025-05-06). https://aws.amazon.com/about-aws/whats-new/2025/05/aws-wavelength-zone-lenexa-kansas/ (accessed 2026-11-16)
