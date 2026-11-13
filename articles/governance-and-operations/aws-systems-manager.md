---
id: kt:aws-systems-manager
type: Service
title: AWS Systems Manager
aliases: [Systems Manager, SSM, Amazon Simple Systems Manager, Amazon EC2 Systems Manager, AWS SSM]
domain: governance-and-operations
summary: >-
  AWS's operations service for viewing, managing and operating nodes at scale across AWS, on-premises and
  other clouds, through a unified console plus tools such as Session Manager, Run Command, Patch Manager,
  Automation and Parameter Store, all built on the SSM Agent.
verified: 2026-11-13
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:amazon-ec2, kt:amazon-cloudwatch, kt:aws-config, kt:aws-secrets-manager]
relations:
  - {predicate: hasPart, object: kt:systems-manager-session-manager}
  - {predicate: hasPart, object: kt:systems-manager-parameter-store}
  - {predicate: dependsOn, object: kt:ssm-agent}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is AWS Systems Manager?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.md
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "AWS Systems Manager Session Manager"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/systems-manager/latest/userguide//session-manager.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "Managing parameter tiers - AWS Systems Manager"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/systems-manager/latest/userguide/parameter-store-advanced-parameters.html
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "AWS Systems Manager pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/systems-manager/pricing
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "Document history - AWS Systems Manager"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-release-history.html
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "AWS Systems Manager simplifies Azure VM management and hybrid node pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/aws-systems-manager-multicloud-vm/
    type: official-blog
    accessed: 2026-11-13
  - id: s7
    title: "AWS Systems Manager Incident Manager availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/incident-manager/latest/userguide/incident-manager-availability-change.html
    type: official-docs
    accessed: 2026-11-13
  - id: s8
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-13
  - id: s9
    title: "AWS Systems Manager change management tools"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-actions-and-change.html
    type: official-docs
    accessed: 2026-11-13
created: 2026-11-13
---

# AWS Systems Manager

AWS Systems Manager is the AWS service for centrally viewing, managing and operating fleets of servers — EC2 instances, on-premises machines, edge devices and VMs in other clouds — across accounts and Regions.[^s1] It was formerly called Amazon Simple Systems Manager and then Amazon EC2 Systems Manager, which is why "SSM" still appears in the agent name, the `ssm.<region>.amazonaws.com` endpoint, CLI commands, CloudFormation resource types and managed policy names.[^s1]

## How it works

A machine becomes a **managed node** when the SSM Agent is installed and can reach the Systems Manager service. When nodes fail to register, a diagnosis-and-remediation runbook (which can run on a schedule) finds causes such as networking misconfigurations and suggests fixes.[^s1] Since November 21, 2024 a **unified console** gives a cross-account, cross-Region dashboard of nodes, filterable by OS, Region, account and agent version, and lets you query node metadata in natural language with Amazon Q Developer.[^s1][^s5]

Underneath sit the individual tools. Node tools include Session Manager, Run Command, Patch Manager, State Manager, Inventory, Fleet Manager and Distributor. Change management tools include Automation runbooks, Documents, Maintenance Windows, Change Calendar and Quick Setup.[^s9] Parameter Store serves applications, and Explorer gives an operations summary.[^s1][^s4] Just-in-time node access, added in April 2025, grants time-bound access to nodes and can record Windows RDP sessions.[^s5]

**Session Manager** is the headline security feature. You can close inbound SSH and remote PowerShell ports and drop bastion hosts and SSH keys, because IAM controls who may start a session on which node. Traffic uses TLS 1.2 and can additionally be encrypted with a KMS key, and VPC endpoints over PrivateLink let nodes without public IPs connect.[^s2] Sessions are logged through CloudTrail (API calls) and to S3 or CloudWatch Logs (session output). Output logging doesn't cover port-forwarding or SSH sessions, because that traffic is end-to-end encrypted inside the tunnel.[^s2]

**Parameter Store** holds configuration and secrets (as `SecureString`) referenced by applications. Standard parameters allow 10,000 per account and Region with values up to 4 KB. Advanced parameters allow 100,000 with values up to 8 KB, and add parameter policies and cross-account sharing.[^s3] You can upgrade a standard parameter but never downgrade an advanced one; you must delete and recreate it.[^s3]

## Pricing and gotchas (as of 2026-11)

Prices below are from the AWS pricing page, which doesn't specify a Region; check the page for your Region.[^s4]

- **Free on EC2:** Session Manager, Run Command and Patch Manager, plus State Manager, Inventory, Maintenance Windows, Fleet Manager and standard parameters.[^s4]
- **Parameter Store advanced tier:** $0.05 per parameter per month. API calls cost $0.05 per 10,000 for advanced parameters, or for any parameter when higher throughput is enabled.[^s4]
- **Automation:** $0.002 per step, plus $0.00003 per second for `aws:executeScript` steps. Patch Manager's Generate Report runs on Automation and is billed.[^s4]
- **Hybrid and multicloud nodes:** the Advanced Instances Tier was removed at the end of June 2026, so there is no longer a per-node fee or 1,000-instance cap. From September 30, 2026, Session Manager costs $0.05 per session and Run Command $0.002 per invocation on non-EC2 nodes.[^s4][^s5]
- **Just-in-time node access:** charged per node-hour, from $0.0137 for the first 72,000 hours per month, falling at higher volumes, after a free trial.[^s4]

## What changed (2025-11 to 2026-11)

Several tools have closed to new customers. Change Manager and Incident Manager closed on November 7, 2025, and AWS will add no new Incident Manager features.[^s5][^s7] Application Manager closed on July 30, 2026, and CloudWatch dashboards hosted inside Systems Manager were removed in October 2026.[^s5] In July 2026 AWS added a Cloud Connector that deploys the SSM Agent to Azure VMs at scale. Those VMs then appear next to EC2 instances for Session Manager, Run Command, Patch Manager, Automation and Inventory.[^s6] The legacy Session Manager console page is due to be deprecated on December 31, 2026, as its features move into the unified console.[^s2] Don't design new processes around the closed tools.

## When to use it

Use Systems Manager when you need to patch, inventory, configure or reach a fleet of servers without opening ports or running a bastion. It is the AWS-native alternative to SSH jump boxes and to third-party remote-execution tools.[^s1][^s2] Parameter Store's free standard tier makes it a low-cost home for configuration values and simple `SecureString` secrets; weigh it against the advanced tier's charges and against a dedicated secrets service when you need more.[^s3][^s4] Systems Manager acts on the nodes themselves, so it complements rather than replaces resource-level configuration recording.[^s1]

## Connections

Systems Manager is a core **management and governance** service and appears in the Cloud Practitioner (CLF-C02) in-scope services list.[^s8] It **depends on** the SSM Agent on every managed node and is **secured by** IAM, which decides who can start sessions or run commands.[^s1][^s2] Its API activity is **monitored by** CloudTrail, with session output going to S3 or CloudWatch Logs.[^s2] **Session Manager** and **Parameter Store** are the tools most practitioners meet first.[^s2][^s3] It most often manages EC2 instances, and Parameter Store sits beside Secrets Manager for application configuration.[^s1][^s3]

## Sources
[^s1]: Amazon Web Services, "What is AWS Systems Manager?". https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.md (accessed 2026-11-13)
[^s2]: Amazon Web Services, "AWS Systems Manager Session Manager". https://docs.aws.amazon.com/systems-manager/latest/userguide//session-manager.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Managing parameter tiers - AWS Systems Manager". https://docs.aws.amazon.com/systems-manager/latest/userguide/parameter-store-advanced-parameters.html (accessed 2026-11-13)
[^s4]: Amazon Web Services, "AWS Systems Manager pricing". https://aws.amazon.com/systems-manager/pricing (accessed 2026-11-13)
[^s5]: Amazon Web Services, "Document history - AWS Systems Manager". https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-release-history.html (accessed 2026-11-13)
[^s6]: Amazon Web Services, "AWS Systems Manager simplifies Azure VM management and hybrid node pricing" (What's New, July 2026). https://aws.amazon.com/about-aws/whats-new/2026/06/aws-systems-manager-multicloud-vm/ (accessed 2026-11-13)
[^s7]: Amazon Web Services, "AWS Systems Manager Incident Manager availability change". https://docs.aws.amazon.com/incident-manager/latest/userguide/incident-manager-availability-change.html (accessed 2026-11-13)
[^s8]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-13)
[^s9]: Amazon Web Services, "AWS Systems Manager change management tools". https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-actions-and-change.html (accessed 2026-11-13)
