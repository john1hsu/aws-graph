---
id: kt:aws-global-network
type: InfrastructureConcept
title: AWS Global Network
aliases: [AWS global backbone, AWS backbone network, AWS backbone, AWS private backbone]
domain: global-infrastructure
summary: >-
  The private, AWS-operated fiber backbone that interconnects Regions, Availability Zones, edge
  locations and Direct Connect locations, carrying inter-Region and edge traffic off the public
  internet with redundant paths and layered, automatic encryption.
verified: 2026-11-15
status: concept
broader: [kt:aws-global-infrastructure]
related: [kt:edge-locations, kt:aws-regions]
relations:
  - {predicate: usedBy, object: kt:aws-global-accelerator}
  - {predicate: usedBy, object: kt:aws-direct-connect}
  - {predicate: usedBy, object: kt:amazon-vpc}
  - {predicate: usedBy, object: kt:aws-transit-gateway}
sources:
  - id: s1
    title: "AWS Global Network"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/global-infrastructure/global-network/
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Data protection in Amazon EC2 - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/data-protection.html
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Amazon VPC FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/vpc/faqs/
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "How AWS Global Accelerator works - AWS Global Accelerator"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/global-accelerator/latest/dg/introduction-how-it-works.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "Access to AWS Regions - AWS Direct Connect"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/directconnect/latest/UserGuide/remote_regions.html
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "Building resilience: Inside AWS's nine million kilometers of fiber-optic cabling"
    publisher: Amazon Web Services (AWS Insights blog)
    url: https://aws.amazon.com/blogs/aws-insights/building-resilience-inside-awss-nine-million-kilometers-of-fiber-optic-cabling/
    type: official-blog
    accessed: 2026-11-15
created: 2026-11-15
---

# AWS Global Network

The AWS Global Network is the private backbone AWS builds and operates to connect its Regions, Availability Zones, points of presence and Direct Connect locations. AWS describes it as the largest private network of any cloud provider, built from terrestrial and subsea fiber plus AWS's own custom routers, switches and optical equipment.[^s1] It is not a service you provision; it is the transport underneath services such as inter-Region VPC peering, Transit Gateway peering, Global Accelerator, CloudFront and Direct Connect, and it is the reason traffic between those endpoints can avoid the public internet.[^s3][^s4][^s5]

## How it works

**Scale and capacity (as of 2026-11).** AWS's network page cites nearly 20 million kilometers of fiber and says most capacity connected to the backbone now runs at 400 GbE, four times the earlier 100 GbE standard. It also lists more than 140 Direct Connect locations.[^s1] These are AWS's own figures and change often, so re-check them before quoting.

**Redundancy.** AWS runs diverse routes through each major subsea corridor and multiple independent paths between Regions, so a failed path reroutes automatically. When landslide damage cut four subsea cables off Abidjan in 2024, AWS kept African customers connected through alternative routes along the west coast.[^s6] A central monitoring system watches every link and continuously adjusts routing, and AWS says most network events are mitigated without human intervention.[^s1]

**Encryption in layers.** All data that crosses AWS Regions over the global network is encrypted at the physical layer before it leaves AWS-secured facilities, and all traffic between AZs is encrypted.[^s2] AWS's network page describes optical encryption at layer 1 and MACsec at layer 2, with TLS and QUIC available at higher layers.[^s1] Cross-Region VPC peering and Transit Gateway peering traffic gets a further layer of bulk encryption when it exits a Region.[^s2] Customer-applied encryption (TLS, VPN) still sits on top, and data encrypted before entering the network stays encrypted.[^s6]

## What it means for architectures

- **Inter-Region VPC peering** rides the AWS backbone with built-in redundancy, AEAD encryption and no single point of failure or bandwidth bottleneck; if a peering connection fails, traffic does not fall back to the internet.[^s3]
- **Global Accelerator** advertises anycast static IPs from the edge, pulls client traffic onto the global network at the nearest edge location, and carries it to your endpoints, avoiding multiple public-internet hops.[^s4]
- **Direct Connect** from a location in a public Region can reach all public Regions except the China Regions, plus GovCloud (US) and Local Zones, and AWS states that this traffic stays on the backbone whether it goes to public services or a VPC.[^s5] Outbound data is billed at the source Region's rate, not the connection's associated Region.[^s5]

## Gotchas

- Backbone transport is not free transport: inter-Region and Direct Connect data transfer is still metered by the Region the data leaves.[^s5]
- Network-level encryption does not replace application-layer encryption when a compliance regime asks you to control keys end to end; AWS itself recommends adding TLS or VPN where needed.[^s6]
- Nitro-based instance-to-instance encryption is separate from backbone encryption. It applies only within one Region, between supported instance types in the same or peered VPCs, and not through load balancers or transit gateways.[^s2]

## Connections

- **Broader: AWS global infrastructure** (`kt:aws-global-infrastructure`). The global network is the fiber that joins the Regions, AZs and edge sites described there.[^s1]
- **Related: AWS Regions** (`kt:aws-regions`) and **edge locations** (`kt:edge-locations`), the endpoints the backbone interconnects.[^s2][^s4]
- **Used by Amazon VPC** (`kt:amazon-vpc`) for inter-Region peering, and by **AWS Transit Gateway** (`kt:aws-transit-gateway`) for inter-Region peering.[^s2][^s3]
- **Used by AWS Global Accelerator** (`kt:aws-global-accelerator`) to carry user traffic from edge to endpoint.[^s4]
- **Used by AWS Direct Connect** (`kt:aws-direct-connect`) to reach remote Regions from any location.[^s5]

## Sources
[^s1]: Amazon Web Services, "AWS Global Network". https://aws.amazon.com/about-aws/global-infrastructure/global-network/ (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Data protection in Amazon EC2 - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/data-protection.html (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Amazon VPC FAQs". https://aws.amazon.com/vpc/faqs/ (accessed 2026-11-15)
[^s4]: Amazon Web Services, "How AWS Global Accelerator works - AWS Global Accelerator". https://docs.aws.amazon.com/global-accelerator/latest/dg/introduction-how-it-works.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "Access to AWS Regions - AWS Direct Connect". https://docs.aws.amazon.com/directconnect/latest/UserGuide/remote_regions.html (accessed 2026-11-15)
[^s6]: Amazon Web Services, AWS Insights blog, "Building resilience: Inside AWS's nine million kilometers of fiber-optic cabling". https://aws.amazon.com/blogs/aws-insights/building-resilience-inside-awss-nine-million-kilometers-of-fiber-optic-cabling/ (accessed 2026-11-15)
