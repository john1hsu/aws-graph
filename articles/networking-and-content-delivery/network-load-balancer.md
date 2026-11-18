---
id: kt:network-load-balancer
type: Service
title: Network Load Balancer
aliases: [NLB, AWS Network Load Balancer, Elastic Load Balancing Network Load Balancer]
domain: networking-and-content-delivery
summary: >-
  The layer-4 load balancer type in Elastic Load Balancing: it forwards TCP, UDP, TLS and QUIC connections
  by flow hash to target groups of instances, IP addresses or an Application Load Balancer, with one static
  IP address per Availability Zone and optional Elastic IPs.
verified: 2026-11-18
status: current
broader: [kt:elastic-load-balancing, kt:aws-networking-and-content-delivery]
related: [kt:application-load-balancer, kt:amazon-route-53, kt:amazon-ec2-auto-scaling]
relations:
  - {predicate: integratesWith, object: kt:aws-privatelink}
  - {predicate: integratesWith, object: kt:amazon-ecs}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: governedBy, object: kt:elb-quotas}
  - {predicate: scopedTo, object: kt:availability-zones}
sources:
  - id: s1
    title: "What is a Network Load Balancer? - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Quotas for your Network Load Balancers - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-limits.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Target groups for your Network Load Balancers - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest//network/load-balancer-target-groups.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Network Load Balancers - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancers
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Update the security groups for your Network Load Balancer - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-security-groups.html
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "Elastic Load Balancing pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/elasticloadbalancing/pricing/
    type: official-docs
    accessed: 2026-11-18
  - id: s7
    title: "AWS Network Load Balancer now supports Listener Rules for custom traffic routing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/07/aws-network-load-balancer-supports-listener-rules/
    type: official-blog
    accessed: 2026-11-18
  - id: s8
    title: "Consolidate dual-stack architectures with listener rules for Network Load Balancer"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/networking-and-content-delivery/consolidate-dual-stack-architectures-with-listener-rules-for-network-load-balancer/
    type: official-blog
    accessed: 2026-11-18
created: 2026-11-18
---

# Network Load Balancer

A Network Load Balancer (NLB) is the Elastic Load Balancing product that works at layer 4, the transport layer of the OSI model.[^s1] It accepts connections on TCP, UDP, TCP_UDP, TLS, QUIC and TCP_QUIC listeners and spreads them across healthy targets in one or more Availability Zones, scaling automatically to millions of requests per second.[^s1] Unlike the layer-7 Application Load Balancer, it does not parse HTTP; it picks a target per connection or flow and passes the bytes through.[^s1]

## How it works

An NLB has the same building blocks as other ELB types: the **load balancer** clients connect to, **listeners** on a protocol and port, and **target groups** that hold registered targets and own the health-check settings.[^s1] For TCP, the node picks a target with a flow hash over protocol, source and destination IP and port, and TCP sequence number, and the connection stays on that target for its life; UDP uses the same hash without the sequence number, and QUIC routes on the server ID in the connection ID.[^s1]

Enabling an Availability Zone creates a load balancer node there with a network interface and a **static IP address**; an internet-facing NLB can instead use one Elastic IP per subnet, which is the classic reason to pick NLB when partners must allow-list fixed addresses.[^s1] Cross-zone load balancing is **off by default**, so each node sends traffic only to targets in its own zone; it can be turned on for the load balancer or per target group.[^s1][^s4] If a zone has no healthy targets, its IP is removed from DNS and the other zones keep serving.[^s1]

Targets can be EC2 instance IDs, private IP addresses (including on-premises addresses reached over Direct Connect or VPN), or a single Application Load Balancer; NLB does not support Lambda targets.[^s3][^s4] Client IP preservation is on by default except for `ip` target groups using TCP or TLS, and it cannot be turned off for UDP, TCP_UDP and QUIC groups.[^s3] An NLB can be `ipv4` or `dualstack`, publishing A and AAAA records for the latter.[^s4]

Since July 2026 an NLB listener can also carry **listener rules**. They match only the client's IP version (IPv4 or IPv6), so a single dual-stack NLB can send each family to a target group of the same type without translation, at no extra charge, in all commercial and GovCloud (US) Regions.[^s7] Rules are evaluated in priority order with a default action as fallback, and require a dual-stack load balancer.[^s8]

## Quotas, pricing and gotchas

As of 2026-11, per Region defaults are 50 NLBs (adjustable), 50 listeners per NLB (fixed), 25 certificates per NLB (adjustable), 3,000 targets per NLB and 500 targets per zone per NLB (adjustable), and 100 rules per NLB with 5 target groups per rule action (fixed).[^s2] With cross-zone enabled the ceiling is 500 targets per load balancer regardless of zone count, and an ALB target counts as 50 or 100 targets.[^s2] The 3,000 target groups per Region quota is shared with ALBs, and reserved NLCUs per Region default to 0 until you request an increase.[^s2]

In US East (N. Virginia), as of 2026-11, an NLB costs $0.0225 per hour plus $0.006 per NLCU-hour.[^s6] One NLCU covers 800 new TCP connections per second, 100,000 active TCP connections or 1 GB processed per hour; UDP and TLS have lower connection allowances, and you pay on the highest dimension.[^s6]

Gotchas worth knowing:

- **Security groups are a creation-time decision.** An NLB created without any security group can never have one added; with at least one, the set can be changed later.[^s5] Inbound rules apply to PrivateLink traffic too unless you turn that enforcement off.[^s5]
- **Idle timeouts.** TCP defaults to 350 seconds (configurable 60–6000); TLS is fixed at 350 and UDP at 120. Raising TCP above 350 means raising the target ENI's connection-tracking timeout as well, or connections drop silently.[^s4]
- **Hairpinning.** A target behind an internal NLB that calls the same NLB, registered by instance ID, can time out; register by IP instead.[^s3]
- **Port exhaustion.** With client IP preservation off, each NLB IP and target pair handles about 55,000 connections per minute.[^s3]
- **DNS caching.** Zonal failover relies on DNS with a 60-second TTL, so clients that cache IPs keep hitting a failed zone.[^s3]

## When to use it (and when not)

Pick NLB for non-HTTP protocols, UDP, extreme connection rates, long-lived connections, fixed IP addresses or source-IP preservation, and for services reached over AWS PrivateLink, whose traffic its security groups can still filter.[^s1][^s3][^s5] Choose an Application Load Balancer instead when you need path or host routing, user authentication, Lambda targets or HTTP-aware features; you can also chain them by registering an ALB as the NLB's target to get static IPs in front of layer-7 routing.[^s3]

## Connections

The NLB is one of the load balancer types **within** Elastic Load Balancing and a sibling of the Application Load Balancer.[^s1] It is **scoped to** Availability Zones, with one node per enabled zone.[^s4] It **integrates with** AWS PrivateLink, whose traffic its security groups can filter, and with Amazon ECS, which registers tasks on dynamic ports; target groups attach to EC2 Auto Scaling groups.[^s5][^s1] It is **monitored by** Amazon CloudWatch metrics such as `ZonalHealthStatus` and the security-group blocked-flow counts, uses Amazon Route 53 alias records for custom domains, and is **governed by** the Elastic Load Balancing quotas above.[^s4][^s5][^s2]

## Sources
[^s1]: Amazon Web Services, "What is a Network Load Balancer?". https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Quotas for your Network Load Balancers". https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-limits.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Target groups for your Network Load Balancers". https://docs.aws.amazon.com/elasticloadbalancing/latest//network/load-balancer-target-groups.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Network Load Balancers". https://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancers (accessed 2026-11-18)
[^s5]: Amazon Web Services, "Update the security groups for your Network Load Balancer". https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-security-groups.html (accessed 2026-11-18)
[^s6]: Amazon Web Services, "Elastic Load Balancing pricing". https://aws.amazon.com/elasticloadbalancing/pricing/ (accessed 2026-11-18)
[^s7]: Amazon Web Services, "AWS Network Load Balancer now supports Listener Rules for custom traffic routing" (What's New, 2026-07-22). https://aws.amazon.com/about-aws/whats-new/2026/07/aws-network-load-balancer-supports-listener-rules/ (accessed 2026-11-18)
[^s8]: Amazon Web Services, "Consolidate dual-stack architectures with listener rules for Network Load Balancer", Networking & Content Delivery Blog. https://aws.amazon.com/blogs/networking-and-content-delivery/consolidate-dual-stack-architectures-with-listener-rules-for-network-load-balancer/ (accessed 2026-11-18)
