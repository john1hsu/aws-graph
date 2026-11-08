---
id: kt:amazon-cloudwatch
type: Service
title: Amazon CloudWatch
aliases: [CloudWatch, AWS CloudWatch]
domain: governance-and-operations
summary: >-
  AWS's monitoring and observability service: it collects metrics, logs and traces from AWS
  resources and applications, evaluates them with alarms, visualizes them on dashboards, and adds
  application-performance, infrastructure and network monitoring on top.
verified: 2026-11-08
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:aws-cloudtrail, kt:amazon-ec2, kt:aws-config]
relations:
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: governedBy, object: kt:cloudwatch-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is Amazon CloudWatch?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.md
    type: official-docs
    accessed: 2026-11-08
  - id: s2
    title: "Amazon CloudWatch pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/cloudwatch/pricing/
    type: official-docs
    accessed: 2026-11-08
  - id: s3
    title: "GetMetricData - Amazon CloudWatch API Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/goto/WebAPI/monitoring/GetMetricData
    type: official-docs
    accessed: 2026-11-08
  - id: s4
    title: "CloudWatch service quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_limits.html
    type: official-docs
    accessed: 2026-11-08
  - id: s5
    title: "New – High-Resolution Custom Metrics and Alarms for Amazon CloudWatch"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/new-high-resolution-custom-metrics-and-alarms-for-amazon-cloudwatch/
    type: official-blog
    accessed: 2026-11-08
  - id: s6
    title: "Amazon CloudWatch Omni: AI-first observability for agents and applications"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-cloudwatch-omni-ai/
    type: official-blog
    accessed: 2026-11-08
  - id: s7
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-08
created: 2026-11-08
---

# Amazon CloudWatch

Amazon CloudWatch is the AWS-native service for monitoring resources and the applications running on them in near real time. It began as a metrics-and-alarms service and now spans logs, traces, application performance monitoring (APM), infrastructure insights and network monitoring, all inside one console and API family.[^s1] It is generally available and listed under management and governance among the in-scope services for the AWS Certified Cloud Practitioner (CLF-C02) exam.[^s7]

## How it works

**Metrics** are time-ordered data points that many AWS services publish automatically; you can add custom metrics through the `PutMetricData` API or over OpenTelemetry (OTLP). CloudWatch now distinguishes OpenTelemetry metrics (the recommended path) from "classic" CloudWatch metrics, and supports PromQL for querying OpenTelemetry metrics.[^s1] **Alarms** watch a metric against a threshold and can notify or trigger automated actions when it is breached; **dashboards** combine metric and log visualizations, can be shared across accounts and Regions, and AWS supplies curated automatic dashboards for many services.[^s1]

**CloudWatch Logs** stores log events in log groups and streams. Logs Insights runs interactive queries (with a choice of three query languages, including SQL and PPL), metric filters turn log values into metrics, anomaly detection flags unusual patterns, and subscription filters stream logs onward to destinations such as Amazon S3 or Firehose.[^s1] The **CloudWatch agent** collects OS-level metrics (memory, disk, processes, GPU), logs and traces from EC2 fleets and on-premises servers on Linux and Windows.[^s1]

On top of that core sit several specialized features: Application Signals for APM without code changes, Synthetics canaries that probe endpoints, RUM for real-user sessions, SLOs with error budgets, Container, Lambda and Database Insights, and Internet Monitor, Network Flow Monitor and Network Synthetic Monitor for network health.[^s1] Cross-account observability lets a central monitoring account view metrics, logs and traces from source accounts, linked individually or automatically through AWS Organizations.[^s1]

## Retention, quotas and pricing

Standard metrics have 1-minute resolution; custom metrics can be published at 1-second ("high") resolution, and high-resolution alarms can evaluate as often as every 10 seconds.[^s5] Data is rolled up as it ages: sub-minute data points are kept for 3 hours, 1-minute points for 15 days, 5-minute points for 63 days, and 1-hour points for 455 days (15 months).[^s3] A practical gotcha follows: after 15 days you can no longer retrieve 1-minute detail, so export anything you need at fine grain.[^s3]

As of 2026-11, the default `PutMetricData` and `GetMetricData` request rates are each 500 per second per account per Region, and both are adjustable through Service Quotas.[^s4]

Pricing is pay-as-you-go. The always-free tier (as of 2026-11) includes basic monitoring metrics from AWS services, 10 custom or detailed-monitoring metrics, 1 million API requests (GetMetricData, GetInsightRuleReport and GetMetricWidgetImage are always charged), 3 custom dashboards of up to 50 metrics each, 10 standard-resolution alarm metrics, and 5 GB of log data across ingestion, archive storage and Logs Insights scans.[^s2] Pricing examples for US East (N. Virginia) use $0.30 per custom metric per month for the first 10,000 metrics, $0.10 per standard-resolution alarm metric per month, and $0.50 per GB of standard log ingestion beyond the free 5 GB; AWS labels these as examples, so check the calculator for your Region.[^s2] Because GetMetricData calls are always billed and log ingestion is charged per GB, dashboards that poll often and verbose application logging are the usual cost surprises.[^s2]

## What changed (2026-09)

AWS made **CloudWatch Omni** generally available on 2026-09-23 in US East (N. Virginia), US West (Oregon) and Europe (Ireland). Omni is an AI-assisted observability experience that discovers services and dependencies, answers plain-language questions by writing SQL or PromQL, and scores AI-agent responses with evaluators alongside latency, errors and token usage.[^s6][^s1] It builds on existing CloudWatch telemetry, so current metrics, alarms and dashboards keep working without reconfiguration.[^s1]

## When to use it (and when not)

CloudWatch is the default first stop for operational monitoring on AWS: many AWS services send basic metrics to it for free, alarms can trigger automated actions as well as notifications, and Container, Lambda and Database Insights extend it to those workloads.[^s1] Teams that already standardize on a third-party observability platform can still instrument with OpenTelemetry and send the same telemetry to both CloudWatch and other destinations.[^s1] CloudWatch answers *how is it performing*; it is not an API audit trail (AWS CloudTrail) or a configuration-history store (AWS Config), so the three are usually deployed together.[^s1]

## Connections

CloudWatch sits under AWS's management and governance services. It is commonly paired with **AWS CloudTrail** (API audit events) and **AWS Config** (resource configuration state), and it is the monitoring layer for **Amazon EC2**, where the agent adds in-guest metrics.[^s1] It integrates with **AWS Organizations** for cross-account observability and with **Amazon S3** as a destination for logs routed by subscription filters; its resources and quotas are per **Region**, governed by the limits in its service-quotas page; and it is covered by the **Cloud Practitioner** exam.[^s1][^s4][^s7]

## Sources
[^s1]: Amazon Web Services, "What is Amazon CloudWatch?". https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.md (accessed 2026-11-08)
[^s2]: Amazon Web Services, "Amazon CloudWatch pricing". https://aws.amazon.com/cloudwatch/pricing/ (accessed 2026-11-08)
[^s3]: Amazon Web Services, "GetMetricData - Amazon CloudWatch API Reference". https://docs.aws.amazon.com/goto/WebAPI/monitoring/GetMetricData (accessed 2026-11-08)
[^s4]: Amazon Web Services, "CloudWatch service quotas". https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_limits.html (accessed 2026-11-08)
[^s5]: Amazon Web Services, "New – High-Resolution Custom Metrics and Alarms for Amazon CloudWatch", AWS News Blog. https://aws.amazon.com/blogs/aws/new-high-resolution-custom-metrics-and-alarms-for-amazon-cloudwatch/ (accessed 2026-11-08)
[^s6]: Amazon Web Services, "Amazon CloudWatch Omni: AI-first observability for agents and applications", What's New with AWS. https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-cloudwatch-omni-ai/ (accessed 2026-11-08)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-08)
