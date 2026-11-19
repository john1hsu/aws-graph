---
id: kt:gateway-load-balancer
type: Service
title: Gateway Load Balancer
aliases: [GWLB, AWS Gateway Load Balancer, Elastic Load Balancing Gateway Load Balancer]
domain: networking-and-content-delivery
summary: >-
  The layer-3 load balancer type in Elastic Load Balancing that inserts fleets of third-party virtual
  appliances (firewalls, IDS/IPS, deep packet inspection) transparently into a traffic path, exchanging
  packets with them over GENEVE on port 6081 and reaching consumer VPCs through Gateway Load Balancer endpoints.
verified: 2026-11-19
status: current
broader: [kt:elastic-load-balancing, kt:aws-networking-and-content-delivery]
related: [kt:network-load-balancer, kt:aws-transit-gateway, kt:aws-network-firewall]
relations:
  - {predicate: dependsOn, object: kt:aws-privatelink}
  - {predicate: integratesWith, object: kt:amazon-vpc}
  - {predicate: governedBy, object: kt:elb-quotas}
  - {predicate: scopedTo, object: kt:availability-zones}
sources:
  - id: s1
    title: "What is a Gateway Load Balancer? - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Gateway Load Balancers - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/gateway-load-balancers.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Target groups for your Gateway Load Balancers - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/target-groups.md
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "Quotas for your Gateway Load Balancers - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/quotas-limits.html
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Elastic Load Balancing pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/elasticloadbalancing/pricing/?z=3
    type: official-docs
    accessed: 2026-11-19
  - id: s6
    title: "Access virtual appliances through AWS PrivateLink (Gateway Load Balancer endpoints)"
    publisher: Amazon Web Services
    url: https://docs.amazonaws.cn/en_us/vpc/latest/privatelink/vpce-gateway-load-balancer.html
    type: official-docs
    accessed: 2026-11-19
  - id: s7
    title: "Introducing AWS Gateway Load Balancer – Easy Deployment, Scalability, and High Availability for Partner Appliances"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/aws/introducing-aws-gateway-load-balancer-easy-deployment-scalability-and-high-availability-for-partner-appliances
    type: official-blog
    accessed: 2026-11-19
  - id: s8
    title: "AWS Gateway Load Balancer now supports TCP Reset for faster failure recovery"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/aws-gateway-load-balancer-tcp-reset/
    type: official-blog
    accessed: 2026-11-19
  - id: s9
    title: "Load Balancer Capacity Unit Reservation for Gateway Load Balancers"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/04/load-balancer-capacity-unit-reservation-gateway-load-balancers
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# Gateway Load Balancer

A Gateway Load Balancer (GWLB) is the Elastic Load Balancing type built for deploying, scaling and managing virtual network appliances such as firewalls, intrusion detection and prevention systems and deep packet inspection tools.[^s1] It combines a transparent network gateway, a single entry and exit point for traffic, with a load balancer that spreads that traffic across a fleet of appliances and scales them with demand.[^s1] AWS made it generally available in November 2020 with partners such as Palo Alto Networks, Fortinet, Check Point and Cisco.[^s7]

## How it works

GWLB operates at layer 3: its single listener accepts every IP packet on every port and forwards it to one target group.[^s1][^s4] Packets travel to the appliances wrapped in GENEVE on port 6081, the only protocol and port its target groups support; GWLB-specific metadata rides in GENEVE TLV options that the appliance must decapsulate.[^s3] Because the original packet is preserved, source and destination addresses are unchanged and no NAT is needed, and both directions of a flow go to the same appliance so it can inspect statefully.[^s7] Stickiness defaults to the 5-tuple; a target group can instead use a 3-tuple (source IP, destination IP, protocol) or 2-tuple (source and destination IP).[^s1][^s3]

Consumers reach the appliances through **Gateway Load Balancer endpoints**, a PrivateLink VPC endpoint type: the provider exposes the GWLB as an endpoint service, the consumer creates an endpoint and points route tables at it as the next hop.[^s1][^s6] For inbound inspection, the internet gateway's route table sends application-subnet CIDRs to the endpoint; for outbound, the application subnet's default route targets it.[^s6] The endpoint and application servers must sit in different subnets.[^s1] Provider and consumer can be different accounts, which lets vendors offer inspection as a hosted service.[^s7]

Targets are registered by instance ID or by private IP address (RFC 1918 or 100.64.0.0/10, never public), and every enabled zone needs at least one registered target.[^s3] By default, existing flows stay on a target that becomes unhealthy or is deregistered (`no_rebalance`); `rebalance` moves them, and the two failover attributes must be set to the same value.[^s3] Since September 2026 a target group can also send TCP resets to clients when a target turns unhealthy, finishes deregistration or a flow idles out, so senders reconnect quickly instead of retrying for minutes; it is off by default and free.[^s8]

## Quotas, pricing and gotchas

As of 2026-11, default quotas per Region are 100 GWLBs (adjustable), 100 per VPC (adjustable), 300 GWLB network interfaces per VPC (adjustable; one per zone per load balancer) and 100 GENEVE target groups (adjustable); fixed limits are one listener per GWLB and 300 targets per GWLB, per zone and per target group per zone.[^s4] Reserved capacity units default to 0 per Region; reservation, launched in April 2025, guarantees minimum bandwidth but is not supported on the endpoints.[^s4][^s9]

In US East (N. Virginia), as of 2026-11, a GWLB costs $0.0125 per hour per Availability Zone plus $0.004 per GLCU-hour, where one GLCU covers 600 new flows per second, 60,000 active flows or 1 GB processed per hour, billed on the highest dimension.[^s5] Each endpoint adds $0.01 per hour and $0.0035 per GB processed in that Region's pricing examples.[^s5][^s6]

- **MTU.** GWLB accepts packets up to 8,500 bytes and drops larger ones; GENEVE adds 68 bytes, there is no fragmentation and no path MTU discovery.[^s2]
- **Idle timeouts.** TCP defaults to 350 seconds (60–6000 configurable, only with 5-tuple stickiness); UDP is fixed at 120 seconds. After a TCP timeout, data packets are dropped and the next SYN may land on a different appliance.[^s2]
- **Asymmetric paths.** Return traffic that bypasses GWLB works but is discouraged; traffic GWLB never saw initially is not supported.[^s2]
- **Subnets are permanent.** A subnet can't be removed after creation; IPv6 needs a `dualstack` GWLB, though appliances still receive IPv4 GENEVE headers.[^s2]
- **Trust.** You choose and qualify the appliance software, which sees all traffic.[^s1]

## When to use it (and when not)

Use GWLB when you want a third-party or custom appliance fleet inline for ingress, egress or east-west traffic, with health-checked scale-out instead of hand-managed active/standby pairs.[^s7] For layer-4 distribution of client connections to applications, choose a Network Load Balancer instead; GWLB does not terminate or route application traffic.[^s1]

## Connections

GWLB is one of the load balancer types **within** Elastic Load Balancing, alongside the Network Load Balancer.[^s1] It **depends on** AWS PrivateLink for its endpoints and **integrates with** Amazon VPC route tables, including internet-gateway ingress routing.[^s6] It is **scoped to** the Availability Zones you enable and **governed by** the ELB quotas above.[^s2][^s4] It is **related** to AWS Transit Gateway and AWS Network Firewall as other building blocks for steering and filtering traffic between VPCs; the launch post contrasts GWLB with ECMP routing, which cannot keep both directions of a flow on one appliance.[^s7]

## Sources
[^s1]: Amazon Web Services, "What is a Gateway Load Balancer?". https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Gateway Load Balancers". https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/gateway-load-balancers.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Target groups for your Gateway Load Balancers". https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/target-groups.md (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Quotas for your Gateway Load Balancers". https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/quotas-limits.html (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Elastic Load Balancing pricing". https://aws.amazon.com/elasticloadbalancing/pricing/?z=3 (accessed 2026-11-19)
[^s6]: Amazon Web Services, "Access virtual appliances through AWS PrivateLink". https://docs.amazonaws.cn/en_us/vpc/latest/privatelink/vpce-gateway-load-balancer.html (accessed 2026-11-19)
[^s7]: Amazon Web Services, "Introducing AWS Gateway Load Balancer", AWS News Blog (2020-11-10). https://aws.amazon.com/blogs/aws/introducing-aws-gateway-load-balancer-easy-deployment-scalability-and-high-availability-for-partner-appliances (accessed 2026-11-19)
[^s8]: Amazon Web Services, "AWS Gateway Load Balancer now supports TCP Reset for faster failure recovery" (What's New, 2026-09-03). https://aws.amazon.com/about-aws/whats-new/2026/09/aws-gateway-load-balancer-tcp-reset/ (accessed 2026-11-19)
[^s9]: Amazon Web Services, "Load Balancer Capacity Unit Reservation for Gateway Load Balancers" (What's New, 2025-04-10). https://aws.amazon.com/about-aws/whats-new/2025/04/load-balancer-capacity-unit-reservation-gateway-load-balancers (accessed 2026-11-19)
