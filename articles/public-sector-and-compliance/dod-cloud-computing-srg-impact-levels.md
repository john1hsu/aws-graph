---
id: kt:dod-cloud-computing-srg-impact-levels
type: ComplianceProgram
title: DoD Cloud Computing SRG impact levels
aliases: [DoD CC SRG, DoD CSP SRG, DoD Cloud Service Provider SRG, DoD SRG impact levels, DoD Impact Levels, DISA impact levels, DoD provisional authorization]
domain: public-sector-and-compliance
summary: >-
  The U.S. Department of Defense scheme, run by DISA, that grades cloud offerings by the sensitivity of
  DoD data they may hold (IL2, IL4, IL5, IL6); on AWS, the US East/West Regions hold IL2, AWS GovCloud (US)
  holds IL2, IL4 and IL5, and the AWS Secret Region holds IL6, scoped per service.
verified: 2026-11-10
status: current
broader: [kt:aws-compliance-programs]
related: [kt:aws-artifact, kt:aws-direct-connect, kt:aws-regions]
relations:
  - {predicate: scopedTo, object: kt:aws-govcloud-us}
  - {predicate: scopedTo, object: kt:aws-secret-and-top-secret-regions}
  - {predicate: dependsOn, object: kt:fedramp-on-aws}
  - {predicate: implements, object: kt:nist-sp-800-53}
sources:
  - id: s1
    title: "DoD Cloud Service Provider Security Requirements Guide - Amazon Web Services"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/dod/
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "DoD CSM FAQs - Amazon Web Services"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/dod-csm-faqs/
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "AWS Services in Scope by Compliance Program: DoD CC SRG"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/DoD_CC_SRG/
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Case Study - Logical Separation on AWS (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/es_es/whitepapers/latest/logical-separation/case-study.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Cloud Computing Security Requirements Guide (CC SRG) briefing"
    publisher: Defense Information Systems Agency (DISA)
    url: https://disa.mil/-/media/Files/DISA/News/Events/Symposium/Cloud-Computing-Security-Requirements-Guide.ashx
    type: government
    accessed: 2026-11-10
  - id: s6
    title: "The DoD CSP SRG and DoD MO SRG"
    publisher: Schellman
    url: https://www.schellman.com/blog/federal-compliance/the-dod-csp-srg-and-dod-mo-srg
    type: partner
    accessed: 2026-11-10
created: 2026-11-10
---

# DoD Cloud Computing SRG impact levels

The DoD Cloud Computing Security Requirements Guide (SRG), now published as the DoD Cloud Service Provider (CSP) SRG, gives cloud providers a standardized way to earn a DoD **provisional authorization (PA)**, building on FedRAMP as the assessment baseline.[^s1] Its organizing idea is the **impact level (IL)**: a grade for how sensitive the DoD information is that a cloud offering may host. A PA is granted to a specific cloud service offering, not to the provider as a whole.[^s5]

## The four impact levels in use

- **IL2**: information approved for public release plus some low-confidentiality data that needs minimal access control, such as public websites; it is tied to the FedRAMP Moderate baseline.[^s5]
- **IL4**: Controlled Unclassified Information (CUI) and non-critical mission information in non-National Security Systems, limited to U.S.-person personnel with background-check designations.[^s5]
- **IL5**: higher-sensitivity CUI, mission-critical information and National Security Systems, limited to the federal community and requiring separation from non-federal tenants; DISA's briefing put the step from IL4 at nine extra FedRAMP+ controls and enhancements.[^s5]
- **IL6**: classified information up to SECRET, needing cleared facilities and personnel and a DoD-approved SIPRNet connection.[^s5]

Former levels 1 and 3 were rolled into the next level up, which is why the sequence skips them.[^s5]

## What AWS holds

As of 2026-11, AWS states that the **US East and US West Regions** hold IL2, **AWS GovCloud (US)** holds IL2, IL4 and IL5 under a DISA provisional authorization, and the **AWS Secret Region** holds a DoD PA for workloads up to and including Secret (IL6).[^s1][^s2] AWS says it leveraged its FedRAMP status to reach these authorizations, and that compliance programs do not raise service prices.[^s1][^s2]

Scope is per service. The services-in-scope table (last updated 2026-10-14) has five columns: IL2 East/West, IL2 GovCloud, IL4 GovCloud, IL5 GovCloud and IL6 Secret Region.[^s3] Core services such as Amazon EC2, Amazon S3, AWS Lambda and Amazon Bedrock are marked in all five, while others are marked only for IL2 East/West or show "3PAO Assessment" or "DISA Review" for some levels.[^s3] Exclusions apply: AWS Direct Connect is approved for IL2, IL4 and IL5 only at U.S.-soil locations, and CloudFront delivery through Embedded POPs is excluded.[^s3]

## How IL5 separation was met

The SRG text for IL5 calls for physical separation from non-DoD, non-federal tenants, but also lets a provider propose alternatives of equivalent security, judged case by case.[^s4] AWS's whitepaper describes how it showed that logical separation combined with dedicated tenancy met the intent of that requirement in GovCloud; AWS marks that whitepaper as historical reference.[^s4]

## How mission owners use it

A PA is not permission to run your system. The mission owner's authorizing official must still grant an **Authorization to Operate (ATO)** for each application, relying on the PA artifacts but assessing the parts the customer configures.[^s5] On AWS, you build your own authorization package and system security plan, inherit physical controls (no data-center walkthrough is needed), and stay responsible for OS and application STIGs and DoD ports-and-protocols guidance.[^s1][^s2] DoD organizations and contractors request AWS's documentation through their account manager, while non-government partners can download the partner FedRAMP package from AWS Artifact.[^s2] Private connectivity at IL4 and IL5 runs through a DoD CIO-approved **Cloud Access Point (CAP)** that the mission owner, not the provider, connects to.[^s5] Prospective IL4/IL5 customers are told to contact DISA to start approval.[^s2]

## Gotchas and recent changes

- AWS's compliance page still cites SRG Version 1, Release 3, while DISA has since retired the combined CC SRG in favor of a CSP SRG (for providers) and a Mission Owner SRG (for customers).[^s1][^s6]
- The CSP SRG aligns with NIST SP 800-53 Rev. 5 and extends FedRAMP reciprocity: IL4 can rest on FedRAMP High plus readiness checks, while IL5 and IL6 need FedRAMP High plus CNSSI 1253 overlays.[^s6] Providers with existing IL5 or IL6 authorizations had to move to the Rev. 5 requirements by the end of 2025, according to a 3PAO's summary.[^s6]
- A service available in GovCloud is not automatically IL5; check its column and footnotes.[^s3]

## When to use it (and when not)

Use the commercial US Regions only for IL2 data. Use GovCloud for CUI at IL4 or IL5, and the Secret Region for classified IL6 work.[^s2] For non-DoD federal work, FedRAMP certification classes are the relevant scheme instead.[^s1]

## Connections

The impact levels sit under the broader **AWS compliance programs** and `implement` **NIST SP 800-53** controls through FedRAMP+ additions.[^s5][^s6] They `dependsOn` **FedRAMP on AWS**, since DoD reuses FedRAMP assessments as its baseline.[^s1] They are `scopedTo` **AWS GovCloud (US)** for IL4/IL5 and to the **AWS Secret and Top Secret Regions** for IL6, with IL2 also covering commercial **AWS Regions**.[^s2] **AWS Artifact** supplies partner packages, and **AWS Direct Connect** carries an explicit IL2–IL5 scope note.[^s2][^s3]

## Sources
[^s1]: Amazon Web Services, "DoD Cloud Service Provider Security Requirements Guide". https://aws.amazon.com/compliance/dod/ (accessed 2026-11-10)
[^s2]: Amazon Web Services, "DoD CSM FAQs". https://aws.amazon.com/compliance/dod-csm-faqs/ (accessed 2026-11-10)
[^s3]: Amazon Web Services, "AWS Services in Scope by Compliance Program: DoD CC SRG". https://aws.amazon.com/compliance/services-in-scope/DoD_CC_SRG/ (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Case Study - Logical Separation on AWS". https://docs.aws.amazon.com/es_es/whitepapers/latest/logical-separation/case-study.html (accessed 2026-11-10)
[^s5]: DISA, "Cloud Computing Security Requirements Guide (CC SRG)" briefing (May 2018). https://disa.mil/-/media/Files/DISA/News/Events/Symposium/Cloud-Computing-Security-Requirements-Guide.ashx (accessed 2026-11-10)
[^s6]: Schellman, "The DoD CSP SRG and DoD MO SRG" (updated 2026-03-10). https://www.schellman.com/blog/federal-compliance/the-dod-csp-srg-and-dod-mo-srg (accessed 2026-11-10)
