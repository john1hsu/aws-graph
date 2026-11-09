---
id: kt:fedramp-on-aws
type: ComplianceProgram
title: FedRAMP on AWS
aliases: [FedRAMP, Federal Risk and Authorization Management Program, AWS FedRAMP, FedRAMP Certification on AWS]
domain: public-sector-and-compliance
summary: >-
  How the U.S. government's FedRAMP cloud security program applies to AWS: AWS GovCloud (US) holds
  FedRAMP Certified Class D (formerly High) and the US East/West Regions Class C (formerly Moderate),
  scoped per service, with agencies still issuing their own ATOs on top of inherited controls.
verified: 2026-11-09
status: current
broader: [kt:aws-compliance-programs]
related: [kt:aws-govcloud-us, kt:aws-artifact, kt:aws-security-services, kt:aws-cloudtrail]
relations:
  - {predicate: scopedTo, object: kt:aws-govcloud-us}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:dod-cloud-computing-srg-impact-levels}
  - {predicate: implements, object: kt:nist-sp-800-53}
sources:
  - id: s1
    title: "FedRAMP - Amazon Web Services (AWS)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/fedramp
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "FedRAMP FAQs - Amazon Web Services"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/fedramp-faqs/
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Certification Classes - FedRAMP Consolidated Rules for 2026"
    publisher: FedRAMP (GSA)
    url: https://www.fedramp.gov/2026/agencies/use/classes/
    type: government
    accessed: 2026-11-09
  - id: s5
    title: "FedRAMP Certification - FedRAMP Consolidated Rules for 2026"
    publisher: FedRAMP (GSA)
    url: https://www.fedramp.gov/2026/reference/fedramp-certification/
    type: government
    accessed: 2026-11-09
  - id: s6
    title: "Prepare for FedRAMP 20x with AWS automation and validation"
    publisher: AWS Public Sector Blog
    url: https://aws.amazon.com/blogs/publicsector/prepare-for-fedramp-20x-with-aws-automation-and-validation
    type: official-blog
    accessed: 2026-11-09
created: 2026-11-09
---

# FedRAMP on AWS

FedRAMP is the U.S. government-wide program that standardizes how cloud products used by federal agencies are security-assessed, certified and continuously monitored. It is built on the NIST SP 800-53 control catalog and requires an independent assessment by a FedRAMP Recognized Independent Assessment Service (the role formerly called a 3PAO).[^s2] On AWS, FedRAMP is not a feature you switch on: it is a status that AWS holds for specific Regions and specific services, which customers then build on and inherit from.[^s1]

## What AWS holds

As of 2026-11, AWS states that **AWS GovCloud (US)** is FedRAMP Certified **Class D (formerly High baseline)** and is suitable for high-impact workloads, while **AWS US East-West** (Northern Virginia, Ohio, Oregon and Northern California) is FedRAMP Certified **Class C (formerly Moderate baseline)**.[^s1] The older JAB Provisional ATO that AWS used to cite is now described as a legacy designation superseded by FedRAMP Certification.[^s1]

Certification is per service as well as per Region. The services-in-scope table (last updated 15 October 2026) has separate Class C (East/West) and Class D (GovCloud) columns; a checkmark means the service is inside the current certification boundary.[^s3] Core services such as Amazon EC2, Amazon S3, AWS Lambda and Amazon Bedrock are marked in both columns, while some, such as Amazon Location Service and Amazon Managed Grafana, appear only under Class D, and some entries carry bracketed exclusions (for example, CloudFront delivery through Embedded POPs is excluded).[^s3] Services still being assessed are marked as undergoing independent assessment rather than certified.[^s3]

## How it works for customers

FedRAMP does not grant an ATO to a cloud provider. Each agency still issues its own ATO under the Risk Management Framework for the system it builds, using the provider's certification package and reviewing its Ongoing Certification Reports.[^s2] Customers inherit many controls from certified AWS infrastructure and services, but an initial analysis of which controls are inherited versus customer-implemented decides their own responsibilities; AWS points customers to service configuration guides and its ATO on AWS team for help.[^s1]

Package access depends on who you are. Government employees and contractors request the AWS certification package, including Ongoing Certification Reports, from the FedRAMP PMO; commercial customers and partners get a FedRAMP Partner Package through AWS Artifact or their account manager.[^s1][^s2] AWS publishes quarterly Ongoing Certification Reports and undergoes an annual independent assessment, and states that FedRAMP compliance does not raise service prices in any Region (as of 2026-11).[^s2]

## The 2026 rules: classes, Rev5 and 20x

The FedRAMP Consolidated Rules for 2026 (CR26) renamed "authorization" to "certification" and introduced certification Classes A to D.[^s2][^s4] FedRAMP stresses that classes describe the **level of assurance** a provider offers, not its level of security, and are not one-for-one replacements for Low, Moderate and High: agencies should categorize their own system under FIPS 199 and FIPS 200 first.[^s4] Class C is presumed adequate for most Low- and Moderate-impact systems, and Class D for most systems regardless of impact level.[^s4]

Providers certify through one of two programs. The Rev5 program requires defined NIST SP 800-53 Rev. 5 controls for Classes B to D, while the 20x program requires automated validation of Key Security Indicators, with Class C needing at least two automated methods per indicator and Class D at least four.[^s5] FedRAMP's rules list 2026-07-04 for initial certification under 20x and 2027-01-01 under Rev5, with ongoing-certification rules applying to both from 2027-01-01.[^s5] AWS's own guidance for software vendors pursuing 20x maps evidence collection to AWS Config, Security Hub, CloudTrail, Audit Manager and Inspector, and uses Config with EventBridge to detect significant changes.[^s6]

## Gotchas

- A service running in GovCloud is not automatically Class D; check the table for each service and any exclusions.[^s3]
- Class C or D at the infrastructure layer does not certify your application. If you sell a SaaS product to agencies, you need your own certification, building on AWS's inherited controls.[^s1][^s5]
- DoD workloads follow the separate DoD Cloud Computing SRG, which AWS lists on its own services-in-scope page.[^s3]

## When to use it (and when not)

Choose the commercial US East/West Regions when a Class C (formerly Moderate) boundary and the needed services are enough; but confirm each service, because some are certified on only one side.[^s3] Choose GovCloud when the system needs Class D (formerly High) scope, such as high-impact workloads.[^s1] For workloads with no federal requirement, the services-in-scope table is still a useful proxy for which services have been independently assessed.[^s3]

## Connections

FedRAMP on AWS sits under the broader **AWS compliance programs** and `implements` the **NIST SP 800-53** control baselines.[^s2] It is `scopedTo` **AWS GovCloud (US)** for Class D and to the commercial US **AWS Regions** for Class C.[^s1] It `contrastsWith` the **DoD Cloud Computing SRG impact levels**, a separate DoD scheme that is often confused with it.[^s3] Commercial customers obtain the partner package through **AWS Artifact**, and the evidence for continuous monitoring comes from **AWS security services** and **AWS CloudTrail**.[^s1][^s6]

## Sources
[^s1]: Amazon Web Services, "FedRAMP". https://aws.amazon.com/compliance/fedramp (accessed 2026-11-09)
[^s2]: Amazon Web Services, "FedRAMP FAQs". https://aws.amazon.com/compliance/fedramp-faqs/ (accessed 2026-11-09)
[^s3]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-09)
[^s4]: FedRAMP, "Certification Classes - FedRAMP Consolidated Rules for 2026". https://www.fedramp.gov/2026/agencies/use/classes/ (accessed 2026-11-09)
[^s5]: FedRAMP, "FedRAMP Certification - FedRAMP Consolidated Rules for 2026". https://www.fedramp.gov/2026/reference/fedramp-certification/ (accessed 2026-11-09)
[^s6]: AWS Public Sector Blog, "Prepare for FedRAMP 20x with AWS automation and validation" (2026-02-18). https://aws.amazon.com/blogs/publicsector/prepare-for-fedramp-20x-with-aws-automation-and-validation (accessed 2026-11-09)
