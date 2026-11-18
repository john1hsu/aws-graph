---
id: kt:aws-audit-manager
type: Service
title: AWS Audit Manager
aliases: [Audit Manager]
domain: public-sector-and-compliance
summary: >-
  AWS's service for continuously collecting evidence about how you use AWS and organizing it against
  compliance frameworks (SOC 2, PCI DSS, HIPAA, NIST 800-53 and others) into audit-ready reports; in
  maintenance mode and closed to new customers since April 30, 2026, with AWS pointing customers to
  AWS Config conformance packs.
verified: 2026-11-18
status: closed-to-new-customers
broader: [kt:aws-compliance-programs, kt:aws-management-and-governance-services]
related: [kt:aws-artifact, kt:nist-sp-800-53]
relations:
  - {predicate: replacedBy, object: kt:aws-config}
  - {predicate: integratesWith, object: kt:aws-security-hub}
  - {predicate: integratesWith, object: kt:aws-cloudtrail}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
sources:
  - id: s1
    title: "What is AWS Audit Manager?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/audit-manager/latest/userguide/what-is.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "AWS Audit Manager availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/audit-manager/latest/userguide/audit-manager-availability-change.md
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "AWS Audit Manager Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/audit-manager/pricing/
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "AWS Audit Manager FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/audit-manager/faqs
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "AWS achieves FedRAMP P-ATO for 20 services in the AWS US East/West Regions and AWS GovCloud (US) Regions"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/security/aws-achieves-fedramp-p-ato-for-20-services-in-the-aws-us-east-west-regions-and-aws-govcloud-us-regions
    type: official-blog
    accessed: 2026-11-18
created: 2026-11-18
---

# AWS Audit Manager

AWS Audit Manager helps customers audit their own use of AWS continuously: it collects evidence from the accounts you put in scope, turns it into audit-friendly records, attaches each record to the control it supports, and packages the result into reports for auditors.[^s1] It covers security, change management, business continuity and software licensing evidence.[^s1] AWS is explicit that Audit Manager does not itself decide whether you are compliant and is no substitute for legal or compliance experts; the evidence it gathers may not be everything an audit needs.[^s1]

**Lifecycle (as of 2026-11).** Audit Manager is in maintenance mode. From April 30, 2026 it can no longer be set up in new accounts, and existing customers cannot extend it to new Regions or new organizations. A single-account deployment keeps working in its Region (including new assessments), and an organization deployment can still add member accounts to assessments.[^s2] AWS keeps fixing bugs and maintaining data-source mappings for the current frameworks, but will add no new features, frameworks, framework versions or Regions.[^s2]

## How it works

- **Frameworks** are prebuilt sets of controls grouped by a standard's requirements; you can also build custom frameworks, share them with another account or replicate them into another Region.[^s1] AWS's comparison lists 35 managed frameworks, including SOC 2, PCI DSS, HIPAA, GDPR, CIS benchmarks, FedRAMP and NIST SP 800-53 Rev 5.[^s2][^s4]
- **Assessments** are created from a framework and run resource assessments across the in-scope accounts. Evidence collection continues until you mark the assessment inactive.[^s1]
- **Evidence** comes from four data sources: service API calls (configuration snapshots taken daily, weekly or monthly), AWS CloudTrail user-activity events, AWS Security Hub control results and AWS Config rule evaluations.[^s2][^s4] You can also upload manual evidence from on-premises or multicloud environments, and stored evidence is kept unaltered.[^s1]
- **Delegation** sends control sets to subject-matter experts to review, comment on and update.[^s1] **Evidence finder** searches evidence and exports results to a report or CSV.[^s1]
- With AWS Organizations, one assessment can span many accounts, with evidence consolidated in a delegated administrator account.[^s4]

## Pricing, quotas and gotchas

- Pricing is pay-as-you-go per resource assessment, where each assessment of one resource produces one piece of evidence. AWS's published examples work out to $1.25 per 1,000 resource assessments, with no minimum fee (as of 2026-11; the page shows no per-Region rates).[^s3] The free tier of 35,000 assessments per month for two months applied only to first-time customers, who can no longer sign up.[^s2][^s3]
- AWS Config, Security Hub, S3 storage for reports, and CloudTrail Lake (used by evidence finder) are billed separately.[^s3]
- It is a Regional service: you enable it per Region and evidence stays in its Region.[^s4] Evidence is retained for two years, then deleted.[^s4]
- Disabling stops new collection but keeps existing evidence unless you choose to delete it. Organization deployments must be disabled from the management account. Turn on evidence finder first if you need the evidence afterwards, because it copies evidence into a CloudTrail Lake data store.[^s2]

## When to use it (and when not)

Existing customers can keep using it as before.[^s2] New compliance programs should start elsewhere. AWS recommends **AWS Config conformance packs**: over 100 sample templates (PCI DSS, HIPAA, NIST, FedRAMP and more), organization-wide deployment, compliance dashboards and remediation, which Audit Manager never offered.[^s2] They are not a drop-in replacement. They cover only controls that a Config rule can test, evidence is limited to configuration items, there is no equivalent PDF audit report, and SOC 2, GDPR and ISO 27001 have no matching pack. For full audit workflows AWS points to partner tools used together with Config.[^s2] AWS Control Tower controls and Security Hub CSPM security standards are other options for frameworks they map.[^s2]

## Connections

Audit Manager sits under **AWS compliance programs** and the **management and governance** services. Its evidence comes from **AWS Security Hub**, **AWS CloudTrail** and **AWS Config**, and it spans accounts through **AWS Organizations**.[^s1][^s4] AWS's recommended path forward is **AWS Config** (conformance packs).[^s2] It complements **AWS Artifact**: Artifact provides AWS's own compliance reports, while Audit Manager shows how *your* use of AWS meets a framework.[^s1] It includes a prebuilt **NIST SP 800-53** framework.[^s4] For **FedRAMP**, AWS listed it at Moderate for the US East/West Regions in 2022,[^s6] and the services-in-scope page (as of 2026-11) still lists it for East/West but not in the GovCloud (US) column.[^s5]

## Sources
[^s1]: Amazon Web Services, "What is AWS Audit Manager?". https://docs.aws.amazon.com/audit-manager/latest/userguide/what-is.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "AWS Audit Manager availability change". https://docs.aws.amazon.com/audit-manager/latest/userguide/audit-manager-availability-change.md (accessed 2026-11-18)
[^s3]: Amazon Web Services, "AWS Audit Manager Pricing". https://aws.amazon.com/audit-manager/pricing/ (accessed 2026-11-18)
[^s4]: Amazon Web Services, "AWS Audit Manager FAQs". https://aws.amazon.com/audit-manager/faqs (accessed 2026-11-18)
[^s5]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-18)
[^s6]: Amazon Web Services, "AWS achieves FedRAMP P-ATO for 20 services in the AWS US East/West Regions and AWS GovCloud (US) Regions" (AWS Security Blog, 2022-08-29). https://aws.amazon.com/blogs/security/aws-achieves-fedramp-p-ato-for-20-services-in-the-aws-us-east-west-regions-and-aws-govcloud-us-regions (accessed 2026-11-18)
