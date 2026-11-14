---
id: kt:aws-outposts
type: Service
title: AWS Outposts
aliases: [Outposts, Outpost, AWS Outposts racks, AWS Outposts servers, Outposts rack, Outposts server]
domain: global-infrastructure
summary: >-
  AWS Outposts is a fully managed service that installs AWS-owned racks or servers at a customer's own
  site and runs them as an extension of an Availability Zone in a parent Region, so the same EC2, EBS,
  VPC and other APIs can serve workloads that need on-premises latency, local processing or data residency.
verified: 2026-11-14
status: current
broader: [kt:aws-global-infrastructure]
related: [kt:amazon-vpc, kt:aws-govcloud-us, kt:shared-responsibility-model]
relations:
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: alternativeTo, object: kt:local-zones}
  - {predicate: integratesWith, object: kt:amazon-ec2}
  - {predicate: integratesWith, object: kt:aws-direct-connect}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
sources:
  - id: s1
    title: "What is AWS Outposts? - AWS Outposts (User Guide for Outposts racks)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/outposts/latest/userguide/what-is-outposts.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "AWS Outposts racks FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/outposts/rack/faqs
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "AWS Outposts servers FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/outposts/servers/faqs
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "AWS Outposts racks pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/outposts/rack/pricing/
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "Second-generation AWS Outposts racks now in the AWS GovCloud (US) Regions"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/aws-outposts-govcloud-us-regions/
    type: official-blog
    accessed: 2026-11-14
  - id: s6
    title: "AWS Services in Scope by Compliance Program - FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-14
created: 2026-11-14
---

# AWS Outposts

AWS Outposts is a fully managed service that puts AWS infrastructure, APIs and tools inside a customer's own data center or colocation facility. An Outpost is a pool of AWS compute and storage capacity at that site which AWS operates, monitors and manages as part of a Region, so applications built against the Region's programming interfaces run unchanged on premises with local latency and local data processing.[^s1] The racks, servers, switches and cabling remain AWS-owned equipment; the customer supplies the building, power and network that meet AWS's site requirements.[^s1][^s2]

## Form factors

- **Outposts racks** are industry-standard 42U racks with servers, switches, a patch panel and a power shelf. Multi-rack deployments add an Aggregation, Core, Edge (ACE) rack, which AWS requires once you have four or more compute racks.[^s1] Second-generation racks add a dedicated Outposts network rack, switch-failure resiliency and newer instance families such as M8i, C8i and R8i. There is no in-place upgrade from the first generation, so moving to the second means decommissioning and re-ordering.[^s2]
- **Single-rack second-generation Outposts** fit one 42U rack drawing 10–30 kVA, aimed at sites short on space and power (FAQ as of 2026-11).[^s2]
- **Outposts servers** are 1U or 2U servers for a standard 19-inch rack, aimed at branch and edge sites. The 1U model offers 64 vCPUs, 128 GiB of memory and 4 TB of NVMe storage on Graviton2 (C6gd instances), and the 2U model up to 128 vCPUs, 256 GiB and 8 TB on Intel (C6id instances).[^s3]

## How it works

Each Outpost is an extension of one Availability Zone in its parent Region. A **service link** carries control-plane and management traffic back to that Region, and you create VPC subnets on the Outpost so its instances talk privately to Region-based resources in the same VPC.[^s1] On-premises traffic leaves through a **local gateway** on racks or a **local network interface** on servers.[^s1] The service link can ride a Direct Connect private or public virtual interface or a VPN over the public internet, and second-generation rack uplinks run at 10, 40 or 100 Gbps.[^s2] You can't connect an Outpost to another Outpost or a Local Zone within the same VPC.[^s1]

Racks run EC2, EBS volumes, S3 on Outposts, ECS, EKS nodes, RDS, ElastiCache, EMR, Application Load Balancers and Route 53, while servers support a narrower set: EC2, ECS, VPC subnets and IoT Greengrass. Availability varies by parent Region.[^s1]

## Pricing, quotas and gotchas

- **Billing model.** Rack capacity is bought as a configuration of EC2 and storage for a term, paid All Upfront, Partial Upfront or No Upfront, with charges starting 24 hours after installation. The price includes delivery, installation, servicing and patching.[^s4] The rack FAQ lists 3-year and 5-year single-rack terms with a 576 vCPU and 20 TB EBS minimum, and a 1-year term that needs at least 960 vCPU (as of 2026-11).[^s2] Multi-rack orders start at 960 vCPU and 60 TB of EBS.[^s2] Server billing starts at the earlier of installation or the 31st day after delivery.[^s3] If you don't act at term end, capacity renews monthly at the No Upfront rate.[^s2][^s4]
- **Extras.** Operating systems and managed services such as RDS or EKS are billed on top of the hardware configuration. Data transfer to the parent Region or out through the local gateway is free, but transfer from the Region into the Outpost is charged at Region rates. AWS Enterprise Support is required to order a rack.[^s4][^s2]
- **Not built to run disconnected.** If the service link drops, running instances and EBS volumes keep serving local traffic, but launch, stop and other control-plane calls fail, and metrics and logs are cached for only up to 7 days.[^s2][^s3]
- **Some metadata leaves the site.** EBS and S3 on Outposts keep data local, but some metadata, such as instance IDs, metrics and bucket names, still goes to the Region.[^s2]

## Compliance and public sector

The FedRAMP services-in-scope list includes AWS Outposts (software) at both the East/West and GovCloud authorization levels (page checked 2026-11), and AWS states that the authorization excludes the hardware, so physical and environmental controls at the site are the customer's job.[^s6][^s2] Second-generation racks became orderable for both AWS GovCloud (US) Regions in September 2026.[^s5] Customer content is bound to a removable Nitro Security Key that is destroyed before hardware returns to AWS.[^s2]

## When to use it (and when not)

Use Outposts when the compute must physically sit in a facility you choose, whether for low latency to on-premises systems, local data processing or residency rules, and you want AWS's APIs rather than your own hypervisor stack.[^s1][^s5] If a nearby AWS-owned site is acceptable, a Local Zone avoids the power, space, term commitment and support-plan requirements.[^s2][^s4]

## Connections

Outposts are part of the **AWS global infrastructure** and are scoped to a parent **Availability Zone** and Region through the service link.[^s1] They are an **alternative to Local Zones** for edge placement. Workloads mostly run as **Amazon EC2** instances in **Amazon VPC** subnets, and the service link often runs over **AWS Direct Connect**.[^s1][^s2] Under the **shared responsibility model**, the customer takes on physical security, and the **FedRAMP** authorization in **AWS GovCloud (US)** covers the software but not the hardware.[^s2][^s5][^s6]

## Sources
[^s1]: Amazon Web Services, "What is AWS Outposts?". https://docs.aws.amazon.com/outposts/latest/userguide/what-is-outposts.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "AWS Outposts racks FAQs". https://aws.amazon.com/outposts/rack/faqs (accessed 2026-11-14)
[^s3]: Amazon Web Services, "AWS Outposts servers FAQs". https://aws.amazon.com/outposts/servers/faqs (accessed 2026-11-14)
[^s4]: Amazon Web Services, "AWS Outposts racks pricing". https://aws.amazon.com/outposts/rack/pricing/ (accessed 2026-11-14)
[^s5]: Amazon Web Services, "Second-generation AWS Outposts racks now in the AWS GovCloud (US) Regions" (What's New, 2026-09-02). https://aws.amazon.com/about-aws/whats-new/2026/09/aws-outposts-govcloud-us-regions/ (accessed 2026-11-14)
[^s6]: Amazon Web Services, "AWS Services in Scope by Compliance Program - FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-14)
