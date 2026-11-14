---
id: kt:aws-privatelink
type: Service
title: AWS PrivateLink
aliases: [PrivateLink, VPC endpoint services]
domain: networking-and-content-delivery
summary: >-
  AWS's technology for privately connecting a VPC to AWS services, other accounts' endpoint services, SaaS
  offerings and shared resources through VPC endpoints with private IP addresses, without internet gateways,
  NAT, VPNs or VPC peering.
verified: 2026-11-14
status: current
broader: [kt:aws-networking-and-content-delivery]
related: [kt:amazon-vpc, kt:vpc-endpoints, kt:amazon-route-53, kt:aws-transit-gateway]
relations:
  - {predicate: dependsOn, object: kt:elastic-load-balancing}
  - {predicate: integratesWith, object: kt:aws-direct-connect}
  - {predicate: integratesWith, object: kt:aws-resource-access-manager}
  - {predicate: alternativeTo, object: kt:vpc-peering}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is AWS PrivateLink?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/privatelink/what-is-privatelink.html?ref=faun
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "AWS PrivateLink concepts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/privatelink/concepts.md
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "AWS PrivateLink quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-limits-endpoints.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "AWS PrivateLink pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/privatelink/pricing/
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "AWS PrivateLink FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/privatelink/faqs
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "AWS PrivateLink now supports cross-region connectivity"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2024/11/aws-privatelink-across-region-connectivity
    type: official-blog
    accessed: 2026-11-14
  - id: s7
    title: "AWS PrivateLink now supports cross-region connectivity for AWS Services"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/aws-privatelink-cross-region-connectivity-aws-services
    type: official-blog
    accessed: 2026-11-14
  - id: s8
    title: "In-scope AWS services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-14
created: 2026-11-14
---

# AWS PrivateLink

AWS PrivateLink is a highly available, scalable technology that lets clients in a VPC reach services and resources as though they were hosted inside that VPC, using private IP addresses.[^s1] The VPC needs no internet gateway, NAT device, public IP address, Direct Connect connection or Site-to-Site VPN, and you decide which APIs, services and resources it can reach.[^s1] Typical uses are calling AWS service APIs privately, consuming services published by other AWS accounts or AWS Marketplace SaaS partners, reaching a database or other resource in another VPC, joining a VPC Lattice service network, and sending traffic through virtual appliances behind a Gateway Load Balancer.[^s1]

## How it works

PrivateLink has two sides. A **provider** (AWS, an AWS Partner or another account) publishes either an *endpoint service*, which must sit behind a load balancer, or a *resource configuration* describing a single resource, a group of resources or a set of CIDR ranges, exposed through a *resource gateway* in the provider VPC.[^s2] Endpoint services are private by default: the provider allow-lists AWS principals, and each consumer connection request can be accepted or rejected.[^s2] Resource configurations are shared across accounts with AWS Resource Access Manager instead.[^s2] The FAQ notes that service owners front their service with a Network Load Balancer, while resource owners need no load balancer at all.[^s5]

A **consumer** creates a VPC endpoint of the matching type: *interface* (TCP or UDP to an endpoint service, resolved via DNS), *Gateway Load Balancer* (selected through route tables), *resource*, *tunnel* (GENEVE-encapsulated access to a shared network segment) or *service network* (one endpoint reaching many services and resources in a VPC Lattice service network).[^s2] For each subnet you choose, AWS places a requester-managed network interface that acts as the entry point; traffic between endpoint and provider stays on the AWS network.[^s2] A VPC endpoint policy, an IAM resource policy, limits which principals may use an endpoint, and by default allows everything.[^s2] With Route 53 private hosted zones you can build split-horizon DNS so the same hostname resolves to endpoint IPs inside the VPC and to public endpoints elsewhere.[^s2] On-premises clients can use interface, resource and service network endpoints over Direct Connect or VPN.[^s5]

Note that **gateway endpoints** for Amazon S3 and DynamoDB are also VPC endpoints but do not use PrivateLink.[^s2]

## Quotas, pricing and gotchas

Default quotas (as of 2026-11, per Region, all adjustable unless noted): 50 interface plus Gateway Load Balancer endpoints per VPC, 200 resource endpoints per VPC, 50 service network endpoints per VPC, 20 gateway endpoints per Region, and 20,480 characters per endpoint policy (not adjustable).[^s3] Each endpoint starts at 10 Gbps per Availability Zone and scales automatically to 100 Gbps; MTU is 8,500 bytes, larger packets are dropped, and Path MTU Discovery is not supported.[^s3] PrivateLink does not encrypt traffic itself, so use TLS, and clients must use the endpoint's private DNS names rather than a service's public name.[^s5]

Pricing (us-east-1 examples on the pricing page, as of 2026-11): interface endpoints cost $0.01 per endpoint network interface-hour, i.e. per AZ, plus data processing of $0.01/GB for the first 1 PB per Region per month, $0.006/GB for the next 4 PB and $0.004/GB beyond 5 PB; resource endpoints cost $0.02 per resource-hour with the same processing tiers.[^s4] Cross-Region service providers pay $0.05 per hour for each active remote Region, and standard inter-Region data transfer applies on top.[^s4] Partial hours bill as full hours.[^s5]

## When to use it (and when not)

Choose PrivateLink when consumers need one-way access to a specific service or resource rather than full network reachability, especially across many accounts or with overlapping CIDRs; AWS contrasts this with VPC peering, which caps at 125 peering connections per VPC.[^s5] Prefer free S3 or DynamoDB gateway endpoints for in-Region access to those two services, and Transit Gateway or peering when whole networks must route to each other.[^s2] Since November 2024 interface endpoints can reach endpoint services in other Regions of the same partition, and since November 2025 select AWS services such as Amazon S3, Route 53 and Amazon ECR cross-Region too, which avoids building inter-Region peering for private access.[^s6][^s7]

## Connections

PrivateLink belongs to AWS networking and content delivery and is built into Amazon VPC, where it implements most VPC endpoint types.[^s1][^s2] Endpoint services depend on Elastic Load Balancing (Network or Gateway Load Balancers), while resource sharing runs through AWS Resource Access Manager.[^s2][^s5] Route 53 private hosted zones supply DNS, and Direct Connect or VPN extend endpoints to on-premises networks.[^s2][^s5] It is an alternative to VPC peering and complements Transit Gateway for full routing; endpoints are Regional, with cross-Region reach as an option.[^s5][^s6] The current SAA-C03 exam guide lists AWS PrivateLink as an in-scope Networking and Content Delivery service.[^s8]

## Sources
[^s1]: Amazon Web Services, "What is AWS PrivateLink?". https://docs.aws.amazon.com/vpc/latest/privatelink/what-is-privatelink.html?ref=faun (accessed 2026-11-14)
[^s2]: Amazon Web Services, "AWS PrivateLink concepts". https://docs.aws.amazon.com/vpc/latest/privatelink/concepts.md (accessed 2026-11-14)
[^s3]: Amazon Web Services, "AWS PrivateLink quotas". https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-limits-endpoints.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, "AWS PrivateLink pricing". https://aws.amazon.com/privatelink/pricing/ (accessed 2026-11-14)
[^s5]: Amazon Web Services, "AWS PrivateLink FAQs". https://aws.amazon.com/privatelink/faqs (accessed 2026-11-14)
[^s6]: Amazon Web Services, "AWS PrivateLink now supports cross-region connectivity" (What's New, 2024-11-26). https://aws.amazon.com/about-aws/whats-new/2024/11/aws-privatelink-across-region-connectivity (accessed 2026-11-14)
[^s7]: Amazon Web Services, "AWS PrivateLink now supports cross-region connectivity for AWS Services" (What's New, 2025-11-19). https://aws.amazon.com/about-aws/whats-new/2025/11/aws-privatelink-cross-region-connectivity-aws-services (accessed 2026-11-14)
[^s8]: Amazon Web Services, "In-scope AWS services - SAA-C03". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-14)
