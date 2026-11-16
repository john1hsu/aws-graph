---
id: kt:aws-support-plans
type: Service
title: AWS Support plans
aliases: [AWS Support, AWS Premium Support, AWS Business Support+, AWS Enterprise Support, AWS Unified Operations]
domain: governance-and-operations
summary: >-
  The tiers in which AWS sells human and AI-assisted technical support: Basic (free for every
  account), Business Support+, Enterprise Support and Unified Operations, which differ in
  response times, designated staff, included tools and a usage-based monthly fee; the older
  Developer, Business and Enterprise On-Ramp plans end on January 1, 2027 outside AWS GovCloud (US).
verified: 2026-11-16
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:aws-trusted-advisor, kt:aws-health-dashboard, kt:aws-billing-and-cost-management, kt:aws-organizations]
relations:
  - {predicate: integratesWith, object: kt:aws-security-incident-response}
  - {predicate: integratesWith, object: kt:aws-devops-agent}
  - {predicate: contrastsWith, object: kt:aws-professional-services}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "AWS Support Plans - AWS Support User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awssupport/latest/user/aws-support-plans.md
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Compare AWS Support Plans"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/premiumsupport/plans
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "AWS Support Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/premiumsupport/pricing
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "New and enhanced AWS Support plans add AI capabilities to expert guidance (AWS News Blog, 2025-12-02)"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/new-and-enhanced-aws-support-plans-add-ai-capabilities-to-expert-guidance
    type: official-blog
    accessed: 2026-11-16
  - id: s5
    title: "AWS Support transformation: AI-powered operations with the human expertise you trust (What's New with AWS, 2025-12-02)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/aws-support-transformation-ai-powered-operations
    type: official-blog
    accessed: 2026-11-16
  - id: s6
    title: "AWS Certified Cloud Practitioner (CLF-C02) - Content Domain 4: Billing, Pricing, and Support"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.md
    type: official-learning
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS Support plans

**AWS Support plans** are the subscription tiers through which AWS provides technical help for an account. As of 2026-11 there are four: **Basic**, **Business Support+**, **Enterprise Support** and **Unified Operations**.[^s1][^s2] Basic is included with every account; the paid tiers add technical cases, faster engagement, designated AWS staff and extra tooling, billed monthly with no long-term contract.[^s1][^s3]

## The plan line-up

AWS restructured its plans at re:Invent in December 2025, adding generative-AI assistance on top of human experts.[^s4][^s5] Each tier includes everything in the one below it.[^s5]

- **Basic** covers one-on-one help with account and billing questions and service quota increases, plus forums, service health checks and documentation, 24x7. It does not include technical support cases.[^s1]
- **Business Support+** targets developers, startups and small businesses. It adds context-aware AI responses, 24x7 phone, web and chat access to Cloud Support Engineers, an unlimited number of cases opened by any user with permission, the AWS Support API, the full set of Trusted Advisor checks, and help with third-party software such as EC2 operating systems.[^s1][^s4]
- **Enterprise Support** adds a designated Technical Account Manager (TAM), strategic business reviews, AWS Countdown event management, and AWS Security Incident Response at no extra charge.[^s1][^s4]
- **Unified Operations** is for mission-critical workloads. A designated team that includes a TAM and a Domain Specialist Engineer provides architecture guidance, infrastructure event management, critical workload reviews and personalized runbooks, with one AWS Countdown Premium subscription included.[^s1][^s4]

Target human response times as of 2026-11 are the same on all paid plans for lower severities: under 1 hour for production system down, 4 hours for production impaired, 12 hours for system impaired and 24 hours for general guidance. They diverge for the most severe cases: under 30 minutes on Business Support+, 15 minutes on Enterprise, and 5 minutes from an Incident Management Engineer on Unified Operations.[^s2] AWS frames these as reasonable-effort targets, not guarantees.[^s2]

## Pricing (as of 2026-11)

Each paid plan charges the greater of a monthly minimum or a tiered percentage of gross monthly AWS charges, so marginal rates drop as spend grows.[^s3][^s4]

- **Business Support+**: the greater of $29 per account, or 9% of the first $10K, 7% from $10K to $80K, 5% from $80K to $250K and 3% above $250K. It is billed per account.[^s3]
- **Enterprise Support**: the greater of $5K, or 10% up to $150K, 7% to $500K, 5% to $1M and 3% above $1M, billed on aggregate charges across subscribed accounts. Qualified customers in LATAM, India and mainland China get lower breakpoints.[^s3]
- **Unified Operations**: the greater of $50K, or 10% up to $1M, 6% to $5M and 5% above $5M; the AWS GovCloud (US) Regions use higher rates of 11%, 7% and 6%.[^s3]

The percentages apply before discounts and credits. Upfront charges for reserved resources count in the month of purchase, which can spike that month's support bill. Charges for AWS Support itself, Professional Services, training and most AWS Marketplace purchases are excluded. The minimum commitment is 30 days, or 90 days for Unified Operations.[^s3] AWS says the new Enterprise minimum is 67% below the previous one, and Business Support+ is 71% below the old Business minimum.[^s4] The plan comparison page also lists AWS DevOps Agent credits of 30%, 75% or 100% of the support charge on Business Support+, Enterprise and Unified Operations respectively.[^s2]

## Legacy plans and gotchas

- **Developer Support** and **Business Support** are discontinued on January 1, 2027. Subscribers can keep them until then or move to Business Support+ at any time.[^s1]
- **Enterprise On-Ramp** also ends on January 1, 2027. Through 2026 AWS upgrades those customers to Enterprise Support automatically at renewal or in batches, with a month's email notice.[^s1]
- All three legacy plans stay available in the AWS GovCloud (US) Region.[^s1]
- Business Support+ is priced per account, so an organization with many small accounts pays a minimum on each; Enterprise and Unified Operations aggregate spend across subscribed accounts.[^s3]
- Security Incident Response is included with Enterprise and Unified Operations but costs extra on Business Support+.[^s2]

## Choosing a plan

Basic suits sandboxes and accounts where only billing help is needed. Any production workload that needs a human engineer on a technical case needs at least Business Support+. Enterprise Support fits organizations that want a named TAM and proactive reviews, and Unified Operations fits workloads whose downtime is measured in minutes.[^s1][^s4] Support plans cover operating what you run on AWS; design and delivery projects are a separate engagement through AWS Professional Services or AWS Partners, which the Cloud Practitioner exam guide lists alongside support as distinct technical assistance options.[^s6]

## Connections

Support plans sit among the **AWS management and governance services** and decide what you get from **AWS Trusted Advisor** (core checks on Basic, the full set and Trusted Advisor Priority on higher tiers) and whether you can call the **AWS Health** API.[^s2] They `integratesWith` **AWS Security Incident Response**, included from Enterprise up, and with **AWS DevOps Agent**, from which customers can open a case in one click and for which plans grant credits.[^s2][^s5] Fees appear on the bill managed through **AWS Billing and Cost Management**, and Enterprise and Unified Operations price across the accounts of an organization in **AWS Organizations**.[^s3] The plan choice `contrastsWith` **AWS Professional Services** engagements.[^s6] AWS Support plans, including Basic, Business Support+, Enterprise Support and Unified Operations, are named in Task Statement 4.3 of the **AWS Certified Cloud Practitioner** exam guide, so the node is `coveredBy` that certification.[^s6]

## Sources

[^s1]: Amazon Web Services, "AWS Support Plans" (AWS Support User Guide). https://docs.aws.amazon.com/awssupport/latest/user/aws-support-plans.md (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Compare AWS Support Plans". https://aws.amazon.com/premiumsupport/plans (accessed 2026-11-16)
[^s3]: Amazon Web Services, "AWS Support Pricing". https://aws.amazon.com/premiumsupport/pricing (accessed 2026-11-16)
[^s4]: Amazon Web Services, "New and enhanced AWS Support plans add AI capabilities to expert guidance" (AWS News Blog, 2025-12-02). https://aws.amazon.com/blogs/aws/new-and-enhanced-aws-support-plans-add-ai-capabilities-to-expert-guidance (accessed 2026-11-16)
[^s5]: Amazon Web Services, "AWS Support transformation: AI-powered operations with the human expertise you trust" (What's New with AWS, 2025-12-02). https://aws.amazon.com/about-aws/whats-new/2025/12/aws-support-transformation-ai-powered-operations (accessed 2026-11-16)
[^s6]: Amazon Web Services, "AWS Certified Cloud Practitioner (CLF-C02) - Content Domain 4: Billing, Pricing, and Support". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.md (accessed 2026-11-16)
