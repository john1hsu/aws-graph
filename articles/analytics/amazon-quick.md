---
id: kt:amazon-quick
type: Service
title: AWS Quick
aliases: [Amazon Quick, Amazon Quick Suite, Quick Suite, Amazon QuickSight, QuickSight]
domain: analytics
summary: >-
  AWS's AI-powered workspace that combines business intelligence dashboards (Quick Sight) with
  chat agents, research, workflow automation and an enterprise index over connected data; it
  evolved from Amazon QuickSight and was renamed from Amazon Quick to AWS Quick in November 2026.
verified: 2026-11-16
status: current
broader: [kt:aws-analytics-services]
related: [kt:aws-govcloud-us, kt:amazon-bedrock]
relations:
  - {predicate: integratesWith, object: kt:amazon-redshift}
  - {predicate: integratesWith, object: kt:amazon-athena}
  - {predicate: dependsOn, object: kt:iam-identity-center}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
sources:
  - id: s1
    title: "What is AWS Quick?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/quick/latest/userguide/what-is.md
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Amazon Quick is now AWS Quick"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/11/amazon-quick-aws-quick/
    type: official-blog
    accessed: 2026-11-16
  - id: s3
    title: "AWS Quick pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/quick/pricing
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Supported data sources (AWS Quick user guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/quick/latest/userguide/supported-data-sources.md
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "AWS Quick Sight endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/quicksight.html
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS Quick

AWS Quick is a fully managed, AI-powered service for analyzing data, automating tasks, building small web apps and doing research, driven mainly through natural-language chat in which AI agents work against the data sources and applications you connect.[^s1] It grew out of **Amazon QuickSight**: the BI product lives on as **AWS Quick Sight**, a feature inside Quick, and AWS states that existing QuickSight APIs, SDKs and integrations keep working unchanged.[^s1] On November 9, 2026 AWS renamed the service from Amazon Quick to AWS Quick (and Amazon Quick Sight to AWS Quick Sight); existing customers need take no action, and dashboards, datasets, analyses, settings and pricing are unaffected.[^s2]

## How it works

Quick bundles several capabilities behind one chat interface:[^s1]

- **Quick Sight** – dashboards, interactive visualizations and embedded analytics over connected data.[^s1]
- **Quick Flows** – AI-driven workflows for repetitive tasks that can act in connected applications.[^s1]
- **Quick Automate** – larger business-process automations in which agents make contextual decisions across systems.[^s1]
- **Quick Index** – an index of organizational documents and data that grounds AI answers.[^s1]
- **Quick Research** – deep research across the web and your data, returned as a cited report.[^s1]
- **Apps** – interactive web apps described in natural language.[^s1]

Integrations use knowledge bases, action connectors, extensions and structured data connections, with Model Context Protocol (MCP) and OpenAPI for custom systems; users can reach Quick on the web, a desktop app, or inside Chrome, Slack, Microsoft Teams and Microsoft 365.[^s1] There are two ways in: individuals can sign up with email or a social login without an AWS account, while organizations provision it through the AWS Management Console using IAM Identity Center or IAM federation and pay through normal AWS billing.[^s1]

For BI, Quick Sight connects to AWS stores such as Amazon Athena, Amazon Redshift (and Redshift Spectrum), Aurora, OpenSearch Service, Amazon S3 and S3 Tables, plus engines like Snowflake, Databricks, BigQuery, Trino, Oracle, SQL Server and PostgreSQL; it also imports CSV, TSV, JSON and XLSX files and reads SaaS sources such as Jira, ServiceNow and Salesforce.[^s4] Redshift, Athena and RDS sources must run in AWS, and on-premises databases are reached through a Quick-specific network interface in an Amazon VPC.[^s4]

## Quotas, pricing and gotchas

**Pricing (as listed on the AWS Quick pricing page, 2026-11):** organization plans need an AWS account and charge per user plus a **$250 per account per month infrastructure fee** — Essentials $3, Professional $20 and Enterprise $40 per user per month, with 1, 8 and 18 agent hours per user per month respectively.[^s3] Agent-hour overage is $3 per hour, metered per second, and index storage beyond the pooled allowance is $5 per GB-month; only Enterprise includes custom dashboards, Quick Automate and asset certification.[^s3] Individual plans (Free, Plus, Max) carry no infrastructure fee.[^s3] SPICE, the in-memory BI engine, costs $0.38 per GB-month, and a 30-day Enterprise trial covers up to 25 users before Enterprise billing starts.[^s3] Former Quick Sight Reader per-user licenses are upgraded to Essentials, while Reader capacity pricing is not included in that upgrade.[^s3]

**Quotas (per Region, as of 2026-11):** most are fixed. Examples: three active sessions per user, 120 seconds query timeout for visuals, 2,000 fields per dataset, 60,000 MB maximum index capacity, a 15-minute minimum schedule interval, and 32 dataset ingestion API calls per 24 hours on Enterprise edition (8 on Standard), all not adjustable.[^s5] Quick Automate limits such as 1,000 automations and 200 automation groups per Region can be raised; a single automation run is capped at 48 hours.[^s5]

**Gotchas:** the infrastructure fee makes very small organizational deployments relatively expensive; the APIs still use the `quicksight` namespace and endpoints despite the renames;[^s5] and Regional coverage differs from the full AWS footprint, so check the endpoint list before choosing a home Region.[^s5]

## When to use it (and when not)

Choose Quick when business users need governed dashboards plus a chat assistant that can answer from company data and act in tools like Jira or Slack, without the team hosting models.[^s1] For SQL analysis by engineers, Athena or Redshift are the engines Quick Sight sits on top of rather than alternatives.[^s4] Quick hosts the models for you, so teams that want to build their own generative-AI application rather than adopt a packaged workspace are the main case for working directly with Amazon Bedrock instead.[^s1]

## Connections

AWS Quick sits under **AWS analytics services** as the BI and presentation layer. It **integrates with** Amazon Redshift and Amazon Athena as relational data sources.[^s4] Organizational deployments **depend on** IAM Identity Center (or IAM federation) for users.[^s1] It is listed in AWS's FedRAMP Services in Scope under its SDK name `quicksight`, checked for both the Moderate (US East/West) and High (GovCloud) baselines, so it **complies with** FedRAMP on AWS;[^s6] Quick Sight endpoints exist in both AWS GovCloud (US) Regions, which is why it is related to GovCloud.[^s5] It is related to Amazon Bedrock as the build-your-own alternative to Quick's packaged agents.[^s1]

## Sources
[^s1]: Amazon Web Services, "What is AWS Quick?". https://docs.aws.amazon.com/quick/latest/userguide/what-is.md (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Amazon Quick is now AWS Quick" (What's New, 2026-11-09). https://aws.amazon.com/about-aws/whats-new/2026/11/amazon-quick-aws-quick/ (accessed 2026-11-16)
[^s3]: Amazon Web Services, "AWS Quick pricing". https://aws.amazon.com/quick/pricing (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Supported data sources". https://docs.aws.amazon.com/quick/latest/userguide/supported-data-sources.md (accessed 2026-11-16)
[^s5]: Amazon Web Services, "AWS Quick Sight endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/quicksight.html (accessed 2026-11-16)
[^s6]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-16)
