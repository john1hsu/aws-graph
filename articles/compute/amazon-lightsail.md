---
id: kt:amazon-lightsail
type: Service
title: Amazon Lightsail
aliases: [Lightsail, AWS Lightsail]
domain: compute
summary: >-
  AWS's simplified, fixed-monthly-price service for virtual private servers, containers, managed
  databases, load balancers, CDN distributions, storage and DNS, aimed at websites and small web
  applications that don't need the full configurability of EC2.
verified: 2026-11-25
status: current
broader: [kt:aws-compute-services]
related: [kt:amazon-ec2, kt:aws-elastic-beanstalk, kt:aws-free-tier]
relations:
  - {predicate: alternativeTo, object: kt:amazon-ec2}
  - {predicate: integratesWith, object: kt:vpc-peering}
  - {predicate: governedBy, object: kt:lightsail-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is Amazon Lightsail?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lightsail/latest/userguide/what-is-amazon-lightsail.html
    type: official-docs
    accessed: 2026-11-25
  - id: s2
    title: "Amazon Lightsail Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/lightsail/pricing/
    type: official-docs
    accessed: 2026-11-25
  - id: s3
    title: "Lightsail instance bundles"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lightsail/latest/userguide/amazon-lightsail-bundles.html
    type: official-docs
    accessed: 2026-11-25
  - id: s4
    title: "Billing and account management - Amazon Lightsail"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lightsail/latest/userguide/amazon-lightsail-frequently-asked-questions-faq-billing-and-account-management.html
    type: official-docs
    accessed: 2026-11-25
  - id: s5
    title: "Amazon Lightsail endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/lightsail.html
    type: official-docs
    accessed: 2026-11-25
  - id: s6
    title: "Connect Lightsail resources to AWS services using VPC peering"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lightsail/latest/userguide/lightsail-how-to-set-up-vpc-peering-with-aws-resources.html
    type: official-docs
    accessed: 2026-11-25
created: 2026-11-25
---

# Amazon Lightsail

Amazon Lightsail is AWS's entry-level hosting service. AWS positions it as the easiest way to start on AWS for anyone building websites or web applications, sold at a low, predictable monthly price.[^s1] Rather than assembling an EC2 instance, a VPC, security groups, EBS volumes and a load balancer by hand, you pick a preconfigured plan in a simplified console and Lightsail sets up the networking around it.[^s1]

## How it works

Lightsail bundles its own versions of the core building blocks: instances (virtual private servers), container services, managed MySQL or PostgreSQL databases, load balancers, CDN distributions built on CloudFront infrastructure, SSD block storage disks, object storage buckets, static IP addresses, DNS management for registered domains, and snapshots for backup.[^s1] The documentation describes individual developers, hobbyists and projects that need only a few servers as the typical users, and lists EC2, EC2 Auto Scaling, Elastic Load Balancing, RDS and ECS as the related full-featured services.[^s1]

An instance is launched from a **bundle**: a fixed combination of vCPUs, memory, SSD storage and a monthly data transfer allowance, billed at an on-demand hourly rate.[^s3] Each bundle comes with public addressing as public IPv4, dual-stack IPv4 plus IPv6, or IPv6 only, and Linux/Unix and Windows bundles exist in both IPv4 and IPv6-only variants.[^s3] Instances can grow by attaching block storage disks, adding load balancers for high availability, and enabling VPC peering to reach other AWS resources.[^s1]

## Pricing (as of 2026-11)

Prices are listed in USD per month and are not broken out by Region; the data transfer allowance is, as described below.[^s2] Linux/Unix bundles with a public IPv4 address start at $5/month (0.5 GB memory, 2 vCPUs, 20 GB SSD, 1 TB transfer). IPv6-only Linux bundles start at $3.50/month with 512 MB memory and the same vCPU, disk and transfer figures. Windows with IPv4 starts at $9.50/month.[^s2] Memory-optimized and compute-optimized plans start at $74 and $42 per month.[^s2] Other resources are also flat-priced: a load balancer is $18/month, the cheapest Standard managed database is $15/month (High Availability $30), the Nano container service is $7/month, and CDN distributions start at $2.50/month for 50 GB.[^s2]

Billing is hourly, and deleting an instance mid-month is prorated by the hours used.[^s4] A common surprise is that instances and managed databases accrue charges until they are deleted, even when stopped.[^s4] Lightsail's old short-term free trials have been replaced by the AWS Free Tier credit offer.[^s4]

Data transfer has its own rules. Inbound and outbound traffic both count toward the allowance, but only outbound traffic beyond it incurs overage.[^s2] Plans in Asia Pacific (Mumbai), (Sydney), (Jakarta), (Malaysia), (Hong Kong) and South America (São Paulo) include half the listed allowance.[^s2]

## Quotas and gotchas

Default quotas apply per supported Region unless noted, and new accounts may start lower.[^s5] Lightsail instances are capped by vCPUs, 20 per Region (adjustable). Static IP addresses default to 5 per Region (adjustable). Fixed quotas include 6 DNS zones per account, 10 load balancers and 40 databases per Region, 20 distributions and 20 buckets per account, and 15 block storage disks per instance with up to 16,000 GB per disk.[^s5]

VPC peering connects Lightsail only to the **default VPC** of a Region, and has to be enabled separately in each Region where you want the connection. Resources on the VPC side must also allow inbound traffic from Lightsail.[^s6] Services reached through public endpoints, such as Amazon S3, CloudFront and DynamoDB, don't need peering.[^s6]

## When to use it (and when not)

Lightsail fits blogs, small business sites, development and test servers, and simple web apps whose owners value one predictable bill over fine-grained control.[^s1][^s2] Once a workload needs a wider choice of instance types, Auto Scaling, Elastic Load Balancing features or tighter integration with the VPC, the full services (EC2, ELB, RDS, ECS) are the better fit, and Lightsail's peering link to the default VPC is the bridge between the two worlds.[^s1][^s6]

## Connections

- **Broader: AWS compute services** (`kt:aws-compute-services`). Lightsail is the simplified, fixed-price option in AWS's compute category, sitting alongside the full-featured services it lists as related.[^s1]
- **Alternative to Amazon EC2** (`kt:amazon-ec2`). EC2 is the configurable counterpart that the Lightsail documentation points to.[^s1]
- **Integrates with VPC peering** (`kt:vpc-peering`). A per-Region peering connection to the default VPC lets Lightsail reach resources such as RDS databases.[^s6]
- **Governed by Lightsail quotas** (`kt:lightsail-quotas`). These are the per-Region and per-account limits in the General Reference.[^s5]
- **Related: AWS Elastic Beanstalk and AWS Free Tier.** Beanstalk is the other "simple on-ramp" for web apps on EC2, and the Free Tier now replaces Lightsail's trials.[^s4]
- **Scoped to AWS Regions** (`kt:aws-regions`). Resources, quotas and transfer allowances are all Regional.[^s2][^s5]

## Sources
[^s1]: Amazon Web Services, "What is Amazon Lightsail?". https://docs.aws.amazon.com/lightsail/latest/userguide/what-is-amazon-lightsail.html (accessed 2026-11-25)
[^s2]: Amazon Web Services, "Amazon Lightsail Pricing". https://aws.amazon.com/lightsail/pricing/ (accessed 2026-11-25)
[^s3]: Amazon Web Services, "Lightsail instance bundles". https://docs.aws.amazon.com/lightsail/latest/userguide/amazon-lightsail-bundles.html (accessed 2026-11-25)
[^s4]: Amazon Web Services, "Billing and account management - Amazon Lightsail". https://docs.aws.amazon.com/lightsail/latest/userguide/amazon-lightsail-frequently-asked-questions-faq-billing-and-account-management.html (accessed 2026-11-25)
[^s5]: Amazon Web Services, "Amazon Lightsail endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/lightsail.html (accessed 2026-11-25)
[^s6]: Amazon Web Services, "Connect Lightsail resources to AWS services using VPC peering". https://docs.aws.amazon.com/lightsail/latest/userguide/lightsail-how-to-set-up-vpc-peering-with-aws-resources.html (accessed 2026-11-25)
