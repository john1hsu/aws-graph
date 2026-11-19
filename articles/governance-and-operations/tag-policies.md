---
id: kt:tag-policies
type: Feature
title: Tag policies
aliases: [AWS Organizations tag policies, Tag policy, AWS tag policies]
domain: governance-and-operations
summary: >-
  An AWS Organizations policy type that standardizes tag keys, their capitalization and their
  allowed values across the accounts in an organization, with compliance reporting, optional
  enforcement on supported resource types, and required-tag checks for infrastructure-as-code
  deployments.
verified: 2026-11-19
status: current
broader: [kt:aws-organizations]
related: [kt:aws-tag-editor, kt:cost-allocation-tags, kt:aws-config]
relations:
  - {predicate: implements, object: kt:tagging-strategy}
  - {predicate: contrastsWith, object: kt:service-control-policies}
  - {predicate: governedBy, object: kt:organizations-quotas}
  - {predicate: integratesWith, object: kt:aws-cloudformation}
  - {predicate: queriedWith, object: kt:aws-resource-groups-tagging-api}
sources:
  - id: s1
    title: "Tag policy syntax and examples - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_example-tag-policies.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Understanding enforcement - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_tag-policies-enforcement.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Enforce \"Required tag key\" with IaC - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/enforce-required-tag-keys-iac.md
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "AWS Organizations tag policies - Tagging AWS Resources and Tag Editor"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/tag-editor/latest/userguide/tag-policies-orgs.html
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Generating an organization-wide compliance report with AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/enforcement-report.html
    type: official-docs
    accessed: 2026-11-19
  - id: s6
    title: "Quotas and service limits for AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.html
    type: official-docs
    accessed: 2026-11-19
  - id: s7
    title: "Implementing and enforcing tagging - Best Practices for Tagging AWS Resources"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/implementing-and-enforcing-tagging
    type: official-docs
    accessed: 2026-11-19
  - id: s8
    title: "Validate and enforce required tags in CloudFormation, Terraform and Pulumi with Tag Policies"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/validate-enforce-required-tags-cloudformation-terraform-pulumi/
    type: official-blog
    accessed: 2026-11-19
  - id: s9
    title: "Simplify AWS Organization Tag Policies using new wildcard statement"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/07/aws-organization-tag-policies-wildcard-statement
    type: official-blog
    accessed: 2026-11-19
  - id: s10
    title: "Implement AWS resource tagging strategy using AWS Tag Policies and Service Control Policies (SCPs)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/mt/implement-aws-resource-tagging-strategy-using-aws-tag-policies-and-service-control-policies-scps
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# Tag policies

Tag policies are one of the management policy types in AWS Organizations. A tag policy is a JSON document, attached to the organization root, an organizational unit (OU) or an account, that declares which tag keys your organization standardizes, the exact capitalization each key must use, and optionally the values it may take. Policies inherit down the organization tree and merge into an *effective* tag policy for each account, which you can retrieve with `organizations:DescribeEffectivePolicy`.[^s1][^s4] They do not grant or deny IAM permissions; they describe what correct tagging looks like and report or block deviations from it.

## How it works

Each entry under the top-level `tags` key is keyed by the lowercase tag name. `tag_key` fixes the required capitalization (so `CostCenter` and `costcenter` are treated as different keys), and `tag_value` lists allowed values, which may include a single `*` wildcard per value, such as `300*`. If `tag_value` is omitted, any value is compliant. Inheritance operators control merging: `@@assign` sets a value, and `@@operators_allowed_for_child_policies` set to `@@none` stops lower-level policies from changing it.[^s1]

Tag policies offer two capabilities, each of which can run in reporting or enforcement mode:[^s2]

- **Basic compliance rules.** By default these only report. Adding `enforced_for` with a list of resource types (for example `ec2:instance`, or `ec2:ALL_SUPPORTED` for every supported EC2 type) makes AWS reject tagging operations that would create noncompliant keys or values on those types.[^s1][^s2] The `ALL_SUPPORTED` wildcard was added on 22 July 2025; it cannot match every service at once.[^s9][^s1]
- **Required tag keys.** The `report_required_tag_for` field marks tags that resources of given types must carry. Announced on 20 November 2025, this is checked *before* deployment by infrastructure-as-code tools: the AWS-managed CloudFormation hook `AWS::TagPolicies::TaggingComplianceValidator`, Terraform AWS Provider 6.22.0 or later with tag policy validation turned on, or a Pulumi policy pack.[^s3][^s8] The CloudFormation hook runs in warn or fail mode, works only as a stack hook, needs an execution role allowed to call `tag:ListRequiredTags`, and must be activated in every account and Region where you want the check (StackSets can roll it out).[^s3]

Compliance is visible per account in Tag Editor. From the management account you can see an organization-wide summary and export a report to an S3 bucket; report creation runs only in US East (N. Virginia), only one report can run at a time, and the organization-wide data is re-evaluated every 48 hours, so results can lag changes.[^s4][^s5]

## Quotas, pricing and gotchas

- **Quotas (as of 2026-11, organization-wide):** a tag policy document can be at most 10,000 characters; up to 10 tag policies can be attached directly to each root, OU or account; and an organization can hold up to 1,000 tag policies. The quotas page calls attachment limits hard limits. The console strips whitespace outside JSON strings before counting, but the CLI and SDKs count it.[^s6]
- **Pricing:** AWS states that tag policies and SCPs can be used at no additional charge.[^s10]
- **Untagged resources are invisible to basic rules.** Basic compliance rules never flag or block a resource that simply lacks the tag; a resource must have a tag for the policy to evaluate it. Use required tag keys (for IaC) or an SCP for that.[^s1][^s2][^s7]
- **Enforcement can break provisioning.** Enforcement covers supported resource types only, and services that create child resources and propagate tags (such as EC2 Auto Scaling groups or EMR clusters) can fail when the child type's policy is stricter than the parent's. AWS advises testing enforcement in a test account before widening it.[^s2]

## When to use it (and when not)

Use tag policies to standardize keys, spelling and value lists so cost allocation and automation can rely on them. AWS's tagging whitepaper recommends a layered approach. Tag policies standardize keys and values, AWS Config's `required-tags` managed rule detects missing keys after the fact, and SCPs act as hard preventive controls. For example, an SCP can deny `ec2:RunInstances` when a cost-center request tag is null.[^s7] An AWS Cloud Operations blog shows the same pairing: SCPs require tags at creation and stop their deletion, while the tag policy controls their values.[^s10] If you only need to bulk-fix tags, Tag Editor is the tool; tag policies define what correct looks like.[^s4]

## Connections

Tag policies are a feature of **AWS Organizations** (`broader`) and **implement** an organization's **tagging strategy**. They **contrast with service control policies**: SCPs restrict API permissions and can require a tag's presence, while tag policies govern its form.[^s7][^s10] Their size and attachment limits are **governed by Organizations quotas**.[^s6] Required-tag checks **integrate with AWS CloudFormation** through an AWS-managed hook.[^s3] Compliance is **queried with the Resource Groups Tagging API** (`GetComplianceSummary`, `StartReportCreation`) and reviewed and fixed in **AWS Tag Editor**.[^s4][^s5] Consistent keys underpin **cost allocation tags**, and **AWS Config** supplies the complementary detective check.[^s7]

## Sources
[^s1]: Amazon Web Services, "Tag policy syntax and examples - AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_example-tag-policies.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Understanding enforcement - AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_tag-policies-enforcement.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Enforce \"Required tag key\" with IaC - AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/enforce-required-tag-keys-iac.md (accessed 2026-11-19)
[^s4]: Amazon Web Services, "AWS Organizations tag policies - Tagging AWS Resources and Tag Editor". https://docs.aws.amazon.com/tag-editor/latest/userguide/tag-policies-orgs.html (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Generating an organization-wide compliance report with AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/enforcement-report.html (accessed 2026-11-19)
[^s6]: Amazon Web Services, "Quotas and service limits for AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.html (accessed 2026-11-19)
[^s7]: Amazon Web Services, "Implementing and enforcing tagging - Best Practices for Tagging AWS Resources" (whitepaper). https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/implementing-and-enforcing-tagging (accessed 2026-11-19)
[^s8]: Amazon Web Services, "Validate and enforce required tags in CloudFormation, Terraform and Pulumi with Tag Policies" (What's New, 2025-11-20). https://aws.amazon.com/about-aws/whats-new/2025/11/validate-enforce-required-tags-cloudformation-terraform-pulumi/ (accessed 2026-11-19)
[^s9]: Amazon Web Services, "Simplify AWS Organization Tag Policies using new wildcard statement" (What's New, 2025-07-22). https://aws.amazon.com/about-aws/whats-new/2025/07/aws-organization-tag-policies-wildcard-statement (accessed 2026-11-19)
[^s10]: Amazon Web Services, AWS Cloud Operations Blog, "Implement AWS resource tagging strategy using AWS Tag Policies and Service Control Policies (SCPs)" (2021-10-28). https://aws.amazon.com/blogs/mt/implement-aws-resource-tagging-strategy-using-aws-tag-policies-and-service-control-policies-scps (accessed 2026-11-19)
