---
id: kt:aws-global-infrastructure
type: InfrastructureConcept
title: AWS global infrastructure
aliases: [AWS Global Infrastructure, AWS global footprint]
domain: global-infrastructure
summary: >-
  The physical footprint AWS runs its services on: Regions made of isolated Availability Zones, plus
  Local Zones, Wavelength Zones and Outposts that extend a Region toward users, and CloudFront edge
  locations, all joined by the AWS global fiber network.
verified: 2026-11-10
status: concept
broader: [kt:amazon-web-services]
related: [kt:aws-partitions]
relations:
  - {predicate: hasPart, object: kt:aws-regions}
  - {predicate: hasPart, object: kt:edge-locations}
  - {predicate: hasPart, object: kt:local-zones}
  - {predicate: hasPart, object: kt:aws-outposts}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Global Infrastructure - AWS"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/global-infrastructure/
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Regions and Zones - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "AWS Regions and Availability Zones - AWS Regions and Availability Zones"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-regions-availability-zones.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "AWS service types - AWS Fault Isolation Boundaries (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/aws-service-types.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Global services - AWS Fault Isolation Boundaries (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/global-services.html
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "AWS Local Zones and AWS Outposts, choosing the right technology for your edge workload"
    publisher: Amazon Web Services (AWS Compute Blog)
    url: https://aws.amazon.com/blogs/compute/aws-local-zones-and-aws-outposts-choosing-the-right-technology-for-your-edge-workload/
    type: official-blog
    accessed: 2026-11-10
  - id: s7
    title: "Content Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# AWS global infrastructure

AWS global infrastructure is the umbrella term for every physical place AWS runs services: Regions and their Availability Zones (AZs), Local Zones and Dedicated Local Zones, Wavelength Zones, Outposts installed on customer premises, Amazon CloudFront edge locations and regional edge caches, and the private fiber backbone that links them.[^s1] Understanding how these pieces nest is the basis for every placement decision about latency, resilience and data residency.[^s3]

## The building blocks

- **Regions** are separate geographic areas, isolated from one another; resources are not copied between Regions unless you replicate them.[^s2][^s3]
- **Availability Zones** are isolated locations inside a Region, each with one or more data centers that have redundant power, networking and connectivity in separate facilities, linked to the other AZs by low-latency, redundant metro fiber.[^s3]
- **Local Zones** extend a Region into a metro area close to users, with their own internet connections and Direct Connect support; zone codes add a location suffix to the Region code, such as `us-west-2-lax-1`.[^s2]
- **Wavelength Zones** place AWS compute and storage at the edge of telecom carriers' 5G networks and are managed by the parent Region's control plane.[^s2]
- **Outposts** are pools of AWS capacity at a customer site, operated by AWS as part of a Region, in rack and server form factors.[^s2]
- **Edge locations** host CloudFront points of presence and regional edge caches for content delivery.[^s1]

Local Zones, Wavelength Zones and AZs can all sit in one VPC; Local Zones and Wavelength Zones are off by default, so you opt in to the zone before creating a subnet there.[^s2]

## Scale (as of 2026-11)

AWS's global infrastructure page lists 39 launched Regions with 124 AZs, with two more Regions (Saudi Arabia and Chile) and seven more AZs announced. It also cites more than 750 CloudFront points of presence, 15 regional edge caches, 33 Wavelength Zones and nearly 20 million km of terrestrial and subsea fiber.[^s1] These counts change frequently; check the page before quoting them.

## Fault isolation and global services

AWS describes its services by scope: zonal services fail within one AZ, so you design redundancy across AZs; regional services span several AZs and are built to survive the loss of one; global services operate across a whole partition.[^s4] Most global services run a single-Region control plane with a distributed data plane. IAM, AWS Organizations, Route 53 and CloudFront have control planes in `us-east-1`, while Global Accelerator's is in `us-west-2`.[^s5] Data planes such as IAM authentication and Route 53 DNS resolution keep working when the control plane is impaired. For that reason AWS advises keeping control-plane calls (creating or changing resources, or using the console) out of failover and recovery paths.[^s5] S3 bucket creation and deletion also depend on `us-east-1`, because bucket names must be globally unique.[^s5]

## Choosing where to run

The default is a Region, spread across multiple AZs for high availability.[^s3] AWS's edge guidance is to use a Region or Local Zone where possible, and to prefer a Local Zone over Outposts when both fit. Local Zones give single-digit-millisecond latency to users in the same metro, and AWS owns their capacity and facilities.[^s6] Outposts suit sub-millisecond on-premises latency or data that must stay in a facility you choose. In return, you supply power and redundant networking, and you need Enterprise Support or Enterprise On-Ramp.[^s6] Wavelength targets mobile and 5G applications that need ultra-low latency.[^s3]

## Connections

- **Has part: AWS Regions** (`kt:aws-regions`), each containing its Availability Zones.[^s3]
- **Has part: edge locations** (`kt:edge-locations`), the CloudFront presence outside Regions.[^s1]
- **Has part: Local Zones** (`kt:local-zones`) and **AWS Outposts** (`kt:aws-outposts`), the Region extensions compared above.[^s2][^s6]
- **Related to AWS partitions** (`kt:aws-partitions`), the scope within which global services' data planes run.[^s5]
- **Covered by the AWS Certified Cloud Practitioner** (`kt:aws-certified-cloud-practitioner`): task 3.2 asks candidates to define the global infrastructure and explain how Regions, AZs and edge locations relate.[^s7]

## Sources
[^s1]: Amazon Web Services, "Global Infrastructure - AWS". https://aws.amazon.com/about-aws/global-infrastructure/ (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Regions and Zones - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html (accessed 2026-11-10)
[^s3]: Amazon Web Services, "AWS Regions and Availability Zones". https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-regions-availability-zones.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "AWS service types - AWS Fault Isolation Boundaries". https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/aws-service-types.html (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Global services - AWS Fault Isolation Boundaries". https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/global-services.html (accessed 2026-11-10)
[^s6]: Amazon Web Services, AWS Compute Blog, "AWS Local Zones and AWS Outposts, choosing the right technology for your edge workload". https://aws.amazon.com/blogs/compute/aws-local-zones-and-aws-outposts-choosing-the-right-technology-for-your-edge-workload/ (accessed 2026-11-10)
[^s7]: Amazon Web Services, "Content Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html (accessed 2026-11-10)
