---
id: kt:aws-ai-factories
type: Service
title: AWS AI Factories
aliases: [AWS AI Factory, AI Factories (AWS)]
domain: global-infrastructure
summary: >-
  AWS AI Factories are dedicated AI environments, built from Trainium and NVIDIA accelerators, AWS
  networking, storage and AI services, that AWS deploys and fully manages inside a customer's own data
  center for workloads with strict sovereignty or isolation needs.
verified: 2026-11-17
status: current
broader: [kt:aws-global-infrastructure]
related: [kt:aws-outposts, kt:local-zones, kt:data-residency-on-aws, kt:aws-european-sovereign-cloud]
relations:
  - {predicate: alternativeTo, object: kt:aws-outposts}
  - {predicate: runsOn, object: kt:aws-nitro-system}
  - {predicate: hasPart, object: kt:aws-trainium-and-inferentia}
  - {predicate: integratesWith, object: kt:amazon-bedrock}
  - {predicate: integratesWith, object: kt:amazon-sagemaker-ai}
  - {predicate: dependsOn, object: kt:aws-regions}
sources:
  - id: s1
    title: "AWS AI Factories"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/global-infrastructure/ai-factories/
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "AWS AI Factories FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/global-infrastructure/ai-factories/faqs/
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Introducing AWS AI Factories"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/aws-ai-factories/
    type: official-blog
    accessed: 2026-11-17
  - id: s4
    title: "Enabling AI sovereignty on AWS"
    publisher: Amazon Web Services (AWS Security Blog)
    url: https://aws.amazon.com/blogs/security/enabling-ai-sovereignty-on-aws/
    type: official-blog
    accessed: 2026-11-17
  - id: s5
    title: "New AWS AI Factories transform customers' existing infrastructure into high-performance AI environments"
    publisher: Amazon (About Amazon)
    url: https://www.aboutamazon.com/news/aws/aws-data-centers-ai-factories
    type: news
    accessed: 2026-11-17
created: 2026-11-17
---

# AWS AI Factories

AWS AI Factories put AWS-built AI infrastructure inside a data center the customer already owns or leases from a colocation provider. The customer supplies floor space and power; AWS deploys, operates and manages the accelerators, network, storage and services on top.[^s1][^s2] AWS announced the offering as available on 2 December 2025 at re:Invent, with engagement through the AWS account team rather than self-service sign-up.[^s3] Each deployment is built exclusively for one customer, or for a designated "trusted community" of organizations with tenant isolation between them.[^s2][^s3]

## How it works

The hardware is the same AWS-designed compute, networking and storage used in public Regions. Accelerators listed in the FAQ (as of 2026-11) are AWS Trainium (Trn2, Trn3) and NVIDIA P6-B200, P6-B300, P6e-GB200 and P6e-GB300 UltraServers, with later NVIDIA Rubin platforms planned; general-purpose, compute-, memory- and storage-optimized EC2 families can sit alongside them.[^s2] Accelerators are wired into EC2 UltraClusters over Elastic Fabric Adapter in a petabit-scale, non-blocking fabric, and storage includes Amazon FSx for Lustre and S3 Express One Zone.[^s1]

A factory is attached to a parent Region of the customer's choice over the AWS global network, and a private Direct Connect point of presence at the facility links the customer's own network without crossing the public internet.[^s2] Teams use the parent Region's normal console and API endpoints: once an administrator authorizes accounts or an organization, the factory shows up next to that Region's Availability Zones.[^s2] Services named in the FAQ include EC2, ECS, EKS, AWS Batch, EBS, VPC, Elastic Load Balancing and Shield, plus Amazon Bedrock (through a distributed inference engine, with AWS validating which third-party models are offered) and SageMaker AI.[^s2] AWS's launch post notes that Bedrock gives access to foundation models without signing separate contracts with each model provider.[^s3]

## Security and sovereignty

Hosts run on the AWS Nitro System, which AWS says leaves no operator access path: AWS staff cannot log in to hosts or read memory or encrypted customer data.[^s1][^s2] Training and inference data, including Bedrock endpoints, stay inside the factory unless the customer chooses to integrate Region services such as S3.[^s2] Customer-managed keys are supported for most services, and the KMS External Key Store lets keys live outside AWS; IAM and Control Tower handle governance.[^s2] Only AWS personnel operate the infrastructure, and extra controls such as audit programs or cleared personnel are available on request.[^s2] Amazon's launch coverage says the offering is built to AWS security standards for government workloads at Unclassified, Sensitive, Secret and Top Secret levels.[^s5] The FAQ itself names no compliance authorizations, so check FedRAMP or other scope with AWS rather than assuming it.[^s2]

## Pricing, timelines and gotchas

- **No list price.** Pricing is custom, based on location, scale, accelerators and services, and is set after a joint assessment.[^s2] Service-specific SLAs apply.[^s2]
- **Months, not minutes.** AWS estimates roughly 3–6 months from data center handover to a running factory, depending on configuration and component availability.[^s2] A site readiness assessment comes first.[^s2]
- **You own the building constraints.** Power and space for growth are the customer's job; AWS plans expansion jointly, supports adding newer EC2 generations and supports EC2 Capacity Reservations.[^s2]
- **Region dependency.** Because the console and APIs live in the parent Region, link resilience to that Region matters for management even when data stays local.[^s2]

## When to use it (and when not)

AWS aims AI Factories at governments and at enterprises with compute-heavy AI training or inference that needs a dedicated, isolated environment under strict residency rules.[^s1][^s2] AWS's security blog lists it beside Outposts, Local Zones, Dedicated Local Zones and Regions (including the European Sovereign Cloud) as ways to bring AWS to where data must live, singling out AI Factories as physically isolated, single-customer deployments for mission-critical AI.[^s4] If a standard or sovereign Region already meets residency rules, renting accelerators there avoids the facility work and multi-month lead time; for general on-premises compute at smaller scale, Outposts is the usual fit.[^s4]

## Connections

AI Factories sit under **AWS global infrastructure** as a customer-sited extension of a parent **AWS Region**.[^s2] They are an **alternative to AWS Outposts** and **related to Local Zones** as ways of placing AWS capacity close to data.[^s4] Their security model **runs on the AWS Nitro System**, their accelerators include **AWS Trainium**, and they **integrate with Amazon Bedrock and SageMaker AI** for model access and training.[^s1][^s2] They serve the same goals as **data residency on AWS** and the **AWS European Sovereign Cloud**, but on the customer's premises.[^s4]

## Sources
[^s1]: Amazon Web Services, "AWS AI Factories". https://aws.amazon.com/about-aws/global-infrastructure/ai-factories/ (accessed 2026-11-17)
[^s2]: Amazon Web Services, "AWS AI Factories FAQs". https://aws.amazon.com/about-aws/global-infrastructure/ai-factories/faqs/ (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Introducing AWS AI Factories", What's New with AWS (2025-12-02). https://aws.amazon.com/about-aws/whats-new/2025/12/aws-ai-factories/ (accessed 2026-11-17)
[^s4]: Stéphane Israël, "Enabling AI sovereignty on AWS", AWS Security Blog (2026-05-12). https://aws.amazon.com/blogs/security/enabling-ai-sovereignty-on-aws/ (accessed 2026-11-17)
[^s5]: Amazon, "New AWS AI Factories transform customers' existing infrastructure into high-performance AI environments", About Amazon (2025-12-02). https://www.aboutamazon.com/news/aws/aws-data-centers-ai-factories (accessed 2026-11-17)
