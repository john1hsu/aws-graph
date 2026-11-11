---
id: kt:aws-management-and-governance-services
type: Concept
title: AWS management and governance services
aliases: [AWS management and governance, AWS management tools, AWS Management & Governance, management and governance on AWS, AWS M&G services]
domain: governance-and-operations
summary: >-
  The AWS service category for running and controlling cloud environments: account structure and
  guardrails (Organizations, Control Tower), configuration and audit (Config, CloudTrail), monitoring
  (CloudWatch, Managed Grafana and Prometheus), operations (Systems Manager), provisioning (CloudFormation,
  Service Catalog) and optimization advice (Trusted Advisor, Compute Optimizer).
verified: 2026-11-11
status: concept
broader: [kt:amazon-web-services]
related: [kt:multi-account-strategy, kt:aws-security-services, kt:aws-well-architected-framework, kt:aws-cloud-adoption-framework]
relations:
  - {predicate: hasPart, object: kt:aws-organizations}
  - {predicate: hasPart, object: kt:aws-control-tower}
  - {predicate: hasPart, object: kt:aws-config}
  - {predicate: hasPart, object: kt:amazon-cloudwatch}
  - {predicate: hasPart, object: kt:aws-systems-manager}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "Management and governance - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/management-governance.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Management and Governance Cloud Environment Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/management-and-governance-guide/management-and-governance-cloud-environment-guide.md
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Journey to cloud-ready environments - Management and Governance Cloud Environment Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/management-and-governance-guide/journey-to-cloud-ready-environments.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Control services - Management and Governance Cloud Environment Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/management-and-governance-guide/aws-controls-and-guardrails-services.html
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "AWS OpsWorks Stacks End of Life FAQs"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/opsworks/latest/userguide/stacks-eol-faqs.html
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "AWS Proton Service Deprecation and Migration Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/proton/latest/userguide/proton-end-of-support.html
    type: official-docs
    accessed: 2026-11-11
  - id: s7
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# AWS management and governance services

"Management and governance" is the AWS service category for operating cloud environments while keeping control over cost, compliance and security. The AWS overview whitepaper frames its promise as not having to choose between moving fast and staying in control.[^s1] Like the other category nodes in this graph, it is not something you provision; it is the map that ties together the services you use to set up accounts, apply guardrails, watch workloads and act on what you find.

## What is in the category

As of 2026-11, the overview whitepaper lists 22 services under management and governance.[^s1] They fall into a few practical groups:

- **Accounts and guardrails** – AWS Organizations for central management of many accounts, and AWS Control Tower to automate a secure multi-account landing zone. Service Catalog curates the IT products teams are allowed to launch, and License Manager tracks vendor licences across AWS and on-premises servers.[^s1]
- **Configuration, audit and provisioning** – AWS Config keeps a resource inventory, configuration history and change notifications. CloudTrail records API calls, and CloudFormation provisions related resources as a unit.[^s1]
- **Monitoring and observability** – Amazon CloudWatch, Amazon Managed Grafana, Amazon Managed Service for Prometheus, AWS Health and AWS User Notifications.[^s1]
- **Operations** – AWS Systems Manager for a unified view of operational data and task automation, plus the Console Mobile Application and Amazon Q Developer in chat applications (formerly AWS Chatbot) for incident response.[^s1]
- **Optimization and review** – AWS Trusted Advisor, AWS Compute Optimizer, AWS Auto Scaling, Launch Wizard and the AWS Well-Architected Tool.[^s1]

## How the pieces fit together

AWS's prescriptive view comes from the Well-Architected Management and Governance Cloud Environment Guide (published 2021-11-22). It builds on the people-and-process focus of the Cloud Adoption Framework with technology guidance, and it organizes the topic as eight interoperating management and governance functions. For each function the guide gives implementation priorities and recommended AWS services.[^s2] The guide describes a three-phase journey:

1. **Setup** – Configure identity, logging, monitoring, network connectivity and security integrations. Provision the landing zone with AWS Control Tower, which brings embedded controls, basic network isolation and a base set of identities.[^s3]
2. **Build and migrate** – Widen network isolation and add environment- and workload-level controls. This phase also tunes change and incident management and extends observability to application insights.[^s3]
3. **Operate** – Make the functions work together more efficiently, add template and software distribution, and use financial insights across accounts.[^s3]

Controls are the clearest example of services interlocking. In the guide's control services list, Organizations service control policies cap what account administrators can delegate. Control Tower applies preventive and detective controls as accounts are provisioned, and Config rules and conformance packs check configuration compliance at scale. Audit Manager collects audit evidence continuously, and Security Hub CSPM gathers findings from GuardDuty, Inspector, Macie, Config and others into one prioritized view.[^s4]

## Gotchas

The category's membership changes, and not every list is current. The overview whitepaper still lists OpsWorks and AWS Proton.[^s1] OpsWorks Stacks was scheduled to reach end of life on 2024-05-26, and AWS directs those workloads to Systems Manager.[^s5] AWS Proton stopped taking new customers after 2025-10-07 and reached end of support on 2026-10-07. Its console and resources are no longer reachable, though infrastructure it deployed stays in place. AWS suggests CloudFormation Git sync, CodePipeline with CodeBuild, Harmonix on AWS, or GitHub Actions as replacements.[^s6] Before you rely on a category list, check each service's own lifecycle page. Category lists also overlap: CloudTrail and Config appear in both governance and security material, and Security Hub CSPM is listed among the guide's control services.[^s1][^s4]

## Connections

This node is the governance branch of `kt:amazon-web-services`. Its `hasPart` edges point to the services that carry most of the load: `kt:aws-organizations`, `kt:aws-control-tower`, `kt:aws-config`, `kt:amazon-cloudwatch` and `kt:aws-systems-manager`.[^s1] It is related to `kt:multi-account-strategy` because the guide's setup phase starts with a Control Tower landing zone.[^s3] It overlaps with `kt:aws-security-services` through shared detective controls.[^s4] It also links to `kt:aws-well-architected-framework` and `kt:aws-cloud-adoption-framework`, because the M&G guide sits in the Well-Architected library and builds on the CAF.[^s2] The `coveredBy` edge reflects the SAA-C03 exam guide's in-scope list, which has a Management and Governance category. That category includes CloudTrail, CloudWatch, Config, Control Tower, Organizations, Service Catalog, Systems Manager and the Well-Architected Tool, and still lists AWS Proton.[^s7]

## Sources
[^s1]: Amazon Web Services, "Management and governance - Overview of Amazon Web Services". https://docs.aws.amazon.com/whitepapers/latest/aws-overview/management-governance.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Management and Governance Cloud Environment Guide". https://docs.aws.amazon.com/wellarchitected/latest/management-and-governance-guide/management-and-governance-cloud-environment-guide.md (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Journey to cloud-ready environments - Management and Governance Cloud Environment Guide". https://docs.aws.amazon.com/wellarchitected/latest/management-and-governance-guide/journey-to-cloud-ready-environments.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Control services - Management and Governance Cloud Environment Guide". https://docs.aws.amazon.com/wellarchitected/latest/management-and-governance-guide/aws-controls-and-guardrails-services.html (accessed 2026-11-11)
[^s5]: Amazon Web Services, "AWS OpsWorks Stacks End of Life FAQs". https://docs.aws.amazon.com/opsworks/latest/userguide/stacks-eol-faqs.html (accessed 2026-11-11)
[^s6]: Amazon Web Services, "AWS Proton Service Deprecation and Migration Guide". https://docs.aws.amazon.com/proton/latest/userguide/proton-end-of-support.html (accessed 2026-11-11)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-11)
