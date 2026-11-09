---
id: kt:aws-govcloud-us
type: InfrastructureConcept
title: AWS GovCloud (US)
aliases: [GovCloud, AWS GovCloud, AWS GovCloud (US) Regions, GovCloud (US-West), GovCloud (US-East)]
domain: public-sector-and-compliance
summary: >-
  A pair of isolated AWS Regions, GovCloud (US-West) and GovCloud (US-East), administered only by
  U.S.-citizen AWS staff, with separate accounts and credentials, built for U.S. government and
  regulated workloads such as CUI, ITAR data, FedRAMP High and DoD SRG IL4/IL5.
verified: 2026-11-09
status: current
broader: [kt:aws-global-infrastructure]
related: [kt:aws-regions, kt:itar-on-aws, kt:aws-secret-and-top-secret-regions, kt:aws-organizations]
relations:
  - {predicate: partOf, object: kt:aws-partitions}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: compliesWith, object: kt:dod-cloud-computing-srg-impact-levels}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
sources:
  - id: s1
    title: "What Is AWS GovCloud (US)? - AWS GovCloud (US) User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/whatis.html
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "AWS GovCloud (US) Compared to Standard AWS Regions - AWS GovCloud (US) User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-differences.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Maintaining U.S. International Traffic in Arms Regulations (ITAR) Compliance - AWS GovCloud (US) User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-itar.md
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Compliance - AWS GovCloud (US) User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-compliance.md
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "CreateGovCloudAccount - AWS Organizations API Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/APIReference/API_CreateGovCloudAccount.html
    type: official-docs
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS GovCloud (US)

AWS GovCloud (US) is a set of isolated AWS Regions intended for U.S. government agencies and other customers that need to move sensitive workloads to the cloud under strict regulatory requirements. There are two of them, AWS GovCloud (US-West) and AWS GovCloud (US-East), and AWS also allows non-government workloads to run there when they need the Regions' particular controls.[^s1] AWS states that the Regions are logically and physically administered only by AWS personnel who are U.S. citizens, which helps customers meet export-control obligations under ITAR and the Export Administration Regulations (EAR), and that they can host all categories of Controlled Unclassified Information (CUI).[^s1]

## How it works

GovCloud is not just another Region you enable. It is physically isolated and network-isolated from all other AWS Regions, and its authentication stack is separate, so standard AWS credentials cannot reach it and GovCloud credentials cannot reach commercial Regions.[^s2] Every GovCloud account is linked to a standard AWS account: billing and usage reports for the GovCloud account appear only in that linked standard account.[^s2] Sign-ups are reviewed to confirm the customer is a U.S. entity, and account credentials must be managed by a U.S. Person.[^s2] You sign in to the GovCloud console as an IAM user with a user name and password rather than with an email address, and all service API endpoints use FIPS 140-3 validated cryptographic modules unless a service's endpoint notes say otherwise.[^s2]

Several defaults differ from commercial Regions. CloudTrail is turned on automatically for accounts created after 15 December 2014, although SNS notifications for it must be set up separately.[^s2] FIDO security keys and authenticator apps work for MFA as they do elsewhere, but TOTP hardware tokens need a GovCloud-compatible device because of the separate authentication stack.[^s2] Support comes from U.S.-based, U.S.-citizen engineers around the clock by phone, email and chat.[^s2] Service capabilities can also differ, so AWS directs customers to check each service's GovCloud notes.[^s2]

## Compliance scope

AWS describes GovCloud as a place to build solutions that meet the FedRAMP High baseline, the CJIS Security Policy, ITAR, EAR, DoD Cloud Computing SRG Impact Levels 2, 4 and 5, FIPS 140-3 and IRS Publication 1075, among other regimes.[^s4] Authorization is per service, not per Region: AWS's FedRAMP services-in-scope table (updated 15 October 2026) now labels the GovCloud column "FedRAMP Class D (formerly High baseline)" and the commercial US East/West column "Class C (formerly Moderate baseline)".[^s5] Some services are in scope only on one side; for example, the table marks Amazon EC2 and Amazon S3 in both, Amazon AppFlow only in Class C, and Amazon Managed Grafana only in Class D (as of 2026-11).[^s5] Always check the current table rather than assuming that a service in GovCloud is authorized.[^s5]

## Gotchas

ITAR use carries obligations for the customer, not just AWS: you must qualify as a U.S. Person, hold a valid DDTC registration, keep full export privileges and run an export-compliance program, and remove export-controlled data if those privileges lapse.[^s3] The GovCloud account owner must be a U.S. person even with no ITAR data, but AWS does not require IAM users or application users to be U.S. persons; restricting their access is your job under the shared responsibility model.[^s3] Some metadata fields, such as S3 bucket names, must not contain export-controlled data.[^s2] AWS recommends designing across both GovCloud Regions.[^s2]

In AWS Organizations, `CreateGovCloudAccount` is called from the management account of a commercial organization that is already paired with a GovCloud account. It creates two accounts with the same email: a commercial member account that handles billing and support, and a standalone GovCloud account.[^s6] The GovCloud account does not join the commercial organization; a GovCloud organization's management account has to invite it.[^s6] Tags in that request apply only to the commercial account.[^s6]

## When to use it (and when not)

Use GovCloud when contracts or regulations require U.S.-citizen operations, ITAR or EAR handling, DoD IL4/IL5, or FedRAMP High (Class D) scope.[^s1][^s4] For FedRAMP Moderate (Class C) workloads, the commercial US East/West Regions may be enough and offer services that GovCloud lacks, so compare the scope table first.[^s5] Expect extra operational overhead from separate credentials, separate organizations and per-service feature differences.[^s2][^s6]

## Connections

GovCloud is part of the broader **AWS global infrastructure** and is its own **partition**, which explains the hard credential and endpoint boundary covered under **AWS Regions**.[^s2] Its value lies in its compliance scope, so it links by `compliesWith` to **FedRAMP on AWS** and to **DoD Cloud Computing SRG Impact Levels**, and relates to **ITAR on AWS**.[^s4][^s5] Access is `securedBy` **IAM**, since IAM users are the console sign-in identity, and it is `monitoredBy` **AWS CloudTrail**, which is on by default.[^s2] Multi-account setups pass through **AWS Organizations**' paired commercial and GovCloud organizations.[^s6] Because GovCloud is documented for CUI and other unclassified data, it is related to, but distinct from, the **AWS Secret and Top Secret Regions** node.[^s2]

## Sources
[^s1]: Amazon Web Services, "What Is AWS GovCloud (US)?". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/whatis.html (accessed 2026-11-09)
[^s2]: Amazon Web Services, "AWS GovCloud (US) Compared to Standard AWS Regions". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-differences.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Maintaining U.S. International Traffic in Arms Regulations (ITAR) Compliance". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-itar.md (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Compliance - AWS GovCloud (US)". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-compliance.md (accessed 2026-11-09)
[^s5]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-09)
[^s6]: Amazon Web Services, "CreateGovCloudAccount". https://docs.aws.amazon.com/organizations/latest/APIReference/API_CreateGovCloudAccount.html (accessed 2026-11-09)
