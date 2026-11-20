---
id: kt:subnets-and-route-tables
type: Resource
title: Subnets and route tables
aliases: [VPC subnets, Subnet route tables, VPC route tables, Main route table]
domain: networking-and-content-delivery
summary: >-
  The two building blocks that shape traffic inside an Amazon VPC: subnets carve the VPC's
  CIDR range into smaller address blocks, and route tables, associated with subnets, decide
  where packets leaving each subnet are sent, using longest-prefix matching.
verified: 2026-11-20
status: current
broader: [kt:amazon-vpc]
related: [kt:security-groups-vs-network-acls, kt:container-networking-on-aws, kt:aws-transit-gateway]
relations:
  - {predicate: partOf, object: kt:amazon-vpc}
  - {predicate: governedBy, object: kt:vpc-quotas}
  - {predicate: integratesWith, object: kt:aws-site-to-site-vpn}
  - {predicate: integratesWith, object: kt:aws-direct-connect}
  - {predicate: integratesWith, object: kt:gateway-load-balancer}
  - {predicate: integratesWith, object: kt:vpc-peering}
sources:
  - id: s1
    title: "Subnet CIDR blocks - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/userguide/subnet-sizing.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Subnet route tables - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/userguide/subnet-route-tables.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "How route priority works - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_en/vpc/latest/userguide/route-tables-priority.html
    type: official-docs
    accessed: 2026-11-20
created: 2026-11-20
---

# Subnets and route tables

A **subnet** is a range of addresses carved out of an Amazon VPC's CIDR block, and a **route table** is the set of destination/target rules that decides where traffic leaving a subnet goes.[^s1][^s2] Together they turn a flat address space into tiers: whether a subnet is "public" or "private" is not a property of the subnet itself but of the routes in the table it is associated with, for example whether it has a `0.0.0.0/0` route to an internet gateway.[^s2]

## Subnet sizing and reserved addresses

An IPv4 subnet can be anywhere from a /28 to a /16, and subnets in the same VPC cannot have overlapping CIDR blocks. A single subnet may use the whole VPC CIDR, or you can split it, for example a /24 into two /25s.[^s1] IPv6 subnets must come from an IPv6 CIDR associated with the VPC and can be /44 to /64 in steps of /4; IPv6 can be added to an existing subnet or at creation.[^s1]

AWS keeps five addresses in every subnet: the first four and the last. In a `10.0.0.0/24` subnet, `.0` is the network address, `.1` is the VPC router, `.2` is reserved for DNS (the Amazon DNS server sits at the VPC base plus two), `.3` is held for future use, and `.255` is reserved because a VPC does not support broadcast.[^s1] IPv6 subnets likewise lose their first four and last addresses.[^s1] The practical gotcha is that a /28 yields only 11 usable addresses, which matters for services that consume one IP per task or pod. A CIDR passed through the CLI or EC2 API is canonicalized, so `100.68.0.18/18` becomes `100.68.0.0/18`; with BYOIP ranges, every address, including network and broadcast, is usable.[^s1]

## How route tables work

Every subnet must be associated with exactly one route table at a time, while one table can serve many subnets. A subnet with no explicit association uses the VPC's **main route table**, which is created with the VPC and, in a non-default VPC, initially contains only the local route.[^s2] You can edit the main table but not delete it, and you can't make a gateway route table the main table. AWS recommends leaving the main table in its default state and explicitly associating each subnet with a custom table; a custom table can be deleted only when nothing is associated with it.[^s2] To swap the main table safely, test the new table on one explicitly associated subnet first, then promote it.[^s2]

Each table holds a **local route** for every VPC CIDR (IPv4 and IPv6) so traffic inside the VPC always works. IPv4 and IPv6 are routed independently, so an IPv6 default route `::/0` must be added separately from `0.0.0.0/0`.[^s2][^s3] You can add routes more specific than the local route only when the destination matches an entire subnet CIDR and the target is a NAT gateway, network interface or Gateway Load Balancer endpoint, which is how middlebox inspection appliances are inserted between subnets.[^s2] Routes to `169.254.168.0/22` and `fd00:ec2::/32` are not allowed.[^s2] On AWS Outposts, subnets can also target a local gateway.[^s2]

## Route priority

When several routes match, the VPC router picks the **longest prefix** first, whatever the route type: a /32 beats a /24.[^s3] For routes with the same destination, static routes (internet gateway, NAT gateway, network interface, instance, gateway endpoint, transit gateway, peering connection, Gateway Load Balancer endpoint) win over prefix-list routes, which win over propagated routes. Among propagated routes, Direct Connect BGP routes come first, then Site-to-Site VPN static routes, then VPN BGP routes.[^s3] If overlapping prefix lists point to different targets, AWS picks one at random and keeps that choice stable, so avoid overlaps.[^s3] Propagated routes appear once you attach a virtual private gateway and turn on route propagation for the table.[^s3]

## When to use which design

Use separate route tables per tier (public, private, isolated) and per Availability Zone when each zone has its own NAT gateway or appliance, so a zone failure doesn't strand another zone's traffic. Use explicit associations rather than relying on the main table, so a new subnet never inherits internet access by accident.[^s2] Size subnets generously up front: you cannot overlap CIDRs, and IP-hungry workloads such as containers exhaust small subnets quickly.[^s1]

## Connections

Subnets and route tables are **part of** Amazon VPC, and their counts and route entries are **governed by** the VPC quotas.[^s2] Route tables are where **VPC peering**, **AWS Transit Gateway**, **Site-to-Site VPN** and **AWS Direct Connect** become reachable, and the priority rules decide between them when prefixes overlap.[^s3] **Gateway Load Balancer** endpoints are inserted through more-specific subnet routes.[^s2] Subnet-level traffic filtering is the job of network ACLs, compared in **security groups vs network ACLs**, and pod and task IP planning is covered in **container networking on AWS**.[^s1]

## Sources
[^s1]: Amazon Web Services, "Subnet CIDR blocks - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/userguide/subnet-sizing.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "Subnet route tables - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/userguide/subnet-route-tables.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "How route priority works - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/en_en/vpc/latest/userguide/route-tables-priority.html (accessed 2026-11-20)
