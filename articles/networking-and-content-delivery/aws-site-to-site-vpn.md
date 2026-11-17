---
id: kt:aws-site-to-site-vpn
type: Service
title: AWS Site-to-Site VPN
aliases: [Site-to-Site VPN, S2S VPN]
domain: networking-and-content-delivery
summary: >-
  AWS's managed IPsec VPN service that connects an on-premises or remote network to Amazon VPCs over the
  internet (or privately over Direct Connect), with two redundant tunnels per connection terminating on a
  virtual private gateway, transit gateway or AWS Cloud WAN.
verified: 2026-11-17
status: current
broader: [kt:aws-networking-and-content-delivery]
related: [kt:amazon-vpc, kt:aws-cloud-wan, kt:aws-client-vpn]
relations:
  - {predicate: alternativeTo, object: kt:aws-direct-connect}
  - {predicate: integratesWith, object: kt:aws-transit-gateway}
  - {predicate: integratesWith, object: kt:aws-global-accelerator}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is AWS Site-to-Site VPN? - AWS Site-to-Site VPN User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "AWS Site-to-Site VPN quotas - AWS Site-to-Site VPN User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpn/latest/s2svpn/vpn-limits.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "AWS VPN Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/vpn/pricing/
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "Introducing AWS Site-to-Site VPN 5 Gbps Tunnels to support high throughput workloads"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/networking-and-content-delivery/introducing-aws-site-to-site-vpn-5-gbps-tunnels-to-support-high-throughput-workloads
    type: official-blog
    accessed: 2026-11-17
  - id: s5
    title: "AWS Site-to-Site VPN now supports modifying tunnel bandwidth on existing VPN connections"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/05/aws-site-to-site-vpn-modify-bandwidth/
    type: official-blog
    accessed: 2026-11-17
  - id: s6
    title: "AWS Site-to-Site VPN Concentrators - AWS Site-to-Site VPN User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpn/latest/s2svpn/vpn-concentrator.html
    type: official-docs
    accessed: 2026-11-17
  - id: s7
    title: "AWS Site-to-Site VPN in AWS GovCloud (US) - AWS GovCloud (US) User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-vpn.html
    type: official-docs
    accessed: 2026-11-17
  - id: s8
    title: "Exam Guide (SAA-C03) AWS Certified Solutions Architect - Associate"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.pdf
    type: official-learning
    accessed: 2026-11-17
  - id: s9
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-17
created: 2026-11-17
---

# AWS Site-to-Site VPN

AWS Site-to-Site VPN is the managed IPsec service that lets resources in an Amazon VPC talk to a remote network such as an on-premises data center, which by default they cannot reach.[^s1] You create a VPN connection, then configure routing so traffic for the remote network flows through it.[^s1] Each connection contains two encrypted tunnels that can carry traffic at the same time, giving built-in redundancy on the AWS side.[^s1] It is the quick, internet-based counterpart to a dedicated AWS Direct Connect circuit.

## How it works

The AWS side of a connection is a **target gateway**: a **virtual private gateway** attached to exactly one VPC, or a **transit gateway** that interconnects many VPCs and on-premises networks; AWS Cloud WAN can also terminate connections.[^s1] Your side is a **customer gateway device** (a physical appliance or software), described to AWS by a **customer gateway** resource.[^s1] Tunnels use IKEv2, support NAT traversal, AES-256 encryption with SHA-2 hashing, and can authenticate with pre-shared keys or private certificates issued by AWS Private Certificate Authority.[^s1] Routing can be static or BGP; the Amazon side accepts a custom private ASN, and the customer gateway uses a 2-byte ASN (1–65535).[^s1] Tunnel metrics are published to Amazon CloudWatch.[^s1]

IPv6 inner traffic is supported, and IPv6 outer tunnel addresses are available on transit gateway and Cloud WAN connections, but not on a virtual private gateway; one connection cannot carry IPv4 and IPv6 traffic at once, and an existing IPv4 connection cannot be converted to IPv6.[^s1] **Accelerated Site-to-Site VPN** routes tunnels through two AWS Global Accelerator accelerators that AWS creates for you, entering the AWS network at the nearest edge.[^s1][^s3]

Since late 2025 there are two tunnel sizes. A **standard** tunnel carries up to 1.25 Gbps; a **Large Bandwidth Tunnel** carries up to 5 Gbps but is available only on transit gateway and Cloud WAN attachments, not on a virtual private gateway.[^s1][^s4] Since May 2026 you can switch an existing connection between the two sizes in place, keeping its tunnel IP addresses and pre-shared keys, in the Regions AWS lists for that feature.[^s5] For many small branches, a **Site-to-Site VPN Concentrator** on a transit gateway aggregates up to 5 Gbps across sites at up to 100 Mbps per site; it requires BGP and is not supported on Cloud WAN or a virtual private gateway.[^s6]

## Quotas, pricing and gotchas

Default quotas (as of 2026-11, per Region, all adjustable): 50 customer gateways, 5 virtual private gateways, 50 VPN connections, 10 connections per virtual private gateway, 10 accelerated connections and 50 Large Bandwidth Tunnel connections.[^s2] Route limits are not adjustable: a VPN on a virtual private gateway accepts 100 dynamic (or 100 static) routes from your device, while one on a transit gateway accepts 1,000 dynamic routes and advertises up to 5,000.[^s2] Per-tunnel ceilings are about 140,000 packets per second for standard and 400,000 for large tunnels; MTU is 1,446 bytes with MSS 1,406, jumbo frames and Path MTU Discovery are unsupported.[^s2][^s1]

Pricing (as of 2026-11) is per connection-hour plus standard data transfer out; AWS's US East (Ohio) example uses $0.05 per hour for a standard connection, and a 5 Gbps connection is $0.60 per hour.[^s3] A Concentrator costs $1.95 per hour plus $0.01 per hour per connected site, accelerated VPN adds two Global Accelerator charges and premium data transfer, each connection also consumes billable public IPv4 addresses, and transit gateway attachments bill separately.[^s3]

Gotchas: a single flow is pinned to one tunnel, so aggregating bandwidth requires ECMP across tunnels on a transit gateway or Cloud WAN, which only works with dynamic (BGP) routing.[^s2][^s4] Two 5 Gbps tunnels can reach 10 Gbps per connection, but your appliance must hash flows evenly and handle the throughput.[^s4] In AWS GovCloud (US), endpoints use FIPS 140-3 validated modules (so different algorithms apply), Accelerated VPN is unavailable, and VPN configuration metadata must not contain export-controlled data.[^s7]

## When to use it (and when not)

Choose Site-to-Site VPN when you need encrypted hybrid connectivity quickly, without ordering circuits, or as a backup or overlay for Direct Connect; AWS positions 5 Gbps connections for that backup role on high-capacity links.[^s4] Prefer a transit gateway or Cloud WAN target for multi-VPC estates, IPv6 outer tunnels or large tunnels, and keep the virtual private gateway for a simple single-VPC link.[^s1][^s4] For sustained, latency-sensitive traffic beyond what tunnels can carry, Direct Connect is the better fit; for individual users rather than whole sites, AWS Client VPN is the relevant service.[^s8]

## Connections

Site-to-Site VPN belongs to the AWS networking and content delivery category and extends Amazon VPC, terminating on AWS Transit Gateway or AWS Cloud WAN for larger designs.[^s1] Its accelerated option builds on AWS Global Accelerator, its tunnels report to Amazon CloudWatch, and quotas apply per AWS Region.[^s1][^s2][^s3] It is the internet-based alternative to AWS Direct Connect.[^s4] AWS Site-to-Site VPN is listed in scope in the current SAA-C03 exam guide (alongside AWS Client VPN) and in the CLF-C02 in-scope services list.[^s8][^s9]

## Sources
[^s1]: Amazon Web Services, "What is AWS Site-to-Site VPN?". https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "AWS Site-to-Site VPN quotas". https://docs.aws.amazon.com/vpn/latest/s2svpn/vpn-limits.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "AWS VPN Pricing". https://aws.amazon.com/vpn/pricing/ (accessed 2026-11-17)
[^s4]: Amazon Web Services, "Introducing AWS Site-to-Site VPN 5 Gbps Tunnels to support high throughput workloads" (Networking & Content Delivery Blog). https://aws.amazon.com/blogs/networking-and-content-delivery/introducing-aws-site-to-site-vpn-5-gbps-tunnels-to-support-high-throughput-workloads (accessed 2026-11-17)
[^s5]: Amazon Web Services, "AWS Site-to-Site VPN now supports modifying tunnel bandwidth on existing VPN connections" (What's New, 2026-05-06). https://aws.amazon.com/about-aws/whats-new/2026/05/aws-site-to-site-vpn-modify-bandwidth/ (accessed 2026-11-17)
[^s6]: Amazon Web Services, "AWS Site-to-Site VPN Concentrators". https://docs.aws.amazon.com/vpn/latest/s2svpn/vpn-concentrator.html (accessed 2026-11-17)
[^s7]: Amazon Web Services, "AWS Site-to-Site VPN in AWS GovCloud (US)". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-vpn.html (accessed 2026-11-17)
[^s8]: Amazon Web Services, "Exam Guide (SAA-C03) AWS Certified Solutions Architect - Associate". https://docs.aws.amazon.com/pdfs/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.pdf (accessed 2026-11-17)
[^s9]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html (accessed 2026-11-17)
