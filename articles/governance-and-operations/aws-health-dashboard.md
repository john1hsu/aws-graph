---
id: kt:aws-health-dashboard
type: Service
title: AWS Health Dashboard
aliases: [AWS Health, AWS Health API, AWS Personal Health Dashboard, Personal Health Dashboard, AWS Service Health Dashboard, Service Health Dashboard]
domain: governance-and-operations
summary: >-
  AWS Health's console view of service availability and of account-specific events such as
  operational issues, scheduled maintenance and lifecycle notices, backed by the AWS Health API,
  Amazon EventBridge delivery and an organization-wide view across AWS Organizations accounts.
verified: 2026-11-16
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:aws-trusted-advisor, kt:service-quotas]
relations:
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: dependsOn, object: kt:aws-support-plans}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is AWS Health? - AWS Health User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/health/latest/ug/what-is-aws-health.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Concepts for AWS Health - AWS Health User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/health/latest/ug/aws-health-concepts-and-terms.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Welcome - AWS Health API Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/health/latest/APIReference/Welcome.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Enabling organizational view - AWS Health User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/health/latest/ug/enable-organizational-view.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "Creating EventBridge rules for AWS Region coverage - AWS Health User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/health/latest/ug/choosing-a-region.html
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "AWS Health in AWS GovCloud (US) - AWS GovCloud (US) User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-health.html
    type: official-docs
    accessed: 2026-11-16
  - id: s7
    title: "AWS Health and AWS Organizations - AWS Organizations User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/services-that-can-integrate-health.html
    type: official-docs
    accessed: 2026-11-16
  - id: s8
    title: "Learning about AWS service health with the new AWS Health Dashboard (AWS Cloud Operations Blog, 2022-02-28)"
    publisher: Amazon Web Services (AWS Cloud Operations Blog)
    url: https://aws.amazon.com/blogs/mt/learning-about-aws-service-health-with-the-new-aws-health-dashboard/
    type: official-blog
    accessed: 2026-11-16
  - id: s9
    title: "AWS Health introduces the version catalog for software lifecycle management (What's New with AWS, 2026-10-02)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/10/aws-health-introduces-version-catalog-software-lifecycle-management/
    type: official-blog
    accessed: 2026-11-16
  - id: s10
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02) Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-16
  - id: s11
    title: "Content Domain 4: Billing, Pricing, and Support - AWS Certified Cloud Practitioner (CLF-C02) Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.html
    type: official-learning
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS Health Dashboard

**AWS Health** reports on the availability of AWS services and the health of your own accounts and resources. It tells you about outages that touch your workloads and about planned work, such as maintenance, that you need to prepare for.[^s1] The **AWS Health Dashboard** is its console. It needs no setup and costs nothing extra for any customer, and the same events can also be read through the **AWS Health API** or received through **Amazon EventBridge**.[^s1] In February 2022 AWS merged the old Service Health Dashboard and Personal Health Dashboard into this single dashboard. Old links redirect to it, and existing API and EventBridge integrations kept working without changes.[^s8]

## How it works

The unit of information is a **Health event**: a notice AWS Health publishes on behalf of another service, with a description, the affected resources and recommended actions.[^s2] Events come in two scopes:

- **Account-specific events** concern your account or accounts in your organization. You can get them through the dashboard, the API, EventBridge or AWS User Notifications.[^s2]
- **Public events** are service issues not tied to an account. They appear on the signed-in dashboard and on the public **Service health** page, which needs no sign-in.[^s2]

Each event has an **event type code** such as `AWS_EC2_SYSTEM_MAINTENANCE_EVENT`. It also has one of three categories: *issue* (unexpected degradation), *account notification* (administrative or security notices) or *scheduled change* (planned work such as retirements, reboots or end of support). Every scheduled change is account-specific.[^s2] Status is open, closed or upcoming. Events also carry an **actionability** label (`ACTION_REQUIRED`, `ACTION_MAY_BE_REQUIRED`, `INFORMATIONAL`) and **personas** (`OPERATIONS`, `SECURITY`, `BILLING`) for routing events to the right team. **Affected entities** list the specific resources involved, such as EC2 instance IDs.[^s2]

**Organizational view** combines events from every account in AWS Organizations. You enable it from the management account; this creates the `AWSServiceRoleForHealth_Organizations` service-linked role, and the API call must go to us-east-1.[^s4] Enabling it also turns on trusted access with Organizations. A member account can be registered as a delegated administrator, and the view keeps only events delivered after it was enabled.[^s7] Historical events can take up to 24 hours to appear after you enable it.[^s4]

In October 2026 AWS added a **version catalog** to the dashboard. It is a central list of supported software versions across AWS services and their support timelines, meant to help you plan upgrades before end of support. It is available in all commercial Regions.[^s9]

## Quotas, pricing and gotchas

- **Retention:** events stay viewable in the dashboard and API for up to 90 days (as of 2026-11).[^s2] Organizational view also keeps events for 90 days.[^s7] Export them through EventBridge if you need a longer audit trail.
- **API access needs a support plan.** As of 2026-11 the AWS Health API requires Business Support+, Enterprise Support or Unified Operations. Where those plans are not offered, or before you transition, Business, Enterprise On-Ramp or Enterprise Support also qualify. Other accounts get `SubscriptionRequiredException`.[^s3] The dashboard and EventBridge delivery are free for everyone.[^s1]
- **Endpoints:** commercial API calls go to `health.us-east-1.amazonaws.com`. Behind it are two Regional endpoints in an active-passive setup, so resilient clients should check which one is active.[^s3]
- **EventBridge Region coverage:** an event is delivered to its own Region and also to a backup Region. US West (Oregon) is the backup for every other commercial Region, and US East (N. Virginia) is the backup for Oregon. Global events, such as IAM events, arrive only in us-east-1. For high availability, create rules in both the primary and backup Regions and de-duplicate on `communicationId`.[^s5]
- **GovCloud:** the API uses one Regional endpoint in us-gov-west-1, and the two GovCloud Regions back each other up. Public events are not sent to EventBridge there; use the API or RSS feed instead.[^s6]

## When to use it (and when not)

Use the dashboard to check quickly whether a problem is yours or AWS's, and to track scheduled changes like instance retirements. For anything you must not miss, send EventBridge rules to chat, ticketing or automation, and use organizational view to watch many accounts.[^s2][^s5] AWS Health reports events that AWS publishes about its services and your resources,[^s1] not your application's own metrics, so keep your own alarms alongside it. The Cloud Practitioner exam pairs it with Trusted Advisor and the AWS Health API as tools for monitoring and managing environments.[^s11]

## Connections

AWS Health sits under **AWS management and governance services**. It `integratesWith` **Amazon EventBridge** for event-driven alerting[^s5] and with **AWS Organizations** for organizational view and trusted access.[^s7] Its API `dependsOn` **AWS Support plans**.[^s3] It sits alongside **AWS Trusted Advisor**, which reviews your configuration where Health reports events,[^s11] and near **Service Quotas**, another governance tool for avoiding availability surprises. The **AWS Certified Cloud Practitioner** exam lists the AWS Health Dashboard as an in-scope service.[^s10]

## Sources

[^s1]: Amazon Web Services, "What is AWS Health?". https://docs.aws.amazon.com/health/latest/ug/what-is-aws-health.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Concepts for AWS Health". https://docs.aws.amazon.com/health/latest/ug/aws-health-concepts-and-terms.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Welcome" (AWS Health API Reference). https://docs.aws.amazon.com/health/latest/APIReference/Welcome.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Enabling organizational view". https://docs.aws.amazon.com/health/latest/ug/enable-organizational-view.html (accessed 2026-11-16)
[^s5]: Amazon Web Services, "Creating EventBridge rules for AWS Region coverage". https://docs.aws.amazon.com/health/latest/ug/choosing-a-region.html (accessed 2026-11-16)
[^s6]: Amazon Web Services, "AWS Health in AWS GovCloud (US)". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-health.html (accessed 2026-11-16)
[^s7]: Amazon Web Services, "AWS Health and AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/services-that-can-integrate-health.html (accessed 2026-11-16)
[^s8]: Niko Nakai, "Learning about AWS service health with the new AWS Health Dashboard" (AWS Cloud Operations Blog, 2022-02-28). https://aws.amazon.com/blogs/mt/learning-about-aws-service-health-with-the-new-aws-health-dashboard/ (accessed 2026-11-16)
[^s9]: Amazon Web Services, "AWS Health introduces the version catalog for software lifecycle management" (What's New, 2026-10-02). https://aws.amazon.com/about-aws/whats-new/2026/10/aws-health-introduces-version-catalog-software-lifecycle-management/ (accessed 2026-11-16)
[^s10]: Amazon Web Services, "In-Scope AWS Services" (AWS Certified Cloud Practitioner CLF-C02 exam guide). https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html (accessed 2026-11-16)
[^s11]: Amazon Web Services, "Content Domain 4: Billing, Pricing, and Support" (AWS Certified Cloud Practitioner CLF-C02 exam guide). https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.html (accessed 2026-11-16)
