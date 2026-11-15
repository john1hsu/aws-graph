---
id: kt:itar-on-aws
type: ComplianceProgram
title: ITAR on AWS
aliases: [AWS ITAR compliance, International Traffic in Arms Regulations on AWS, ITAR in the cloud, ITAR-regulated workloads on AWS]
domain: public-sector-and-compliance
summary: >-
  How customers subject to the U.S. International Traffic in Arms Regulations (ITAR) handle
  defense-related technical data on AWS: there is no ITAR certification, so qualified U.S. persons
  use the AWS GovCloud (US) Regions, whose AWS-side access is limited to U.S. persons, and keep
  export-compliance duties under the shared responsibility model.
verified: 2026-11-15
status: current
broader: [kt:aws-compliance-programs]
related: [kt:fedramp-on-aws, kt:aws-partitions, kt:dod-cloud-computing-srg-impact-levels]
relations:
  - {predicate: scopedTo, object: kt:aws-govcloud-us}
  - {predicate: governedBy, object: kt:shared-responsibility-model}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: securedBy, object: kt:aws-cloudhsm}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
sources:
  - id: s1
    title: "US International Traffic in Arms Regulations (ITAR)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/itar/
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Maintaining U.S. International Traffic in Arms Regulations (ITAR) Compliance - AWS GovCloud (US) User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-itar.html
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Compliance - AWS GovCloud (US) User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-compliance.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "AWS GovCloud (US) FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/govcloud-us/faqs/
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "22 CFR 120.54 - Activities that are not exports, reexports, retransfers, or temporary imports"
    publisher: U.S. Government Publishing Office / Office of the Federal Register (eCFR)
    url: https://www.ecfr.gov/current/title-22/chapter-I/subchapter-M/part-120/subpart-C/section-120.54
    type: government
    accessed: 2026-11-15
created: 2026-11-15
---

# ITAR on AWS

The International Traffic in Arms Regulations (ITAR) control the export of defense-related articles from the United States. The articles are those on the U.S. Munitions List (USML), which covers equipment, components, materials and software as well as technical information.[^s1] In a cloud setting, ITAR compliance is mainly about preventing technical data from being released, even by accident, to foreign persons or countries without authorization. AWS defines U.S. persons for this purpose as U.S. citizens and lawful permanent residents (Green Card holders).[^s1] AWS states plainly that no formal ITAR certification exists. It supports customers who build ITAR-compliant systems, but it does not certify those systems.[^s1][^s4]

## How it works

AWS's answer to ITAR is the **AWS GovCloud (US)** Regions. They are physically located in the United States, and AWS personnel access is limited to U.S. persons, so qualified companies can store, process and transmit ITAR-controlled data there.[^s1][^s3] AWS cannot see what customers upload, so it treats all customer data in GovCloud as ITAR data and applies the same controls to all of it.[^s1] AWS points to continuous assessment by a FedRAMP-accredited third-party assessor (3PAO) and to GovCloud's FedRAMP High authorization as evidence for those controls, in place of an ITAR certificate.[^s1][^s4]

GovCloud is open only to customers who are U.S. persons, are not subject to export restrictions, and follow U.S. export-control law. Access is requested from a standard AWS account, and GovCloud then gets its own account ID and credentials, separate from that commercial account.[^s4] The FAQ also notes that billing and account questions are handled through the linked commercial account, which may sit outside the GovCloud Regions.[^s4]

## Customer obligations and gotchas

Using GovCloud does not make a workload ITAR compliant. Customers who put ITAR-regulated data there must qualify as a U.S. person, hold and keep a valid Directorate of Defense Trade Controls (DDTC) registration, keep full export privileges (not denied, debarred or sanctioned), and run an effective export-compliance program.[^s2] If those privileges are revoked, suspended or terminated, the customer must remove ITAR and other export-controlled data from AWS services right away.[^s2]

The GovCloud account owner must be a U.S. person even if no ITAR data is processed. AWS does **not** require IAM users or application users to be U.S. persons, so restricting who can reach the data is the customer's job.[^s2] The general GovCloud rule is the same: restrict protected data to U.S. persons and to U.S. locations.[^s3] Per-service guidance on what may hold export-controlled data lives in the GovCloud user guide's service pages, so check each service you use rather than assuming.[^s2]

AWS lists controls that help: server-side encryption in Amazon S3, key management with AWS KMS and AWS CloudHSM, CloudTrail logging of API calls to audit access, and identity federation plus access rules that limit access by person, time and location.[^s1] GovCloud also offers FIPS 140-2 service endpoints.[^s4]

## Encryption and the regulatory carve-out

The ITAR text itself says that sending, taking or storing unclassified technical data is not an export when it is protected by end-to-end encryption. No third party, such as a cloud provider, may hold the means to decrypt it.[^s5] The encryption must use FIPS 140-2 (or successor) compliant modules, or other means at least as strong as AES-128. The data must also not be sent to, stored in, or sent from a country listed in 22 CFR 126.1. Transit over the internet does not count as storage in a country it passes through.[^s5] Customer-held keys outside the provider's reach are therefore central to this route. Whether it fits a given program is a legal question for the customer's export-compliance counsel; AWS's own ITAR guidance centers on GovCloud.[^s1][^s5]

## When to use it (and when not)

Choose GovCloud when contracts or ITAR obligations call for a U.S.-persons-only environment.[^s1][^s4] AWS's FAQ notes that agencies without that need can use other AWS Regions, which provide FISMA-Moderate controls.[^s4]

## Connections

ITAR on AWS sits under **AWS compliance programs** as a law-or-regulation case with no certification.[^s1] It is `scopedTo` **AWS GovCloud (US)**, the environment AWS provides for it, and relates to the **AWS partitions** model that keeps GovCloud credentials separate.[^s3][^s4] The split of duties is `governedBy` the **shared responsibility model**.[^s1][^s2] It is `securedBy` **AWS KMS** and **AWS CloudHSM** and `monitoredBy` **AWS CloudTrail**, the controls AWS names.[^s1] It relates to **FedRAMP on AWS** and **DoD Cloud Computing SRG impact levels**, because GovCloud's FedRAMP High authorization is the assurance AWS points to.[^s1][^s4]

## Sources
[^s1]: Amazon Web Services, "US International Traffic in Arms Regulations (ITAR)". https://aws.amazon.com/compliance/itar/ (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Maintaining U.S. International Traffic in Arms Regulations (ITAR) Compliance - AWS GovCloud (US) User Guide". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-itar.html (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Compliance - AWS GovCloud (US) User Guide". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-compliance.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "AWS GovCloud (US) FAQs". https://aws.amazon.com/govcloud-us/faqs/ (accessed 2026-11-15)
[^s5]: eCFR, "22 CFR 120.54 - Activities that are not exports, reexports, retransfers, or temporary imports". https://www.ecfr.gov/current/title-22/chapter-I/subchapter-M/part-120/subpart-C/section-120.54 (accessed 2026-11-15)
