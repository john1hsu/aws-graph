---
id: kt:aws-resource-groups-tagging-api
type: API
title: AWS Resource Groups Tagging API
aliases: [Resource Groups Tagging API, Tagging API, resourcegroupstaggingapi]
domain: governance-and-operations
summary: >-
  AWS's cross-service, per-Region API for finding tagged resources and adding or removing tags on
  many resource types through one interface, plus organization-wide tag-policy compliance reporting
  through GetComplianceSummary.
verified: 2026-11-20
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:tagging-strategy, kt:cost-allocation-tags, kt:aws-tag-editor, kt:attribute-based-access-control]
relations:
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: integratesWith, object: kt:tag-policies}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: contrastsWith, object: kt:aws-resource-explorer}
sources:
  - id: s1
    title: "GetResources - Resource Groups Tagging API"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/API_GetResources.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "TagResources - Resource Groups Tagging API"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/API_TagResources.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "GetComplianceSummary - Resource Groups Tagging API"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/API_GetComplianceSummary.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "AWS Resource Groups and Tagging endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/arg.html
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "Service quotas - Tagging AWS Resources and Tag Editor"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/tag-editor/latest/userguide/reference.html
    type: official-docs
    accessed: 2026-11-20
created: 2026-11-20
---

# AWS Resource Groups Tagging API

The Resource Groups Tagging API is the single, service-neutral interface for working with tags across AWS. Instead of calling `ec2:CreateTags`, `s3:PutBucketTagging` and dozens of other per-service operations, automation can list, add and remove tags on many resource types through one regional endpoint, `tagging.<region>.amazonaws.com`.[^s4] It is what the CLI exposes as `resourcegroupstaggingapi` and what the console's bulk tagging experience calls behind the scenes. The read side returns resources together with their tags, optionally annotated with compliance against tag policies.[^s1]

## How it works

The API has three groups of operations:

- **Discovery.** `GetResources` returns resources in one Region of one account that are tagged, or were tagged at some point, filtered by tag, by resource type (`ec2` or `ec2:instance` style) or by an explicit list of ARNs. `GetTagKeys` and `GetTagValues` enumerate the keys and values in use.[^s1][^s4]
- **Mutation.** `TagResources` and `UntagResources` apply or remove tags on a list of ARNs in the Region you call.[^s2]
- **Compliance.** `GetComplianceSummary` returns counts of resources that break their effective tag policies. It can group the counts by target (account or OU), Region or resource type.[^s3]

Filtering in `GetResources` follows simple rules. Up to 50 tag keys can be supplied, each with up to 20 values. Different keys are combined with AND; values of one key are combined with OR. A key given without values matches whatever value it carries.[^s1] Up to 100 resource-type filters are allowed. An ARN list (1–100 ARNs) is an alternative mode and cannot be mixed with the type filters, tag filters or paging parameters; ARNs that don't exist are silently dropped rather than raising an error.[^s1] Results are paged with `ResourcesPerPage` (1–100) or `TagsPerPage` (100–500, with `ResourcesPerPage` the recommended option). A page may hold fewer items than requested even when more remain, so loop until the pagination token comes back null. Tokens expire after at most 15 minutes.[^s1]

## Quotas and gotchas (as of 2026-11)

These limits apply to both Tag Editor and the Tagging API:[^s4][^s5]

- 50 user-created tags per resource; tags that AWS creates don't count toward this.[^s4][^s5]
- Tag keys are 1–128 Unicode characters and cannot start with `aws:`; values are 0–256 characters.[^s4][^s5]
- `GetResources`: 15 calls per second.[^s4][^s5]
- `TagResources`, `UntagResources`, `GetTagKeys`, `GetTagValues`: 5 calls per second each.[^s4][^s5]
- None of these can be raised in the Service Quotas console; AWS Support is the route for requests.[^s4][^s5]

Gotchas:

- **Untagged resources are invisible.** `GetResources` returns only resources that are or were tagged. For a "what's missing a tag" inventory, AWS points to a Resource Explorer search with `tag:none`.[^s1]
- **Partial success.** `TagResources` accepts 1–20 ARNs per call and reports per-resource failures in `FailedResourcesMap`, each with an error code, message and status code. A 200 response does not mean every resource was tagged.[^s2]
- **Two permissions.** A caller needs `tag:TagResources` and also the owning service's tagging action, such as `ec2:CreateTags`. Some services need more; S3 buckets also need `s3:GetBucketTagging`.[^s2]
- **Coverage.** Not every resource type works with the API. Unsupported services must be tagged through their own APIs.[^s2]
- **CloudFormation.** Tagging stack sets through this API calls `UpdateStack` and can trigger further property changes. AWS advises tagging stacks through CloudFormation instead.[^s2]
- **Throttling.** `ThrottledException` is returned as an HTTP 400, so retry logic should key on the error code rather than only on 5xx statuses.[^s1]
- **No secrets in tags.** AWS warns against putting personal or sensitive data in tags.[^s2]

## Organization compliance reporting

`GetComplianceSummary` is an organization-level call. It works only from the AWS Organizations management account and only in us-east-1, and it needs tag policies set up first: the tag policies service principal enabled in Organizations and a policy attached to the root, an OU or an account. Otherwise it fails with `ConstraintViolationException`.[^s3] Each row of the summary carries a target, Region, resource type, the count of noncompliant resources and a last-updated time.[^s3] For per-resource detail in a member account, call `GetResources` with `IncludeComplianceDetails`. Add `ExcludeCompliantResources` to get only the offenders, listed with their missing keys and noncompliant keys or values.[^s1]

## When to use it (and when not)

Use the Tagging API for scripted, repeatable tagging work: back-filling a mandatory cost-center key in a pipeline, nightly drift reports on tag compliance, or building an inventory of tagged resources per Region.[^s1][^s3] With 5 writes per second and 20 ARNs per call, very large back-fills must be paced (as of 2026-11).[^s4][^s2] For one-off interactive clean-up, the console tool built on the same API is simpler. For finding resources that were never tagged, Resource Explorer is the better fit.[^s1] It is a remediation and reporting interface, not an enforcement point: blocking untagged creation belongs in IAM conditions, SCPs and tag policies.[^s3]

## Connections

The API sits within AWS management and governance services and puts a **tagging strategy** into practice at scale, including back-filling **cost allocation tags** and the tags that **attribute-based access control** relies on.[^s2] **AWS Tag Editor** is its interactive front end.[^s5] It is **secured by IAM**, with each write needing the `tag:` action plus the owning service's action.[^s2] It is **scoped to Regions**, since every endpoint and call is regional.[^s4] It **integrates with tag policies** and **AWS Organizations** through its compliance details and the management-account summary.[^s3] It **contrasts with AWS Resource Explorer**, which can also find resources that have no tags at all.[^s1]

## Sources

[^s1]: Amazon Web Services, "GetResources - Resource Groups Tagging API". https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/API_GetResources.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "TagResources - Resource Groups Tagging API". https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/API_TagResources.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "GetComplianceSummary - Resource Groups Tagging API". https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/API_GetComplianceSummary.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "AWS Resource Groups and Tagging endpoints and quotas - AWS General Reference". https://docs.aws.amazon.com/general/latest/gr/arg.html (accessed 2026-11-20)
[^s5]: Amazon Web Services, "Service quotas - Tagging AWS Resources and Tag Editor". https://docs.aws.amazon.com/tag-editor/latest/userguide/reference.html (accessed 2026-11-20)
