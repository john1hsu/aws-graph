---
id: kt:aws-dedicated-local-zones
type: InfrastructureConcept
title: AWS Dedicated Local Zones
aliases: [Dedicated Local Zones, Dedicated Local Zone, DLZ]
domain: global-infrastructure
summary: >-
  AWS Dedicated Local Zones are Local Zones built for the exclusive use of one customer or community,
  placed at a location the customer chooses and fully managed and operated by AWS, aimed at sovereign,
  public-sector and regulated workloads that need data isolation and in-country residency.
verified: 2026-11-18
status: current
broader: [kt:local-zones, kt:aws-global-infrastructure]
related: [kt:aws-european-sovereign-cloud, kt:aws-ai-factories]
relations:
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: alternativeTo, object: kt:aws-outposts}
  - {predicate: runsOn, object: kt:aws-nitro-system}
  - {predicate: enables, object: kt:data-residency-on-aws}
sources:
  - id: s1
    title: "AWS Dedicated Local Zones FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/dedicatedlocalzones/faqs/
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "AWS Dedicated Local Zones features"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/dedicatedlocalzones/features/
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "AWS Digital Sovereignty Pledge: Announcing new dedicated infrastructure options (AWS Security Blog)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/security/aws-digital-sovereignty-pledge-announcing-new-dedicated-infrastructure-options
    type: official-blog
    accessed: 2026-11-18
  - id: s4
    title: "Meet digital sovereignty needs with AWS Dedicated Local Zones expanded services (AWS Security Blog)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/security/meet-digital-sovereignty-needs-with-aws-dedicated-local-zones-expanded-services
    type: official-blog
    accessed: 2026-11-18
  - id: s5
    title: "Local snapshots in Local Zones - Amazon EBS User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ebs/latest/userguide/snapshots-localzones.html
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "Amazon EBS launches Local Snapshots for AWS Dedicated Local Zones (What's New with AWS)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2024/12/amazon-ebs-local-snapshots-aws-dedicated-local-zones
    type: official-blog
    accessed: 2026-11-18
created: 2026-11-18
---

# AWS Dedicated Local Zones

AWS Dedicated Local Zones (DLZs) are a variant of AWS Local Zones reserved for a single customer or a defined community, rather than open to every AWS account. AWS places the infrastructure in a location the customer specifies, such as the customer's own data center, a co-location facility or an AWS site, and AWS builds, operates and maintains it.[^s1][^s2] AWS announced them in August 2023 under its Digital Sovereignty Pledge, with Singapore's Smart Nation and Digital Government Group (working through GovTech Singapore) as the first customer.[^s3][^s4] The offering is generally available; AWS sells it through a configuration engagement with its specialists rather than self-service enablement.[^s1][^s2]

## How it works

Like any Local Zone, a DLZ is an extension of a parent Region: you reach it with the same APIs, console and tools, and management services such as AWS CloudFormation, Amazon CloudWatch and AWS CloudTrail run from the parent Region.[^s2] AWS manages connectivity from the DLZ back to that Region, plus capacity planning and day-to-day operations, which is the main operational difference from Outposts.[^s1] A DLZ can serve multiple AWS accounts across a customer's agencies or business units, so one deployment can act as a shared government or community cloud.[^s3]

Getting one is a programme rather than a click. The customer defines the services, configuration and security requirements, agrees a build-out plan with AWS, and then goes through launch, acceptance and orientation before workloads move in.[^s2] Customers can deploy several DLZs for resiliency; AWS says Region and service-level SLAs apply when DLZs are deployed in two or more isolated locations, and it suggests AWS Elastic Disaster Recovery for replication between them.[^s2][^s3]

## Services and sovereignty controls

At launch the service set was Amazon EC2, Amazon VPC, Amazon EBS, Elastic Load Balancing, Amazon ECS, Amazon EKS and AWS Direct Connect.[^s3] AWS's features page now also lists EC2 accelerated-computing and HPC instance families, Amazon S3 (including S3 Express One Zone and S3 One Zone-Infrequent Access), Amazon FSx, NAT gateways, AWS Transit Gateway, Amazon EMR and Route 53 geoproximity routing (as of 2026-11).[^s2] A December 2025 AWS post added EC2 seventh-generation instances, EBS gp3 and io1 volumes, local AMIs and Amazon Data Lifecycle Manager for automating local snapshots.[^s4]

Isolation controls are the point of the offering. DLZs meet the same AWS security standards as Regions, run on the AWS Nitro System, and can add data-access monitoring and audit programmes, restrictions so that only customer-selected accounts can use the infrastructure, and residency or nationality clearance criteria for the AWS staff who operate it.[^s1][^s3] They follow the shared responsibility model, and AWS says a DLZ can inherit the certifications and attestations of its parent Region.[^s1][^s2] DLZs can also be parented to the AWS European Sovereign Cloud Region in Germany, in which case they follow that Region's operating model with locally based personnel.[^s1]

## Gotchas

- **Snapshots go to the parent Region by default.** EBS snapshots of Local Zone volumes are stored in Amazon S3 in the parent Region unless you choose local storage. Since December 2024 EBS Local Snapshots keep backups inside the DLZ, but residency is only guaranteed if you enforce it with IAM policies that deny regional snapshot creation.[^s5][^s6]
- **Local snapshots are sticky and limited.** Once a volume has a local snapshot, all later snapshots of it must also be local; local snapshots restore only in the same zone and do not support fast snapshot restore, Recycle Bin, snapshot archive, EBS direct APIs or VM Import/Export.[^s5]
- **No public price list.** AWS describes pay-as-you-go consumption but publishes no prices, minimums or lead times; terms are set in the engagement (as of 2026-11).[^s1][^s2]
- **Parent-Region dependency.** Control-plane and management services live in the parent Region, so its availability and the DLZ-to-Region link matter to operations.[^s2]

## When to use it (and when not)

AWS targets DLZs at governments with classified or sensitive workloads, regulated industries tied to on-premises systems, research organisations building community clouds, and companies whose internal policies require dedicated infrastructure.[^s1] Choose **AWS Outposts** instead when you are prepared to own facility security, connectivity and capacity planning for racks on your premises; choose a DLZ when you want AWS to carry that work.[^s1] Standard **Local Zones** suit latency needs where shared, multi-tenant infrastructure is acceptable.[^s1]

## Connections

- **Narrower than AWS Local Zones** (`kt:local-zones`) and part of **AWS global infrastructure** (`kt:aws-global-infrastructure`): a Local Zone dedicated to one customer or community.[^s1]
- **Scoped to AWS Regions** (`kt:aws-regions`), since every DLZ is parented to a Region whose services manage it.[^s2]
- **Alternative to AWS Outposts** (`kt:aws-outposts`), the customer-operated way to run AWS infrastructure on premises.[^s1]
- **Related to AWS AI Factories** (`kt:aws-ai-factories`), another AWS-managed, customer-sited offering; DLZs themselves support accelerated compute for AI/ML inference, LLMs and HPC.[^s2]
- **Runs on the AWS Nitro System** (`kt:aws-nitro-system`) for host isolation.[^s1]
- **Enables data residency on AWS** (`kt:data-residency-on-aws`) through local storage and local snapshots.[^s4][^s6]
- **Related to the AWS European Sovereign Cloud** (`kt:aws-european-sovereign-cloud`), which can act as a DLZ's parent Region.[^s1]

## Sources

[^s1]: Amazon Web Services, "AWS Dedicated Local Zones FAQs". https://aws.amazon.com/dedicatedlocalzones/faqs/ (accessed 2026-11-18)
[^s2]: Amazon Web Services, "AWS Dedicated Local Zones features". https://aws.amazon.com/dedicatedlocalzones/features/ (accessed 2026-11-18)
[^s3]: Amazon Web Services, "AWS Digital Sovereignty Pledge: Announcing new dedicated infrastructure options", AWS Security Blog, 2023-08-23. https://aws.amazon.com/blogs/security/aws-digital-sovereignty-pledge-announcing-new-dedicated-infrastructure-options (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Meet digital sovereignty needs with AWS Dedicated Local Zones expanded services", AWS Security Blog, 2025-12-12. https://aws.amazon.com/blogs/security/meet-digital-sovereignty-needs-with-aws-dedicated-local-zones-expanded-services (accessed 2026-11-18)
[^s5]: Amazon Web Services, "Local snapshots in Local Zones - Amazon EBS User Guide". https://docs.aws.amazon.com/ebs/latest/userguide/snapshots-localzones.html (accessed 2026-11-18)
[^s6]: Amazon Web Services, "Amazon EBS launches Local Snapshots for AWS Dedicated Local Zones", What's New with AWS, 2024-12-16. https://aws.amazon.com/about-aws/whats-new/2024/12/amazon-ebs-local-snapshots-aws-dedicated-local-zones (accessed 2026-11-18)
