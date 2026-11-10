---
id: kt:aws-networking-and-content-delivery
type: Concept
title: AWS networking and content delivery
aliases: [AWS Networking and Content Delivery, AWS networking services, networking and content delivery services on AWS]
domain: networking-and-content-delivery
summary: >-
  The category of AWS services that build private networks in the cloud, connect them to each other and to
  on-premises sites, route and accelerate traffic at the edge, and secure it, from Amazon VPC and Transit
  Gateway to CloudFront, Route 53, Direct Connect and Elastic Load Balancing.
verified: 2026-11-10
status: concept
broader: [kt:aws-global-infrastructure]
related: [kt:aws-security-services, kt:edge-locations]
relations:
  - {predicate: hasPart, object: kt:amazon-vpc}
  - {predicate: hasPart, object: kt:amazon-cloudfront}
  - {predicate: hasPart, object: kt:amazon-route-53}
  - {predicate: hasPart, object: kt:elastic-load-balancing}
  - {predicate: hasPart, object: kt:aws-direct-connect}
  - {predicate: hasPart, object: kt:aws-transit-gateway}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Networking and content delivery - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/networking-services.md
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Choosing an AWS networking and content delivery service - AWS Decision Guides"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/networking-on-aws-how-to-choose/choosing-networking-and-content-delivery-service.html
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Networking and Content Delivery on AWS"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/networking/
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Migrating from AWS App Mesh to Amazon VPC Lattice"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/containers/migrating-from-aws-app-mesh-to-amazon-vpc-lattice/
    type: official-blog
    accessed: 2026-11-10
  - id: s5
    title: "Content Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html
    type: official-learning
    accessed: 2026-11-10
  - id: s6
    title: "AWS Weekly Roundup: AWS re:Invent keynote recap, on-demand videos, and more (December 8, 2025)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/aws/aws-weekly-roundup-aws-reinvent-keynote-recap-on-demand-videos-and-more-december-8-2025
    type: official-blog
    accessed: 2026-11-10
  - id: s7
    title: "Top announcements of AWS re:Invent 2025"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/fr/blogs/aws/top-announcements-of-aws-reinvent-2025
    type: official-blog
    accessed: 2026-11-10
created: 2026-11-10
---

# AWS networking and content delivery

"Networking and content delivery" is one of the categories AWS uses to organize its portfolio rather than a single product. It covers the services that give workloads a private network in the cloud, connect that network to other networks and to on-premises sites, deliver content and applications to users from the edge, and protect the traffic along the way.[^s1] AWS positions the category on top of its purpose-built global network and the Region and Availability Zone model that provides high availability.[^s3]

## What is in the category

AWS's decision guide sorts the services into five groups:[^s2]

- **Networking foundations:** Amazon VPC for a logically isolated virtual network with your own IP ranges, subnets, route tables and gateways; AWS PrivateLink for private connectivity to services across VPCs and accounts; and AWS Transit Gateway as a hub that VPCs and on-premises networks attach to instead of peering with each other.[^s2][^s1]
- **Global and hybrid connectivity:** AWS Site-to-Site VPN (encrypted tunnels to a VPC or transit gateway), AWS Client VPN for remote users, AWS Direct Connect for dedicated links partitioned into 802.1Q virtual interfaces, and AWS Cloud WAN for a managed global wide-area network.[^s2][^s1]
- **Edge networking and content delivery:** Amazon CloudFront (the CDN), Amazon Route 53 (DNS, health checks, traffic routing and domain registration), AWS Global Accelerator (static IP addresses that carry users over the AWS network to the nearest healthy endpoint) and AWS Data Transfer Terminal.[^s2][^s1]
- **Application networking:** Elastic Load Balancing (Application, Network, Gateway and Classic load balancers), Amazon API Gateway, Amazon VPC Lattice for service-to-service connectivity, Amazon VPC IPAM, and AWS Cloud Map for service discovery.[^s2][^s1]
- **Network security and remote access:** AWS Network Firewall, AWS Shield, AWS WAF, AWS Firewall Manager and AWS Verified Access, which grants zero-trust access to applications without a VPN.[^s2][^s1]

The aws.amazon.com category page uses a similar split and also lists AWS Interconnect under connectivity and Amazon Interactive Video Service under edge networking.[^s3] The groupings overlap with other categories: API Gateway also belongs to serverless, and Shield and WAF also belong to security.[^s3]

## How to choose

The decision guide frames the choice around business objectives, workload characteristics, data protection, availability, performance, operational excellence, connectivity and security.[^s2] Its main rules of thumb:

- Use Transit Gateway for self-managed routing between many VPCs (including multi-Region), and Cloud WAN when you want AWS to manage a global WAN with central policy.[^s2]
- For hybrid links, use Site-to-Site VPN to connect on-premises networks over encrypted tunnels, and Direct Connect when you need more consistent performance; AWS says Direct Connect can also lower network costs and raise bandwidth compared with internet links.[^s2][^s1]
- Use CloudFront to cut latency for web content, and Global Accelerator for latency-sensitive applications such as gaming, IoT and VoIP.[^s2]
- Spread workloads across multiple Availability Zones behind Elastic Load Balancing, and pick the load balancer type by protocol and OSI layer.[^s2]
- Layer security groups and network ACLs for defense in depth, and use PrivateLink to keep service traffic off the public internet.[^s2]

## Lifecycle notes and gotchas

- **AWS App Mesh** still appears in the AWS overview whitepaper, but AWS closed it to new customers on 2024-09-24 and announced it would be discontinued on 2026-09-30. AWS recommends Amazon VPC Lattice for EKS workloads and Amazon ECS Service Connect for ECS.[^s1][^s4] Treat App Mesh as a migration source, not an option for new designs.
- **Classic Load Balancer** exists for applications built in EC2-Classic, which AWS retired on 2022-08-15; new designs use the Application, Network or Gateway Load Balancer.[^s1]
- There is no category-wide price: each service is billed on its own terms. CloudFront, for example, is pay-as-you-go with no upfront fees or long-term contracts. Check each service's pricing page for your Region.[^s1]

## What changed (2025-12)

At re:Invent 2025 (held 2025-11-30 to 2025-12-04) AWS previewed Amazon Route 53 Global Resolver, an anycast DNS resolver for public and private domains in hybrid networks.[^s7] It also previewed AWS Interconnect – multicloud, which gives private, dedicated-bandwidth links between Amazon VPCs and other clouds. Google Cloud is the first partner, and Microsoft Azure support is planned for 2026.[^s6] Both were previews when announced; check their current status before relying on them.

## Connections

- **Broader:** AWS global infrastructure (`kt:aws-global-infrastructure`). These services run on the AWS global network and its Regions and Availability Zones.[^s3]
- **Has part:** Amazon VPC, Amazon CloudFront, Amazon Route 53, Elastic Load Balancing, AWS Direct Connect and AWS Transit Gateway, the core building blocks named in AWS's overview of the category.[^s1]
- **Related:** AWS security services, which share Shield, WAF, Firewall Manager and Verified Access with this category,[^s2] and edge locations, where CloudFront, Route 53 and Global Accelerator serve users.[^s1]
- **Covered by AWS Certified Cloud Practitioner:** Task 3.5 in Domain 3 (Cloud Technology and Services, 34% of scored content) asks candidates to identify AWS network services. It covers VPC components and security, the purpose of Route 53, and connectivity options such as AWS VPN and Direct Connect.[^s5]

## Sources
[^s1]: Amazon Web Services, "Networking and content delivery - Overview of Amazon Web Services" (whitepaper). https://docs.aws.amazon.com/whitepapers/latest/aws-overview/networking-services.md (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Choosing an AWS networking and content delivery service - AWS Decision Guides". https://docs.aws.amazon.com/decision-guides/latest/networking-on-aws-how-to-choose/choosing-networking-and-content-delivery-service.html (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Networking and Content Delivery on AWS". https://aws.amazon.com/networking/ (accessed 2026-11-10)
[^s4]: Amazon Web Services, AWS Containers Blog, "Migrating from AWS App Mesh to Amazon VPC Lattice" (2024-10-01). https://aws.amazon.com/blogs/containers/migrating-from-aws-app-mesh-to-amazon-vpc-lattice/ (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Content Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html (accessed 2026-11-10)
[^s6]: Amazon Web Services, AWS News Blog, "AWS Weekly Roundup: AWS re:Invent keynote recap, on-demand videos, and more (December 8, 2025)". https://aws.amazon.com/blogs/aws/aws-weekly-roundup-aws-reinvent-keynote-recap-on-demand-videos-and-more-december-8-2025 (accessed 2026-11-10)
[^s7]: Amazon Web Services, AWS News Blog, "Top announcements of AWS re:Invent 2025". https://aws.amazon.com/fr/blogs/aws/top-announcements-of-aws-reinvent-2025 (accessed 2026-11-10)
