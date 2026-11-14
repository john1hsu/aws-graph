---
id: kt:cost-allocation-tags
type: Feature
title: Cost allocation tags
aliases: [AWS cost allocation tags, Cost allocation tagging, aws:createdBy tag]
domain: cost-management
summary: >-
  A Billing and Cost Management feature that turns resource tag keys, once activated by the
  management (or standalone) account, into dimensions for grouping and filtering AWS costs in
  Cost Explorer, Budgets and cost and usage reports; tags are either user-defined or AWS-generated.
verified: 2026-11-14
status: current
broader: [kt:aws-billing-and-cost-management]
related: [kt:aws-cost-explorer, kt:aws-budgets, kt:cost-optimization-pillar]
relations:
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: integratesWith, object: kt:aws-cost-and-usage-report}
  - {predicate: dependsOn, object: kt:aws-cloudtrail}
  - {predicate: configuredVia, object: kt:aws-tag-editor}
  - {predicate: governedBy, object: kt:tag-policies}
sources:
  - id: s1
    title: "Organizing and tracking costs using AWS cost allocation tags - AWS Billing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.md
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "Using AWS-generated tags - AWS Billing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/aws-tags.md
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Activating user-defined cost allocation tags - AWS Billing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/activating-tags.md
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Backfill cost allocation tags - AWS Billing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-allocation-backfill.md
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "AWS Cost Allocation Tags now support retroactive application"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2024/03/aws-cost-allocation-tags-retroactive-application
    type: official-blog
    accessed: 2026-11-14
  - id: s6
    title: "AWS Organizations – Tag policies - Best Practices for Tagging AWS Resources"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/aws-organizations-tag-policies.html
    type: official-docs
    accessed: 2026-11-14
  - id: s7
    title: "Cost Allocation Blog Series #2: AWS Generated vs User-defined Cost Allocation Tag"
    publisher: Amazon Web Services (AWS Cloud Financial Management Blog)
    url: https://aws.amazon.com/blogs/aws-cost-management/cost-allocation-blog-series-2-aws-generated-vs-user-defined-cost-allocation-tag
    type: official-blog
    accessed: 2026-11-14
created: 2026-11-14
---

# Cost allocation tags

A tag is a key–value label attached to an AWS resource; each key is unique per resource and holds a single value. Cost allocation tags are the subset of tag keys that you explicitly *activate* in the Billing and Cost Management console so AWS uses them to break down your bill. Once active, they become columns in the cost allocation report and filter or group-by dimensions in Cost Explorer, which lets you attribute spend to business categories such as cost centers, applications or owners across many services.[^s1] Tagging a resource on its own does nothing for billing until the key is activated.[^s3]

## How it works

AWS offers two kinds of cost allocation tag, and each must be activated separately before it shows up in Cost Explorer or the report.[^s1]

- **User-defined tags** are keys you create and apply to resources yourself (for example with Tag Editor). After they're applied, it can take up to 24 hours for the key to appear on the Cost allocation tags page, and up to another 24 hours for activation to take effect. Activating a key covers every value under that key, and the `UpdateCostAllocationTagsStatus` API activates keys in bulk.[^s3] In reports these keys carry a `user:` prefix.[^s2]
- **AWS-generated tags** are defined and applied by AWS. The main one, `aws:createdBy`, records which root user, IAM user, assumed role or federated user created a resource. It is applied only to resources created *after* it is activated, only for a listed set of create events (such as EC2 `RunInstances`, S3 `CreateBucket` and CloudFormation `CreateStack`), and only in a listed subset of Regions. It is visible only in billing tools, not in Tag Editor.[^s2] AWS Marketplace sellers can also emit tags under the `aws:marketplace:isv:` prefix, which need the same activation.[^s2]

Only the management account of an organization, or a standalone account, can use the cost allocation tags manager; activating an AWS-generated tag in the management account also turns it on for all member accounts.[^s1][^s2] The `awsApplication` tag that Service Catalog AppRegistry adds to application resources is activated automatically.[^s3] The report covers tagged and untagged costs, so at the end of a billing period its total reconciles with the Bills page.[^s1]

## Backfill

Since March 2024, activation is no longer forward-only: a management account can request a backfill that applies the current activation status to up to 12 months of past cost data, in all commercial Regions.[^s4][^s5] Backfill only helps if the resource actually carried the tag in those months; it can also *deactivate* a key historically. Only one backfill can run at a time, requests are limited to one every 24 hours, and Cost Explorer, Data Exports and the Cost and Usage Report pick up the change on their next daily refresh.[^s4]

## Quotas and gotchas (as of 2026-11)

- At most 500 active tag keys for Billing and Cost Management reports. AWS-generated tags don't count toward the 50-user-tags-per-resource quota; `awsApplication` doesn't count toward the cost allocation tag quota.[^s2][^s3]
- `aws:createdBy` is best-effort: it is built from CloudTrail logs, so CloudTrail problems or oversized log entries can leave gaps, and you can't edit or delete AWS-generated tags.[^s2]
- Tag values that are null are dropped from Cost Explorer and AWS Budgets.[^s2] Resources with a missing or misspelled key simply fall out of a tag-filtered view, so a consistent key naming scheme matters.[^s7]
- AWS advises keeping sensitive data out of tags.[^s1]

## When to use it

Activate cost allocation tags whenever you need showback or chargeback below the account level — for example several teams or applications sharing one account. Account boundaries in AWS Organizations give cleaner separation for coarse splits, while tags handle the finer ones. AWS's own guidance suggests a hybrid: turn on `aws:createdBy` for the services it covers and rely on a disciplined user-defined scheme (such as a cost-center key) everywhere else.[^s7]

## Connections

Cost allocation tags are a feature of **AWS Billing and Cost Management** and feed **AWS Cost Explorer**, **AWS Budgets** and the **AWS Cost and Usage Report**, all of which can filter or group by active tags.[^s1][^s2][^s4] They are managed from the **AWS Organizations** management account and propagate to members.[^s2] User-defined tags are applied through **Tag Editor**, and the AWS-generated `createdBy` tag depends on **AWS CloudTrail**.[^s1][^s2] **Tag policies** in Organizations standardize allowed keys and values (a cost-allocation schema is a typical example) and can report or optionally enforce them, but they don't activate tags for billing.[^s6] Consistent tagging underpins the attribution practices in the **Cost Optimization Pillar** of the Well-Architected Framework.

## Sources
[^s1]: Amazon Web Services, "Organizing and tracking costs using AWS cost allocation tags". https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.md (accessed 2026-11-14)
[^s2]: Amazon Web Services, "Using AWS-generated tags". https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/aws-tags.md (accessed 2026-11-14)
[^s3]: Amazon Web Services, "Activating user-defined cost allocation tags". https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/activating-tags.md (accessed 2026-11-14)
[^s4]: Amazon Web Services, "Backfill cost allocation tags". https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-allocation-backfill.md (accessed 2026-11-14)
[^s5]: Amazon Web Services, "AWS Cost Allocation Tags now support retroactive application" (What's New, 2024-03-27). https://aws.amazon.com/about-aws/whats-new/2024/03/aws-cost-allocation-tags-retroactive-application (accessed 2026-11-14)
[^s6]: Amazon Web Services, "AWS Organizations – Tag policies", Best Practices for Tagging AWS Resources whitepaper. https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/aws-organizations-tag-policies.html (accessed 2026-11-14)
[^s7]: Bowen Wang, "Cost Allocation Blog Series #2: AWS Generated vs User-defined Cost Allocation Tag", AWS Cloud Financial Management Blog, 2020-09-08. https://aws.amazon.com/blogs/aws-cost-management/cost-allocation-blog-series-2-aws-generated-vs-user-defined-cost-allocation-tag (accessed 2026-11-14)
