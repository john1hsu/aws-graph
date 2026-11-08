---
id: kt:aws-cloudtrail
type: Service
title: AWS CloudTrail
aliases: [CloudTrail]
domain: security
summary: >-
  AWS's audit-logging service that records actions taken by users, roles and AWS services as
  events, with a free 90-day event history, trails that deliver logs to Amazon S3, anomaly
  detection through Insights, and the CloudTrail Lake event data store.
verified: 2026-11-08
status: current
broader: [kt:aws-security-services]
related: [kt:amazon-cloudwatch, kt:aws-organizations, kt:aws-config]
relations:
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: queriedWith, object: kt:amazon-athena}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: governedBy, object: kt:cloudtrail-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What Is AWS CloudTrail?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html
    type: official-docs
    accessed: 2026-11-08
  - id: s2
    title: "How CloudTrail works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awscloudtrail/latest/userguide/how-cloudtrail-works.html
    type: official-docs
    accessed: 2026-11-08
  - id: s3
    title: "AWS CloudTrail pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/cloudtrail/pricing/
    type: official-docs
    accessed: 2026-11-08
  - id: s4
    title: "Quotas in AWS CloudTrail"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html
    type: official-docs
    accessed: 2026-11-08
  - id: s5
    title: "CloudTrail Lake availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-lake-service-availability-change.html
    type: official-docs
    accessed: 2026-11-08
  - id: s6
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-08
created: 2026-11-08
---

# AWS CloudTrail

AWS CloudTrail records the actions that a user, a role or an AWS service takes in an account as *events*, whether the action came from the AWS Management Console, the AWS CLI, an SDK or a direct API call. Each event captures who or what acted, which resources were touched, when, and further request details, which makes CloudTrail the foundation for operational and risk auditing, governance and compliance on AWS.[^s1]

## How it works

CloudTrail offers several ways to consume the same activity record:

- **Event history** is on by default for every account. It is an immutable, searchable and downloadable view of the past 90 days of management events in a Region, filterable on one attribute at a time, and viewing it carries no CloudTrail charge.[^s1]
- **Trails** deliver events continuously to an Amazon S3 bucket, optionally also to CloudWatch Logs and Amazon EventBridge, for one account or for a whole AWS Organizations organization.[^s1] Trails created in the console are multi-Region (the recommended practice), while single-Region trails can only be created with the CLI or API, where they are the default. An organization trail is created from the management account or a delegated administrator; member accounts can see it but cannot change or delete it.[^s2]
- **CloudTrail Lake** is a managed data lake that converts JSON events to columnar Apache ORC in immutable *event data stores* that you query with SQL, with retention of up to 3,653 days on the one-year extendable pricing option or 2,557 days on the seven-year option.[^s1]
- **CloudTrail Insights** analyzes management events for unusual API call rates and error rates. It is off by default and billed separately.[^s2]

Events fall into categories. Management (control-plane) events are what event history shows; data events and network activity events (which can be filtered by VPC endpoint ID) must be selected explicitly, and advanced event selectors can filter them by fields such as `eventName` to control cost.[^s2] CloudTrail publishes log files about every five minutes, with delivery typically averaging about five minutes after the API call, though that is not guaranteed.[^s2] Log file integrity validation lets you prove that delivered files have not been altered, and console-created trails encrypt log and digest files with an AWS KMS key by default; otherwise S3 server-side encryption applies.[^s2]

## Quotas, pricing and gotchas

Quotas (as of 2026-11) are not adjustable: 5 trails per Region, 10 event data stores per Region, 5 event selectors per trail, 500 conditions across all advanced event selectors, and 250 data resources across a trail's event selectors when you scope data events to specific resources. Log files are capped at 50 MB before compression.[^s4]

Pricing, as listed on the AWS pricing page as of 2026-11 (no Region is specified there):[^s3]

- Event history and the first copy of management events delivered to S3 by a trail cost nothing from CloudTrail; S3 storage is still billed.[^s1][^s3]
- Additional copies of management events: $2.00 per 100,000 events. Data events and network activity events: $0.10 per 100,000 events each.[^s3]
- Insights: $0.35 per 100,000 management events analyzed per insight type.[^s3]
- CloudTrail Lake ingestion on the one-year extendable option is $0.75/GB for CloudTrail events, with extended retention at $0.023/GB-month and queries at $0.005/GB scanned.[^s3]

Gotchas:

- A second trail that also captures management events is a paid copy, so overlapping trails across teams can quietly add cost.[^s3]
- If a trail's destination bucket becomes unreachable, CloudTrail retries delivery for 30 days and those attempts are charged; delete the trail to stop the charges.[^s2]
- By default, member accounts cannot read an organization trail's log files in the S3 bucket.[^s2]
- A customer-managed KMS key, once associated with an event data store, cannot be removed or changed.[^s2]

## CloudTrail Lake availability (since 2026-05)

CloudTrail Lake closed to new customers on May 31, 2026 and now receives only critical bug fixes and security updates; trails, Insights and aggregated events are unaffected.[^s5] Existing organization event data stores keep covering new member accounts and Regions, but account-level event data stores will not ingest accounts added to the organization later.[^s5] AWS recommends migrating to Amazon CloudWatch, which can import event data store history (events from before 2023 are not migrated) and ingest CloudTrail logs going forward, though features such as nested SQL do not carry over.[^s5] The overall service `status` remains current; only the Lake feature is closed to newcomers.[^s5]

## When to use it (and when not)

Every account should have at least one multi-Region trail (ideally an organization trail) writing to a locked-down S3 bucket, because event history alone is limited to 90 days and to management events.[^s1][^s2] Add data events selectively for sensitive buckets or functions, since they are priced per event.[^s3] For long-term SQL analysis, new customers should plan on CloudWatch or querying S3-delivered logs rather than CloudTrail Lake.[^s5]

## Connections

CloudTrail falls under the broader set of **AWS security services**. Trails deliver to **Amazon S3**, encrypted with **AWS KMS** by default in the console, and organization-wide coverage depends on **AWS Organizations**.[^s1][^s2] Logs can be streamed to **Amazon CloudWatch**, which is also AWS's recommended successor for CloudTrail Lake analytics, and Lake data can be queried with **Amazon Athena** at Athena rates.[^s3][^s5] **AWS Config** is a related governance service. Fixed limits are captured in **CloudTrail quotas**.[^s4] CloudTrail is listed among the management and governance services in scope for the **AWS Certified Cloud Practitioner** (CLF-C02) exam.[^s6]

## Sources
[^s1]: Amazon Web Services, "What Is AWS CloudTrail?". https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html (accessed 2026-11-08)
[^s2]: Amazon Web Services, "How CloudTrail works". https://docs.aws.amazon.com/awscloudtrail/latest/userguide/how-cloudtrail-works.html (accessed 2026-11-08)
[^s3]: Amazon Web Services, "AWS CloudTrail pricing". https://aws.amazon.com/cloudtrail/pricing/ (accessed 2026-11-08)
[^s4]: Amazon Web Services, "Quotas in AWS CloudTrail". https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html (accessed 2026-11-08)
[^s5]: Amazon Web Services, "CloudTrail Lake availability change". https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-lake-service-availability-change.html (accessed 2026-11-08)
[^s6]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-08)
