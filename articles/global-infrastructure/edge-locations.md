---
id: kt:edge-locations
type: InfrastructureConcept
title: Edge locations
aliases: [AWS edge locations, Edge location, Points of presence, Point of presence, PoP, PoPs, AWS edge network, Regional edge caches]
domain: global-infrastructure
summary: >-
  AWS edge locations (points of presence) are a globally distributed network of sites, separate from
  Regions and Availability Zones, that host edge services such as Amazon CloudFront, Amazon Route 53
  and AWS Global Accelerator close to end users and connect back to Regions over the AWS backbone.
verified: 2026-11-12
status: concept
broader: [kt:aws-global-infrastructure]
related: [kt:amazon-route-53, kt:aws-shield]
relations:
  - {predicate: usedBy, object: kt:amazon-cloudfront}
  - {predicate: usedBy, object: kt:aws-global-accelerator}
  - {predicate: contrastsWith, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Points of presence - AWS Fault Isolation Boundaries (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/points-of-presence.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Amazon CloudFront Key Features"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/cloudfront/features/
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "AWS Global Infrastructure"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/global-infrastructure/index.md
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "How AWS Global Accelerator works - AWS Global Accelerator"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/global-accelerator/latest/dg/introduction-how-it-works.html
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "AWS Shield Standard overview - AWS WAF, AWS Firewall Manager, AWS Shield Advanced"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/waf/latest/developerguide/ddos-standard-summary.html
    type: official-docs
    accessed: 2026-11-12
  - id: s6
    title: "Content Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html
    type: official-learning
    accessed: 2026-11-12
created: 2026-11-12
---

# Edge locations

Alongside Regions and Availability Zones, AWS runs a globally distributed network of **points of presence (PoPs)**, usually called edge locations, that host edge services close to end users: Amazon CloudFront (the CDN), Amazon Route 53 (public DNS) and AWS Global Accelerator (network path optimization).[^s1] Edge locations are not where you launch EC2 instances or store data; they are the front door where user traffic first meets AWS and, ideally, gets answered or accelerated before it ever reaches a Region.[^s1][^s2]

## How it works

AWS describes its edge network in three tiers as of 2026-11:[^s2]

- **Regional edge caches (RECs)** sit inside AWS Regions, between your origin servers and the PoPs. CloudFront lists 15 of them globally, matching the count on the AWS Global Infrastructure page.[^s2][^s3]
- **Points of presence** sit within the AWS network and peer with internet service providers; CloudFront lists 750+ PoPs in 100+ cities across 50+ countries.[^s2]
- **Embedded PoPs** sit inside ISP networks, closest to viewers; CloudFront lists 1,140+ in 300+ cities.[^s2]

These counts grow often, and AWS's fault-isolation whitepaper still cites older, smaller figures (over 700 PoPs and 13 RECs) while pointing readers to the CloudFront features page for current numbers, so treat any count as a dated snapshot.[^s1] Edge locations connect to Regions over AWS's fully redundant backbone of parallel 400 GbE fibers, and AWS peers with thousands of Tier 1, 2 and 3 carriers.[^s1][^s2]

Each service uses the edge differently. CloudFront caches responses at PoPs and RECs and runs CloudFront Functions at all of its edge locations for lightweight request handling.[^s2] Global Accelerator announces static anycast IP addresses from the edge network, so client traffic enters AWS at the nearest edge location; it terminates TCP there and opens a fresh connection to your regional endpoints (load balancers, EC2 instances or Elastic IPs), keeping traffic on the AWS network instead of the public internet.[^s4]

## Resilience and security

Each PoP is isolated from the others: a failure affecting one PoP or metro area doesn't affect the rest of the global network.[^s1] That makes the edge a useful security perimeter. CloudFront, Route 53, AWS Shield and AWS WAF co-locate at the edge to form a layered defense against network- and application-layer DDoS attacks.[^s2] Shield Standard protects every AWS customer at no extra charge, and Route 53 hosted zones, CloudFront distributions and Global Accelerator standard accelerators get comprehensive protection against known network and transport layer attacks. When users reach an application through Route 53 and then CloudFront or Global Accelerator, the perimeter starts at the AWS edge rather than at your VPC.[^s5]

## Gotchas

- **Edge locations are not mini-Regions.** You can't choose them for compute or storage the way you choose a Region or AZ; services place traffic there for you.[^s1][^s4] For low-latency compute near users, look at Local Zones or Outposts instead (see the AWS global infrastructure article).
- **Counts differ by source and date.** The AWS Global Infrastructure page breaks locations down by continent (for example, 31 edge network locations and 3 edge cache locations in North America) rather than giving one global PoP total.[^s3]
- **Regional services still depend on a Region.** Global Accelerator health-checks and fails over between regional endpoints, but existing connections stay pinned to their endpoint until an idle timeout (340 seconds for TCP, 30 for UDP).[^s4]

## When to use it

Push traffic through the edge whenever users are geographically spread: CloudFront for cacheable web, video and API content; Global Accelerator for non-HTTP (TCP/UDP) traffic, static IPs or fast multi-Region failover; and Route 53 for DNS answered close to users.[^s1][^s4] Fronting an application with these services also moves its DDoS perimeter out to the edge.[^s5]

## Connections

Edge locations are part of the **AWS global infrastructure** and contrast with **AWS Regions**: Regions host your resources, while edge locations host edge services and reach Regions over the backbone.[^s1] They are used by **Amazon CloudFront** and **AWS Global Accelerator**, and they also serve **Amazon Route 53**; **AWS Shield** protects traffic entering through them.[^s1][^s4][^s5] The AWS Certified Cloud Practitioner (CLF-C02) exam guide lists edge locations, their benefits and how they relate to Regions and Availability Zones under task statement 3.2.[^s6]

## Sources
[^s1]: Amazon Web Services, "Points of presence - AWS Fault Isolation Boundaries". https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/points-of-presence.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Amazon CloudFront Key Features". https://aws.amazon.com/cloudfront/features/ (accessed 2026-11-12)
[^s3]: Amazon Web Services, "AWS Global Infrastructure". https://aws.amazon.com/about-aws/global-infrastructure/index.md (accessed 2026-11-12)
[^s4]: Amazon Web Services, "How AWS Global Accelerator works". https://docs.aws.amazon.com/global-accelerator/latest/dg/introduction-how-it-works.html (accessed 2026-11-12)
[^s5]: Amazon Web Services, "AWS Shield Standard overview". https://docs.aws.amazon.com/waf/latest/developerguide/ddos-standard-summary.html (accessed 2026-11-12)
[^s6]: Amazon Web Services, "Content Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html (accessed 2026-11-12)
