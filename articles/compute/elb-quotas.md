---
id: kt:elb-quotas
type: Limit
title: Elastic Load Balancing quotas
aliases: [ELB quotas, ELB limits, Elastic Load Balancing limits, Load balancer quotas, ELB service quotas]
domain: compute
summary: >-
  The per-Region and per-load-balancer limits that apply to Application, Network, Gateway and Classic
  Load Balancers and their target groups, including the reserved-capacity (LCU) quotas that default to
  zero and must be raised before an LCU reservation can be made.
verified: 2026-11-19
status: current
broader: [kt:elastic-load-balancing]
related: [kt:application-load-balancer, kt:network-load-balancer, kt:gateway-load-balancer, kt:amazon-vpc, kt:aws-lambda]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
sources:
  - id: s1
    title: "Elastic Load Balancing endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/elb.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Quotas for your Application Load Balancers"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-limits.md
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Quotas for your Gateway Load Balancers"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/quotas-limits.html
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "Capacity reservations for your Network Load Balancer"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/network/capacity-unit-reservation.md
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Capacity reservations for your Application Load Balancer"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/application/capacity-unit-reservation.html
    type: official-docs
    accessed: 2026-11-19
  - id: s6
    title: "Elastic Load Balancing pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/elasticloadbalancing/pricing/?z=3
    type: official-docs
    accessed: 2026-11-19
  - id: s7
    title: "Using Load Balancer Capacity Unit Reservation to prepare for sharp increases in traffic"
    publisher: Amazon Web Services (AWS Networking & Content Delivery Blog)
    url: https://aws.amazon.com/blogs/networking-and-content-delivery/using-load-balancer-capacity-unit-reservation-to-prepare-for-sharp-increases-in-traffic/
    type: official-blog
    accessed: 2026-11-19
  - id: s8
    title: "Load Balancer Capacity Unit Reservation for Gateway Load Balancers"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/04/load-balancer-capacity-unit-reservation-gateway-load-balancers
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# Elastic Load Balancing quotas

Elastic Load Balancing quotas are the account-level ceilings on how many load balancers, listeners, target groups, targets, rules and certificates you can create, plus fixed size limits on things like HTTP headers and trust stores. Unless a quota says otherwise it applies per Region. Each one is marked adjustable or fixed: adjustable quotas are raised through the Service Quotas console (or an AWS Support case when a quota is not yet listed there), and fixed ones cannot change.[^s1][^s2] The AWS General Reference gathers the headline values for all four load balancer types on one page, while each type's user guide adds the finer-grained limits.[^s1][^s3]

## Headline quotas by load balancer type (as of 2026-11)

**Application Load Balancer.** An account starts with 50 ALBs per Region, 50 listeners, 100 rules (default rules excluded), 25 certificates (default certificates excluded) and 1,000 targets per ALB; those are adjustable. Fixed: 100 target groups per ALB, 5 target groups per forward action, 5 condition values and 6 wildcards per rule.[^s2]

**Network Load Balancer.** Defaults are 50 NLBs per Region, 3,000 targets per NLB and 500 targets per Availability Zone per NLB, 25 certificates, and 1,200 NLB network interfaces per VPC, all adjustable; the 50-listener cap per NLB is fixed.[^s1]

**Gateway Load Balancer.** GWLB starts higher on count, at 100 per Region and 100 per VPC, with 100 GENEVE target groups per Region and 300 GWLB network interfaces per VPC (each GWLB uses one interface per zone), all adjustable. Its appliance-fleet limits are fixed: one listener per GWLB, and 300 targets per GWLB, per zone per GWLB, and per zone per GENEVE target group.[^s3]

**Classic Load Balancer.** The previous-generation type keeps its own adjustable set: 20 per Region, 100 listeners and 1,000 registered instances per load balancer.[^s1]

## Target group, TLS and header limits

Target groups are counted once for both ALBs and NLBs: the 3,000-per-Region quota is shared, and each group holds up to 1,000 instance or IP targets (adjustable). A target group that points at a Lambda function can hold exactly one function, and any target group can attach to only one load balancer; neither can be raised.[^s2] ALB mutual TLS adds trust-store quotas (20 trust stores per account, 25 CA certificates and 500,000 revocation entries per store, adjustable) and fixed ceilings such as a certificate chain depth of 4, a 50 MB revocation list file and at most 2 listeners per load balancer verifying client certificates.[^s2] HTTP size limits are fixed: 16 K for the request line and for any single header, 32 K for the whole response header and 64 K for the whole request header.[^s2]

## Reserved capacity (LCU) quotas

Every load balancer type has a "reserved capacity units per Region" quota whose default is **0**, so an account cannot pre-provision capacity until it asks for that quota to be raised.[^s1][^s3] ALB also caps reservations at 15,000 LCUs per load balancer (adjustable).[^s2] Reservations set a floor, not a ceiling: the load balancer still scales with traffic but never below the reserved amount.[^s5] An ALB reservation must total at least 100 LCU, while an NLB reservation must be at least 2,750 LCU per Availability Zone, with each NLB LCU equal to 2.2 Mbps; NLB reservations are offered only in ten Regions, including us-east-1, eu-west-1 and ap-northeast-1.[^s5][^s4] GWLB gained reservations on 2025-04-10 in the same ten Regions, but not for GWLB endpoints.[^s8]

Reserved capacity is billed whether or not it is used. In the us-east-1 examples on the pricing page (as of 2026-11), reserved ALB capacity costs $0.008 per LCU-hour and reserved NLB capacity $0.006 per NLCU-hour, with usage above the reservation charged on top.[^s6] AWS's networking blog recommends sizing an ALB reservation from the per-minute sum of `PeakLCUs`, not `ConsumedLCUs`, and cancelling the reservation once the event is over; it also notes that this self-service feature replaces opening a support case to pre-warm a load balancer.[^s7][^s5]

## Gotchas

- NLB targets are limited twice, per load balancer and per Availability Zone, and each is a separate quota, so a deployment can need both raised.[^s1]
- The fixed 300-target ceiling on GWLB means very large inspection fleets need several GWLBs rather than a quota request.[^s3]
- Listing quotas in Service Quotas or with the `describe-account-limits` CLI call shows current values before a launch.[^s2]

## Connections

These quotas sit **broader** under Elastic Load Balancing (`kt:elastic-load-balancing`) and apply to its Application, Network and Gateway Load Balancer nodes.[^s1] They are **configured via** Service Quotas (`kt:service-quotas`) and **scoped to** AWS Regions (`kt:aws-regions`).[^s2] Per-VPC interface limits tie them to Amazon VPC (`kt:amazon-vpc`), the one-function rule to AWS Lambda (`kt:aws-lambda`), and capacity planning depends on Amazon CloudWatch (`kt:amazon-cloudwatch`) metrics such as `PeakLCUs` and `ProcessedBytes`.[^s3][^s2][^s4]

## Sources

[^s1]: Amazon Web Services, "Elastic Load Balancing endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/elb.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Quotas for your Application Load Balancers". https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-limits.md (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Quotas for your Gateway Load Balancers". https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/quotas-limits.html (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Capacity reservations for your Network Load Balancer". https://docs.aws.amazon.com/elasticloadbalancing/latest/network/capacity-unit-reservation.md (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Capacity reservations for your Application Load Balancer". https://docs.aws.amazon.com/elasticloadbalancing/latest/application/capacity-unit-reservation.html (accessed 2026-11-19)
[^s6]: Amazon Web Services, "Elastic Load Balancing pricing". https://aws.amazon.com/elasticloadbalancing/pricing/?z=3 (accessed 2026-11-19)
[^s7]: P. Staev and J. Zobrist, "Using Load Balancer Capacity Unit Reservation to prepare for sharp increases in traffic", AWS Networking & Content Delivery Blog (2025-01-28). https://aws.amazon.com/blogs/networking-and-content-delivery/using-load-balancer-capacity-unit-reservation-to-prepare-for-sharp-increases-in-traffic/ (accessed 2026-11-19)
[^s8]: Amazon Web Services, "Load Balancer Capacity Unit Reservation for Gateway Load Balancers" (What's New, 2025-04-10). https://aws.amazon.com/about-aws/whats-new/2025/04/load-balancer-capacity-unit-reservation-gateway-load-balancers (accessed 2026-11-19)
