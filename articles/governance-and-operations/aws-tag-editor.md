---
id: kt:aws-tag-editor
type: Tool
title: AWS Tag Editor
aliases: [Tag Editor, AWS Resource Groups Tag Editor]
domain: governance-and-operations
summary: >-
  The AWS Management Console tool for finding resources across Regions and resource types and adding,
  editing or removing their tags in bulk; since November 2026 its bulk tag management lives in the
  AWS Resource Explorer console, and Tag Editor pages redirect there.
verified: 2026-11-18
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:tagging-strategy, kt:cost-allocation-tags, kt:tag-policies, kt:attribute-based-access-control]
relations:
  - {predicate: dependsOn, object: kt:aws-resource-explorer}
  - {predicate: integratesWith, object: kt:aws-resource-groups-tagging-api}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "Finding resources to tag - Tagging AWS Resources and Tag Editor"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/tag-editor/latest/userguide/find-resources-to-tag.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Managing tags with Tag Editor - Tagging AWS Resources and Tag Editor"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/tag-editor/latest/userguide/tagging-resources.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Set up permissions - Tagging AWS Resources and Tag Editor"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/tag-editor/latest/userguide/gettingstarted-prereqs-permissions.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "TagResources - Resource Groups Tagging API"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/API_TagResources.html
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Tag Editor moves to AWS Resource Explorer: bulk-tag resources across Regions"
    publisher: Amazon Web Services (AWS Cloud Operations Blog)
    url: https://aws.amazon.com/blogs/mt/tag-editor-moves-to-aws-resource-explorer-bulk-tag-resources-across-regions/
    type: official-blog
    accessed: 2026-11-18
  - id: s6
    title: "Managing resources in the Resource Explorer console - AWS Resource Explorer"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/resource-explorer/latest/userguide/managing-resources.html
    type: official-docs
    accessed: 2026-11-18
created: 2026-11-18
---

# AWS Tag Editor

Tag Editor is the console tool AWS provides for managing tags, the key-value pairs that categorize resources, across many resources at once. The workflow has always been two steps: search for the resources you care about, then add, edit or remove tags on some or all of the results.[^s2] It is a console experience rather than a separately billed service; programmatic bulk tagging goes through the Resource Groups Tagging API (the `resourcegroupstaggingapi` CLI commands and SDK clients).[^s2]

## How it works

In the classic Tag Editor, a search combined one or more Regions (the current Region by default), at least one resource type (up to 20 individual types per query, or "All resource types"), and optional tag filters.[^s1] Multiple tag keys are ANDed, multiple values for the same key are ORed, matching is case sensitive, wildcards are not supported in the console, and leaving the tag fields empty returns untagged resources too, which is how teams hunt for gaps.[^s1] From the results you could select up to 500 resources and apply changes, and Tag Editor shows whether each tag was added in Tag Editor, in the owning service's console, or through the API.[^s2]

## What changed (2026-11)

On 3 November 2026 AWS announced that bulk tag management had moved into the AWS Resource Explorer console, with Tag Editor requests redirecting there automatically.[^s5] Instead of picking types and Regions from drop-downs, you choose a Resource Explorer *view* and type a query that can mix resource type, Region and tag filters, such as finding EC2 instances that lack a cost-center tag; the 20-type cap goes away.[^s5] You then select up to 400 resources (down from 500), choose **Manage tags**, and apply; selected resources need not be in the current Region, and new searches can take up to 30 seconds to reflect the change.[^s6] Cross-Region selection requires an aggregator index, which can take up to a couple of hours to build, and AWS states there is no additional charge for bulk tag management in Resource Explorer (as of 2026-11).[^s5]

## Permissions, quotas and gotchas

Tag Editor's own actions (`tag:GetResources`, `tag:TagResources`, `tag:UntagResources`, `tag:GetTagKeys`, `tag:GetTagValues`, plus Resource Groups and Resource Explorer search actions) are not sufficient alone: tagging also needs the owning service's permission, for example `ec2:CreateTags` for an instance, and reading tags needs read access to the resource itself.[^s3] AWS ships `ResourceGroupsandTagEditorReadOnlyAccess` and `ResourceGroupsandTagEditorFullAccess` managed policies for the classic tool.[^s3] For the Resource Explorer flow you additionally need permission on the view you search with.[^s5]

Under the hood, `TagResources` accepts 1–20 ARNs per call, tags only resources in the Region you call it in, and can return success while listing failures in `FailedResourcesMap`, so automation must check that map.[^s4] Each resource can carry up to 50 tags; keys are 1–128 characters and values 0–256 characters.[^s4] When you set a key that already exists, the new value is applied to every selected resource.[^s6] Two cautions recur in the documentation: do not put PII or other sensitive data in tags, since tags are used for billing and administration and are not encrypted, and do not bulk-tag CloudFormation stacks, because that triggers stack updates; tag them through CloudFormation instead.[^s4][^s6]

## When to use it (and when not)

Use Tag Editor (now in Resource Explorer) for interactive clean-up: back-filling a newly mandated tag, fixing inconsistent spellings, or auditing which resources are missing an owner tag.[^s5] It is a remediation tool, not an enforcement mechanism; preventing bad tags in the first place belongs to tag policies, SCPs and infrastructure-as-code defaults, and repeatable bulk changes are better scripted against the Tagging API with its partial-failure handling.[^s4]

## Connections

Tag Editor is how a **tagging strategy** is applied by hand and how **cost allocation tags** get back-filled on existing resources, so both articles point to it via `configuredVia`. It now **depends on AWS Resource Explorer** for search and cross-Region scope, wraps the **Resource Groups Tagging API**, is **secured by IAM** (tag actions plus service permissions), and remains **scoped to Regions** through views and aggregator indexes.[^s3][^s5][^s6] Tag-based access control (ABAC) and **tag policies** consume the tags it writes.[^s3]

## Sources
[^s1]: Amazon Web Services, "Finding resources to tag - Tagging AWS Resources and Tag Editor". https://docs.aws.amazon.com/tag-editor/latest/userguide/find-resources-to-tag.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Managing tags with Tag Editor". https://docs.aws.amazon.com/tag-editor/latest/userguide/tagging-resources.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Set up permissions - Tagging AWS Resources and Tag Editor". https://docs.aws.amazon.com/tag-editor/latest/userguide/gettingstarted-prereqs-permissions.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "TagResources - Resource Groups Tagging API". https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/API_TagResources.html (accessed 2026-11-18)
[^s5]: Amazon Web Services, "Tag Editor moves to AWS Resource Explorer: bulk-tag resources across Regions", AWS Cloud Operations Blog, 2026-11-03. https://aws.amazon.com/blogs/mt/tag-editor-moves-to-aws-resource-explorer-bulk-tag-resources-across-regions/ (accessed 2026-11-18)
[^s6]: Amazon Web Services, "Managing resources in the Resource Explorer console". https://docs.aws.amazon.com/resource-explorer/latest/userguide/managing-resources.html (accessed 2026-11-18)
