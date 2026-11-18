---
id: kt:tagging-strategy
type: Pattern
title: Tagging strategy
aliases: [AWS tagging strategy, AWS tagging best practices, Resource tagging strategy, Tagging schema]
domain: governance-and-operations
summary: >-
  The organization-wide practice of defining which key-value tags AWS resources must and may
  carry, how they are named, who owns them, and how they are applied, enforced and audited, so
  that tags can reliably drive cost allocation, operations automation, access control and
  compliance reporting.
verified: 2026-11-18
status: concept
broader: [kt:aws-management-and-governance-services]
related: [kt:cost-allocation-tags, kt:attribute-based-access-control, kt:multi-account-strategy, kt:cost-optimization-pillar]
relations:
  - {predicate: governedBy, object: kt:tag-policies}
  - {predicate: configuredVia, object: kt:aws-tag-editor}
  - {predicate: monitoredBy, object: kt:aws-config}
  - {predicate: securedBy, object: kt:service-control-policies}
  - {predicate: integratesWith, object: kt:aws-cloudformation}
  - {predicate: dependsOn, object: kt:aws-organizations}
sources:
  - id: s1
    title: "Best practices and strategies - Tagging AWS Resources and Tag Editor"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/tag-editor/latest/userguide/best-practices-and-strats.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "What are tags? - Best Practices for Tagging AWS Resources"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/what-are-tags.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Defining needs and use cases - Best Practices for Tagging AWS Resources"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/defining-needs-and-use-cases.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Building your tagging strategy - Best Practices for Tagging AWS Resources"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/building-your-tagging-strategy.html
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Implementing and enforcing tagging - Best Practices for Tagging AWS Resources"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/implementing-and-enforcing-tagging
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "Understanding enforcement - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_tag-policies-enforcement.html
    type: official-docs
    accessed: 2026-11-18
  - id: s7
    title: "Validate and enforce required tags in CloudFormation, Terraform and Pulumi with Tag Policies"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/validate-enforce-required-tags-cloudformation-terraform-pulumi/
    type: official-blog
    accessed: 2026-11-18
created: 2026-11-18
---

# Tagging strategy

A tag is a key-value pair of metadata attached to an AWS resource; the value is optional.[^s2] Tags are only as useful as they are consistent, so a **tagging strategy** is the agreed set of rules for which keys exist, what values they allow, who applies them and how compliance is checked. AWS's tagging whitepaper treats it as an iterative programme: start with the most pressing use case, then grow the schema as new needs appear.[^s4]

## Defining needs and ownership

AWS recommends starting from stakeholder needs rather than a list of keys. Finance and lines of business want to map spend to cost centers and business units; governance and compliance teams want data classification and audit scope; operations and development want lifecycle stage, backup, patching and observability hooks; security teams decide which controls apply where.[^s3] Stakeholders then decide which keys are **mandatory** (finance might require `CostCenter` and `BusinessUnit`) and which are **optional** (developers might add `OptIn`/`OptOut` keys for automation).[^s3] Ownership should be formalised, for example in a RACI matrix, so it is clear who defines, implements and improves the schema; in a small organization one team may hold every role.[^s4] The agreed approach should also record how tags are enforced, how effectiveness is measured and how often the strategy is reviewed.[^s3]

## Naming conventions and hard limits

Tag keys and values are case sensitive, so `costCenter` and `costcenter` are different keys, and spelling variants such as `costcentre` split your data too.[^s2] The Tag Editor guide recommends the same style AWS uses for its own tags: all lowercase, hyphen-separated words, and an organization prefix followed by a colon, such as `anycompany:cost-center`.[^s1] Keys starting with `aws` are reserved for AWS-generated tags (for example `aws:cloudformation:stack-name`), which you cannot use, edit or delete.[^s1][^s2]

Limits as of 2026-11, from the Tag Editor user guide: up to 50 user-created tags per resource (AWS-generated `aws:` tags don't count); keys of 1–128 and values of 0–256 UTF-8 characters; one value per key per resource; and allowed characters that vary by service, generally letters, numbers, spaces and `_ . : / = + - @`.[^s1] Tags are not encrypted, so never put personal or confidential data in them.[^s1][^s2] AWS also advises erring towards more tags rather than fewer, and remembering that renaming a tag used for access control means updating every policy that references it.[^s1]

## Applying and enforcing tags

Enforcement comes in proactive and reactive layers, and no single tool covers everything:[^s1][^s5]

- **Infrastructure as code.** Set tags in CloudFormation templates (and launch templates for Auto Scaling groups), and validate them before deployment with CloudFormation Guard rules or Hooks. Tags added outside the template by automation cause drift, so manage them in code.[^s5]
- **Service control policies** can deny a create call, such as `ec2:RunInstances`, when a required tag key is absent from the request, but they only check presence, not values, and don't touch existing resources.[^s5]
- **Tag policies** in AWS Organizations standardise keys, capitalisation and allowed values. With enforcement on, they block noncompliant values for supported resource types, but basic rules do not stop a resource being created with no tags at all.[^s6] Since November 2025 a *Reporting for Required Tags* capability lets tag policies list mandatory keys and validate CloudFormation (through the `AWS::TagPolicies::TaggingComplianceValidator` hook), Terraform and Pulumi deployments against them.[^s7]
- **Detective controls.** The AWS Config managed rule `REQUIRED_TAGS` flags resources missing mandatory keys, and Tag Editor or the Resource Groups Tagging API can find and bulk-fix them.[^s5]

AWS's advice for tag policies is to begin in reporting mode, clean up existing inconsistencies, test enforcement in one account, then widen it by OU, because strict rules can block legitimate provisioning, especially where services such as EMR or Auto Scaling propagate tags to the instances they create.[^s6]

## When it matters

Any organization past a handful of accounts needs a tagging strategy, because cost allocation, ABAC, automated start/stop or backup selection and compliance scoping all fail silently on a missing or misspelled key.[^s1][^s3]

## Connections

The strategy sits under **AWS management and governance services** and depends on **AWS Organizations**, whose **tag policies** govern key and value standards.[^s6] **Service control policies** secure it by denying untagged creation, **AWS Config** monitors compliance, **Tag Editor** applies and repairs tags, and **AWS CloudFormation** applies tags as code.[^s1][^s5][^s7] It is the prerequisite for **cost allocation tags** and for **attribute-based access control**, both of which depend on exact, case-sensitive matches, and it complements a **multi-account strategy** and the cost-attribution practices of the **Cost Optimization Pillar**.[^s1][^s6]

## Sources
[^s1]: Amazon Web Services, "Best practices and strategies", Tagging AWS Resources and Tag Editor User Guide. https://docs.aws.amazon.com/tag-editor/latest/userguide/best-practices-and-strats.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "What are tags?", Best Practices for Tagging AWS Resources whitepaper. https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/what-are-tags.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Defining needs and use cases", Best Practices for Tagging AWS Resources whitepaper. https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/defining-needs-and-use-cases.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Building your tagging strategy", Best Practices for Tagging AWS Resources whitepaper. https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/building-your-tagging-strategy.html (accessed 2026-11-18)
[^s5]: Amazon Web Services, "Implementing and enforcing tagging", Best Practices for Tagging AWS Resources whitepaper. https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/implementing-and-enforcing-tagging (accessed 2026-11-18)
[^s6]: Amazon Web Services, "Understanding enforcement", AWS Organizations User Guide. https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_tag-policies-enforcement.html (accessed 2026-11-18)
[^s7]: Amazon Web Services, "Validate and enforce required tags in CloudFormation, Terraform and Pulumi with Tag Policies" (What's New, 2025-11-20). https://aws.amazon.com/about-aws/whats-new/2025/11/validate-enforce-required-tags-cloudformation-terraform-pulumi/ (accessed 2026-11-18)
