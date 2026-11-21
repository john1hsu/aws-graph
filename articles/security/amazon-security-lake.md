---
id: kt:amazon-security-lake
type: Service
title: Amazon Security Lake
aliases: [Security Lake, AWS Security Lake]
domain: security
summary: >-
  AWS's fully managed security data lake, which collects logs and findings from AWS services, SaaS
  applications and third-party sources into Amazon S3 buckets in your own account, normalizes them to the
  Open Cybersecurity Schema Framework (OCSF) in Apache Parquet, and shares them with analytics and SIEM
  subscribers.
verified: 2026-11-21
status: current
broader: [kt:aws-security-services, kt:data-lake-on-aws]
related: [kt:amazon-detective, kt:aws-security-hub, kt:amazon-athena, kt:amazon-opensearch-service]
relations:
  - {predicate: dependsOn, object: kt:amazon-s3}
  - {predicate: dependsOn, object: kt:aws-lake-formation}
  - {predicate: integratesWith, object: kt:aws-glue}
  - {predicate: implements, object: kt:open-cybersecurity-schema-framework}
  - {predicate: configuredVia, object: kt:aws-organizations}
  - {predicate: alternativeTo, object: kt:aws-cloudtrail-lake}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
sources:
  - id: s1
    title: "What is Amazon Security Lake?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/security-lake/latest/userguide/what-is-security-lake.html
    type: official-docs
    accessed: 2026-11-21
  - id: s2
    title: "Concepts and terminology - Amazon Security Lake"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/security-lake/latest/userguide/service-concepts.html
    type: official-docs
    accessed: 2026-11-21
  - id: s3
    title: "Open Cybersecurity Schema Framework (OCSF) in Security Lake"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/security-lake/latest/userguide/open-cybersecurity-schema-framework.html
    type: official-docs
    accessed: 2026-11-21
  - id: s4
    title: "Amazon Security Lake FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/security-lake/faqs/
    type: official-docs
    accessed: 2026-11-21
  - id: s5
    title: "Amazon Security Lake Features"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/security-lake/features/
    type: official-docs
    accessed: 2026-11-21
  - id: s6
    title: "Amazon Security Lake Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/security-lake/pricing/
    type: official-docs
    accessed: 2026-11-21
  - id: s7
    title: "How Security Lake pricing is determined"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/security-lake/latest/userguide/estimating-costs.html
    type: official-docs
    accessed: 2026-11-21
  - id: s8
    title: "Managing multiple accounts with AWS Organizations in Security Lake"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/security-lake/latest/userguide/multi-account-management.html
    type: official-docs
    accessed: 2026-11-21
  - id: s9
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-21
created: 2026-11-21
---

# Amazon Security Lake

Amazon Security Lake is a fully managed security data lake. It centralizes security logs and findings from AWS environments, SaaS providers, on-premises systems and other clouds into Amazon S3 buckets that live in your own AWS account, so you keep ownership of the data.[^s1] Instead of every team building its own log pipeline, Security Lake converts what it ingests into one open schema, the **Open Cybersecurity Schema Framework (OCSF)**, stored as Apache Parquet, and then shares it with the tools that need it.[^s1][^s3]

## How it works

A **source** is a set of logs from one system that maps to an OCSF event class.[^s2] Natively supported AWS sources are CloudTrail management events and S3 and Lambda data events, Amazon EKS audit logs, Route 53 Resolver query logs, Security Hub CSPM findings, VPC Flow Logs and AWS WAFv2 logs.[^s1] Because Security Hub aggregates findings from services such as GuardDuty, Inspector, Macie, AWS Config and IAM Access Analyzer, those findings reach the lake through it.[^s4] **Custom sources** (third-party tools or your own applications) must convert their data to OCSF and Parquet before sending it.[^s2][^s3] AWS AppFabric can normalize SaaS audit logs to OCSF and deliver them.[^s5]

OCSF is an open-source effort started by AWS and partners across the security industry that gives common security events one standard schema.[^s3] Security Lake has two generations of its AWS source schemas, version 1 (OCSF 1.0.0-rc.2) and version 2 (OCSF 1.1.0). Version 2 added the EKS audit log and WAFv2 sources.[^s3]

Under the hood Security Lake builds on other services. AWS Glue crawlers create Data Catalog tables and AWS Lake Formation creates a table per contributing source. EventBridge and SQS handle notifications, and Lambda runs ETL.[^s1] Tables can be Apache Iceberg tables in the Glue catalog.[^s5] You set retention and automated storage tiering on the S3 data.[^s1]

Consumers are **subscribers**. A subscriber with **data access** gets notified when new objects land and reads them directly. A subscriber with **query access** queries the lake in place through Lake Formation, with Amazon Athena, Amazon Redshift or a third-party tool that integrates with Glue.[^s5][^s1] Amazon OpenSearch Service can query Security Lake directly and comes with prebuilt OCSF dashboards.[^s5]

Data is Regional. You can make one Region a **rollup Region**, which gathers data from one or more **contributing Regions** into one place and can help you meet data-residency rules.[^s2] Across an organization, the management account registers one **delegated administrator** using the Security Lake API, not the Organizations console. AWS recommends the Log Archive account, and the management account itself can't fill the role.[^s8] The delegated administrator turns on sources for member accounts, grants subscribers access, and can auto-enable accounts that join later. Existing accounts are not enabled automatically.[^s8]

## Quotas, pricing and gotchas

Pricing has two dimensions: **ingestion** per GB, which depends on the log's origin, and **normalization** (conversion to OCSF and Parquet) per GB.[^s6][^s7] AWS's US East (N. Virginia) worked example, as of 2026-11, prices CloudTrail ingestion at $0.75 per GB, other AWS log ingestion at $0.25 per GB and normalization at $0.035 per GB. Data you bring from third parties or your own sources isn't charged by Security Lake.[^s6] On top of that you pay for S3 storage, Glue, EventBridge, Lambda and SQS, and for cross-Region transfer to a rollup Region. Subscribers pay for their own queries.[^s6][^s7]

New accounts get an automatic 15-day free trial when log collection is first turned on. After the trial, charges start unless you disable the service.[^s7][^s4] Other gotchas:

- Collecting CloudTrail management events needs an organization trail, and creating a new one adds CloudTrail charges.[^s4][^s6]
- Removing the delegated administrator deletes the data lake and disables Security Lake for member accounts.[^s8]
- Security Lake doesn't support S3 Select on its buckets.[^s1]

## When to use it (and when not)

Choose Security Lake when you want security telemetry from many accounts, Regions and vendors in one OCSF-normalized store that you own, and that several tools can analyze without each running its own collectors.[^s1][^s5] AWS positions **AWS CloudTrail Lake** differently: it is a managed store for CloudTrail activity that you query with SQL and can keep for up to seven years. Security Lake covers a broader set of sources and feeds external SIEMs.[^s4] Security Lake doesn't detect threats or investigate them. You pair it with Security Hub, GuardDuty or Amazon Detective, or with your own SIEM.[^s1][^s4]

## Connections

Security Lake belongs to the **AWS security services** family, and it is a specialized case of a **data lake on AWS** (both **broader**).[^s1] It **depends on Amazon S3** for storage and **on AWS Lake Formation** for table permissions. It **integrates with AWS Glue** for its catalog.[^s1] It **implements OCSF**.[^s3] Multi-account rollout is **configured via AWS Organizations** through a delegated administrator.[^s8] It is an **alternative to AWS CloudTrail Lake** for some log-analytics needs.[^s4] Data is **scoped to AWS Regions**, with rollup Regions to consolidate it.[^s2] Its **related** nodes are Amazon Detective, which can query its raw logs, AWS Security Hub, a key source of findings, and Amazon Athena and Amazon OpenSearch Service, which act as query subscribers.[^s1][^s5] AWS's FedRAMP services-in-scope list (last updated 2026-11-11) shows Amazon Security Lake in scope for both the East/West and the GovCloud authorizations (**compliesWith** FedRAMP on AWS).[^s9]

## Sources
[^s1]: Amazon Web Services, "What is Amazon Security Lake?". https://docs.aws.amazon.com/security-lake/latest/userguide/what-is-security-lake.html (accessed 2026-11-21)
[^s2]: Amazon Web Services, "Concepts and terminology - Amazon Security Lake". https://docs.aws.amazon.com/security-lake/latest/userguide/service-concepts.html (accessed 2026-11-21)
[^s3]: Amazon Web Services, "Open Cybersecurity Schema Framework (OCSF) in Security Lake". https://docs.aws.amazon.com/security-lake/latest/userguide/open-cybersecurity-schema-framework.html (accessed 2026-11-21)
[^s4]: Amazon Web Services, "Amazon Security Lake FAQs". https://aws.amazon.com/security-lake/faqs/ (accessed 2026-11-21)
[^s5]: Amazon Web Services, "Amazon Security Lake Features". https://aws.amazon.com/security-lake/features/ (accessed 2026-11-21)
[^s6]: Amazon Web Services, "Amazon Security Lake Pricing". https://aws.amazon.com/security-lake/pricing/ (accessed 2026-11-21)
[^s7]: Amazon Web Services, "How Security Lake pricing is determined". https://docs.aws.amazon.com/security-lake/latest/userguide/estimating-costs.html (accessed 2026-11-21)
[^s8]: Amazon Web Services, "Managing multiple accounts with AWS Organizations in Security Lake". https://docs.aws.amazon.com/security-lake/latest/userguide/multi-account-management.html (accessed 2026-11-21)
[^s9]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-21)
