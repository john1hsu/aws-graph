---
id: kt:aws-partitions
type: InfrastructureConcept
title: AWS partitions
aliases: [AWS partition, partition (AWS), ARN partition, aws-cn partition, aws-us-gov partition, aws-eusc partition]
domain: global-infrastructure
summary: >-
  A partition is a group of AWS Regions with its own independent IAM instance, accounts and
  control plane; every Region and every account belongs to exactly one partition (aws, aws-cn,
  aws-us-gov, aws-eusc), and partitions form a hard boundary that credentials and most
  cross-Region features cannot cross.
verified: 2026-11-09
status: concept
broader: [kt:aws-global-infrastructure]
related: [kt:aws-iam, kt:aws-organizations, kt:aws-cloudformation]
relations:
  - {predicate: hasPart, object: kt:aws-regions}
  - {predicate: hasPart, object: kt:aws-govcloud-us}
  - {predicate: hasPart, object: kt:aws-european-sovereign-cloud}
sources:
  - id: s1
    title: "Partitions - AWS Fault Isolation Boundaries (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/partitions.html
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "Identify AWS resources with Amazon Resource Names (ARNs) - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/reference-arns
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Get AWS values using pseudo parameters - AWS CloudFormation"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "AWS Standard Account Linking - AWS GovCloud (US) User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/getting-started-standard-account-linking.html
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "Accounts and credentials - Getting Started with Amazon Web Services in China"
    publisher: Amazon Web Services
    url: https://docs.amazonaws.cn/en_us/aws/latest/userguide/accounts-and-credentials.html
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "Architecting a secure landing zone in the AWS European Sovereign Cloud"
    publisher: Amazon Web Services (AWS Security Blog)
    url: https://aws.amazon.com/blogs/security/architecting-a-secure-landing-zone-in-the-aws-european-sovereign-cloud/
    type: official-blog
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS partitions

A partition is AWS's largest isolation boundary: a named group of Regions. Every Region belongs to exactly one partition, and each partition contains one or more Regions.[^s1] Each partition runs its own independent instance of AWS Identity and Access Management (IAM), so identities and credentials are scoped to a single partition, and AWS describes the partition as a hard boundary between Regions in different partitions.[^s1] An AWS account is likewise scoped to one partition.[^s2] Most practitioners only ever touch the commercial partition, but the concept matters as soon as a workload must also run in AWS GovCloud (US), the China Regions or the AWS European Sovereign Cloud.

## The partitions

AWS's documentation names these partitions and their identifiers:[^s1][^s2][^s3][^s6]

- `aws` — the standard commercial Regions.
- `aws-cn` — the China (Beijing) and China (Ningxia) Regions.
- `aws-us-gov` — the AWS GovCloud (US) Regions.
- `aws-eusc` — the AWS European Sovereign Cloud, whose first Region is `eusc-de-east-1` in Brandenburg, Germany, and which AWS describes as physically and logically separate from existing Regions.

CloudFormation's documentation adds that resources in any other partition follow the `aws-partitionname` naming pattern, so code should not assume the list above is closed.[^s3]

## How it works

The partition identifier is the second field of every Amazon Resource Name, as in `arn:partition:service:region:account-id:resource-id`; IAM does not allow a wildcard in that field.[^s2] Endpoints can differ too: CloudFormation's `AWS::URLSuffix` is typically `amazonaws.com` but `amazonaws.com.cn` in China (Beijing), and the European Sovereign Cloud uses the `amazonaws.eu` endpoint domain while IAM service principals still end in `amazonaws.com`.[^s3][^s6]

Because IAM is separate, credentials from one partition cannot act on resources in another.[^s1] Cross-Region features such as S3 Cross-Region Replication and Transit Gateway inter-Region peering work only between Regions in the same partition.[^s1] For the European Sovereign Cloud, AWS lists `sts:AssumeRole`, VPC peering, Transit Gateway, AWS RAM and S3 replication as unavailable across the boundary; Direct Connect and VPC endpoints do not cross it either, and container images or AMIs must be pushed in explicitly.[^s6]

## Accounts, billing and organizations

Each partition has its own accounts. A GovCloud (US) account is linked one-to-one to a standard AWS account used for billing, service and support, and AWS recommends keeping that standard account dedicated to the link rather than running workloads in it.[^s4] Your standard AWS Organizations organization cannot manage GovCloud accounts; you create a separate organization inside GovCloud, ending up with one organization per partition.[^s4] China Region accounts are fully separate: accounts made for other Regions do not work there, China credentials do not work elsewhere, and the Regions are operated by Sinnet (Beijing) and NWCD (Ningxia).[^s5] The European Sovereign Cloud has its own Organizations organization, its own IAM Identity Center instance, and a separate payer billed in euros that cannot be rolled up with the commercial partition.[^s6]

## Gotchas and design guidance

The most common failure when deploying into a second partition is a hard-coded `arn:aws:` string in a policy, template or SDK call. CloudFormation's `AWS::Partition` pseudo parameter returns `aws`, `aws-cn` or `aws-us-gov` (or the other partition's name) so templates can build ARNs that work anywhere, and AWS's sovereign-cloud guidance calls for the same partition-aware construction in all infrastructure as code.[^s3][^s6] Plan cross-partition integration at the network or API layer with separate credentials per partition, rather than expecting role assumption or resource sharing to bridge the gap.[^s6] Treat each partition as a separate identity, billing and governance domain when designing a multi-account strategy.[^s4][^s6]

## Connections

Partitions sit directly under the global infrastructure: they contain Regions, including the GovCloud (US) Regions and the European Sovereign Cloud, so this node has `hasPart` edges to those nodes.[^s1][^s6] The partition is the scope of an IAM instance, which is why IAM is related, and the scope of an organization in AWS Organizations, which must be duplicated per partition.[^s1][^s4] CloudFormation is related because its `AWS::Partition` pseudo parameter is the standard way to keep templates portable across partitions.[^s3]

## Sources
[^s1]: Amazon Web Services, "Partitions - AWS Fault Isolation Boundaries". https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/partitions.html (accessed 2026-11-09)
[^s2]: Amazon Web Services, "Identify AWS resources with Amazon Resource Names (ARNs)". https://docs.aws.amazon.com/IAM/latest/UserGuide/reference-arns (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Get AWS values using pseudo parameters - AWS CloudFormation". https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "AWS Standard Account Linking - AWS GovCloud (US)". https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/getting-started-standard-account-linking.html (accessed 2026-11-09)
[^s5]: Amazon Web Services, "Accounts and credentials - Getting Started with Amazon Web Services in China". https://docs.amazonaws.cn/en_us/aws/latest/userguide/accounts-and-credentials.html (accessed 2026-11-09)
[^s6]: Amazon Web Services (AWS Security Blog), "Architecting a secure landing zone in the AWS European Sovereign Cloud". https://aws.amazon.com/blogs/security/architecting-a-secure-landing-zone-in-the-aws-european-sovereign-cloud/ (accessed 2026-11-09)
