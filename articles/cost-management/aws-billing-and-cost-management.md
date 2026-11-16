---
id: kt:aws-billing-and-cost-management
type: Service
title: AWS Billing and Cost Management
aliases: [AWS Billing, Billing and Cost Management console, AWS Billing console, Billing and Cost Management]
domain: cost-management
summary: >-
  The AWS service and console where an account or organization receives its monthly bills and invoices,
  pays them through payment methods and payment profiles, manages credits, purchase orders and billing
  preferences, and opens the cost tools (Cost Explorer, Budgets, data exports) that sit beside billing.
verified: 2026-11-16
status: current
broader: [kt:aws-cost-management-services]
related: [kt:aws-cost-explorer, kt:aws-budgets, kt:cost-allocation-tags, kt:aws-free-tier]
relations:
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: integratesWith, object: kt:aws-billing-conductor}
  - {predicate: hasPart, object: kt:aws-billing-transfer}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is AWS Billing and Cost Management? - AWS Billing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-what-is.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Understanding your bill - AWS Billing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/getting-viewing-bill.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Using payment profiles - AWS Billing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/manage-paymentprofiles.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Overview of managing access permissions - AWS Billing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/control-access-billing.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "AWS Billing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/aws-cost-management/aws-billing/
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "AWS launches Billing Transfer for multi-organization billing and cost management"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/billing-transfer-multi-organization-billing-cost-management/
    type: official-blog
    accessed: 2026-11-16
  - id: s7
    title: "Enhanced Transactions view now available in AWS Billing Console"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/01/enhanced-transactions-view-aws-billing-console/
    type: official-blog
    accessed: 2026-11-16
  - id: s8
    title: "Content Domain 4: Billing, Pricing, and Support - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.html
    type: official-learning
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS Billing and Cost Management

AWS Billing and Cost Management is the service and console through which an AWS account sets up billing, retrieves and pays invoices, and reaches the tools for analysing spend. AWS describes the default case simply: individuals and small organizations are charged automatically to the credit card on file, while larger organizations consolidate charges across accounts and configure invoicing, tax, purchase orders and payment methods to fit their procurement process.[^s1] This article covers the billing side of the console; the wider family of cost tools is mapped in `kt:aws-cost-management-services`.

## Bills, invoices and sellers of record

AWS bills usage and recurring fees monthly, at the end of each billing period, while one-time fees such as an All Upfront Reserved Instance purchase are charged immediately. If you pay by card, the card on file is charged when the invoice is issued.[^s2] The **Bills** page shows each period with one of two statuses: *Pending* means the period is still open and the totals are estimates based on usage metered so far, and *Issued* means the period has closed, the invoice exists and the totals are final.[^s2] Tabs break the bill down by service, by account (for a management account), by savings and by taxes.[^s2] The page also includes AWS Marketplace purchases, and the product page stresses reconciliation: you can match an invoice back to the line items that produced it, such as instance-hours, API requests or GB-months.[^s5]

A single month can produce several invoices, because each AWS **service provider** (seller of record) issues its own. The Invoices tab lists one row per provider, with Amazon Web Services, Inc. and AWS EMEA SARL as the documented examples; tax invoices are separate documents that reference the commercial invoice, and not every provider issues them.[^s2] You can correct the company name, address or purchase order on an invoice and have it reprocessed; other corrections go through AWS Support.[^s2]

## Payments, payment profiles and preferences

The billing-and-payments group of the console contains the Bills page, purchase orders, a Payments page for outstanding and past-due balances and payment history, payment profiles, credits (where you review balances and choose where they apply) and billing preferences such as email delivery of invoices and credit and discount sharing.[^s1] **Payment profiles** tie a currency and payment method to one service provider, so that, for example, AWS Inc. invoices are paid in USD by card while AWS EMEA SARL invoices are paid in EUR. Creating a profile changes only *new* invoices from that provider. Without one, AWS uses the default payment preference from sign-up, which may not be valid for every provider because of country and technology limits.[^s3] In January 2026 the Payments page's Transactions view was rebuilt to load very large histories and add balance tracking and filtering.[^s7]

## Access control

By default, IAM users and roles cannot open the Billing and Cost Management console, even when their policies grant billing permissions. The root user must first turn on **Activate IAM Access**, once per account; member accounts created through AWS Organizations have it on by default.[^s4] Activation alone grants nothing: you still attach managed or customer managed policies for the specific pages. The setting governs pages such as Bills, Payments, Payment methods, Tax settings, Budgets and Cost Explorer, but not the billing SDK APIs or the in-console Pricing Calculator.[^s4] If a new admin role can't see billing, check this switch before the policy.

## Multi-account billing

With AWS Organizations **consolidated billing**, the management account pays for every member account, gets one bill and pools usage for volume pricing and Reserved Instance and Savings Plans discounts, at no additional cost.[^s1] **Billing transfer**, launched on 2025-11-19, goes a step further: one external management account can receive and pay the consolidated bills of several organizations while each organization keeps its own security governance.[^s6] Bill source accounts stop receiving invoices while the transfer is active but remain responsible for charges from before it.[^s2] It integrates with AWS Billing Conductor to control what cost data each organization sees. As of 2026-11, AWS states there is no charge with an AWS managed pricing plan, and USD 50 per AWS Organization with a customer managed pricing plan, charged from 2026-06-01 after a free trial; it is not available in AWS GovCloud (US) or the China Regions.[^s6]

## When to use what

Use this console for anything about *paying*: invoices, tax documents, payment methods and who in the organization may see them. For *analysing* or *controlling* spend, move to the neighbouring tools it hosts, Cost Explorer for trends and forecasts and AWS Budgets for alerts, and to cost allocation tags and cost categories for attributing spend.[^s1] Since 2025-11-01 the Bills page no longer offers "Download all to CSV"; AWS points users to the Data Exports page for detailed billing data.[^s2] Billing Conductor is for showback and chargeback: it produces pro forma billing data with custom rates without changing what AWS actually bills.[^s1]

## Connections

The node sits under `kt:aws-cost-management-services`, the category map, and is related to the cost tools reached from its console: `kt:aws-cost-explorer`, `kt:aws-budgets`, `kt:cost-allocation-tags` and `kt:aws-free-tier` usage tracking.[^s1] It `integratesWith` `kt:aws-organizations` through consolidated billing and `kt:aws-billing-conductor` for pro forma views, and `hasPart` `kt:aws-billing-transfer`, the multi-organization feature.[^s1][^s6] It is `securedBy` `kt:aws-iam` through the Activate IAM Access switch and billing policies.[^s4] The `coveredBy` edge reflects CLF-C02 Task Statement 4.2, which lists billing support and information, AWS Organizations and consolidated billing among its knowledge and skills.[^s8]

## Sources
[^s1]: Amazon Web Services, "What is AWS Billing and Cost Management? - AWS Billing". https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-what-is.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Understanding your bill - AWS Billing". https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/getting-viewing-bill.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Using payment profiles - AWS Billing". https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/manage-paymentprofiles.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Overview of managing access permissions - AWS Billing". https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/control-access-billing.html (accessed 2026-11-16)
[^s5]: Amazon Web Services, "AWS Billing". https://aws.amazon.com/aws-cost-management/aws-billing/ (accessed 2026-11-16)
[^s6]: Amazon Web Services, "AWS launches Billing Transfer for multi-organization billing and cost management". https://aws.amazon.com/about-aws/whats-new/2025/11/billing-transfer-multi-organization-billing-cost-management/ (accessed 2026-11-16)
[^s7]: Amazon Web Services, "Enhanced Transactions view now available in AWS Billing Console". https://aws.amazon.com/about-aws/whats-new/2026/01/enhanced-transactions-view-aws-billing-console/ (accessed 2026-11-16)
[^s8]: Amazon Web Services, "Content Domain 4: Billing, Pricing, and Support - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.html (accessed 2026-11-16)
