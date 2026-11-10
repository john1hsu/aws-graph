---
id: kt:elastic-load-balancing
type: Service
title: Elastic Load Balancing
aliases: [ELB, AWS Elastic Load Balancing]
domain: compute
summary: >-
  The AWS service that spreads incoming traffic across healthy targets (EC2 instances, containers,
  IP addresses, Lambda functions or appliances) in one or more Availability Zones, offered as
  Application, Network and Gateway Load Balancers plus the previous-generation Classic Load Balancer.
verified: 2026-11-10
status: current
broader: [kt:aws-compute-services, kt:aws-networking-and-content-delivery]
related: [kt:amazon-ec2, kt:availability-zones, kt:aws-lambda, kt:application-load-balancer, kt:network-load-balancer, kt:gateway-load-balancer]
relations:
  - {predicate: integratesWith, object: kt:amazon-ec2-auto-scaling}
  - {predicate: integratesWith, object: kt:amazon-route-53}
  - {predicate: integratesWith, object: kt:amazon-ecs}
  - {predicate: securedBy, object: kt:aws-waf}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: governedBy, object: kt:elb-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "Elastic Load Balancing User Guide (PDF)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/elb-ug.pdf
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Elastic Load Balancing features"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/elasticloadbalancing/details
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Quotas for your Application Load Balancers - Elastic Load Balancing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-limits.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Elastic Load Balancing pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/elasticloadbalancing/classicloadbalancer/pricing
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Document history for Application Load Balancers"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticloadbalancing/latest/application/doc-history.html
    type: official-docs
    accessed: 2026-11-10
created: 2026-11-10
---

# Elastic Load Balancing

Elastic Load Balancing (ELB) distributes incoming traffic across multiple targets, such as EC2 instances, containers and IP addresses, in one or more Availability Zones.[^s1] It health-checks those targets, sends traffic only to healthy ones, and scales its own capacity as traffic changes, so a fleet behind it can grow, shrink or lose members without clients noticing.[^s1] It can also offload TLS encryption and decryption from your compute, and it is billed on usage.[^s1]

## Load balancer types

ELB is one service with four products. Application, Network and Gateway Load Balancers are the current generation; the Classic Load Balancer is the previous generation, and AWS recommends migrating off it.[^s1]

- **Application Load Balancer (ALB)** works at layer 7 for HTTP, HTTPS and gRPC, routes to instance, IP or Lambda targets, and adds features such as header-based routing, redirects, fixed responses, user authentication and desync mitigation.[^s2] AWS positions it for flexible application traffic management.[^s2]
- **Network Load Balancer (NLB)** works at layer 4 for TCP, UDP and TLS, and can target instances, IPs or an ALB.[^s2] It offers static and Elastic IP addresses, PrivateLink support, long-lived connections and source IP preservation, and is the choice for extreme performance or a fixed IP.[^s2]
- **Gateway Load Balancer (GWLB)** combines a layer 3 gateway with layer 4 balancing to insert fleets of network appliances (firewalls, inspection) into the traffic path, exposed through GWLB endpoints over PrivateLink.[^s2][^s1]
- **Classic Load Balancer (CLB)** handles TCP, SSL/TLS, HTTP and HTTPS and exists for applications built on the old EC2-Classic network.[^s2] The user guide documents a migration wizard, a copy utility and manual migration, and explains how to stop users creating new CLBs.[^s1]

## How it works

Each load balancer has **listeners**, each defined by a protocol and port for client connections and another for connections to targets.[^s1] Enabling an Availability Zone places a load balancer node there; targets in zones you have not enabled stay registered but get no traffic, and an ALB needs at least two zones.[^s1] Clients resolve the load balancer's DNS name, which returns the node IP addresses with a 60-second TTL; ELB updates that record as it scales.[^s1] NLBs give each zone a static IP and can use an Elastic IP per zone.[^s1]

Routing differs by type. An ALB evaluates listener rules in priority order and picks a target from the matching target group, round robin by default.[^s1] An NLB hashes the flow (protocol, addresses, ports, TCP sequence number) so each TCP connection sticks to one target for its life.[^s1] A GWLB uses a 5-tuple hash so a flow always reaches the same appliance, encapsulating traffic in GENEVE on port 6081.[^s1] A CLB uses round robin for TCP and least outstanding requests for HTTP/HTTPS.[^s1]

**Cross-zone load balancing** defaults vary: always on for ALB at the load balancer level (it can be turned off per target group), off by default for NLB and GWLB, and for CLB off when created by API or CLI but on when created in the console.[^s1]

## Quotas, pricing and gotchas

As of 2026-11, default ALB quotas per Region are 50 ALBs, 3,000 target groups (shared with NLBs), 50 listeners and 100 rules (excluding defaults) per ALB, and 1,000 targets per ALB; all of these are adjustable.[^s3] Some are fixed: 100 target groups per ALB, 5 target groups per action, a single Lambda function per target group, and one load balancer per target group.[^s3]

Pricing (US East (N. Virginia), as of 2026-11): an ALB costs $0.0225 per hour plus $0.008 per LCU-hour, and an NLB $0.0225 per hour plus $0.006 per NLCU-hour; partial hours bill as full hours.[^s4] An ALB LCU is metered on whichever of four dimensions is highest in the hour: new connections, active connections, processed bytes, or rule evaluations, and the first 10 rules are free.[^s4] EC2, data transfer and public IPv4 addresses are billed separately.[^s4] The Free Tier includes 750 hours a month shared between Classic and Application Load Balancers, with 15 LCUs for ALB.[^s4]

Gotchas: once a connection upgrades to WebSockets, ALB listener rules and AWS WAF inspection no longer apply to it.[^s1] Turning cross-zone load balancing off means a zonal shift away from an impaired zone also removes that zone's target capacity.[^s1]

## Recent changes

ALB added request transforms that rewrite host headers and URLs before routing (October 2025) and validation of JSON Web Tokens for service-to-service traffic (November 2025).[^s5]

## When to use it (and when not)

Put an ALB in front of HTTP microservices, containers and Lambda functions that need content-based routing; use an NLB for non-HTTP protocols, very high connection rates, static IPs or exposing a service through PrivateLink; use a GWLB to scale third-party virtual appliances.[^s2] Keep a CLB only for legacy workloads you have not migrated yet.[^s1]

## Connections

ELB fronts Amazon EC2 instances and spreads them across Availability Zones; instances launched by Amazon EC2 Auto Scaling are registered and deregistered automatically.[^s1] Amazon Route 53 maps a domain name to the load balancer, Amazon ECS services can use it to distribute traffic across tasks, and ALB can invoke AWS Lambda functions as targets.[^s1][^s2] It is **secured by** AWS WAF web ACLs on ALBs and uses AWS Certificate Manager certificates on HTTPS listeners, and it is **monitored by** Amazon CloudWatch.[^s1] Its per-Region and per-load-balancer limits form the ELB quotas node, and each load balancer is **scoped to** one AWS Region.[^s3] The ALB, NLB and GWLB nodes cover each type in more depth.[^s2]

## Sources
[^s1]: Amazon Web Services, "Elastic Load Balancing User Guide". https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/elb-ug.pdf (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Elastic Load Balancing features". https://aws.amazon.com/elasticloadbalancing/details (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Quotas for your Application Load Balancers". https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-limits.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Elastic Load Balancing pricing". https://aws.amazon.com/elasticloadbalancing/classicloadbalancer/pricing (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Document history for Application Load Balancers". https://docs.aws.amazon.com/elasticloadbalancing/latest/application/doc-history.html (accessed 2026-11-10)
