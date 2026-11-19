---
id: kt:cmmc-on-aws
type: ComplianceProgram
title: CMMC on AWS
aliases: [Cybersecurity Maturity Model Certification on AWS, CMMC, CMMC 2.0, AWS CMMC compliance, CMMC Level 2 on AWS]
domain: public-sector-and-compliance
summary: >-
  How U.S. defense contractors meet the DoD's Cybersecurity Maturity Model Certification (CMMC) on AWS:
  CMMC certifies the contractor, not the cloud, so contractors build their CUI environment in AWS
  GovCloud (US) or the US East/West Regions on FedRAMP-authorized services and inherit part of the
  NIST SP 800-171 controls through AWS's CMMC Customer Package.
verified: 2026-11-19
status: current
broader: [kt:aws-compliance-programs]
related: [kt:fedramp-on-aws, kt:itar-on-aws, kt:dod-cloud-computing-srg-impact-levels, kt:aws-artifact, kt:aws-organizations]
relations:
  - {predicate: implements, object: kt:nist-sp-800-171}
  - {predicate: scopedTo, object: kt:aws-govcloud-us}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: governedBy, object: kt:shared-responsibility-model}
  - {predicate: monitoredBy, object: kt:aws-config}
sources:
  - id: s1
    title: "Cybersecurity Maturity Model Certification (CMMC)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/cmmc
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Preparation guide for CMMC Level 2 on AWS - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/cmmc-level-2-compliance-on-aws/introduction.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Defense Federal Acquisition Regulation Supplement (DFARS)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/dfars
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "Operational Best Practices for CMMC 2.0 Level 1 - AWS Config Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/config/latest/developerguide/operational-best-practices-for-cmmc_2.0_level_1.html
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "About CMMC"
    publisher: U.S. Department of War (DoD) Chief Information Officer
    url: https://dodcio.defense.gov/CMMC/About/
    type: government
    accessed: 2026-11-19
  - id: s6
    title: "Defense Federal Acquisition Regulation Supplement: Assessing Contractor Implementation of Cybersecurity Requirements (DFARS Case 2019-D041), Federal Register Vol. 90 No. 173"
    publisher: U.S. Government Publishing Office (govinfo)
    url: https://www.govinfo.gov/content/pkg/FR-2025-09-10/html/2025-17359.htm
    type: government
    accessed: 2026-11-19
  - id: s7
    title: "32 CFR 170.16 - CMMC Level 2 self-assessment and affirmation requirements"
    publisher: Legal Information Institute, Cornell Law School
    url: https://www.law.cornell.edu/cfr/text/32/170.16
    type: academic
    accessed: 2026-11-19
created: 2026-11-19
---

# CMMC on AWS

The Cybersecurity Maturity Model Certification (CMMC) program is the U.S. Department of Defense's (now styled Department of War, DoW) way of checking that companies in the Defense Industrial Base (DIB) actually protect the Federal Contract Information (FCI) and Controlled Unclassified Information (CUI) they receive. It has three parts: a tiered model, assessments that verify implementation, and enforcement through contract clauses.[^s1][^s5] AWS's key point is that CMMC measures a contractor's own processes, so cloud services themselves do not need a CMMC certification. The contractor is the one assessed, and AWS supplies authorized infrastructure and inheritable controls.[^s1]

## Levels and requirements

- **Level 1** applies to companies handling only FCI. DoD counts 15 security requirements from FAR clause 52.204-21; AWS's page describes the same baseline as 17 practices. It needs an annual self-assessment and affirmation posted to SPRS, and POA&Ms are not allowed.[^s5][^s1]
- **Level 2** applies to CUI and requires the 110 requirements of NIST SP 800-171 Rev. 2. Self-assessments run every three years with annual affirmation. POA&Ms are allowed but must be closed within 180 days.[^s5][^s7] AWS notes that these 110 requirements come from 14 families derived from NIST SP 800-53.[^s3]
- **Level 3** covers the highest-priority CUI programs. It adds a subset of NIST SP 800-172 and is assessed by government officials.[^s1]

## Rulemaking and current status (as of 2026-11)

The program rule sits in 32 CFR Part 170. The acquisition rule that places CMMC into contracts (DFARS Case 2019-D041, amending 48 CFR Parts 204, 212, 217 and 252) took effect on November 10, 2025, with a phase-in lasting three years from that date.[^s6] Phase 1, which relies on self-assessments, began on that date.[^s5] Phase II, which would have started on November 10, 2026, was suspended by the DoW on July 13, 2026. The DoW also set up a CMMC reform task force to review the program. Phase 1 self-assessment requirements stay in force, and DFARS 252.204-7012 still applies.[^s5]

**Gotcha:** AWS's Prescriptive Guidance guide still says C3PAO certification assessments begin in November 2026.[^s2] The DoD CIO page is the authority on timing and currently shows that phase paused.[^s5] Check it before planning around third-party assessment dates.

## How it works on AWS

The regulation lets a contractor process, store or transmit CUI in a cloud offering only if it is FedRAMP Authorized at Moderate or higher, or meets an equivalent level under DoD policy. The contractor's own infrastructure connecting to the cloud stays in assessment scope, and the provider's customer responsibility matrix (CRM) is documented in, or referenced from, the system security plan.[^s7] AWS states that its standard US Regions offer services authorized at FedRAMP Moderate, and AWS GovCloud (US) services at FedRAMP High.[^s3] AWS also holds DISA SRG Impact Levels 2, 4 and 5, and it supports CMMC solutions in both US East/West and GovCloud (US).[^s1]

For DFARS 252.204-7012, AWS says the cloud provider duties in paragraphs (c) through (g), which cover incident reporting, media preservation and forensic access, map directly onto FedRAMP Moderate controls.[^s3] Use the AWS Services in Scope pages to confirm a specific service is authorized rather than assuming it.[^s3]

AWS's main inheritance artifact is the **AWS CMMC Customer Package**, available in AWS Artifact in both standard and GovCloud Regions. It maps the CMMC Level 2 / NIST SP 800-171 controls a customer can inherit when deploying the **Landing Zone Accelerator** in GovCloud (US).[^s1] The Prescriptive Guidance Level 2 guide adds a reference architecture. It uses AWS Organizations account isolation to scope the CUI boundary and FIPS-validated endpoints, and it covers automated evidence collection and continuous monitoring across all 110 requirements.[^s2] AWS Config provides CMMC 2.0 Level 1 and Level 2 conformance packs that map controls to managed rules, such as MFA checks, multi-Region CloudTrail and TLS-only S3 access. AWS warns that these packs are sample templates and do not by themselves ensure compliance.[^s4]

AWS offers no CMMC reciprocity with other programs, and DoD has not defined how FedRAMP or ISO 27001 map onto CMMC levels.[^s1]

## When to use GovCloud vs. commercial Regions

Both partitions can host a CMMC Level 2 environment.[^s1][^s2] Choose GovCloud (US) when the same data is also export-controlled (ITAR) or the contract calls for FedRAMP High or a U.S.-persons environment. The Customer Package's inheritance mapping is also written around the Landing Zone Accelerator in GovCloud.[^s1][^s3] Commercial US Regions suit CUI workloads where FedRAMP Moderate is enough, but the contractor still owns every requirement it does not inherit.[^s3][^s7]

## Connections

CMMC on AWS sits under **AWS compliance programs**. It `implements` **NIST SP 800-171**, which is Level 2 itself, and that standard draws on **NIST SP 800-53**.[^s3][^s5] It is `scopedTo` **AWS GovCloud (US)** and the standard US **AWS Regions**, the two places AWS supports it. It relates to **FedRAMP on AWS** and **DoD SRG impact levels**, the authorizations that make AWS eligible to hold CUI.[^s1][^s7] The split of duties is `governedBy` the **shared responsibility model**, with **AWS Artifact** delivering the Customer Package and **AWS Organizations** scoping the boundary.[^s1][^s2] It is `monitoredBy` **AWS Config** through the CMMC conformance packs.[^s4] It relates to **ITAR on AWS**, since many defense workloads face both rules.[^s1]

## Sources
[^s1]: Amazon Web Services, "Cybersecurity Maturity Model Certification (CMMC)". https://aws.amazon.com/compliance/cmmc (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Preparation guide for CMMC Level 2 on AWS - AWS Prescriptive Guidance". https://docs.aws.amazon.com/prescriptive-guidance/latest/cmmc-level-2-compliance-on-aws/introduction.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Defense Federal Acquisition Regulation Supplement (DFARS)". https://aws.amazon.com/compliance/dfars (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Operational Best Practices for CMMC 2.0 Level 1 - AWS Config Developer Guide". https://docs.aws.amazon.com/en_us/config/latest/developerguide/operational-best-practices-for-cmmc_2.0_level_1.html (accessed 2026-11-19)
[^s5]: U.S. Department of War Chief Information Officer, "About CMMC". https://dodcio.defense.gov/CMMC/About/ (accessed 2026-11-19)
[^s6]: govinfo, "DFARS: Assessing Contractor Implementation of Cybersecurity Requirements (DFARS Case 2019-D041)", Federal Register, 2025-09-10. https://www.govinfo.gov/content/pkg/FR-2025-09-10/html/2025-17359.htm (accessed 2026-11-19)
[^s7]: Legal Information Institute, "32 CFR 170.16 - CMMC Level 2 self-assessment and affirmation requirements". https://www.law.cornell.edu/cfr/text/32/170.16 (accessed 2026-11-19)
