---
id: kt:aws-marketplace-vendor-insights
type: Feature
title: AWS Marketplace Vendor Insights
aliases: [Vendor Insights, AWS Vendor Insights, Marketplace Vendor Insights]
domain: public-sector-and-compliance
summary: >-
  An AWS Marketplace capability that gave buyers a security and compliance profile for a seller's
  product, built from the seller's self-assessment, third-party audit reports and live AWS Config
  evidence; closed to new profiles and access requests since October 27, 2026 and ending on
  November 26, 2026.
verified: 2026-11-21
status: closed-to-new-customers
broader: [kt:aws-marketplace, kt:aws-compliance-programs]
related: [kt:fedramp-on-aws, kt:hipaa-eligible-services, kt:pci-dss-on-aws, kt:shared-responsibility-model]
relations:
  - {predicate: integratesWith, object: kt:aws-artifact}
  - {predicate: dependsOn, object: kt:aws-audit-manager}
  - {predicate: dependsOn, object: kt:aws-config}
  - {predicate: configuredVia, object: kt:cloudformation-stacksets}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: scopedTo, object: kt:aws-govcloud-us}
sources:
  - id: s1
    title: "AWS Marketplace Vendor Insights (Seller Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/marketplace/latest/userguide/vendor-insights.html
    type: official-docs
    accessed: 2026-11-21
  - id: s2
    title: "AWS Marketplace Vendor Insights (Buyer Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/marketplace/latest/buyerguide/buyer-vendor-insights.html
    type: official-docs
    accessed: 2026-11-21
  - id: s3
    title: "Viewing the security profile of a product with AWS Marketplace Vendor Insights"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/marketplace/latest/buyerguide/buyer-vendor-insights-view-profile.html
    type: official-docs
    accessed: 2026-11-21
  - id: s4
    title: "Understanding AWS Marketplace Vendor Insights"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/marketplace/latest/userguide/vendor-insights-understanding.html
    type: official-docs
    accessed: 2026-11-21
  - id: s5
    title: "Setting up AWS Marketplace Vendor Insights"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/marketplace/latest/userguide/vendor-insights-setting-up.html
    type: official-docs
    accessed: 2026-11-21
  - id: s6
    title: "AWS Marketplace Vendor Insights – Simplify Third-Party Software Risk Assessments"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/aws-marketplace-vendor-insights-simplify-third-party-software-risk-assessments/
    type: official-blog
    accessed: 2026-11-21
  - id: s7
    title: "Pricing | Vendor Insights for Third-Party Software"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/marketplace/features/vendor-insights/pricing
    type: official-docs
    accessed: 2026-11-21
  - id: s8
    title: "New self-service Vendor Insights onboarding experience for AWS Marketplace sellers"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2023/08/self-service-vendor-insights-onboarding-experience-aws-marketplace-sellers
    type: official-blog
    accessed: 2026-11-21
created: 2026-11-21
---

# AWS Marketplace Vendor Insights

AWS Marketplace Vendor Insights is a feature of AWS Marketplace that let a buyer run a third-party risk assessment of a seller's software from a single dashboard instead of exchanging questionnaires and audit reports by email. AWS announced it on November 30, 2022, pitching assessments that take days rather than months.[^s6] The dashboard combined three kinds of evidence: the seller's self-attestation, industry audit reports such as ISO 27001, and live evidence pulled from the seller's own AWS accounts.[^s1][^s4]

**Lifecycle (as of 2026-11).** Vendor Insights is being shut down. Since October 27, 2026, sellers cannot create or update security profiles and buyers cannot request access to profiles they don't already have; existing profiles and existing buyer access remain until November 26, 2026, after which the feature is no longer available.[^s1][^s2] AWS says new compliance validation features are coming but has not named a successor, so this node has no `replacedBy` edge yet. Buyers and sellers should download any profile documents they need before the end date; Marketplace subscriptions and purchases themselves are unaffected.[^s2][^s4]

## How it works

**Evidence sources.** A seller's security profile drew on (1) a self-assessment, either the Vendor Insights security self-assessment (a Yes/No/N/A spreadsheet template) or the Cloud Security Alliance's CAIQ; (2) uploaded certifications, which the setup guide lists as FedRAMP, GDPR and HIPAA compliance reports and ISO/IEC 27001, PCI DSS and SOC 2 Type 2 audit reports; and (3) optional automated assessments from AWS Audit Manager.[^s5] Uploaded documents moved through statuses such as ValidationPending, Available and ValidationFailed as AWS checked them.[^s5]

**Live evidence.** For automated assessments the seller turned on AWS Config and Audit Manager, deployed AWS CloudFormation templates through StackSets (about five minutes per Region, with the primary Region first), and provisioned IAM roles that let Vendor Insights read assessment results.[^s4][^s5] AWS Config then evaluated production resources continuously; AWS's example is an S3 bucket becoming public, which flips an access-control status from Compliant to Undetermined.[^s1] The buyer guide says 25 controls support live evidence, each backed by one or more AWS Config rules.[^s2]

**What buyers saw.** Profiles were organised into 10 control categories, including access management, data security, infrastructure security, business resiliency and continuity, and risk management and incident response.[^s2][^s3] Control categories were mapped to controls in the seller's ISO 27001 and SOC 2 Type II reports, and a red counter on the Security and compliance tab showed how many controls were non-compliant.[^s2][^s3] Buyers could also view or download the seller's audit reports, including auditor exceptions, through AWS Artifact third-party reports.[^s2][^s3]

**Access model.** A buyer requested access to a profile; the seller's compliance or deal-desk team was emailed the buyer's contact details, typically had the buyer sign an NDA, and then approved the request, a process AWS said could take a few days. Subscribers were notified when a profile changed.[^s6][^s7] From August 2023 sellers could build a profile themselves in the AWS Marketplace Management Portal, after AWS validated their uploads.[^s8]

## Quotas, pricing and gotchas

- **Pricing (as of 2026-11, all Regions):** Vendor Insights itself carried no charge for buyers or sellers. Sellers paid for the AWS Config and Audit Manager usage behind live evidence, and AWS warns that Config activity runs higher in the first month of recording.[^s5][^s7]
- **60-day access window:** a subscription taken for pre-purchase evaluation expires after 60 days by default unless the buyer purchases the product through AWS Marketplace, which keeps access active.[^s6][^s7]
- **Profile quota:** sellers could create only a limited number of security profiles, with increases requested through Service Quotas; AWS does not publish the default number.[^s5]
- **Audit Manager dependency:** because Audit Manager entered maintenance mode on April 30, 2026 and accepts no new accounts, automated assessments were already closed to new sellers before the wider shutdown.[^s5]
- **Regions:** AWS stated in 2023 that the feature ran in all public Regions with AWS Marketplace, including the AWS GovCloud (US) Regions.[^s8]

## When to use it (and when not)

With the feature closing, practitioners should treat any profile as a snapshot to archive, not a monitoring source. Vendor Insights never replaced a buyer's own due diligence: it assessed the seller's side of the stack, while the buyer stays responsible for how it configures and uses the software, in line with the shared responsibility model. For AWS's own attestations (as opposed to a vendor's), the source has always been AWS Artifact.[^s2][^s3]

## Connections

Vendor Insights sits **under AWS Marketplace** and the wider family of **AWS compliance programs**. It **integrates with AWS Artifact**, which delivered vendors' third-party reports, and **depended on AWS Audit Manager** and **AWS Config** for live evidence, onboarded **via CloudFormation StackSets** with access **secured by AWS IAM** roles.[^s2][^s4][^s5] It was **available in AWS GovCloud (US)**, and the certifications it accepted map to **FedRAMP on AWS**, **HIPAA eligible services** and **PCI DSS on AWS**.[^s5][^s8]

## Sources
[^s1]: Amazon Web Services, "AWS Marketplace Vendor Insights (Seller Guide)". https://docs.aws.amazon.com/marketplace/latest/userguide/vendor-insights.html (accessed 2026-11-21)
[^s2]: Amazon Web Services, "AWS Marketplace Vendor Insights (Buyer Guide)". https://docs.aws.amazon.com/marketplace/latest/buyerguide/buyer-vendor-insights.html (accessed 2026-11-21)
[^s3]: Amazon Web Services, "Viewing the security profile of a product with AWS Marketplace Vendor Insights". https://docs.aws.amazon.com/marketplace/latest/buyerguide/buyer-vendor-insights-view-profile.html (accessed 2026-11-21)
[^s4]: Amazon Web Services, "Understanding AWS Marketplace Vendor Insights". https://docs.aws.amazon.com/marketplace/latest/userguide/vendor-insights-understanding.html (accessed 2026-11-21)
[^s5]: Amazon Web Services, "Setting up AWS Marketplace Vendor Insights". https://docs.aws.amazon.com/marketplace/latest/userguide/vendor-insights-setting-up.html (accessed 2026-11-21)
[^s6]: Amazon Web Services, AWS News Blog, "AWS Marketplace Vendor Insights – Simplify Third-Party Software Risk Assessments". https://aws.amazon.com/blogs/aws/aws-marketplace-vendor-insights-simplify-third-party-software-risk-assessments/ (accessed 2026-11-21)
[^s7]: Amazon Web Services, "Pricing | Vendor Insights for Third-Party Software". https://aws.amazon.com/marketplace/features/vendor-insights/pricing (accessed 2026-11-21)
[^s8]: Amazon Web Services, What's New, "New self-service Vendor Insights onboarding experience for AWS Marketplace sellers". https://aws.amazon.com/about-aws/whats-new/2023/08/self-service-vendor-insights-onboarding-experience-aws-marketplace-sellers (accessed 2026-11-21)
