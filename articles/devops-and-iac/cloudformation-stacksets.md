---
id: kt:cloudformation-stacksets
type: Feature
title: AWS CloudFormation StackSets
aliases: [CloudFormation StackSets, StackSets, CloudFormation stack sets, CFN StackSets]
domain: devops-and-iac
summary: >-
  The AWS CloudFormation capability that deploys one template as stacks across many AWS accounts and
  Regions from a single administrator account, using either self-managed IAM roles or service-managed
  permissions through AWS Organizations, with automatic deployment to new accounts.
verified: 2026-11-19
status: current
broader: [kt:aws-cloudformation, kt:infrastructure-as-code]
related: [kt:aws-control-tower, kt:multi-account-strategy, kt:cloudformation-drift-detection]
relations:
  - {predicate: partOf, object: kt:aws-cloudformation}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: governedBy, object: kt:cloudformation-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-devops-engineer-professional}
sources:
  - id: s1
    title: "StackSets concepts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/he_il/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Create AWS CloudFormation StackSets with service-managed permissions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/account-level-targets.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Grant self-managed permissions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-prereqs-self-managed.html
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "AWS CloudFormation StackSets and AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/services-that-can-integrate-cloudformation
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Understand CloudFormation quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html
    type: official-docs
    accessed: 2026-11-19
  - id: s6
    title: "AWS CloudFormation StackSets now supports deployment ordering"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/aws-cloudformation-stacksets-deployment-ordering
    type: official-blog
    accessed: 2026-11-19
  - id: s7
    title: "Content Domain 2: Configuration Management and IaC (DOP-C02 exam guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/devops-engineer-professional-02/devops-engineer-professional-02-domain2.html
    type: official-learning
    accessed: 2026-11-19
created: 2026-11-19
---

# AWS CloudFormation StackSets

A **StackSet** is a CloudFormation container that rolls one template out as ordinary stacks into many accounts and Regions, so a baseline such as a logging bucket, an IAM role or a security-tooling stack is defined once and kept identical everywhere. You create and manage it from an **administrator account**; the accounts that receive stacks are **target accounts**, and they must trust the administrator before anything can be deployed. Parameters can be overridden to customise individual stacks, but the template itself always applies to all of them. A StackSet is a Regional resource: it is visible and editable only in the Region where it was created, even though it deploys into many.[^s1]

## How it works

Each deployed copy is tracked as a **stack instance**, a reference to one stack in one account and Region. An instance can exist without a stack, for example to record why creation failed, and it belongs to exactly one StackSet. Instance states such as `CURRENT`, `OUTDATED` (a create or update didn't reach it) and `INOPERABLE` (a failed delete left it unstable and excluded from updates) show where the fleet has drifted from the definition.[^s1]

Operations (create, update, delete stacks, delete StackSet) are tuned with deployment options:

- **Maximum concurrent accounts**, as a count or a percentage that rounds down.[^s1]
- **Failure tolerance**, counted per Region; exceeding it marks the operation `FAILED` and cancels the remaining Regions.[^s1]
- **Region concurrency**, sequential in a chosen Region order (the default) or parallel.[^s1]
- **Concurrency mode**: *strict* lowers concurrency as failures occur, keeping it within failure tolerance plus one; *soft* keeps the configured concurrency regardless.[^s2]

A StackSet can be deleted only when it has no stack instances. Removing instances with **Retain stacks** detaches them but leaves the stacks and resources running, now managed only in the target account.[^s1]

## Permission models

**Self-managed** permissions work with any account where you can create IAM roles. The administrator account holds `AWSCloudFormationStackSetAdministrationRole`, which assumes `AWSCloudFormationStackSetExecutionRole` in each target; the execution role name must be identical in every target account. AWS's sample execution role grants full administrator access, which you should cut down to `cloudformation:*` plus the services your templates use. Custom administration and execution roles can restrict which teams reach which accounts and which resource types they can deploy, and AWS recommends `aws:SourceArn` / `aws:SourceAccount` trust conditions against the confused-deputy problem.[^s3]

**Service-managed** permissions rely on trusted access with AWS Organizations, which only the management account can turn on. CloudFormation then creates service-linked roles: `AWSServiceRoleForCloudFormationStackSetsOrgAdmin` in the management account and `AWSServiceRoleForCloudFormationStackSetsOrgMember` in each member account it deploys to.[^s4] You target the whole organization or OUs (child OUs included), optionally filtered by account with intersection, difference or union rules. **Automatic deployment** extends the StackSet to accounts that later join a targeted OU and, with retain-on-removal, decides whether stacks stay or are deleted when an account leaves.[^s2] Since November 2025, auto-deployment also accepts a `DependsOn` list of up to 10 other StackSets, with cycle detection, so networking or security baselines land before application stacks; the feature costs nothing extra.[^s6]

## Quotas and gotchas

As of 2026-11 (per account; the page does not vary them by Region), an administrator account can hold 1,000 StackSets and each StackSet 100,000 stack instances, both raisable through Service Quotas. Up to 10,000 stack instance operations can run at once per Region per administrator account, and each StackSet can queue 10,000 operations; the page doesn't say whether those two can be raised.[^s5]

- **The management account never receives stacks** from a service-managed StackSet, even if it sits in a targeted OU.[^s2]
- **Delegated administrators are all-powerful.** Up to five member accounts can be registered, and they can deploy to any account in the organization; the management account cannot scope them to OUs or operations.[^s4] Their CLI calls need `--call-as DELEGATED_ADMIN`.[^s2]
- **Template limits.** Service-managed StackSets don't support nested stacks, macros or transforms.[^s2]
- **Automatic deployment is all-or-nothing** per StackSet; it can't be switched off for one OU, account or Region.[^s2]
- **"Succeeded" can mean "skipped".** If role provisioning fails or an account is suspended, its instances are skipped, and the operation can still report `SUCCEEDED`, so check the RoleProvisioning stage.[^s1]
- **Opt-in Regions** need the Regional CloudFormation service principal in self-managed trust policies.[^s3]

## When to use it

Use StackSets for guardrail and baseline resources that must exist in every account and Region, especially combined with Organizations auto-deployment so new accounts arrive compliant. For one-off or application-specific stacks, plain CloudFormation stacks driven by a pipeline are simpler. StackSets is examinable: the DevOps Engineer – Professional (DOP-C02) guide lists applying stack sets across accounts and Regions as a skill under Task 2.1.[^s7]

## Connections

StackSets is **part of AWS CloudFormation** and an **infrastructure-as-code** technique for **multi-account strategies**.[^s1] It **integrates with AWS Organizations** through trusted access and service-linked roles, and is **secured by AWS IAM** roles in the self-managed model.[^s3][^s4] Capacity is **governed by CloudFormation quotas**,[^s5] each StackSet is **scoped to** the AWS Region it was created in,[^s1] **drift detection** works across its instances, and **AWS Control Tower** builds on the same multi-account patterns. The topic is **covered by** the DevOps Engineer – Professional exam.[^s7]

## Sources
[^s1]: Amazon Web Services, "StackSets concepts", AWS CloudFormation User Guide. https://docs.aws.amazon.com/he_il/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Create AWS CloudFormation StackSets with service-managed permissions", AWS CloudFormation User Guide. https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/account-level-targets.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Grant self-managed permissions", AWS CloudFormation User Guide. https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-prereqs-self-managed.html (accessed 2026-11-19)
[^s4]: Amazon Web Services, "AWS CloudFormation StackSets and AWS Organizations", AWS Organizations User Guide. https://docs.aws.amazon.com/organizations/latest/userguide/services-that-can-integrate-cloudformation (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Understand CloudFormation quotas", AWS CloudFormation User Guide. https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html (accessed 2026-11-19)
[^s6]: Amazon Web Services, "AWS CloudFormation StackSets now supports deployment ordering", What's New with AWS, 2025-11-21. https://aws.amazon.com/about-aws/whats-new/2025/11/aws-cloudformation-stacksets-deployment-ordering (accessed 2026-11-19)
[^s7]: Amazon Web Services, "Content Domain 2: Configuration Management and IaC", AWS Certified DevOps Engineer – Professional (DOP-C02) exam guide. https://docs.aws.amazon.com/aws-certification/latest/devops-engineer-professional-02/devops-engineer-professional-02-domain2.html (accessed 2026-11-19)
