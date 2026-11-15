---
id: kt:data-residency-on-aws
type: Concept
title: Data residency on AWS
aliases: [AWS data residency, data localization on AWS, data residency controls, in-country data storage on AWS]
domain: public-sector-and-compliance
summary: >-
  How customers keep content stored and processed in a chosen geography on AWS: they pick the
  Regions, AWS commits not to move content out of them without agreement, and customers enforce the
  boundary with Region-restricting policies, AWS Control Tower data residency controls and, where no
  local Region exists, AWS Outposts or Local Zones.
verified: 2026-11-15
status: concept
broader: [kt:aws-compliance-programs]
related: [kt:aws-european-sovereign-cloud, kt:aws-outposts, kt:local-zones, kt:aws-nitro-system]
relations:
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: configuredVia, object: kt:aws-control-tower}
  - {predicate: securedBy, object: kt:service-control-policies}
  - {predicate: securedBy, object: kt:aws-kms}
sources:
  - id: s1
    title: "Data Privacy FAQ"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/data-privacy-faq/
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Defining Boundaries for Regional Services Access - Navigating GDPR Compliance on AWS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/navigating-gdpr-compliance/defining-boundaries-for-regional-services-access.html
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Optional controls - Designing an AWS Control Tower landing zone (AWS Prescriptive Guidance)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/designing-control-tower-landing-zone/optional.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "DRHCREL03-BP01 Use AWS Outposts or Local Zones for scenarios where data must reside within a country or jurisdiction without a local AWS Region - Data Residency and Hybrid Cloud Lens"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/data-residency-hybrid-cloud-services-lens/drhcrel03-bp01.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "Architecting for data residency with AWS Outposts rack and landing zone guardrails"
    publisher: AWS Compute Blog
    url: https://aws.amazon.com/blogs/compute/architecting-for-data-residency-with-aws-outposts-rack-and-landing-zone-guardrails/
    type: official-blog
    accessed: 2026-11-15
  - id: s6
    title: "European Digital Sovereignty FAQ"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/europe-digital-sovereignty/faq/
    type: official-docs
    accessed: 2026-11-15
created: 2026-11-15
---

# Data residency on AWS

Data residency is the requirement that certain data be stored, and often processed, inside a particular country or jurisdiction. On AWS the starting point is the customer's choice of Region: customers decide which AWS Region(s) hold their content, for example an Australian customer limiting storage to the Sydney Region.[^s1] AWS commits not to move or replicate content outside the chosen Region(s) without the customer's agreement. The FAQ names two exceptions: moves needed to provide a service the customer started, and moves required by law or a binding government order.[^s1] Residency is narrower than sovereignty. AWS frames sovereignty around operational control and independence as well as location.[^s6]

## How it works

Region choice is only the policy; customers enforce it themselves. The GDPR whitepaper shows an IAM or service control policy (SCP) that denies every action whose `aws:RequestedRegion` falls outside an approved list such as `eu-*`. It exempts commonly used global services: CloudFront, IAM, Route 53 and AWS Support.[^s2] Applied as an SCP in AWS Organizations, the same statement caps permissions for whole accounts or organizational units (OUs).[^s2] Regions launched after March 20, 2019 are disabled by default and must be enabled before use, which adds another gate.[^s2]

AWS Control Tower packages this pattern. Its Region deny control is an elective, preventive control that limits provisioning to approved Regions. It is enabled in the landing zone settings and then applies to every OU, not just one.[^s3] Control Tower also offers a set of data residency controls. Preventive ones block VPC internet access, Site-to-Site VPN connections and cross-Region networking. Detective ones flag public IP addresses, public S3 access settings and public EKS, Redshift or OpenSearch endpoints. None of these controls is on by default.[^s3]

For countries with no AWS Region, the Well-Architected Data Residency and Hybrid Cloud Lens recommends AWS Outposts or Local Zones. It suggests Outposts at more than one site with redundant power and network, and Amazon S3 on Outposts for local backup and recovery.[^s4]

## Gotchas

- **Most guardrails detect, not prevent.** Prescriptive Guidance notes that most data residency controls are detective. Because Region deny covers all OUs, check the approved list against every workload before enabling it.[^s3]
- **Metadata and management data.** In AWS's Outposts reference design, tags and KMS keys are outside the guardrails' scope. Application logs can still reach CloudWatch Logs in the parent Region if the application sends them, because the sample SCPs don't block `logs:PutLogEvents`.[^s5]
- **Copies are the usual leak.** The same design uses SCPs that deny snapshots to the Region, copying Outpost snapshots, RDS and ElastiCache snapshot copies, and `s3:PutObject` to regional buckets.[^s5]
- **Service availability varies.** Not every service runs in every Region, so a residency boundary can rule out services.[^s1]
- **Keys.** AWS offers encryption in transit and at rest and lets customers manage their own keys through AWS KMS, which limits who can read data wherever it sits.[^s1]

Residency controls restrict infrastructure, not applications. AWS's Outposts guidance advises confirming the design with legal and security teams.[^s5]

## When to use it (and when not)

Region selection plus SCPs or Control Tower controls covers most residency rules where a local Region exists.[^s2][^s3] Outposts or Local Zones fit jurisdictions without one.[^s4] When the requirement extends to who operates the cloud, a sovereign offering is the better fit. The AWS European Sovereign Cloud has its first Region in Brandenburg, Germany, with day-to-day operations controlled by EU residents located in the EU.[^s6]

## Connections

Data residency sits under **AWS compliance programs** as a customer-side control rather than a certification.[^s1] It is `scopedTo` **AWS Regions**, the unit customers choose.[^s1][^s2] It is `configuredVia` **AWS Control Tower** and `securedBy` **service control policies** and **AWS KMS**.[^s1][^s2][^s3] It relates to **AWS Outposts** and **Local Zones** for in-country hosting without a Region.[^s4][^s5] It also relates to the **AWS European Sovereign Cloud** and the **AWS Nitro System**, which AWS says has no operator access by design.[^s6]

## Sources
[^s1]: Amazon Web Services, "Data Privacy FAQ". https://aws.amazon.com/compliance/data-privacy-faq/ (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Defining Boundaries for Regional Services Access - Navigating GDPR Compliance on AWS". https://docs.aws.amazon.com/whitepapers/latest/navigating-gdpr-compliance/defining-boundaries-for-regional-services-access.html (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Optional controls - Designing an AWS Control Tower landing zone". https://docs.aws.amazon.com/prescriptive-guidance/latest/designing-control-tower-landing-zone/optional.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "DRHCREL03-BP01 Use AWS Outposts or Local Zones … - Data Residency and Hybrid Cloud Lens". https://docs.aws.amazon.com/wellarchitected/latest/data-residency-hybrid-cloud-services-lens/drhcrel03-bp01.html (accessed 2026-11-15)
[^s5]: AWS Compute Blog, "Architecting for data residency with AWS Outposts rack and landing zone guardrails". https://aws.amazon.com/blogs/compute/architecting-for-data-residency-with-aws-outposts-rack-and-landing-zone-guardrails/ (accessed 2026-11-15)
[^s6]: Amazon Web Services, "European Digital Sovereignty FAQ". https://aws.amazon.com/compliance/europe-digital-sovereignty/faq/ (accessed 2026-11-15)
