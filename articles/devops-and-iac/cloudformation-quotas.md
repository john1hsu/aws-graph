---
id: kt:cloudformation-quotas
type: Limit
title: AWS CloudFormation quotas
aliases: [CloudFormation quotas, CloudFormation limits, CloudFormation service limits, CFN quotas]
domain: devops-and-iac
summary: >-
  The per-account, per-Region and per-template limits that bound AWS CloudFormation: how many
  stacks and StackSets you can own, how large a template can be and how many resources,
  parameters, mappings and outputs it may declare, plus registry, Hooks and API rate limits.
  Account-level counts are adjustable; template-shape limits are fixed.
verified: 2026-11-19
status: current
broader: [kt:aws-cloudformation]
related: [kt:cloudformation-stacksets, kt:aws-cdk, kt:infrastructure-as-code, kt:service-quotas]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "Understand CloudFormation quotas - AWS CloudFormation"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "AWS CloudFormation endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-endpoints.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "describe-account-limits - AWS CLI Command Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/goto/cli2/cloudformation-2010-05-15/DescribeAccountLimits
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "AccountLimit - AWS CloudFormation API Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/goto/WebAPI/cloudformation-2010-05-15/AccountLimit
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "AWS CloudFormation now supports increased limits on five service quotas (What's New with AWS, 2020-10-22)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2020/10/aws-cloudformation-now-supports-increased-limits-on-five-service-quotas
    type: official-blog
    accessed: 2026-11-19
  - id: s6
    title: "AWS CloudFormation now supports more stacks per AWS account (What's New with AWS, 2021-07-15)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2021/06/aws-cloudformation-now-supports-more-stacks-per-aws-account
    type: official-blog
    accessed: 2026-11-19
  - id: s7
    title: "AWS CloudFormation StackSets increases limits on three service quotas (What's New with AWS, 2022-10-11)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2022/10/aws-cloudformation-stacksets-increases-limits-three-service-quotas
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# AWS CloudFormation quotas

CloudFormation quotas fall into three groups: account-level counts (stacks, StackSets, registry extensions, Hooks), template-shape limits (size, resources, parameters, mappings, outputs, name lengths), and per-operation limits such as how many resources a nested-stack deployment can touch.[^s1] The account-level counts apply per account in each Region, and the General Reference marks the main ones as adjustable through Service Quotas, while the template-shape limits are fixed.[^s2]

## Account and Region quotas (as of 2026-11)

| Quota | Default | Adjustable |
|---|---|---|
| Active stacks per Region | 2,000 | Yes |
| StackSets per administrator account | 1,000 | Yes |
| Stack instances per StackSet | 100,000 | Yes |
| Concurrent stack instance operations per Region, per administrator account | 10,000 | Yes |
| Private resource types in the registry | 50 | Yes |
| Modules in the registry | 100 | Yes |
| Hooks per account per Region | 100 | No[^s2] |

These defaults have grown a lot. Stacks per account went from 200 to 2,000 in July 2021.[^s6] In October 2022 AWS raised stack instances per StackSet from 2,000 to 100,000, StackSets per account from 100 to 1,000, and concurrent instance operations from 3,500 to 10,000.[^s7] A StackSet can also queue at most 10,000 operations (not adjustable), and one import operation can bring in 200 stacks listed in an S3 object or 10 passed inline.[^s1][^s2] Each module can hold 100 versions and each Hook 100 versions; deregistering unused versions with `DeregisterType` frees slots.[^s1]

## Template limits (fixed)

A template can declare 500 resources, 200 parameters, 200 mappings (each with up to 200 attributes) and 200 outputs, plus 60 dynamic references.[^s1] A template body passed inline in `CreateStack`, `UpdateStack` or `ValidateTemplate` is capped at 51,200 bytes; one stored in Amazon S3 may be 1 MB.[^s1] Parameter values and custom-resource responses are limited to 4,096 bytes each, as is data sent by `cfn-signal`, and names of resources, parameters, outputs and mappings to 255 characters; stack names to 128 and descriptions to 1,024 bytes.[^s1] Most of these limits were themselves raised in October 2020, when resources per template went from 200 to 500 and the S3 template size from 450 KB to 1 MB.[^s5]

A nested-stack hierarchy may contain more than 2,500 resources in total, but a single create, update or delete can act on no more than 2,500 of them.[^s1] The number of resources across all concurrent stack operations in a Region is not printed in the docs; call `DescribeAccountLimits` (CLI `describe-account-limits`), which returns `StackLimit`, `StackOutputsLimit` and `ConcurrentResourcesLimit` for the account in the current Region.[^s1][^s3][^s4] The CLI page's sample output shows older values (200 stacks, 60 outputs), so trust the live call, not the example.[^s3][^s6]

## API throttling

CloudFormation APIs are rate-limited per account per Region, and those limits are not adjustable. Read-heavy calls such as `DescribeStacks` and `DescribeStackEvents` allow 10 requests per second, `DescribeStackResource` 20, and `DetectStackResourceDrift` 3.[^s2] Mutating calls such as `CreateStack`, `UpdateStack` and `DeleteStack` allow 2 per second, so pipelines that create or poll many stacks in parallel need to pace their calls.[^s2]

## Gotchas and when they bite

- **500 resources is the classic wall.** The documented fix is to split the template into multiple templates, for example nested stacks.[^s1]
- **Over 51,200 bytes, upload to S3.** The CLI and pipelines must stage larger templates in an S3 bucket; minification or nested stacks help past 1 MB.[^s1]
- **Hooks are capped at 100 per account per Region and cannot be raised,** so budget them like any other scarce account resource.[^s1][^s2]
- **Raise counts before a big StackSets rollout.** Fleet-wide deployments across many accounts and Regions can approach the instance and operation ceilings; request increases in advance.[^s1][^s7]

## Connections

- **Broader: AWS CloudFormation** (`kt:aws-cloudformation`), whose stacks, templates and registry these limits bound.[^s1]
- **Related to CloudFormation StackSets** (`kt:cloudformation-stacksets`), which carries its own StackSet, instance and operation quotas.[^s1][^s7]
- **Related to AWS CDK** (`kt:aws-cdk`) and **infrastructure as code** (`kt:infrastructure-as-code`): code that generates templates is still bound by the template limits on this page.[^s1]
- **Configured via Service Quotas** (`kt:service-quotas`) for the adjustable account-level counts.[^s2]
- **Scoped to AWS Regions** (`kt:aws-regions`): stack, registry, Hook and throttle limits are counted per account in each Region.[^s2]

## Sources
[^s1]: Amazon Web Services, "Understand CloudFormation quotas". https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, AWS General Reference, "AWS CloudFormation endpoints and quotas". https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-endpoints.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, AWS CLI Command Reference, "describe-account-limits". https://docs.aws.amazon.com/goto/cli2/cloudformation-2010-05-15/DescribeAccountLimits (accessed 2026-11-19)
[^s4]: Amazon Web Services, AWS CloudFormation API Reference, "AccountLimit". https://docs.aws.amazon.com/goto/WebAPI/cloudformation-2010-05-15/AccountLimit (accessed 2026-11-19)
[^s5]: Amazon Web Services, What's New, "AWS CloudFormation now supports increased limits on five service quotas" (2020-10-22). https://aws.amazon.com/about-aws/whats-new/2020/10/aws-cloudformation-now-supports-increased-limits-on-five-service-quotas (accessed 2026-11-19)
[^s6]: Amazon Web Services, What's New, "AWS CloudFormation now supports more stacks per AWS account" (2021-07-15). https://aws.amazon.com/about-aws/whats-new/2021/06/aws-cloudformation-now-supports-more-stacks-per-aws-account (accessed 2026-11-19)
[^s7]: Amazon Web Services, What's New, "AWS CloudFormation StackSets increases limits on three service quotas" (2022-10-11). https://aws.amazon.com/about-aws/whats-new/2022/10/aws-cloudformation-stacksets-increases-limits-three-service-quotas (accessed 2026-11-19)
