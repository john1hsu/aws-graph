---
id: kt:aws-direct-connect
type: Service
title: AWS Direct Connect
aliases: [Direct Connect, DX, AWS DX]
domain: networking-and-content-delivery
summary: >-
  AWS's private, dedicated network connectivity service: a fiber link from your network into an AWS Direct
  Connect location, carrying BGP-routed virtual interfaces to VPCs, transit gateways and AWS public services
  without traversing the public internet.
verified: 2026-11-12
status: current
broader: [kt:aws-networking-and-content-delivery]
related: [kt:amazon-vpc, kt:aws-cloud-wan]
relations:
  - {predicate: alternativeTo, object: kt:aws-site-to-site-vpn}
  - {predicate: integratesWith, object: kt:aws-transit-gateway}
  - {predicate: pricedBy, object: kt:data-transfer-costs}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is Direct Connect? - AWS Direct Connect User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "AWS Direct Connect - Hybrid Networking Lens, AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/hybrid-networking-lens/aws-direct-connect.md
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Direct Connect quotas - AWS Direct Connect User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/directconnect/latest/UserGuide/limits.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "AWS Direct Connect Pay-as-you-go pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/directconnect/pricing/pay-as-you-go/
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "AWS Direct Connect announces flat-rate pricing for dedicated connections"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/aws-direct-connect-announces-flat-rate-pricing/
    type: official-blog
    accessed: 2026-11-12
  - id: s6
    title: "AWS Direct Connect Resiliency Recommendations"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/directconnect/resiliency-recommendation
    type: official-docs
    accessed: 2026-11-12
  - id: s7
    title: "In-scope AWS services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-12
created: 2026-11-12
---

# AWS Direct Connect

AWS Direct Connect links your internal network to an AWS Direct Connect location over a standard Ethernet fiber-optic cable, so traffic to Amazon VPC or to AWS public services such as Amazon S3 can bypass internet service providers in the path.[^s1] Each location gives access to the AWS Region it is associated with, and a single connection in a public Region or AWS GovCloud (US) can reach public AWS services in all other public Regions.[^s1] AWS positions it as the way to get consistent, low-latency, high-bandwidth connectivity between data centers or branch offices and AWS, because internet paths and their latency change as routes shift.[^s2]

## How it works

There are two connection types. A **dedicated connection** is a physical link between an AWS device and your device at 1, 10, 100 or 400 Gbps; a **hosted connection** is provisioned by an AWS Direct Connect Partner over links it already has into AWS, at 50 Mbps up to 25 Gbps.[^s2] Physically you need single-mode fiber with the matching transceiver (for example 10GBASE-LR for 10 Gbps), 802.1Q VLAN encapsulation, and BGP with MD5 authentication; asynchronous BFD is optional and IPv4 and IPv6 are both supported.[^s1]

On top of the physical link you create **virtual interfaces (VIFs)**, each a VLAN with its own BGP session.[^s2] A **private VIF** reaches the private IP range of a VPC, making it a layer-3 extension of your network; a **public VIF** reaches AWS public endpoints such as S3 and DynamoDB; a **transit VIF** reaches AWS Transit Gateway or an AWS Cloud WAN core network edge.[^s2] A **Direct Connect gateway** is a global resource that lets one connection reach VPCs or transit gateways in other Regions and accounts (China Regions excepted), and AWS charges nothing extra for multi-account gateway use.[^s1] For the common case AWS recommends starting with a transit VIF; for very high-volume or latency-sensitive flows, or full 100 Gbps into one VPC, it recommends a private VIF.[^s2]

## Quotas, pricing and gotchas

Quotas (as of 2026-11, per the Direct Connect User Guide): 50 private or public VIFs per dedicated connection and only 1 VIF per hosted connection, neither adjustable; 100 IPv4 and 100 IPv6 BGP routes per private or transit VIF session from on premises by default, raisable to 1,000 each with prefix controls; 1,000 routes per public VIF session, not adjustable; 200 Direct Connect gateways per account (raisable via your SA or TAM); and per gateway, at most 20 virtual private gateways or 6 transit gateways, not adjustable.[^s3] A link aggregation group (LAG) holds up to 4 connections below 100 Gbps or 2 at 100 Gbps.[^s3] All LAG members terminate on the same AWS device, so AWS says a LAG is not a high-availability strategy.[^s2]

Pay-as-you-go pricing has no minimum fee or setup charge and bills port hours plus data transfer out; data in is free.[^s4] As of 2026-11, dedicated port hours at US (non-Japan) locations are $0.30 for 1 Gbps, $2.25 for 10 Gbps, $22.50 for 100 Gbps and $85.00 for 400 Gbps, and data transfer out from a contiguous US Region to a US location is $0.02/GB.[^s4] Since September 2026 AWS also offers **flat-rate pricing** for 10 and 100 Gbps dedicated connections: a fixed monthly fee by bandwidth and one of five geographic tiers, with data transfer out included, available at all locations in commercial Regions except China, plus an optional redundant "port-pair" second connection at no extra charge.[^s5]

Two gotchas: traffic is not encrypted by default, but MACsec layer-2 encryption is available at selected locations on dedicated 10, 100 and 400 Gbps connections and LAGs; and anything on Amazon's public network, including other customers' public EC2 instances, can reach a public VIF, so plan security accordingly.[^s2]

## When to use it (and when not)

Choose Direct Connect for steady, high-volume or latency-sensitive hybrid traffic, such as backups or data migration, where predictable performance and lower transfer rates justify ordering circuits.[^s2] For production, AWS recommends either the **Maximum Resiliency** model (separate connections on separate devices in more than one location) or **High Resiliency** (one connection at each of multiple locations); a single-location design only protects against device failure and is not meant for production.[^s6] AWS Site-to-Site VPN is the cheaper alternative or backup, but a VPN to a virtual private gateway is not recommended as backup for links above 1 Gbps because each tunnel tops out at 1.25 Gbps; terminating the VPN on a transit gateway allows ECMP up to 50 Gbps.[^s6]

## Connections

Direct Connect is part of the AWS networking and content delivery category and terminates into Amazon VPC through private VIFs, into AWS Transit Gateway or AWS Cloud WAN through transit VIFs, and works per AWS Region through its associated locations.[^s1][^s2] Site-to-Site VPN is its internet-based alternative and common backup.[^s6] Its bill is driven by port hours and data transfer out, tying it to AWS data transfer costs.[^s4] The current SAA-C03 exam guide lists AWS Direct Connect among in-scope Networking and Content Delivery services.[^s7]

## Sources
[^s1]: Amazon Web Services, "What is Direct Connect?". https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "AWS Direct Connect - Hybrid Networking Lens". https://docs.aws.amazon.com/wellarchitected/latest/hybrid-networking-lens/aws-direct-connect.md (accessed 2026-11-12)
[^s3]: Amazon Web Services, "Direct Connect quotas". https://docs.aws.amazon.com/directconnect/latest/UserGuide/limits.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, "AWS Direct Connect Pay-as-you-go pricing". https://aws.amazon.com/directconnect/pricing/pay-as-you-go/ (accessed 2026-11-12)
[^s5]: Amazon Web Services, "AWS Direct Connect announces flat-rate pricing for dedicated connections" (What's New, 2026-09-15). https://aws.amazon.com/about-aws/whats-new/2026/09/aws-direct-connect-announces-flat-rate-pricing/ (accessed 2026-11-12)
[^s6]: Amazon Web Services, "AWS Direct Connect Resiliency Recommendations". https://aws.amazon.com/directconnect/resiliency-recommendation (accessed 2026-11-12)
[^s7]: Amazon Web Services, "In-scope AWS services - SAA-C03". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-12)
