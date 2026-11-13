---
id: kt:aws-secret-and-top-secret-regions
type: InfrastructureConcept
title: AWS Secret and Top Secret Regions
aliases: [AWS Secret Region, AWS Secret Cloud, AWS Secret-West, AWS Top Secret Cloud, AWS Top Secret-East, AWS Top Secret-West, AWS classified Regions]
domain: public-sector-and-compliance
summary: >-
  AWS's physically and network-isolated Regions for U.S. classified workloads: two Secret-level Regions
  (Secret, 2017, and Secret-West, 2025), authorized at DoD SRG IL6 and ICD 503, and two Top Secret Regions
  (Top Secret-East, 2014, and Top Secret-West, 2021) for the intelligence, defense and national security communities.
verified: 2026-11-13
status: current
broader: [kt:aws-global-infrastructure]
related: [kt:aws-regions, kt:aws-govcloud-us, kt:availability-zones, kt:disaster-recovery-strategies]
relations:
  - {predicate: compliesWith, object: kt:dod-cloud-computing-srg-impact-levels}
  - {predicate: compliesWith, object: kt:nist-sp-800-53}
  - {predicate: compliesWith, object: kt:icd-503}
  - {predicate: integratesWith, object: kt:aws-direct-connect}
  - {predicate: contrastsWith, object: kt:aws-govcloud-us}
sources:
  - id: s1
    title: "AWS Secret Cloud - Amazon Web Services"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/federal/secret-cloud/
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "AWS Top Secret Cloud - Amazon Web Services"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/federal/top-secret-cloud/
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "AWS Services in Scope by Compliance Program: DoD CC SRG"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/DoD_CC_SRG/
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "AWS Secret-West Region is now available"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/10/aws-secret-west-region-is-now-available
    type: official-blog
    accessed: 2026-11-13
  - id: s5
    title: "Announcing the New AWS Secret Region"
    publisher: AWS Public Sector Blog
    url: https://aws.amazon.com/blogs/publicsector/announcing-the-new-aws-secret-region
    type: official-blog
    accessed: 2026-11-13
  - id: s6
    title: "Announcing second AWS Top Secret Region, extending support for U.S. government classified missions"
    publisher: AWS Public Sector Blog
    url: https://aws.amazon.com/blogs/publicsector/announcing-second-aws-top-secret-region-extending-support-us-government-classified-missions
    type: official-blog
    accessed: 2026-11-13
  - id: s7
    title: "Amazon to launch second Secret cloud Region in 2025"
    publisher: AWS Public Sector Blog
    url: https://aws.amazon.com/blogs/publicsector/amazon-to-launch-second-secret-cloud-region-in-2025
    type: official-blog
    accessed: 2026-11-13
  - id: s8
    title: "15 additional AWS services authorized at DoD Impact Level 6 for the AWS Secret Region"
    publisher: AWS Security Blog
    url: https://aws.amazon.com/blogs/security/15-additional-aws-services-authorized-dod-impact-level-6-aws-secret-region
    type: official-blog
    accessed: 2026-11-13
created: 2026-11-13
---

# AWS Secret and Top Secret Regions

AWS operates a small set of Regions built only for U.S. government **classified** data, separate from both the commercial Regions and AWS GovCloud (US). Two are accredited up to the **Secret** level and two up to **Top Secret**.[^s1][^s2] AWS positions them as the classified end of a range that runs from Unclassified and CUI through Secret to Top Secret.[^s2][^s5] They serve the U.S. Intelligence Community and Department of Defense, plus federal civilian agencies, the defense industrial base, research centers and other mission partners.[^s1][^s2]

## The four Regions

- **AWS Top Secret-East** (2014): the first Top Secret Region. AWS called it the first air-gapped commercial cloud accredited for classified workloads.[^s6]
- **AWS Secret Region** (announced November 2017): for workloads up to Secret. At launch, Intelligence Community members reached it through the IC's Commercial Cloud Services (C2S) contract, and other cleared agencies used their own contract vehicles.[^s5]
- **AWS Top Secret-West** (December 2021): a second Top Secret Region with multiple Availability Zones, located more than 1,000 miles from Top Secret-East.[^s6]
- **AWS Secret-West** (live October 22, 2025): a second Secret Region in the western U.S. It places capacity closer to western missions and makes multi-Region architectures possible at the Secret level.[^s4][^s7]

## How they differ from other Regions

The Top Secret cloud has complete physical and network isolation and 24/7 support from cleared U.S. citizens.[^s2] On-premises classified networks connect over AWS Direct Connect, not the internet.[^s1][^s2] Each classified Region has multiple Availability Zones. Customers can replicate synchronously between AZs and asynchronously between Regions, the same resilience model used in the rest of AWS.[^s2][^s7] AWS says the Secret Region meets the same reliability, availability and durability standards as its other Regions.[^s1]

Accreditation is the main difference. The Secret Regions are authorized at DoD Cloud Computing SRG **Impact Level 6** and under **ICD 503**, the intelligence directive for authorizing IT systems.[^s1][^s4] The Top Secret Regions comply with ICD 503 and NIST SP 800-53 Rev. 4.[^s2][^s6] DISA's IL6 provisional authorization also uses the DoD CC SRG and CNSS Instruction 1253.[^s8]

## Service availability and gotchas

- **The service catalog is smaller and authorized one service at a time.** In March 2020 AWS had 26 services at IL6 in the Secret Region. They were core building blocks: EC2, EBS, S3, VPC, IAM, KMS, RDS, DynamoDB, CloudTrail, CloudFormation and similar services.[^s8] The current list is the IL6 column of AWS's DoD CC SRG Services in Scope page.[^s3] There, a double asterisk means a service is IL6-authorized but not yet generally available in the Region. Other entries still show "3PAO Assessment" or "DISA Review".[^s3] Check that page before designing around a service.
- **Onboarding goes through AWS.** The Secret and Top Secret pages and the Secret-West announcement direct interested organizations to contact AWS. None of them publishes prices.[^s1][^s2][^s4]
- **Secret-level multi-Region DR is new.** It became possible only after Secret-West opened in 2025. Before that, Secret workloads could spread across AZs but had no second Secret Region.[^s4][^s7]

## When to use them (and when not)

Use the classified Regions only for data that is actually classified at Secret (DoD IL6) or Top Secret, and only with the required clearances and network access.[^s1][^s2] On AWS's DoD scope page, IL4 and IL5 are in AWS GovCloud (US). IL2 is in the commercial US East/West Regions and GovCloud.[^s3] Moving up the scale means fewer authorized services and more operational overhead, so choose the lowest level that fits the data's classification.[^s3][^s8]

## Connections

These Regions are part of the **AWS global infrastructure** and follow the same **Region/Availability Zone** model.[^s2][^s7] They **comply with** the **DoD SRG impact levels** (IL6), **ICD 503** and **NIST SP 800-53**.[^s1][^s2][^s8] They **integrate with** **AWS Direct Connect** for links to accredited networks.[^s1] They **contrast with** **AWS GovCloud (US)**, which covers unclassified-but-regulated data up to IL5.[^s3] Two Regions at each level make **disaster recovery strategies** across Regions possible.[^s6][^s7]

## Sources
[^s1]: Amazon Web Services, "AWS Secret Cloud". https://aws.amazon.com/federal/secret-cloud/ (accessed 2026-11-13)
[^s2]: Amazon Web Services, "AWS Top Secret Cloud". https://aws.amazon.com/federal/top-secret-cloud/ (accessed 2026-11-13)
[^s3]: Amazon Web Services, "AWS Services in Scope by Compliance Program: DoD CC SRG". https://aws.amazon.com/compliance/services-in-scope/DoD_CC_SRG/ (accessed 2026-11-13)
[^s4]: Amazon Web Services, "AWS Secret-West Region is now available" (What's New, 2025-10-22). https://aws.amazon.com/about-aws/whats-new/2025/10/aws-secret-west-region-is-now-available (accessed 2026-11-13)
[^s5]: AWS Public Sector Blog, "Announcing the New AWS Secret Region" (2017-11-20). https://aws.amazon.com/blogs/publicsector/announcing-the-new-aws-secret-region (accessed 2026-11-13)
[^s6]: AWS Public Sector Blog, "Announcing second AWS Top Secret Region, extending support for U.S. government classified missions" (2021-12-06). https://aws.amazon.com/blogs/publicsector/announcing-second-aws-top-secret-region-extending-support-us-government-classified-missions (accessed 2026-11-13)
[^s7]: AWS Public Sector Blog, "Amazon to launch second Secret cloud Region in 2025" (2025-06-10, updated 2025-11). https://aws.amazon.com/blogs/publicsector/amazon-to-launch-second-secret-cloud-region-in-2025 (accessed 2026-11-13)
[^s8]: AWS Security Blog, "15 additional AWS services authorized at DoD Impact Level 6 for the AWS Secret Region" (2020-03-17). https://aws.amazon.com/blogs/security/15-additional-aws-services-authorized-dod-impact-level-6-aws-secret-region (accessed 2026-11-13)
