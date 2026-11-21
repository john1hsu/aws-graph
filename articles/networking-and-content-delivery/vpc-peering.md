---
id: kt:vpc-peering
type: Resource
title: VPC peering
aliases: [VPC peering connection, Amazon VPC peering, inter-Region VPC peering]
domain: networking-and-content-delivery
summary: >-
  A point-to-point networking connection between two Amazon VPCs, in the same or different
  accounts and Regions, that routes traffic between them over private IPv4 or IPv6 addresses
  without an internet gateway, VPN or Direct Connect.
verified: 2026-11-21
status: current
broader: [kt:amazon-vpc]
related: [kt:subnets-and-route-tables, kt:security-groups-vs-network-acls]
relations:
  - {predicate: alternativeTo, object: kt:aws-transit-gateway}
  - {predicate: governedBy, object: kt:vpc-quotas}
  - {predicate: pricedBy, object: kt:data-transfer-costs}
  - {predicate: dependsOn, object: kt:subnets-and-route-tables}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "VPC peering connections - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html
    type: official-docs
    accessed: 2026-11-21
  - id: s2
    title: "How VPC peering connections work - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-basics.html
    type: official-docs
    accessed: 2026-11-21
  - id: s3
    title: "VPC peering connection quotas for an account - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-connection-quotas.html
    type: official-docs
    accessed: 2026-11-21
  - id: s4
    title: "VPC peering - Building a Scalable and Secure Multi-VPC AWS Network Infrastructure"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/building-scalable-secure-multi-vpc-network-infrastructure/vpc-peering.html
    type: official-docs
    accessed: 2026-11-21
created: 2026-11-21
---

# VPC peering

A VPC peering connection links two virtual private clouds so that resources in each can talk to the other using private IPv4 or IPv6 addresses, as if they shared a network. The two VPCs can sit in the same Region or in different Regions, and in the same or different AWS accounts. Peered traffic never crosses the public internet, a VPN connection or an AWS Direct Connect link.[^s1] AWS describes the connectivity as full bidirectional but strictly point-to-point.[^s4]

## How it works

Setup is a request-and-accept handshake. The owner of the requester VPC sends a request to the owner of the accepter VPC, which may be in another account; the two VPCs' CIDR blocks must not overlap. Once the accepter agrees, the connection becomes active, but nothing flows until **both** owners add routes in their route tables pointing the peer's IP range at the peering connection. Security groups may also need new rules; within one Region you can reference a security group in the peer VPC as a source or destination.[^s2]

A connection moves through defined lifecycle states. A pending request expires after 7 days if nobody acts on it, and expired or rejected requests stay visible for a short window (for example 2 days to both owners for an expired one). Once active, either owner can delete the connection, but it can no longer be rejected. AWS notes that a Regional event that halts traffic does not change the status from Active.[^s2]

DNS needs a deliberate choice. By default, a peer instance's public DNS hostname resolves to its public IP; enabling DNS resolution support on the connection makes it resolve to the private IP instead. For inter-Region peering this option is required to get private-IP answers even when both VPCs use RFC 1918 ranges.[^s2]

## Quotas, limits and gotchas

As of 2026-11, the documented account quotas are 50 active peering connections per VPC (adjustable up to 125), 25 outstanding peering requests (adjustable), and a fixed 1-week (168-hour) expiry for unaccepted requests, which cannot be changed.[^s3] Only one peering connection can exist between any pair of VPCs.[^s2]

The behavioural limits matter more than the numbers:

- **No overlapping CIDRs.** If either VPC has any IPv4 or IPv6 block overlapping the other's, peering is refused, even when you only intend to route the non-overlapping ranges.[^s2]
- **No transitive peering.** If A peers with B and with C, B still cannot reach C through A; B and C need their own connection.[^s2]
- **No edge-to-edge routing.** A peer cannot use the other VPC's internet gateway, NAT device, VPN or Direct Connect connection, or S3 gateway endpoint.[^s2]
- **MTU differs by scope:** 9001 bytes for same-Region peering, 8500 bytes across Regions.[^s2]
- **No unicast reverse path forwarding**, and you cannot query the Amazon DNS server in the peer VPC.[^s2]
- In shared VPCs, only the VPC owner can create, accept, modify or delete peering connections.[^s2]

On cost, the multi-VPC whitepaper states that peered traffic staying within one Availability Zone is free, traffic crossing AZs is billed at standard in-Region data transfer rates, and cross-Region peering pays standard inter-Region transfer rates.[^s4] The same paper calls peering the lowest-cost, highest-aggregate-performance option for inter-VPC connectivity.[^s4]

## When to use it (and when not)

AWS's guidance is to use peering when two VPCs need to exchange traffic, both environments are controlled and secured, and fewer than 10 VPCs are involved.[^s4] The catch is scale: a full mesh needs n(n−1)/2 connections, so 100 VPCs would need 4,950 peerings, with route tables to maintain on every side, and each VPC is capped at 125 active peerings.[^s4] Because routing is not transitive, every VPC also needs its own path to on-premises networks rather than borrowing a neighbour's.[^s4]

Past that point, the whitepaper's alternative is a hub such as a transit gateway, which avoids the mesh and lets VPCs share hybrid connectivity.[^s4]

## Connections

Peering is a resource inside **Amazon VPC** and depends on **subnets and route tables**, since each side must add routes to the peering connection before traffic flows.[^s2] **Security groups** control what the peered traffic may reach, including cross-VPC group references within a Region.[^s2] **AWS Transit Gateway** is its main alternative once the number of VPCs grows.[^s4] Its account limits belong with the wider **VPC quotas**, and its charges are a case of **data transfer costs**: free within an AZ, billed across AZs and Regions.[^s3][^s4] A single connection can link VPCs in different **AWS Regions**.[^s1]

## Sources

[^s1]: Amazon Web Services, "VPC peering connections - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html (accessed 2026-11-21)
[^s2]: Amazon Web Services, "How VPC peering connections work - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-basics.html (accessed 2026-11-21)
[^s3]: Amazon Web Services, "VPC peering connection quotas for an account - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-connection-quotas.html (accessed 2026-11-21)
[^s4]: Amazon Web Services, "VPC peering - Building a Scalable and Secure Multi-VPC AWS Network Infrastructure". https://docs.aws.amazon.com/whitepapers/latest/building-scalable-secure-multi-vpc-network-infrastructure/vpc-peering.html (accessed 2026-11-21)
