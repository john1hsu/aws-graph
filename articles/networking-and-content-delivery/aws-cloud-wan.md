---
id: kt:aws-cloud-wan
type: Service
title: AWS Cloud WAN
aliases: [Cloud WAN, Amazon Cloud WAN]
domain: networking-and-content-delivery
summary: >-
  AWS's managed wide-area networking service that builds a global core network across AWS Regions from a single
  declarative policy, connecting VPCs, VPNs, Direct Connect, SD-WAN and transit gateways into segmented routing domains.
verified: 2026-11-17
status: current
broader: [kt:aws-networking-and-content-delivery]
related: [kt:aws-global-network, kt:multi-region-architecture, kt:aws-resource-access-manager]
relations:
  - {predicate: alternativeTo, object: kt:aws-transit-gateway}
  - {predicate: integratesWith, object: kt:aws-direct-connect}
  - {predicate: integratesWith, object: kt:aws-site-to-site-vpn}
  - {predicate: dependsOn, object: kt:amazon-vpc}
  - {predicate: governedBy, object: kt:cloud-wan-quotas}
  - {predicate: scopedTo, object: kt:aws-govcloud-us}
sources:
  - id: s1
    title: "What is AWS Cloud WAN?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/network-manager/latest/cloudwan/what-is-cloudwan.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "AWS Cloud WAN Quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/network-manager/latest/cloudwan/cloudwan-quotas.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "AWS Cloud WAN pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/cloud-wan/pricing/
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "AWS Cloud WAN FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/cloud-wan/faqs/
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "AWS Cloud WAN now supports BGP communities on Direct Connect attachments for routing policy"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/11/cloud-wan-bgp-community-directconnect/
    type: official-blog
    accessed: 2026-11-17
  - id: s6
    title: "AWS Transit Gateway and AWS Cloud WAN now support 1:1 NAT"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/10/aws-transit-gateway-cloud-wan-1-1-nat/
    type: official-blog
    accessed: 2026-11-17
  - id: s7
    title: "Introducing AWS Cloud WAN in AWS GovCloud (US) Regions"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/publicsector/introducing-aws-cloud-wan-in-aws-govcloud-us-regions/
    type: official-blog
    accessed: 2026-11-17
created: 2026-11-17
---

# AWS Cloud WAN

AWS Cloud WAN is a managed wide-area networking service for building, operating and monitoring one global network that ties together Amazon VPCs, branch offices and data centers over the AWS global network.[^s1] Instead of configuring Regional routers one by one, you describe the whole network in a single declarative policy, and AWS provisions and keeps it consistent across Regions; the network is managed and visualized through AWS Network Manager.[^s1][^s4]

## How it works

A **global network** is the top-level container; it can hold transit gateways and one Cloud WAN **core network**, the AWS-managed part that spans the Regions you choose.[^s1][^s2] The **core network policy** is a JSON document (editable visually or as JSON) that lists those Regions, defines segments, maps attachments to segments and declares how routes are shared; you can keep many versions, only one is live, and you can roll back.[^s1] For each Region in the policy AWS deploys a **core network edge**, and the edges form a full mesh with redundant paths.[^s1]

**Attachments** connect resources to an edge: VPCs, Site-to-Site VPNs, Connect attachments for SD-WAN or appliances, transit gateway route table attachments, and Direct Connect gateways (BGP, with an ASN different from the edge's).[^s1][^s4] Each attachment lands in exactly one **segment**, an isolated routing domain that AWS compares to a globally consistent VRF or an MPLS IP VPN; by default only members of the same segment talk to each other.[^s1] Attachments can be placed explicitly or automatically by tag, and segment actions share routes across segments, for example for shared services or centralized internet egress.[^s1] **Network function groups** and **service insertion** steer traffic between segments, within or across Regions, through firewalls or IDS/IPS appliances without static routes.[^s1][^s4] **Peering** links an edge to a transit gateway in the same Region, so existing Transit Gateway estates can join gradually.[^s1] The core network owner can share it via AWS Resource Access Manager, letting other accounts attach their VPCs without core network permissions.[^s1]

## Quotas, pricing and gotchas

Default quotas (as of 2026-11): 5 global networks per account (adjustable); 1 core network per global network and 1 edge per Region per core network (not adjustable); 40 segments per core network and 10,000 routes per core network across segments (raise via SA/TAM); 5,000 attachments per core network (adjustable); a 1 MB policy size limit (not adjustable).[^s2] A VPC attachment carries up to 100 Gbps per AZ and 7,500,000 packets per second; a VPN tunnel tops out at 1.25 Gbps and a GRE Connect peer at 5 Gbps.[^s2] MTU is 8,500 bytes between VPCs but 1,500 bytes over VPN; larger packets are dropped, and Path MTU Discovery works only for traffic entering via VPC attachments.[^s2]

Pricing (as of 2026-11) has a core network edge charge of $0.50 per hour and data processing of $0.02 per GB, both the same in every Region, plus a Region-dependent hourly fee per attachment and per transit gateway peering; AWS's US East (N. Virginia) example uses $0.065 per VPC attachment-hour.[^s3] Standard data transfer charges apply on top, and AWS's single-Region example (one edge, two VPCs, 100 GB) totals $461.90 a month, so the fixed edge cost dominates small networks.[^s3] Since October 2026, attachments can use managed 1:1 NAT to join networks with overlapping CIDRs, billed at Regional NAT gateway rates.[^s6][^s1] US West (Oregon) is the only supported home Region for the dashboard's aggregated data, and it cannot be changed.[^s1]

## When to use it (and when not)

AWS positions Transit Gateway for organizations in a few Regions that are happy to manage their own peering and routing, and Cloud WAN when you want built-in automation, segmentation, central configuration and automatic VPC attachment across many Regions.[^s4] Cloud WAN also serves as an underlay for SD-WAN, with policy able to keep some traffic (such as voice) on an existing WAN.[^s4] If you don't want a core network, use AWS Global Networks for Transit Gateways instead.[^s1]

## What changed (2026-11)

In November 2026, Cloud WAN routing policy began evaluating BGP communities on Direct Connect attachments (previously only VPN and Connect), enabling tag-driven filtering and path preference for hybrid traffic, in Regions offering both services.[^s5]

## Connections

Cloud WAN belongs to AWS networking and content delivery and is the policy-driven alternative to AWS Transit Gateway, which it can peer with.[^s4][^s1] It connects Amazon VPCs to on-premises networks through AWS Site-to-Site VPN and AWS Direct Connect, runs over the AWS global network, and is a common backbone for multi-Region architectures.[^s1][^s4] Its limits are collected in its quotas page, and sharing relies on AWS Resource Access Manager.[^s2][^s1] AWS made it generally available in AWS GovCloud (US-West and US-East) in June 2026; core networks there cannot extend into commercial Regions, and Direct Connect gateways could not yet attach directly at launch.[^s7]

## Sources
[^s1]: Amazon Web Services, "What is AWS Cloud WAN?". https://docs.aws.amazon.com/network-manager/latest/cloudwan/what-is-cloudwan.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "AWS Cloud WAN Quotas". https://docs.aws.amazon.com/network-manager/latest/cloudwan/cloudwan-quotas.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "AWS Cloud WAN pricing". https://aws.amazon.com/cloud-wan/pricing/ (accessed 2026-11-17)
[^s4]: Amazon Web Services, "AWS Cloud WAN FAQs". https://aws.amazon.com/cloud-wan/faqs/ (accessed 2026-11-17)
[^s5]: Amazon Web Services, "AWS Cloud WAN now supports BGP communities on Direct Connect attachments for routing policy" (What's New, 2026-11-12). https://aws.amazon.com/about-aws/whats-new/2026/11/cloud-wan-bgp-community-directconnect/ (accessed 2026-11-17)
[^s6]: Amazon Web Services, "AWS Transit Gateway and AWS Cloud WAN now support 1:1 NAT" (What's New, 2026-10-23). https://aws.amazon.com/about-aws/whats-new/2026/10/aws-transit-gateway-cloud-wan-1-1-nat/ (accessed 2026-11-17)
[^s7]: Amazon Web Services, "Introducing AWS Cloud WAN in AWS GovCloud (US) Regions" (AWS Public Sector Blog, 2026-06-03). https://aws.amazon.com/blogs/publicsector/introducing-aws-cloud-wan-in-aws-govcloud-us-regions/ (accessed 2026-11-17)
