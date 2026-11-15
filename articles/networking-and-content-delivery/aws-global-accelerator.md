---
id: kt:aws-global-accelerator
type: Service
title: AWS Global Accelerator
aliases: [Global Accelerator, AGA]
domain: networking-and-content-delivery
summary: >-
  A global network-layer service that gives an application static anycast IP addresses advertised
  from AWS edge locations, then carries TCP and UDP traffic over the AWS backbone to the nearest
  healthy Regional endpoint, with fast health-based failover and per-Region traffic dials.
verified: 2026-11-15
status: current
broader: [kt:aws-networking-and-content-delivery]
related: [kt:aws-global-network, kt:edge-locations, kt:multi-region-architecture]
relations:
  - {predicate: contrastsWith, object: kt:amazon-cloudfront}
  - {predicate: contrastsWith, object: kt:amazon-route-53}
  - {predicate: integratesWith, object: kt:elastic-load-balancing}
  - {predicate: integratesWith, object: kt:amazon-application-recovery-controller}
  - {predicate: securedBy, object: kt:aws-shield}
  - {predicate: governedBy, object: kt:global-accelerator-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
  - {predicate: coveredBy, object: kt:aws-certified-advanced-networking-specialty}
sources:
  - id: s1
    title: "What is AWS Global Accelerator? - AWS Global Accelerator"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/global-accelerator/latest/dg/what-is-global-accelerator.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "AWS Global Accelerator FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/global-accelerator/faqs
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Quotas for AWS Global Accelerator"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/global-accelerator/latest/dg/limits-global-accelerator.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "AWS Global Accelerator pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/global-accelerator/pricing
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "Endpoints for standard accelerators in AWS Global Accelerator"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/global-accelerator/latest/dg/about-endpoints.md
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "Maximising application resiliency with AWS Global Accelerator"
    publisher: Amazon Web Services (Networking & Content Delivery Blog)
    url: https://aws.amazon.com/blogs/networking-and-content-delivery/maximising-application-resiliency-with-aws-global-accelerator
    type: official-blog
    accessed: 2026-11-15
  - id: s7
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-15
  - id: s8
    title: "In-Scope AWS Services - AWS Certified Advanced Networking - Specialty"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/advanced-networking-specialty-01/ans-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-15
created: 2026-11-15
---

# AWS Global Accelerator

AWS Global Accelerator is a network-layer service: you create an *accelerator*, and it directs users' traffic over the AWS global network to optimal endpoints in one or more Regions, improving availability and performance for applications with a global audience.[^s1] It does not cache content; it proxies TCP and UDP packets at the edge, which makes it a fit for gaming, IoT, VoIP and HTTP workloads that need fixed IP addresses or deterministic Regional failover.[^s2] AWS lists it as in scope for PCI DSS, ISO, SOC and HIPAA eligibility.[^s2]

## How it works

**Static anycast IPs.** An IPv4 accelerator receives two static IPv4 addresses; a dual-stack accelerator receives four (two IPv4, two IPv6). The addresses are advertised by anycast from the AWS edge network, and for IPv4 you can bring your own address ranges (BYOIP).[^s1] Each edge location runs two independently managed network zones, and the two addresses come from different zones, so a fault in one zone should not take out both.[^s6] Disabling an accelerator keeps its addresses assigned; deleting it releases them for good, so AWS suggests restricting deletion with IAM, for example tag-based permissions.[^s1]

**Two accelerator types.** A *standard* accelerator sends each client to the nearest healthy endpoint, using endpoint health, client location and your policies. A *custom routing* accelerator instead maps users deterministically to a specific private IP and port, and supports only VPC subnet endpoints.[^s1] Custom routing subnets can be at most /17, have no external health checks, and one accelerator can address more than 64,000 destinations; you add accelerators to go beyond that.[^s2]

**Listeners, endpoint groups and endpoints.** A listener accepts traffic on ports or port ranges; each listener has per-Region endpoint groups, and each group holds endpoints. Standard endpoints can be Network Load Balancers, Application Load Balancers, EC2 instances or Elastic IP addresses.[^s5] A traffic dial on each endpoint group (default 100%) shifts load into or out of a Region for testing or maintenance, and weights split traffic among the endpoints inside a group. Client affinity can pin a user to one endpoint for stateful applications.[^s2]

**Health and failover.** Global Accelerator continuously checks endpoint health and routes only to healthy ones; if a Region has no healthy endpoint, it sends traffic to all endpoints there.[^s5] AWS says it can remove an unhealthy endpoint from service in under a minute, and because the client-facing IPs never change, there is no DNS cache to wait out.[^s2] If the nearest edge POP fails, anycast shifts users to the next-nearest one, and if health checkers were impaired the service keeps using the last known health state.[^s6] It also honours ARC zonal shift and zonal autoshift.[^s1]

## Quotas, pricing and gotchas

- **Quotas (defaults as of 2026-11):** 20 standard and 10 custom routing accelerators per account, 10 listeners per accelerator (all adjustable); 42 endpoint groups per accelerator and 10 port ranges per listener (fixed). Endpoint groups allow 10 endpoints; the limit is fixed for ALB-only or mixed groups but adjustable for NLB, EC2, Elastic IP or subnet-only groups.[^s3]
- **Pricing (as of 2026-11):** a fixed $0.025 per hour per accelerator, charged whether it is enabled or disabled (about $18 for a 30-day month), plus a per-GB *DT-Premium* fee on the dominant traffic direction only. From a US or Canada source Region, DT-Premium is $0.015/GB to North American or European edges and $0.105/GB to Australia and New Zealand. Standard EC2 data transfer out and public IPv4 address charges apply on top.[^s4]
- **IPv6** traffic reaches only dual-stack-capable endpoints (NLB, ALB and EC2), so plan endpoint types before choosing dual-stack.[^s5] BYOIP allows up to two ranges per account, each /24 or larger, and a pool is advertised by either Global Accelerator or EC2, never both.[^s2]

## When to use it (and when not)

Choose Global Accelerator when clients need a short list of fixed IPs (firewall allow-lists, IoT devices, hard-coded game clients), when traffic is non-HTTP TCP or UDP, or when Regional failover has to happen faster than DNS TTLs allow.[^s2] Choose CloudFront for cacheable or dynamic HTTP content where edge caching and request-level features matter.[^s2] DNS-based routing such as Route 53 costs less but is subject to client and resolver caching, which can delay failover; Global Accelerator changes propagate in seconds.[^s2] AWS also lists the service in scope for the Solutions Architect – Associate and Advanced Networking – Specialty exams.[^s7][^s8]

## Connections

- **Broader: AWS networking and content delivery** (`kt:aws-networking-and-content-delivery`), the service family it belongs to.[^s7]
- **Related: AWS Global Network** (`kt:aws-global-network`) and **edge locations** (`kt:edge-locations`), the backbone and POPs its anycast IPs ride on, and **multi-Region architecture** (`kt:multi-region-architecture`), where traffic dials and health-based failover do the routing.[^s1][^s6]
- **Contrasts with Amazon CloudFront** (`kt:amazon-cloudfront`, edge caching for HTTP) and **Amazon Route 53** (`kt:amazon-route-53`, DNS-level routing affected by caching).[^s2]
- **Integrates with Elastic Load Balancing** (`kt:elastic-load-balancing`) as endpoints, and with **ARC** (`kt:amazon-application-recovery-controller`) zonal shift.[^s1][^s5]
- **Secured by AWS Shield** (`kt:aws-shield`) DDoS protection, which both Global Accelerator and CloudFront integrate with.[^s2]
- **Governed by Global Accelerator quotas** (`kt:global-accelerator-quotas`).[^s3]
- **Covered by** the Solutions Architect – Associate (`kt:aws-certified-solutions-architect-associate`) and Advanced Networking – Specialty (`kt:aws-certified-advanced-networking-specialty`) exams.[^s7][^s8]

## Sources
[^s1]: Amazon Web Services, "What is AWS Global Accelerator? - AWS Global Accelerator". https://docs.aws.amazon.com/en_us/global-accelerator/latest/dg/what-is-global-accelerator.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "AWS Global Accelerator FAQs". https://aws.amazon.com/global-accelerator/faqs (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Quotas for AWS Global Accelerator". https://docs.aws.amazon.com/global-accelerator/latest/dg/limits-global-accelerator.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "AWS Global Accelerator pricing". https://aws.amazon.com/global-accelerator/pricing (accessed 2026-11-15)
[^s5]: Amazon Web Services, "Endpoints for standard accelerators in AWS Global Accelerator". https://docs.aws.amazon.com/global-accelerator/latest/dg/about-endpoints.md (accessed 2026-11-15)
[^s6]: Amazon Web Services, Networking & Content Delivery Blog, "Maximising application resiliency with AWS Global Accelerator". https://aws.amazon.com/blogs/networking-and-content-delivery/maximising-application-resiliency-with-aws-global-accelerator (accessed 2026-11-15)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-15)
[^s8]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Advanced Networking - Specialty". https://docs.aws.amazon.com/aws-certification/latest/advanced-networking-specialty-01/ans-01-in-scope-services.html (accessed 2026-11-15)
