---
id: kt:nist-sp-800-53
type: ExternalConcept
title: NIST SP 800-53
aliases: [NIST 800-53, NIST SP 800-53 Rev. 5, NIST Special Publication 800-53, Security and Privacy Controls for Information Systems and Organizations, 800-53 Rev 5]
domain: public-sector-and-compliance
summary: >-
  NIST's catalog of security and privacy controls (Revision 5, 20 control families, currently at
  Release 5.2.0) that underpins U.S. federal authorization programs; on AWS it is mapped, never
  fully automated, through Security Hub CSPM controls, AWS Config conformance packs and Audit Manager.
verified: 2026-11-14
status: concept
broader: [kt:aws-compliance-programs]
related: [kt:fedramp-on-aws, kt:dod-cloud-computing-srg-impact-levels]
relations:
  - {predicate: monitoredBy, object: kt:aws-security-hub}
  - {predicate: monitoredBy, object: kt:aws-config}
  - {predicate: monitoredBy, object: kt:aws-audit-manager}
  - {predicate: contrastsWith, object: kt:nist-cybersecurity-framework}
sources:
  - id: s1
    title: "SP 800-53 Rev. 5, Security and Privacy Controls for Information Systems and Organizations"
    publisher: NIST Computer Security Resource Center
    url: https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final
    type: standards-body
    accessed: 2026-11-14
  - id: s2
    title: "NIST Releases Revision to SP 800-53 Controls"
    publisher: NIST Computer Security Resource Center
    url: https://csrc.nist.gov/news/2025/nist-releases-revision-to-sp-800-53-controls
    type: standards-body
    accessed: 2026-11-14
  - id: s3
    title: "Control Baselines for Information Systems and Organizations: NIST Publishes SP 800-53B"
    publisher: NIST Computer Security Resource Center
    url: https://csrc.nist.gov/news/2020/control-baselines-nist-publishes-sp-800-53b
    type: standards-body
    accessed: 2026-11-14
  - id: s4
    title: "NIST SP 800-53 Revision 5 in Security Hub CSPM"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/securityhub/latest/userguide/standards-reference-nist-800-53.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "Operational Best Practices for NIST 800-53 rev 5 - AWS Config"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/config/latest/developerguide/operational-best-practices-for-nist-800-53_rev_5.html
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "NIST SP 800-53 Rev 5 - AWS Audit Manager"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/audit-manager/latest/userguide/NIST800-53r5.html
    type: official-docs
    accessed: 2026-11-14
created: 2026-11-14
---

# NIST SP 800-53

NIST Special Publication 800-53, *Security and Privacy Controls for Information Systems and Organizations*, is a catalog of controls meant to protect organizations, individuals and the nation from hostile attacks, human error, natural disasters, structural failures, foreign intelligence entities and privacy risks. NIST describes the controls as flexible and customizable, to be selected inside an organization-wide risk management process.[^s1] U.S. federal agencies and their contractors are required to follow it, while private organizations may adopt it voluntarily.[^s4]

## How it is organized

Revision 5 was published in September 2020 and updated with errata on 10 December 2020; it superseded Revision 4.[^s1] The catalog groups controls into 20 families: Access Control; Awareness and Training; Audit and Accountability; Assessment, Authorization and Monitoring; Configuration Management; Contingency Planning; Identification and Authentication; Incident Response; Maintenance; Media Protection; Physical and Environmental Protection; Planning; Program Management; Personnel Security; PII Processing and Transparency; Risk Assessment; System and Services Acquisition; System and Communications Protection; System and Information Integrity; and Supply Chain Risk Management.[^s1] Controls carry identifiers such as AC-2 or AU-6, and numbered enhancements such as AC-2(4) refine a base control.[^s5]

Revision 5 separated the catalog from the baselines. The companion SP 800-53B supplies three security baselines, for low-, moderate- and high-impact systems, plus a privacy baseline that applies regardless of impact level and is tied to agencies' duties under OMB Circular A-130. It also gives tailoring guidance and explains how to build overlays for particular communities, technologies or environments.[^s3] Assessment procedures live in SP 800-53A, which is listed as another publication part.[^s1][^s2]

## What changed recently

NIST now ships minor *releases* within Revision 5 rather than a new revision. Release 5.2.0, issued on 27 August 2025 in response to Executive Order 14306, focuses on the security and reliability of software updates and patches, covering resiliency by design, developer testing, update deployment and management, and software integrity and validation.[^s2] It adds new controls and enhancements, revises an existing control, and updates control discussions; the Revision 5 publication page still carries a Final status and links to the 5.2.0 catalog.[^s1] SP 800-53A received matching assessment updates, while SP 800-53B got a new release number with no content changes. The catalog is available in the browser and as OSCAL, JSON and spreadsheet downloads.[^s2]

## Mapping 800-53 on AWS

AWS does not certify customers against 800-53; it offers three mapping tools, each with explicit disclaimers.

- **AWS Security Hub CSPM** has a NIST SP 800-53 Revision 5 standard whose automated controls support only a subset of the requirements; anything that needs a manual check is out of scope. Each control's *Related requirements* field names the NIST requirements it supports. The controls depend on AWS Config recording, which must be enabled for the checked resource types before turning the standard on.[^s4]
- **AWS Config** publishes an *Operational Best Practices for NIST 800-53 rev 5* conformance pack, a sample template mapping controls to managed Config rules. Mappings are many-to-many, so one rule such as `cloudtrail-enabled` supports several controls, and rule parameters (for example access-key rotation days) should reflect your own policies. AWS states the pack does not by itself ensure compliance.[^s5]
- **AWS Audit Manager** has a prebuilt *NIST 800-53 Rev 5* framework with 132 automated controls, 875 manual controls and 20 control sets (as of 2026-11). It collects evidence from Security Hub CSPM and Config but cannot check procedural controls, and it does not guarantee a passing audit. Audit Manager is closed to new customers, though existing customers can keep using it.[^s6]

## Gotchas

- Automated coverage is always partial: policies, training, personnel and physical controls need human evidence.[^s4][^s6]
- AWS's mappings reflect its own interpretation; where Audit Manager's framework differs from NIST's publication, NIST's documents win.[^s6]
- Baseline selection (low, moderate, high) comes from your system's impact categorization, not from any AWS setting.[^s3][^s6]

## Connections

The catalog sits under **AWS compliance programs** as a framework AWS supports with mappings rather than a certification it holds for you.[^s5] It is the control source behind federal authorization work, which is why the **FedRAMP on AWS** and **DoD Cloud Computing SRG** nodes point here.[^s4] **AWS Security Hub**, **AWS Config** and **AWS Audit Manager** are the services that monitor and gather evidence against it.[^s4][^s5][^s6] It contrasts with the **NIST Cybersecurity Framework**, a separate NIST publication that Audit Manager maps with its own framework.[^s6]

## Sources
[^s1]: NIST Computer Security Resource Center, "SP 800-53 Rev. 5, Security and Privacy Controls for Information Systems and Organizations". https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final (accessed 2026-11-14)
[^s2]: NIST Computer Security Resource Center, "NIST Releases Revision to SP 800-53 Controls". https://csrc.nist.gov/news/2025/nist-releases-revision-to-sp-800-53-controls (accessed 2026-11-14)
[^s3]: NIST Computer Security Resource Center, "Control Baselines for Information Systems and Organizations: NIST Publishes SP 800-53B". https://csrc.nist.gov/news/2020/control-baselines-nist-publishes-sp-800-53b (accessed 2026-11-14)
[^s4]: Amazon Web Services, "NIST SP 800-53 Revision 5 in Security Hub CSPM". https://docs.aws.amazon.com/securityhub/latest/userguide/standards-reference-nist-800-53.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, "Operational Best Practices for NIST 800-53 rev 5 - AWS Config". https://docs.aws.amazon.com/config/latest/developerguide/operational-best-practices-for-nist-800-53_rev_5.html (accessed 2026-11-14)
[^s6]: Amazon Web Services, "NIST SP 800-53 Rev 5 - AWS Audit Manager". https://docs.aws.amazon.com/audit-manager/latest/userguide/NIST800-53r5.html (accessed 2026-11-14)
