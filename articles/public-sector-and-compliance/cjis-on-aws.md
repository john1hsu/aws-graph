---
id: kt:cjis-on-aws
type: ComplianceProgram
title: CJIS on AWS
aliases: [AWS CJIS compliance, CJIS Security Policy on AWS, Criminal Justice Information Services on AWS, CJI on AWS]
domain: public-sector-and-compliance
summary: >-
  How U.S. criminal justice and public safety agencies host criminal justice information (CJI) on AWS
  under the FBI CJIS Security Policy: there is no central CJIS authorization, so agencies and their
  state CJIS Systems Agency assess compliance, typically relying on customer-controlled encryption keys
  in AWS KMS and the AWS Nitro System, in either AWS GovCloud (US) or the US commercial Regions.
verified: 2026-11-20
status: current
broader: [kt:aws-compliance-programs]
related: [kt:fedramp-on-aws, kt:aws-govcloud-us, kt:fips-140-3-on-aws, kt:data-residency-on-aws]
relations:
  - {predicate: governedBy, object: kt:shared-responsibility-model}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: securedBy, object: kt:aws-nitro-system}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "Using AWS for Criminal Justice Information Solutions (CJIS Compliance)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/cjis/
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Continued innovation in CJIS compliance in both AWS GovCloud (US) and AWS US Commercial Regions"
    publisher: Amazon Web Services (AWS Public Sector Blog)
    url: https://aws.amazon.com/blogs/publicsector/continued-innovation-cjis-compliance-both-aws-govcloud-us-aws-us-commercial-regions/
    type: official-blog
    accessed: 2026-11-20
  - id: s3
    title: "How the latest FBI CJIS Security Policy updates help you control your criminal justice information"
    publisher: Amazon Web Services (AWS Public Sector Blog)
    url: https://aws.amazon.com/blogs/publicsector/latest-fbi-cjis-security-policy-updates-help-you-control-criminal-justice-information/
    type: official-blog
    accessed: 2026-11-20
  - id: s4
    title: "CJIS Security Policy, Section 5: Policy and Implementation"
    publisher: FBI Criminal Justice Information Services Division
    url: https://le.fbi.gov/cjis-division/cjis-security-policy-resource-center/policy-and-implementation
    type: government
    accessed: 2026-11-20
created: 2026-11-20
---

# CJIS on AWS

The FBI's Criminal Justice Information Services (CJIS) Division sets the CJIS Security Policy, the baseline that law enforcement and other agencies must meet when they access, store or exchange criminal justice information (CJI) such as records returned from FBI systems.[^s4] The policy applies to criminal justice agencies, noncriminal justice agencies (government and private) and their contractors, and it says CJI needs protection throughout its life cycle.[^s4] AWS supports agencies that put CJI workloads in the cloud, but it does not hold a "CJIS certification": AWS notes there is no central CJIS authorization body, no accredited assessor pool and no standard assessment method.[^s1] Compliance is decided by the agency and its oversight chain, not stamped on the provider.

## How it works

The CJIS policy is built on agreements. Before CJI is exchanged, the parties must have formal agreements in place, and each state's CJIS Systems Agency (CSA) head signs a user agreement with the FBI CJIS Division.[^s4] Private contractors that handle CJI must be bound by the CJIS Security Addendum, approved by the FBI Director under 28 CFR 20.33(a)(7).[^s4] In practice the state CSA and its information security officer decide how a cloud deployment is assessed.

Traditionally that meant fingerprint-based background checks, security awareness training and signed addendums for vendor staff who could reach CJI.[^s3] The 2022 update to the policy (version 5.9.1, as covered by AWS) changed the personnel-security area so that fingerprint checks may not be needed for all cloud-provider staff, depending on the service and on who can reach the encryption keys.[^s3] The CJIS ISO Program does not recommend letting the cloud provider access the keys, and the update allows service offerings to be built so the provider cannot see unencrypted CJI.[^s3]

AWS's design answer rests on two services. **AWS KMS** lets agencies create and control their own customer managed keys; AWS says KMS uses FIPS 140-3 validated hardware security modules.[^s1] The **AWS Nitro System** blocks all interactive administrative access to Nitro instances, including by Amazon employees.[^s2][^s3] AWS argues that together these remove the need to rely on physical security and background checks to keep its staff away from unencrypted CJI.[^s1]

## Regions, encryption and gotchas

CJIS on AWS began in early 2019 with reference to AWS GovCloud (US). Since then, agencies have also run CJIS workloads in the US commercial Regions while meeting the policy's data residency rules, using the same Nitro and KMS controls.[^s2] AWS cites Georgia state agencies using AWS Managed Services in the commercial Regions under the Georgia Technology Authority's shared services program.[^s2]

AWS states that all its services that store data at rest support AES-256 symmetric encryption (FIPS 197), and that FIPS endpoints, offered in both the US East/West commercial Regions and GovCloud (US), are needed for CJI in transit.[^s1] A common mistake is to call the default (non-FIPS) endpoint from a CJI workload. For hybrid services such as AWS Storage Gateway and AWS Snowball, the agency also manages the local volumes or transfer workstations and the S3 buckets behind them, including at-rest encryption.[^s1]

The policy also sets operational controls the agency must run itself, such as logging successful and failed log-on attempts, reviewing audit records at least weekly, keeping audit records for at least one year, and validating accounts at least once a year.[^s4] AWS CloudTrail and similar logs help, but retention and review are on the agency. AWS publishes a CJIS reference architecture from its compliance page as a starting point for these designs.[^s1]

## When to use it (and when not)

AWS's position is that agencies can deploy CJIS workloads in either GovCloud (US) or the US commercial Regions, so the choice usually turns on what the CSA, contracts and other programs (such as FedRAMP High or ITAR needs) require, and on where the needed services are available.[^s2] Either way, engage the state CSA early, because it sets the assessment approach that AWS cannot set for you.[^s1][^s4]

## Connections

CJIS on AWS sits under **AWS compliance programs** as a policy with no provider certification.[^s1] It is `governedBy` the **shared responsibility model**, since the agency owns keys, endpoints, local devices and audit review.[^s1][^s4] It is `securedBy` **AWS KMS** and the **AWS Nitro System**, the two controls AWS builds its CJIS case on, and `monitoredBy` **AWS CloudTrail** for the audit duties.[^s2][^s3][^s4] It is `scopedTo` **AWS Regions** and related to **AWS GovCloud (US)** and **data residency on AWS**, because workloads may run in GovCloud or the US commercial Regions under residency rules.[^s2] It relates to **FIPS 140-3 on AWS** for FIPS endpoints and validated HSMs, and to **FedRAMP on AWS**, which AWS's CJIS page links as related assurance.[^s1]

## Sources
[^s1]: Amazon Web Services, "Using AWS for Criminal Justice Information Solutions (CJIS Compliance)". https://aws.amazon.com/compliance/cjis/ (accessed 2026-11-20)
[^s2]: Amazon Web Services, "Continued innovation in CJIS compliance in both AWS GovCloud (US) and AWS US Commercial Regions" (2023-10-06). https://aws.amazon.com/blogs/publicsector/continued-innovation-cjis-compliance-both-aws-govcloud-us-aws-us-commercial-regions/ (accessed 2026-11-20)
[^s3]: Amazon Web Services, "How the latest FBI CJIS Security Policy updates help you control your criminal justice information" (2022-11-15). https://aws.amazon.com/blogs/publicsector/latest-fbi-cjis-security-policy-updates-help-you-control-criminal-justice-information/ (accessed 2026-11-20)
[^s4]: FBI CJIS Division, "CJIS Security Policy, Section 5: Policy and Implementation". https://le.fbi.gov/cjis-division/cjis-security-policy-resource-center/policy-and-implementation (accessed 2026-11-20)
