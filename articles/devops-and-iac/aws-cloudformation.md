---
id: kt:aws-cloudformation
type: Service
title: AWS CloudFormation
aliases: [CloudFormation, CFN]
domain: devops-and-iac
summary: >-
  AWS's native infrastructure-as-code service: you declare resources in a JSON or YAML template and
  CloudFormation creates, updates and deletes them together as a stack, with change-set previews,
  drift handling and multi-account, multi-Region deployment through StackSets.
verified: 2026-11-09
status: current
broader: [kt:infrastructure-as-code]
related: [kt:aws-cdk, kt:cloudformation-drift-detection, kt:aws-control-tower]
relations:
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: governedBy, object: kt:cloudformation-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "Welcome - AWS CloudFormation API Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/AWSCloudFormation/latest/APIReference/Welcome.html
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "How CloudFormation works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-overview.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Understand CloudFormation quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "AWS CloudFormation pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/cloudformation/pricing/
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "AWS CloudFormation accelerates dev-test cycle with early validation and simplified troubleshooting"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/cloudformation-dev-test-cycle-validation-troubleshooting
    type: official-blog
    accessed: 2026-11-09
  - id: s6
    title: "Safely handle configuration drift with AWS CloudFormation drift-aware change sets"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/configuration-drift-enhanced-cloudformation-sets
    type: official-blog
    accessed: 2026-11-09
  - id: s7
    title: "AWS CloudFormation 2025 year in review"
    publisher: Amazon Web Services (AWS DevOps & Developer Productivity Blog)
    url: https://aws.amazon.com/blogs/devops/aws-cloudformation-2025-year-in-review
    type: official-blog
    accessed: 2026-11-09
  - id: s8
    title: "Reference stack outputs across accounts and Regions with AWS CloudFormation and CDK"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/05/aws-cloudformation-cdk-stack/
    type: official-blog
    accessed: 2026-11-09
  - id: s9
    title: "Accelerate your infrastructure deployments by up to 4x with AWS CloudFormation express mode"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/accelerate-your-infrastructure-deployments-by-up-to-4x-with-aws-cloudformation-express-mode/
    type: official-blog
    accessed: 2026-11-09
  - id: s10
    title: "Configure and perform CDK stack synthesis"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cdk/v2/guide/configure-synth.html
    type: official-docs
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS CloudFormation

AWS CloudFormation is AWS's own service for creating and managing infrastructure in a predictable, repeatable way. Instead of clicking resources together, you declare them and their dependencies in a template, and CloudFormation provisions the whole group as a single unit called a **stack**, creating and deleting its resources together and working out the order from their dependencies.[^s1] It is generally available, and its API also covers change sets, StackSets, an IaC generator that scans existing resources into templates, stack refactoring, and a registry of extensions such as Hooks, resource types and modules.[^s1]

## How it works

A **template** is a declarative YAML or JSON document listing resources and their properties; resources can reference one another, and **parameters** let the same template be reused with different inputs.[^s2] When you create a stack, CloudFormation validates the template, then makes the underlying AWS API calls on your behalf, provisioning independent resources in parallel and dependent ones afterwards. Because it calls those APIs, the identity running the stack needs IAM permission for every resource type in the template.[^s2] If creation fails, CloudFormation by default rolls back by deleting what it created; that rollback can be turned off for debugging.[^s2]

To change a running stack you submit a new template or parameter values and, ideally, generate a **change set** first: it lists each proposed action and shows whether a resource will be modified in place or *replaced*, which matters for stateful resources such as databases.[^s2] A change set does not guarantee success; it doesn't check quotas, unsupported updates or missing permissions.[^s2] Since November 2025, **early validation** runs when a change set is created and catches invalid property syntax, name conflicts with existing resources and non-empty S3 buckets on delete, and every stack operation carries an operation ID you can filter events by.[^s5]

**Drift** is what happens when someone changes a stack's resources outside CloudFormation. Drift detection reports it, and **drift-aware change sets** (November 2025) do a three-way comparison of the new template, the last-deployed template and the live resources, so you can preview and then revert out-of-band changes, including recreating resources that were deleted by hand.[^s6][^s1] **StackSets** deploy one template to many accounts and Regions from a central administrator account, and **nested stacks** compose large systems from smaller templates.[^s1][^s3]

## Quotas, pricing and gotchas

As of 2026-11, a template can hold 500 resources, 200 parameters, 200 mappings and 200 outputs, none adjustable; a template body sent inline is capped at 51,200 bytes, and one stored in Amazon S3 at 1 MB.[^s3] An account can have 2,000 stacks and 1,000 StackSets per administrator account, each adjustable through Service Quotas; a single nested-stack operation can touch at most 2,500 resources.[^s3] Hitting the 500-resource ceiling is the classic signal to split a template into nested or separate stacks.[^s3]

CloudFormation adds no charge for resources in the `AWS::*` and `Alexa::*` namespaces or for AWS managed hooks; you pay only for the resources it creates.[^s4] Third-party resource providers and custom hooks are billed per handler operation beyond a monthly free tier of 1,000 operations, plus duration over 30 seconds per operation; the pricing page's worked examples (as of 2026-11, not Region-specific) use $0.0009 per operation and $0.00008 per second.[^s4]

## What changed (2026)

In May 2026 AWS added `Fn::GetStackOutput`, an intrinsic function that reads another stack's outputs across accounts and Regions by assuming an IAM role you name, replacing custom resources or SSM parameters for that job.[^s8] In June 2026 **express mode** arrived at no extra cost in all commercial Regions: deployments complete once configuration is applied, skipping extended stabilization checks, with rollback disabled by default. The trade-off is that resources may not yet serve traffic when the stack reports complete.[^s9]

## When to use it (and when not)

CloudFormation is the natural choice when you want AWS-native IaC with no extra state backend, managed rollback and governance via Hooks; the 2025 Language Server also brings auto-completion and validation into VS Code and Kiro.[^s7] Teams that prefer general-purpose languages usually write AWS CDK instead, but CDK synthesizes CloudFormation templates and deploys through it, so CloudFormation's quotas and behaviors still apply.[^s10] Use express mode for fast development loops, and keep standard mode where downstream steps need fully stabilized resources.[^s9]

## Connections

CloudFormation is the reference implementation of **infrastructure as code** on AWS. **AWS CDK** builds on it by synthesizing CloudFormation templates and deploying them through the service.[^s10] It is secured by **AWS IAM**, since every resource call needs permissions, stores large templates in **Amazon S3**, and several of its quotas (modules, private resources, hooks) are counted per account per **Region**, as listed in **CloudFormation quotas**.[^s2][^s3] **Drift detection** is one of its core features, and its managed Hooks can apply **AWS Control Tower** catalog controls in warn or enforce mode.[^s6][^s7]

## Sources
[^s1]: Amazon Web Services, "Welcome - AWS CloudFormation API Reference". https://docs.aws.amazon.com/fr_fr/AWSCloudFormation/latest/APIReference/Welcome.html (accessed 2026-11-09)
[^s2]: Amazon Web Services, "How CloudFormation works". https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-overview.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Understand CloudFormation quotas". https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "AWS CloudFormation pricing". https://aws.amazon.com/cloudformation/pricing/ (accessed 2026-11-09)
[^s5]: Amazon Web Services, "AWS CloudFormation accelerates dev-test cycle with early validation and simplified troubleshooting", What's New with AWS. https://aws.amazon.com/about-aws/whats-new/2025/11/cloudformation-dev-test-cycle-validation-troubleshooting (accessed 2026-11-09)
[^s6]: Amazon Web Services, "Safely handle configuration drift with AWS CloudFormation drift-aware change sets", What's New with AWS. https://aws.amazon.com/about-aws/whats-new/2025/11/configuration-drift-enhanced-cloudformation-sets (accessed 2026-11-09)
[^s7]: Amazon Web Services, "AWS CloudFormation 2025 year in review", AWS DevOps & Developer Productivity Blog. https://aws.amazon.com/blogs/devops/aws-cloudformation-2025-year-in-review (accessed 2026-11-09)
[^s8]: Amazon Web Services, "Reference stack outputs across accounts and Regions with AWS CloudFormation and CDK", What's New with AWS. https://aws.amazon.com/about-aws/whats-new/2026/05/aws-cloudformation-cdk-stack/ (accessed 2026-11-09)
[^s9]: Amazon Web Services, "Accelerate your infrastructure deployments by up to 4x with AWS CloudFormation express mode", AWS News Blog. https://aws.amazon.com/blogs/aws/accelerate-your-infrastructure-deployments-by-up-to-4x-with-aws-cloudformation-express-mode/ (accessed 2026-11-09)
[^s10]: Amazon Web Services, "Configure and perform CDK stack synthesis". https://docs.aws.amazon.com/cdk/v2/guide/configure-synth.html (accessed 2026-11-09)
