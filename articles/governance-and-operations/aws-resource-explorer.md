---
id: kt:aws-resource-explorer
type: Service
title: AWS Resource Explorer
aliases: [Resource Explorer, AWS Resource Explorer 2, resource-explorer-2]
domain: governance-and-operations
summary: >-
  A no-additional-charge AWS service that indexes resource metadata (ARNs, names, IDs, tags) per Region
  and lets you search it with a query language, across Regions through an aggregator index and across
  accounts through AWS Organizations multi-account views.
verified: 2026-11-20
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:aws-tag-editor, kt:tagging-strategy, kt:aws-resource-groups-tagging-api]
relations:
  - {predicate: integratesWith, object: kt:aws-config}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: configuredVia, object: kt:aws-systems-manager}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: integratesWith, object: kt:aws-resource-access-manager}
sources:
  - id: s1
    title: "What is AWS Resource Explorer? - AWS Resource Explorer"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/resource-explorer/latest/userguide/welcome.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Quotas for Resource Explorer - AWS Resource Explorer"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/resource-explorer/latest/userguide/quotas.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Using AWS Resource Explorer to search for resources - AWS Resource Explorer"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/resource-explorer/latest/userguide/using-search.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "AWS Resource Explorer FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/resourceexplorer/faqs
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "AWS Resource Explorer endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/arex.html
    type: official-docs
    accessed: 2026-11-20
  - id: s6
    title: "AWS Resource Explorer Features"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/resourceexplorer/features
    type: official-docs
    accessed: 2026-11-20
created: 2026-11-20
---

# AWS Resource Explorer

AWS Resource Explorer is a search and discovery service for the resources in your AWS accounts. It indexes resource metadata such as names, tags and resource IDs, and can enrich results with details drawn from AWS Config and AWS Cloud Control; the search itself spans Regions in an account and, with extra setup, accounts in an organization.[^s1] It indexes only metadata (ARN, resource ID, Region and similar), never the data inside a resource.[^s4] Since 6 October 2025 Resource Explorer is turned on by default in AWS accounts, so basic search works without any setup steps.[^s1]

## How it works

Resource Explorer keeps an **index** in each Region it is enabled in. The documentation distinguishes Resource Explorer-owned indexes, which return immediate but partial results, from user-owned indexes, which return complete results and update automatically.[^s1] To search everything in an account from one place you promote one Region's index to the **aggregator index**: the other Regions replicate their index data into it, which is the only case in which index metadata leaves its home Region.[^s1][^s4] Cross-Region search therefore requires an aggregator index, and only views that live in the aggregator Region return results from all Regions.[^s3]

A **view** controls what a search can see: administrators create custom views that filter which resources appear and grant different groups of users access to different views through IAM.[^s1][^s6] Quick setup creates a local index in every supported Region, promotes the Region you pick to aggregator and creates a default view there; Advanced setup lets you choose Regions, the aggregator Region and default views yourself.[^s6] For **multi-account search**, an organization enables trusted access with AWS Organizations, deploys Resource Explorer to member accounts with CloudFormation or Systems Manager Quick Setup, designates a delegated administrator, and creates multi-account views for the whole organization or an OU; those views can be shared through AWS RAM.[^s1][^s4]

Queries mix free-form keywords with filters such as `region:`, `service:`, `resourcetype:` and `tag:`; keywords are ORed together while named filters are ANDed, so `test instance service:EC2 region:us-west-2` finds either keyword but only EC2 resources in that Region.[^s3] Only one filter per single-valued attribute works (two `region:` filters return nothing), whereas multi-valued attributes such as tags can repeat.[^s3] Results also surface in the unified search bar of the AWS Management Console, with links straight to each resource's console page.[^s4][^s6]

## Quotas, pricing and gotchas

As of 2026-11, AWS states there is no additional charge for Resource Explorer: searching, creating views and completing setup in Regions are free, though optional data sources such as AWS Config bill separately and API calls made while building the inventory may incur charges.[^s1][^s4] Per-Region quotas (as of 2026-11) are 10 views, 5 Search operations per second and 3 non-Search operations per second.[^s2][^s5] The Resource Explorer user guide says none of its quotas can be increased; the General Reference page uses generic wording about some quotas being adjustable without marking which, so plan around the user guide's fixed values.[^s2][^s5]

Searches that include free-form text return at most the first 1,000 results, sorted by relevance; filter-only queries go through `ListResources`, which has no upper limit but is not relevance-sorted.[^s3] The index is eventually consistent: most changes appear within minutes, but AWS documents that some can take much longer, with the FAQ citing up to 36 hours for replication across the service.[^s3][^s4] Do not treat a missing search hit as proof a resource does not exist, and use AWS Config when you need configuration history or compliance state rather than discovery.[^s4]

## When to use it (and when not)

Use Resource Explorer to answer "what do we have and where is it?" quickly: finding untagged or mis-tagged resources, locating everything for an application by tag, or inventorying an organization's resources from a delegated administrator account.[^s4][^s6] It is not a configuration-management database: AWS Config provides inventory with configuration history and change tracking, and when Config is enabled Resource Explorer can show that compliance and configuration data alongside its results.[^s4]

## Connections

Resource Explorer sits under **AWS management and governance services** and is **scoped to Regions** through local and aggregator indexes.[^s1] It **integrates with AWS Organizations** for multi-account views and with **AWS RAM** for sharing them, is **configured via Systems Manager** Quick Setup (or CloudFormation) across accounts, and is **secured by IAM** permissions on views.[^s4][^s6] It **integrates with AWS Config** for richer resource details.[^s1][^s4] Its tag filters make it the search layer behind **Tag Editor** bulk tagging and a practical tool for enforcing a **tagging strategy** alongside the **Resource Groups Tagging API**.[^s3][^s6]

## Sources
[^s1]: Amazon Web Services, "What is AWS Resource Explorer?". https://docs.aws.amazon.com/resource-explorer/latest/userguide/welcome.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "Quotas for Resource Explorer". https://docs.aws.amazon.com/resource-explorer/latest/userguide/quotas.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "Using AWS Resource Explorer to search for resources". https://docs.aws.amazon.com/resource-explorer/latest/userguide/using-search.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "AWS Resource Explorer FAQs". https://aws.amazon.com/resourceexplorer/faqs (accessed 2026-11-20)
[^s5]: Amazon Web Services, "AWS Resource Explorer endpoints and quotas - AWS General Reference". https://docs.aws.amazon.com/general/latest/gr/arex.html (accessed 2026-11-20)
[^s6]: Amazon Web Services, "AWS Resource Explorer Features". https://aws.amazon.com/resourceexplorer/features (accessed 2026-11-20)
