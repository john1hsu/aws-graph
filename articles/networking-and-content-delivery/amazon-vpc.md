---
id: kt:amazon-vpc
type: Service
title: Amazon VPC
aliases: [Amazon Virtual Private Cloud, VPC, AWS VPC]
domain: networking-and-content-delivery
summary: >-
  The AWS service for defining logically isolated virtual networks in a Region, with your own IP
  ranges, Availability Zone–scoped subnets, route tables, gateways, endpoints and traffic controls,
  into which you launch resources such as EC2 instances, databases and load balancers.
verified: 2026-11-10
status: current
broader: [kt:aws-networking-and-content-delivery]
related: [kt:amazon-ec2, kt:aws-privatelink]
relations:
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: hasPart, object: kt:subnets-and-route-tables}
  - {predicate: securedBy, object: kt:security-groups-vs-network-acls}
  - {predicate: governedBy, object: kt:vpc-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is Amazon VPC? - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "VPC CIDR blocks - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-cidr-blocks.html
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Subnet CIDR blocks - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/userguide/subnet-sizing.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Amazon VPC quotas - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Amazon VPC Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/vpc/pricing/
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "Enhancing VPC Security with Amazon VPC Block Public Access"
    publisher: AWS Networking & Content Delivery Blog
    url: https://aws.amazon.com/blogs/networking-and-content-delivery/vpc-block-public-access/
    type: official-blog
    accessed: 2026-11-10
  - id: s7
    title: "AWS VPC Encryption Controls now available in AWS GovCloud (US) Regions"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/03/aws-vpc-encryption-controls/
    type: official-blog
    accessed: 2026-11-10
  - id: s8
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# Amazon VPC

Amazon Virtual Private Cloud (Amazon VPC) lets you launch AWS resources into a logically isolated virtual network that you define, much like a traditional data-center network but running on AWS infrastructure.[^s1] A VPC lives in one AWS Region; you carve it into subnets, each of which sits entirely in a single Availability Zone, and you control where traffic goes with route tables, gateways and endpoints.[^s1][^s3] Every account starts with a **default VPC** in each Region, so EC2 instances and managed services such as Elastic Load Balancing, Amazon RDS and Amazon EMR can be launched without building a network first.[^s1]

## How it works

**Addressing.** A VPC's primary IPv4 CIDR block must be between /16 (65,536 addresses) and /28 (16 addresses); AWS recommends RFC 1918 private ranges and prohibits ranges such as 127.0.0.0/8 and 169.254.0.0/16. You can't resize a CIDR block after creation, but you can add secondary IPv4 blocks and Amazon-assigned IPv6 blocks (/44 to /60).[^s2] Subnets are /16 to /28 for IPv4 and /44 to /64 for IPv6, and AWS reserves five IPv4 addresses in each subnet: the network address, the VPC router, the DNS server, one held for future use, and the broadcast address (broadcast itself is unsupported). A /24 subnet therefore offers 251 usable addresses.[^s3]

**Routing and connectivity.** Route tables decide where traffic from a subnet or gateway goes. A subnet is *public* when its route table has a route to an internet gateway, *private* when it reaches the internet only through a NAT device, *VPN-only* when it routes to a Site-to-Site VPN, and *isolated* when it has no routes leaving the VPC.[^s1] VPC endpoints reach AWS services privately without an internet gateway or NAT, peering connections route between two VPCs, and transit gateways act as a hub for many VPCs, VPN and Direct Connect connections.[^s1]

**Visibility and control.** VPC Flow Logs record IP traffic metadata for network interfaces, and Traffic Mirroring copies packets to inspection appliances.[^s1] **VPC Block Public Access**, launched in November 2024, is a per-account, per-Region switch that blocks internet-gateway traffic in bidirectional or ingress-only mode, overriding security group and network ACL settings, with per-VPC or per-subnet exclusions.[^s6] **VPC Encryption Controls** audit and can enforce encryption in transit within and across VPCs, turning on hardware AES-256 encryption between supported resources; AWS extended it to the GovCloud (US) Regions in 2026.[^s7]

## Quotas, pricing and gotchas

Default quotas (as of 2026-11, per account): 5 VPCs per Region, 200 subnets per VPC, 5 IPv4 CIDR blocks per VPC (adjustable up to 50), 5 NAT gateways per Availability Zone, 5 Elastic IP addresses per Region, 200 route tables per VPC with 500 non-propagated routes each (up to 1,000), and 2,500 security groups per Region. A security group holds 60 inbound and 60 outbound rules and an interface can carry 5 security groups (up to 16), but rules multiplied by groups can't exceed 1,000. Network ACLs default to 20 rules per direction (up to 40). All of these are adjustable, and raising the VPC quota raises the internet gateway quota with it.[^s4]

There's no charge for a VPC itself; you pay for components.[^s1] As of 2026-11, AWS lists public IPv4 addresses at $0.005 per address-hour whether in use or idle, across commercial, GovCloud and China Regions. Its US East (Ohio) example prices a NAT gateway at $0.045 per hour plus $0.045 per GB processed, Traffic Mirroring at $0.015 per session-hour, and cross-AZ VPC peering traffic at $0.01 per GB in each direction. Reachability Analyzer costs $0.10 per analysis.[^s5] Private RFC 1918 addresses are free, so IPv6 or private addressing behind load balancers can cut cost.[^s1]

Plan CIDRs early. Overlapping ranges block peering and Direct Connect gateway associations, and a pending peering request stops the requester VPC from adding any CIDR until it is accepted or deleted.[^s2]

## When to use it (and when not)

Nearly every non-trivial AWS workload runs in a custom VPC. The default VPC suits experiments, but its subnets are built for quick launch, not for a production network design.[^s1] Use multiple subnets across Availability Zones for resilience, keep workloads in private subnets, and choose a transit gateway rather than full-mesh peering once many VPCs must interconnect.[^s1]

## Connections

Amazon VPC is the foundation of AWS **networking and content delivery** and is listed as an in-scope service for the **AWS Certified Solutions Architect – Associate** exam.[^s8] It is **scoped to** a single **AWS Region**, with subnets pinned to Availability Zones.[^s1] **Subnets and route tables** are its core building blocks.[^s3] Traffic is filtered by **security groups and network ACLs**, whose limits are part of the **VPC quotas**.[^s4] **Amazon EC2** instances launch into its subnets, and **AWS PrivateLink** interface endpoints give private access to services from inside it.[^s1]

## Sources

[^s1]: Amazon Web Services, "What is Amazon VPC? - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html (accessed 2026-11-10)
[^s2]: Amazon Web Services, "VPC CIDR blocks - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/userguide/vpc-cidr-blocks.html (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Subnet CIDR blocks - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/userguide/subnet-sizing.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Amazon VPC quotas - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Amazon VPC Pricing". https://aws.amazon.com/vpc/pricing/ (accessed 2026-11-10)
[^s6]: AWS Networking & Content Delivery Blog, "Enhancing VPC Security with Amazon VPC Block Public Access". https://aws.amazon.com/blogs/networking-and-content-delivery/vpc-block-public-access/ (accessed 2026-11-10)
[^s7]: Amazon Web Services, "AWS VPC Encryption Controls now available in AWS GovCloud (US) Regions". https://aws.amazon.com/about-aws/whats-new/2026/03/aws-vpc-encryption-controls/ (accessed 2026-11-10)
[^s8]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-10)
