---
id: kt:aws-artifact
type: Service
title: AWS Artifact
aliases: [Artifact, AWS Artifact Reports, AWS Artifact Agreements]
domain: public-sector-and-compliance
summary: >-
  AWS's free, self-service portal for downloading AWS's own audit reports and certifications (SOC,
  PCI DSS, ISO and more) and for reviewing, accepting and terminating legal agreements such as the HIPAA
  Business Associate Addendum, per account or across an AWS Organizations organization.
verified: 2026-11-11
status: current
broader: [kt:aws-compliance-programs, kt:aws-security-services]
related: [kt:fedramp-on-aws, kt:dod-cloud-computing-srg-impact-levels, kt:hipaa-eligible-services, kt:shared-responsibility-model]
relations:
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: scopedTo, object: kt:aws-govcloud-us}
  - {predicate: integratesWith, object: kt:aws-marketplace-vendor-insights}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is AWS Artifact?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/artifact/latest/ug/what-is-aws-artifact.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "AWS Artifact FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/artifact/faq
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Managing agreements in AWS Artifact"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/artifact/latest/ug/managing-agreements.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "AWS Artifact now includes Assurance Assistant for compliance inquiries"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/07/aws-artifact-assurance-assistant/
    type: official-blog
    accessed: 2026-11-11
  - id: s5
    title: "AWS Artifact in AWS GovCloud (US)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-artifact.html
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "In-Scope AWS Services - AWS Certification (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# AWS Artifact

AWS Artifact is the place in the AWS console where customers get AWS's own compliance evidence. It offers on-demand downloads of AWS security and compliance documents (ISO and PCI DSS reports, System and Organization Controls (SOC) reports, and certifications from accreditation bodies), and it lets you review, accept and track legal agreements with AWS for a single account or for every account in an organization.[^s1] Documents and agreements are provided free of charge, and every AWS account can use the portal.[^s1][^s2]

Artifact answers the auditor's question "prove your cloud provider's controls work". It does not prove that *your* workload is compliant: under the shared responsibility model, customers must still produce or obtain their own evidence for the parts they operate.[^s1]

## How it works

**Reports.** You sign in, accept the terms attached to a report (confidential reports sit behind the AWS Artifact NDA), and download it. There is no limit on how many times you download, and AWS suggests using the reports to assess AWS controls, monitor suppliers and guide your own architecture.[^s2] FedRAMP security packages are an exception: they are requested through AWS's FedRAMP compliance page rather than downloaded here.[^s2] Third-party reports from independent software vendors that sell on AWS Marketplace are also available, but only once a vendor grants you access through AWS Marketplace Vendor Insights; questions about those reports go to the vendor, not AWS.[^s1][^s2]

**Agreements.** An *account agreement* covers only the account you are signed in to. An *organization agreement* covers all current and future accounts in an AWS Organizations organization, and only the management account can accept or terminate it; the organization must have all features enabled, not just consolidated billing.[^s2][^s3] When both exist, the organization agreement takes precedence, and terminating the organization agreement does not terminate a matching account agreement.[^s2]

The most common agreement is the HIPAA **Business Associate Addendum (BAA)**. Accepting it online makes the account (or every organization account) a "HIPAA Account"; you may use any service there, but protected health information belongs only in HIPAA Eligible Services.[^s2] Accepted at the organization level, the BAA automatically covers existing and new member accounts.[^s3] Australian and New Zealand notifiable-data-breach addenda work the same way.[^s2]

**Notifications and Assurance Assistant.** A one-time opt-in lets you create notification configurations for new reports and agreements, each with up to 20 verified email addresses, delivered through AWS User Notifications (as of 2026-11).[^s2] Since July 2026, Assurance Assistant answers security and compliance questions with citations to AWS compliance documents such as SOC, ISO and C5 materials. It can answer one question at a time or bulk-fill an uploaded XLSX questionnaire (for example CAIQ or SIG) and export the answers. It is available in all commercial Regions at no additional charge, and Artifact is a global service, so no Region choice is needed.[^s4]

## Gotchas

- Agreements are confidential and may not be shared outside your company.[^s2]
- An organization agreement cannot be scoped to only some member accounts; those accounts must accept account agreements individually.[^s2]
- A member account that leaves the organization silently loses organization-agreement coverage, so warn its owners first.[^s2] Terminate a BAA only after all PHI has been removed.[^s2]
- Access is controlled by IAM. Common errors come from missing `artifact:` actions or `organizations:` permissions; AWS recommends giving each external auditor separate IAM credentials limited to the reports they need.[^s2] Assurance Assistant has its own managed policies (read-only and full access).[^s4]
- In AWS GovCloud (US), AWS documents no service differences for Artifact.[^s5]

## When to use it (and when not)

Use Artifact during vendor-risk reviews, audits (SOC 2, PCI DSS, ISO 27001) and procurement, and whenever a workload needs a signed AWS agreement such as the BAA.[^s1][^s2] It gives you static evidence about AWS. To check your own resource configurations continuously, use services such as AWS Config or AWS Security Hub. For U.S. federal authorization, see FedRAMP on AWS and the DoD SRG impact levels.[^s2]

## Connections

Artifact sits under **AWS compliance programs** and the **AWS security services** family. It is the download point for the evidence behind programs such as **FedRAMP on AWS** and the **DoD Cloud Computing SRG**, and its BAA is what activates **HIPAA eligible services** for PHI.[^s2] Organization agreements **integrate with AWS Organizations**, access is **secured by AWS IAM**, third-party reports come through **AWS Marketplace Vendor Insights**,[^s1][^s3] and it runs unchanged in **AWS GovCloud (US)**.[^s5] It reflects the **shared responsibility model**, because AWS's reports cover only AWS's side.[^s1] AWS Artifact is an in-scope service (Security, Identity and Compliance) for the **AWS Certified Cloud Practitioner** exam.[^s6]

## Sources
[^s1]: Amazon Web Services, "What is AWS Artifact?". https://docs.aws.amazon.com/artifact/latest/ug/what-is-aws-artifact.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "AWS Artifact FAQs". https://aws.amazon.com/artifact/faq (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Managing agreements in AWS Artifact". https://docs.aws.amazon.com/artifact/latest/ug/managing-agreements.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "AWS Artifact now includes Assurance Assistant for compliance inquiries" (What's New, 2026-07-01). https://aws.amazon.com/about-aws/whats-new/2026/07/aws-artifact-assurance-assistant/ (accessed 2026-11-11)
[^s5]: Amazon Web Services, "AWS Artifact in AWS GovCloud (US)". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-artifact.html (accessed 2026-11-11)
[^s6]: Amazon Web Services, "In-Scope AWS Services - AWS Certification" (CLF-C02). https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-11)
