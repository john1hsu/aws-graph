---
id: kt:service-quotas
type: Service
title: Service Quotas
aliases: [AWS Service Quotas, AWS service limits console, Service Quotas console]
domain: governance-and-operations
summary: >-
  The free AWS service that shows default and applied quota values for AWS services in one place,
  submits and tracks quota increase requests, pre-seeds increases for new accounts through
  organization request templates, and (with automatic management) watches usage and can raise
  quotas on your behalf.
verified: 2026-11-14
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:iam-quotas, kt:organizations-quotas, kt:reliability-pillar, kt:aws-trusted-advisor]
relations:
  - {predicate: integratesWith, object: kt:amazon-cloudwatch}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is Service Quotas? - Service Quotas User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/servicequotas/latest/userguide/intro.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "AWS service quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Service quotas for Service Quotas - Service Quotas User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/servicequotas/latest/userguide/reference_limits.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Using Service Quotas request templates - Service Quotas User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/servicequotas/latest/userguide/organization-templates.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "Visualizing your service quotas and setting alarms - Amazon CloudWatch User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Quotas-Visualize-Alarms.html
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "REL01-BP01 Aware of service quotas and constraints - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_manage_service_limits_aware_quotas_and_constraints.html
    type: official-docs
    accessed: 2026-11-14
  - id: s7
    title: "Automatic quota management is now generally available for AWS Service Quotas (What's New with AWS, 2025-10-07)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/10/automatic-quota-management-service-quotas
    type: official-blog
    accessed: 2026-11-14
  - id: s8
    title: "AWS Service Quotas adds support for automatic quota management (What's New with AWS, 2025-11-25)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/automatic-quota-management-service-quota-management
    type: official-blog
    accessed: 2026-11-14
  - id: s9
    title: "Introducing Service Quotas: View and manage your quotas for AWS services from one central location (What's New with AWS, 2019-06-24)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2019/06/introducing-service-quotas-view-and-manage-quotas-for-aws-services-from-one-location
    type: official-blog
    accessed: 2026-11-14
  - id: s10
    title: "Service Quotas in AWS GovCloud (US) - AWS GovCloud (US) User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-servicequotas.html
    type: official-docs
    accessed: 2026-11-14
  - id: s11
    title: "Service Quota Observability Across Regions and Accounts (AWS Cloud Operations Blog, 2023-07-18)"
    publisher: Amazon Web Services (AWS Cloud Operations Blog)
    url: https://aws.amazon.com/blogs/mt/service-quota-observability-across-regions-and-accounts
    type: official-blog
    accessed: 2026-11-14
created: 2026-11-14
---

# Service Quotas

Every AWS account starts with default quotas (formerly called limits) for each AWS service: the maximum number of resources, actions or items the account may use.[^s2] **Service Quotas** is the AWS service that lets you view those quotas for many services in one place and ask for higher values. Each service still defines its own quotas and defaults; Service Quotas is the central window and request channel, and AWS may approve, deny or partially approve any request.[^s1] It launched in June 2019 at no additional charge.[^s9]

## How it works

The console, AWS CLI, SDKs and Tools for PowerShell expose the same model.[^s1] Its vocabulary is worth knowing:

- **Default value** is what AWS sets; an approved increase becomes the **applied value**, which overrides the default.[^s1]
- **Adjustable** marks whether a quota can be raised at all; fixed quotas cannot.[^s1]
- **Regional** quotas apply separately in each Region (VPCs per Region), while **global** quotas apply account-wide (IAM roles per account). Unless a quota says otherwise, it is Regional.[^s1][^s2]
- **Usage** and **utilization** (usage as a percentage of the quota) are graphed once an account has been active for a while.[^s1]
- **Resource-level quotas** apply to individual resources rather than the whole account, such as instances per OpenSearch Service domain; the CLI needs v2.13.20 or later for these.[^s1]

AWS recommends requesting increases through the Service Quotas console or the `request-service-quota-increase` CLI call, and opening a support case only for services not yet in Service Quotas. Approved increases can take up to a couple of days.[^s2] Global quota increases must be requested from US East (N. Virginia) in the commercial partition, AWS GovCloud (US-West) for GovCloud, and China (Beijing) for the China partition.[^s1]

## Monitoring and automation

Services that publish usage metrics to the CloudWatch `AWS/Usage` namespace can be compared with their quota in CloudWatch. The metric math function `SERVICE_QUOTA(m1)` returns the quota for a usage metric, so `m1/SERVICE_QUOTA(m1)*100` gives percent utilization, and an alarm on that expression (the docs use 80 percent) can notify an SNS topic. This only works for services that integrate their usage metrics with Service Quotas.[^s5]

**Automatic management**, generally available since 2025-10-07, monitors usage and sends notifications before you run out. Notifications go by email, SMS or Slack, through AWS Health, or as CloudTrail events you can automate on.[^s7] On 2025-11-25 AWS added a mode that automatically adjusts quota values based on usage. AWS describes both as free and available in all commercial Regions.[^s7][^s8]

**Quota request templates** apply increases to new accounts. You add up to 10 quotas, each for a chosen Region, and associate the template with your organization. Every new account created in that organization then gets those increase requests automatically. Templates need AWS Organizations with all features enabled, do not change existing accounts, and work only in commercial Regions (not China or opt-in Regions).[^s4] They are also unavailable in AWS GovCloud (US), where Service Quotas otherwise behaves as elsewhere. Default and applied values, recent requests and quota tags may leave the GovCloud Regions during normal operation.[^s10]

## Quotas, pricing and gotchas

Service Quotas has its own fixed limits (as of 2026-11, not adjustable): 20 active increase requests per account (global), 2 per opt-in Region, and 1 active request per quota or resource. `RequestServiceQuotaIncrease` is limited to 3 requests per second, and overall API traffic to 50 requests per second per account and 100 per organization.[^s3]

- Defaults differ by Region and service. AWS's blog example is SNS publish throughput, whose default was 30,000 messages per second in us-east-1 but 1,500 in us-east-2. An increase in one Region does not carry to another.[^s11]
- A new or lightly used account may have values below the published default.[^s2]
- Some limits live outside Service Quotas, such as API Gateway usage plan throttles and per-resource settings like EBS volume allocations.[^s6]
- Service *constraints* differ from quotas. A gp2 volume's 1 GB to 16 TB size range cannot be raised, and the fix is a different resource type or a different design.[^s6]

## When to use it (and when not)

The Well-Architected reliability pillar rates unawareness of quotas as a high risk. It lists anti-patterns such as assuming quotas grow automatically or match across Regions. Its guidance is to inventory the services you use and map them to quotas with Service Quotas and Trusted Advisor's quota check. It also says to alarm before limits are reached and to raise quotas ahead of launches or failover.[^s6] Use Service Quotas for adjustable quotas. For fixed quotas, change the architecture rather than waiting for an increase.[^s6] To compare quotas across accounts and Regions, AWS has shown scripts that diff `list-service-quotas` output per profile and Region.[^s11]

## Connections

Service Quotas belongs to AWS management and governance services. It is where adjustable limits in nodes like **IAM quotas** and **AWS Organizations quotas** are raised.[^s1][^s2] It `integratesWith` **Amazon CloudWatch** through usage metrics and the `SERVICE_QUOTA` function.[^s5] It also `integratesWith` **AWS Organizations** through request templates.[^s4] It is `scopedTo` **AWS Regions** because most quotas, and most increases, apply one Region at a time.[^s1][^s11] It puts the **Reliability Pillar**'s REL01 quota practices into action, alongside the service quota check in **AWS Trusted Advisor**.[^s6]

## Sources

[^s1]: Amazon Web Services, "What is Service Quotas?". https://docs.aws.amazon.com/servicequotas/latest/userguide/intro.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "AWS service quotas" (AWS General Reference). https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, "Service quotas for Service Quotas". https://docs.aws.amazon.com/servicequotas/latest/userguide/reference_limits.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, "Using Service Quotas request templates". https://docs.aws.amazon.com/servicequotas/latest/userguide/organization-templates.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, "Visualizing your service quotas and setting alarms" (Amazon CloudWatch). https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Quotas-Visualize-Alarms.html (accessed 2026-11-14)
[^s6]: Amazon Web Services, "REL01-BP01 Aware of service quotas and constraints" (AWS Well-Architected Framework). https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_manage_service_limits_aware_quotas_and_constraints.html (accessed 2026-11-14)
[^s7]: Amazon Web Services, "Automatic quota management is now generally available for AWS Service Quotas" (What's New, 2025-10-07). https://aws.amazon.com/about-aws/whats-new/2025/10/automatic-quota-management-service-quotas (accessed 2026-11-14)
[^s8]: Amazon Web Services, "AWS Service Quotas adds support for automatic quota management" (What's New, 2025-11-25). https://aws.amazon.com/about-aws/whats-new/2025/11/automatic-quota-management-service-quota-management (accessed 2026-11-14)
[^s9]: Amazon Web Services, "Introducing Service Quotas" (What's New, 2019-06-24). https://aws.amazon.com/about-aws/whats-new/2019/06/introducing-service-quotas-view-and-manage-quotas-for-aws-services-from-one-location (accessed 2026-11-14)
[^s10]: Amazon Web Services, "Service Quotas in AWS GovCloud (US)". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-servicequotas.html (accessed 2026-11-14)
[^s11]: Yassin Abouel Seoud, Brad Hein and Vincent Nicotra, "Service Quota Observability Across Regions and Accounts" (AWS Cloud Operations Blog, 2023-07-18). https://aws.amazon.com/blogs/mt/service-quota-observability-across-regions-and-accounts (accessed 2026-11-14)
