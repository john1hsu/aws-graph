---
id: kt:infrastructure-as-code
type: Concept
title: Infrastructure as code
aliases: [IaC, Infrastructure-as-code, Operations as code]
domain: devops-and-iac
summary: >-
  The practice of defining and provisioning cloud infrastructure through version-controlled
  configuration files or programs instead of manual console steps, so environments are repeatable,
  reviewable and deployable through the same pipelines as application code.
verified: 2026-11-12
status: concept
broader: [kt:devops]
related: [kt:aws-cloudformation, kt:aws-cdk, kt:aws-sam, kt:cloudformation-drift-detection]
relations:
  - {predicate: enables, object: kt:ci-cd}
  - {predicate: enables, object: kt:immutable-infrastructure}
  - {predicate: integratesWith, object: kt:aws-codepipeline}
sources:
  - id: s1
    title: "Infrastructure as code - Introduction to DevOps on AWS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/introduction-devops-aws/infrastructure-as-code.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Choosing an infrastructure as code tool for your organization - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/choose-iac-tool/introduction.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Choosing an IaC tool - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/choose-iac-tool/choose-tool.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "Design principles - AWS Well-Architected Framework (Operational excellence)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/oe-design-principles.html
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "REL08-BP05 Deploy changes with automation - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_tracking_change_management_automated_changemgmt.html
    type: official-docs
    accessed: 2026-11-12
  - id: s6
    title: "Detect unmanaged configuration changes to stacks and resources with drift detection - AWS CloudFormation"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-drift.html
    type: official-docs
    accessed: 2026-11-12
  - id: s7
    title: "Generate templates from existing resources with IaC generator - AWS CloudFormation"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/generate-IaC.html
    type: official-docs
    accessed: 2026-11-12
  - id: s8
    title: "FAQ - Getting started with Terraform (AWS Prescriptive Guidance)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/getting-started-terraform/faq.html
    type: official-docs
    accessed: 2026-11-12
created: 2026-11-12
---

# Infrastructure as code

Infrastructure as code (IaC) means provisioning and managing an application's infrastructure through a set of configuration files rather than by hand, and AWS treats it as a core part of DevOps practices such as version control, continuous integration and continuous deployment.[^s2] The AWS DevOps whitepaper frames it as applying the same rigor used for application code to infrastructure: configurations are defined declaratively and kept in source control next to the application, so every change has a history.[^s1] It contrasts this with the older mix of scripts, manual steps and runbooks that drifted out of date, which produced environments that were neither repeatable nor consistent.[^s1]

## How it works

An IaC tool reads a definition (a template or a program), works out which resources must be created, changed or deleted, and calls the cloud APIs to reach that state. AWS lists its own building blocks as AWS CloudFormation, AWS SAM, the AWS CDK, CDK for Kubernetes, CDK for Terraform and the AWS Cloud Control API.[^s1] The AWS CDK still deploys through CloudFormation: it generates CloudFormation templates from code in a general-purpose language, and CDK for Terraform plays the same role for Terraform configurations.[^s8] Because definitions are code, they can be reviewed, tested and scanned for security problems before anything is deployed, and the same definition can stand up identical environments quickly.[^s2]

The Well-Architected Framework generalizes the idea. Its operational excellence pillar asks you to define the whole workload, including applications, infrastructure, configuration and procedures, as code and to automate operations in response to events.[^s4] Its reliability pillar recommends keeping IaC in a Git-based repository and pushing every change through an automated pipeline with testing and automatic rollback, and it names manual changes and manual "emergency" shortcuts as anti-patterns.[^s5]

## Choosing a tool

AWS Prescriptive Guidance compares five tools for provisioning AWS resources: CloudFormation, AWS SAM, the AWS CDK, HashiCorp Terraform and Pulumi.[^s2] Its recommendations, as of 2026-11:[^s3]

- **CloudFormation or the AWS CDK** when infrastructure lives entirely on AWS, because they manage state for you and support new AWS features natively.[^s3]
- **AWS SAM** for serverless applications with few dependencies; it keeps CloudFormation's features while simplifying serverless testing and deployment.[^s3]
- **The AWS CDK** for organizations that publish reusable modules in common programming languages.[^s3]
- **Terraform** for multi-provider, multi-cloud or hybrid infrastructure, given its plugin ecosystem and community.[^s3]
- **Pulumi** for multi-cloud needs only where the organization can accept higher risk.[^s3]

The guide also suggests matching the tool to team skills, for example a JavaScript-fluent team choosing the CDK with TypeScript.[^s2] Another AWS guide notes that for workloads mainly on AWS, CloudFormation's native support is something Terraform cannot fully match.[^s8]

## Gotchas

**Drift.** IaC only stays authoritative if people stop changing resources by hand. CloudFormation drift detection compares deployed resources with the template's expected values, but it checks only properties set explicitly in the template or parameters, skips nested stacks unless you run it on them directly, and reports some resource types as `NOT_CHECKED`.[^s6] Equivalent values written differently (1024 MB vs. 1 GB) can show up as false drift.[^s6]

**Brownfield adoption.** Existing hand-built resources can be brought under management with the CloudFormation IaC generator, which scans a Region and writes a template, which you can then import as a stack or migrate into a CDK app.[^s7] As of 2026-11 its documented limits include 100,000 resources per scan, 500 resources per generated template and 1,000 generated templates per account, and scans expire after 30 days.[^s7] It covers only resource types that Cloud Control API supports in that Region.[^s7]

## Connections

IaC sits under DevOps practice and is the foundation for CI/CD: pipelines such as AWS CodePipeline deploy infrastructure changes the same way they deploy application changes.[^s5] Because a whole environment can be recreated from its definition, IaC also underpins immutable infrastructure, which the reliability pillar pairs with automated deployment as related best practices.[^s5] CloudFormation, the AWS CDK and AWS SAM are AWS's own implementations,[^s1] and CloudFormation drift detection is the main way to catch changes made outside the code.[^s6]

## Sources
[^s1]: Amazon Web Services, "Infrastructure as code - Introduction to DevOps on AWS". https://docs.aws.amazon.com/whitepapers/latest/introduction-devops-aws/infrastructure-as-code.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Choosing an infrastructure as code tool for your organization - AWS Prescriptive Guidance". https://docs.aws.amazon.com/prescriptive-guidance/latest/choose-iac-tool/introduction.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "Choosing an IaC tool - AWS Prescriptive Guidance". https://docs.aws.amazon.com/prescriptive-guidance/latest/choose-iac-tool/choose-tool.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, "Design principles - AWS Well-Architected Framework (Operational excellence)". https://docs.aws.amazon.com/wellarchitected/latest/framework/oe-design-principles.html (accessed 2026-11-12)
[^s5]: Amazon Web Services, "REL08-BP05 Deploy changes with automation - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_tracking_change_management_automated_changemgmt.html (accessed 2026-11-12)
[^s6]: Amazon Web Services, "Detect unmanaged configuration changes to stacks and resources with drift detection - AWS CloudFormation". https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-drift.html (accessed 2026-11-12)
[^s7]: Amazon Web Services, "Generate templates from existing resources with IaC generator - AWS CloudFormation". https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/generate-IaC.html (accessed 2026-11-12)
[^s8]: Amazon Web Services, "FAQ - Getting started with Terraform (AWS Prescriptive Guidance)". https://docs.aws.amazon.com/prescriptive-guidance/latest/getting-started-terraform/faq.html (accessed 2026-11-12)
