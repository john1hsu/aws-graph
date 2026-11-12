---
id: kt:aws-transit-gateway
type: Service
title: AWS Transit Gateway
aliases: [Transit Gateway, TGW, AWS TGW]
domain: networking-and-content-delivery
summary: >-
  AWS's Regional, managed layer-3 router that acts as a hub connecting VPCs, Site-to-Site VPN, Direct Connect
  gateways, SD-WAN appliances and other transit gateways, with multiple route tables for network segmentation.
verified: 2026-11-12
status: current
broader: [kt:aws-networking-and-content-delivery]
related: [kt:amazon-vpc, kt:aws-cloud-wan, kt:multi-account-strategy]
relations:
  - {predicate: integratesWith, object: kt:aws-direct-connect}
  - {predicate: integratesWith, object: kt:aws-site-to-site-vpn}
  - {predicate: alternativeTo, object: kt:vpc-peering}
  - {predicate: governedBy, object: kt:transit-gateway-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is AWS Transit Gateway for Amazon VPC?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/tgw/what-is-transit-gateway.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "How AWS Transit Gateway works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/tgw/how-transit-gateways-work.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "AWS Transit Gateway Quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-quotas
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "AWS Transit Gateway pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/transit-gateway/pricing/
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "AWS Transit Gateway design best practices"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/tgw//tgw-best-design-practices.html
    type: official-docs
    accessed: 2026-11-12
  - id: s6
    title: "AWS announces general availability of Policy-Based Routing on AWS Transit Gateway"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/07/aws-transit-gateway-policy-based-routing/
    type: official-blog
    accessed: 2026-11-12
  - id: s7
    title: "AWS Transit Gateway FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/transit-gateway/faqs
    type: official-docs
    accessed: 2026-11-12
  - id: s8
    title: "In-scope AWS services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-12
created: 2026-11-12
---

# AWS Transit Gateway

AWS Transit Gateway is a network transit hub that interconnects VPCs and on-premises networks, replacing a mesh of point-to-point links with a hub-and-spoke design; inter-Region peering extends it across Regions over the AWS global network.[^s1] Technically it is a Regional virtual router that forwards IPv4 and IPv6 packets between its attachments at layer 3 by destination address, and it scales elastically with traffic.[^s2] Multiple route tables let you segment one gateway into isolated domains, which AWS likens to VRFs on traditional routers.[^s7]

## How it works

You connect networks through **attachments**: VPCs, Site-to-Site VPN connections, Direct Connect gateways, Connect attachments for SD-WAN or third-party appliances, peering with another transit gateway, VPN Concentrators, Client VPN endpoints and network function attachments.[^s1] Each attachment is **associated** with exactly one transit gateway route table, while it can **propagate** its routes into one or more tables; a new gateway gets a default table used for both association and propagation.[^s2] VPN and Direct Connect gateway attachments exchange routes with your on-premises router over BGP, whereas peering attachments need static routes.[^s1]

For a VPC attachment you pick exactly one subnet per Availability Zone, where the gateway places a network interface; only AZs with an attachment can reach the gateway, so AWS recommends enabling several AZs.[^s2] You also need a route in the VPC subnet route table pointing at the gateway.[^s2] When routes overlap, the longest prefix wins; for an identical CIDR, static routes beat propagated ones, and VPC-propagated routes beat Direct Connect, Connect and VPN routes.[^s2] **Appliance mode** keeps both directions of a flow on the same AZ interface, which stateful firewalls in an inspection VPC need to avoid dropped return traffic.[^s2] Since July 2026, **policy-based routing** can also steer traffic by source, port and protocol via ordered rules in a policy table, at no extra charge beyond standard fees, in all commercial Regions where Transit Gateway is available.[^s6]

## Quotas, pricing and gotchas

Default quotas (as of 2026-11, Region-specific unless noted): 5 transit gateways per account (adjustable), 5,000 attachments per gateway (adjustable), 20 route tables per gateway (adjustable), 10,000 combined routes per gateway (raise via your SA or TAM), 50 peering attachments per gateway (adjustable), and at most 5 transit gateways per VPC (not adjustable).[^s3] Each VPC attachment carries up to 100 Gbps per direction per AZ and up to 7,500,000 packets per second; a Connect peer (GRE tunnel) tops out at 5 Gbps, not adjustable.[^s3] MTU is 8,500 bytes for VPC, Direct Connect, Connect and peering traffic but 1,500 bytes over VPN, so migrate from VPC peering by updating both VPCs at once to avoid dropped jumbo packets.[^s3][^s5]

You pay per attachment-hour plus per GB processed; AWS's US East (Ohio) example uses $0.05 per VPC attachment-hour and $0.02 per GB of data processed (as of 2026-11).[^s4] Data arriving from a peering attachment is not charged data processing, and inter-Region transfer is billed separately.[^s4] By default, processing charges go to the owner of the source attachment, though flexible cost allocation can change that.[^s1] ECMP works for dynamic-routed VPN, Connect and Direct Connect gateway attachments, but not for VPC or peering attachments, nor across attachment types.[^s2]

## When to use it (and when not)

Use Transit Gateway when many VPCs and accounts need shared connectivity to each other and to on-premises networks, centralized inspection or egress, or segmentation by route table. AWS's design guidance is to give each VPC attachment its own small subnet (for example /28), keep the network ACL on those subnets open, prefer BGP-based VPNs, and not deploy extra gateways for high availability because the service is highly available by design; use one gateway per Region and a unique ASN on each when you have several.[^s5] For a handful of VPCs, plain VPC peering may be simpler; for a global, policy-driven WAN, AWS Cloud WAN can peer with transit gateways (one peering per gateway and core network edge pair).[^s3]

## Connections

Transit Gateway is part of AWS networking and content delivery and connects Amazon VPCs per AWS Region.[^s1][^s2] It terminates AWS Direct Connect transit traffic via Direct Connect gateways and AWS Site-to-Site VPN connections, and peers with AWS Cloud WAN.[^s1][^s3] VPC peering is the simpler alternative for small topologies, and the gateway's own limits are collected in its quotas page.[^s3] It is a common building block of a multi-account strategy, and AWS Network Manager gives a dashboard and Route Analyzer for its route tables.[^s7] AWS's FAQ notes it inherits Amazon VPC's compliance, including FedRAMP Moderate and High and HIPAA eligibility.[^s7] The current SAA-C03 exam guide lists AWS Transit Gateway as an in-scope Networking and Content Delivery service.[^s8]

## Sources
[^s1]: Amazon Web Services, "What is AWS Transit Gateway for Amazon VPC?". https://docs.aws.amazon.com/vpc/latest/tgw/what-is-transit-gateway.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "How AWS Transit Gateway works". https://docs.aws.amazon.com/vpc/latest/tgw/how-transit-gateways-work.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "AWS Transit Gateway Quotas". https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-quotas (accessed 2026-11-12)
[^s4]: Amazon Web Services, "AWS Transit Gateway pricing". https://aws.amazon.com/transit-gateway/pricing/ (accessed 2026-11-12)
[^s5]: Amazon Web Services, "AWS Transit Gateway design best practices". https://docs.aws.amazon.com/vpc/latest/tgw//tgw-best-design-practices.html (accessed 2026-11-12)
[^s6]: Amazon Web Services, "AWS announces general availability of Policy-Based Routing on AWS Transit Gateway" (What's New, 2026-07-30). https://aws.amazon.com/about-aws/whats-new/2026/07/aws-transit-gateway-policy-based-routing/ (accessed 2026-11-12)
[^s7]: Amazon Web Services, "AWS Transit Gateway FAQs". https://aws.amazon.com/transit-gateway/faqs (accessed 2026-11-12)
[^s8]: Amazon Web Services, "In-scope AWS services - SAA-C03". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-12)
