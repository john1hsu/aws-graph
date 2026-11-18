---
id: kt:amazon-detective
type: Service
title: Amazon Detective
aliases: [Detective, AWS Detective]
domain: security
summary: >-
  AWS's security investigation service, which automatically collects CloudTrail, VPC Flow Logs and
  GuardDuty data into a behavior graph and uses machine learning, statistics and graph analysis to help
  analysts find the root cause and scope of security findings.
verified: 2026-11-18
status: current
broader: [kt:aws-security-services]
related: [kt:amazon-guardduty, kt:aws-security-hub, kt:amazon-inspector, kt:amazon-security-lake]
relations:
  - {predicate: dependsOn, object: kt:aws-cloudtrail}
  - {predicate: integratesWith, object: kt:amazon-guardduty}
  - {predicate: configuredVia, object: kt:aws-organizations}
  - {predicate: governedBy, object: kt:detective-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is Amazon Detective?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/detective/latest/userguide/what-is-detective
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Amazon Detective FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/detective/faqs
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Source data used in a Detective behavior graph"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/detective/latest/userguide/detective-source-data-about.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Amazon Detective Regions and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/detective/latest/userguide/regions-limitations.html
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Amazon Detective adds investigations and finding group summaries to help you investigate security findings"
    publisher: AWS News Blog
    url: https://aws.amazon.com/blogs/aws/amazon-detective-adds-investigations-and-finding-group-summaries-to-help-you-investigate-security-findings
    type: official-blog
    accessed: 2026-11-18
  - id: s6
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-18
  - id: s7
    title: "In-Scope AWS Services (AWS Certified Cloud Practitioner CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-18
created: 2026-11-18
---

# Amazon Detective

Amazon Detective is AWS's service for investigating security findings and suspicious activity after something has been flagged. It collects log data from your AWS resources automatically and applies machine learning, statistical analysis and graph theory to produce visualizations of who did what, from where and how that compares with normal behavior.[^s1] The point is to spare analysts from building their own pipelines: there are no queries, algorithms or data models to write and tune.[^s1] Detective is generally available and sits in the "respond and investigate" stage of the AWS security toolset, downstream of detection services such as Amazon GuardDuty.[^s1][^s2]

## How it works

Enabling Detective in an account makes that account the **administrator** of a **behavior graph**, a linked set of extracted and analyzed data from one or more accounts. The administrator invites **member accounts** to contribute their data, or, with AWS Organizations, a delegated Detective administrator enables organization accounts as members.[^s1] Data stays inside the Region where the graph lives: Detective is enabled Region by Region and does not move data across Regional boundaries.[^s2]

The **core source package** starts with Detective: AWS CloudTrail events, Amazon VPC Flow Logs and, for accounts enrolled in GuardDuty, GuardDuty findings.[^s3] Flow-log ingestion covers only EC2 instances in the monitored VPCs, not NAT gateways, RDS instances or Fargate tasks. It takes IPv4 and IPv6 records, both accepted and rejected traffic.[^s3] Two **optional packages** can be turned on or off at any time: Amazon EKS audit logs, and AWS security findings from Security Hub CSPM.[^s3] GuardDuty is not required, but without it Detective has no GuardDuty findings to pivot from.[^s2]

Detective retains data for up to one year, and it builds activity baselines after roughly two weeks of monitoring.[^s2][^s3] Entity profiles answer questions such as whether an API call is unusual for a role or whether a traffic spike from an instance is expected.[^s1] **Finding groups** cluster related findings and entities from one potential incident, and **finding group summaries** use generative AI to describe the chain of events in plain language.[^s2] **Detective Investigations**, launched in November 2023, examines IAM users and roles for indicators of compromise and maps activity to MITRE ATT&CK tactics. You can run it from the console or with the `StartInvestigation` API.[^s5][^s1] An Amazon Security Lake integration lets Detective query raw CloudTrail management-event and VPC Flow Log records held in Security Lake.[^s1]

## Quotas, pricing and gotchas

Detective's quotas are fixed (as of 2026-11, per behavior graph): at most 1,200 member accounts per administrator. At 9 TB of data per day Detective warns you. At 10 TB per day you can't add member accounts, and at 15 TB per day ingestion stops until AWS Support re-enables it.[^s4]

Pricing has no minimum commitment. Detective charges a tiered flat rate per GB of data ingested, per account, per Region, per month, whatever the source.[^s1][^s2] Each Region gets a 30-day free trial for all core and optional packages. This applies to new accounts too, including those enabled through Organizations, and the console shows an estimate of what you will pay afterwards.[^s3][^s1] Gotchas: if you stop and later restart an optional package, Detective does not backfill the gap.[^s3] Because data is Regional, multi-Region estates need a behavior graph in every Region they want to investigate.[^s2]

## When to use it (and when not)

Use Detective when a GuardDuty or Security Hub finding needs triage. It answers how far an incident has spread and what the affected principal or instance normally does, without exporting logs into a SIEM.[^s1][^s2] It does not detect threats on its own the way GuardDuty does, and it doesn't aggregate posture checks the way Security Hub does. It also does not remediate anything; pair it with EventBridge-driven automation or your incident-response runbooks.[^s1] Teams that already centralize logs in a SIEM or in Security Lake may lean on those for hunting, and use Detective's prebuilt visualizations for fast first-pass triage.[^s1]

## Connections

Detective belongs to the **AWS security services** family (**broader**). It **depends on AWS CloudTrail** as a core data source and **integrates with Amazon GuardDuty**, whose findings it ingests and links to activity.[^s3] It is **related to** AWS Security Hub, which you can pivot from and whose findings it can ingest, to Amazon Security Lake, whose raw logs it can query, and to Amazon Inspector, a neighbouring security service.[^s1][^s3] It is **configured via AWS Organizations** through a delegated administrator.[^s1] Its fixed limits are **Detective quotas**, and behavior graphs are **scoped to AWS Regions**.[^s4][^s2] AWS's FedRAMP services-in-scope list marks Amazon Detective for both the East/West and GovCloud authorizations (**compliesWith** FedRAMP on AWS).[^s6] It is listed under Security, Identity and Compliance in the in-scope services for the Cloud Practitioner (CLF-C02) exam (**coveredBy**).[^s7]

## Sources
[^s1]: Amazon Web Services, "What is Amazon Detective?". https://docs.aws.amazon.com/detective/latest/userguide/what-is-detective (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Amazon Detective FAQs". https://aws.amazon.com/detective/faqs (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Source data used in a Detective behavior graph". https://docs.aws.amazon.com/detective/latest/userguide/detective-source-data-about.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Amazon Detective Regions and quotas". https://docs.aws.amazon.com/detective/latest/userguide/regions-limitations.html (accessed 2026-11-18)
[^s5]: AWS News Blog, "Amazon Detective adds investigations and finding group summaries to help you investigate security findings" (26 November 2023). https://aws.amazon.com/blogs/aws/amazon-detective-adds-investigations-and-finding-group-summaries-to-help-you-investigate-security-findings (accessed 2026-11-18)
[^s6]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-18)
[^s7]: Amazon Web Services, "In-Scope AWS Services" (AWS Certified Cloud Practitioner CLF-C02). https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-18)
