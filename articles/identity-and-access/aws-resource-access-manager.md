---
id: kt:aws-resource-access-manager
type: Service
title: AWS Resource Access Manager
aliases: [AWS RAM, RAM resource sharing]
domain: identity-and-access
summary: >-
  AWS's free service for sharing resources you own, such as VPC subnets, transit gateways,
  Route 53 Resolver rules and IPAM pools, with other AWS accounts, an AWS Organizations
  organization or OU, or (for some resource types) individual IAM roles and users.
verified: 2026-11-16
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:multi-account-strategy, kt:amazon-vpc, kt:aws-transit-gateway, kt:aws-cloud-map]
relations:
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: dependsOn, object: kt:aws-iam}
  - {predicate: governedBy, object: kt:ram-quotas}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is AWS Resource Access Manager?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ram/latest/userguide/what-is.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Terms and concepts for AWS RAM"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ram/latest/userguide/getting-started-terms-and-concepts.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Service quotas for AWS RAM"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ram/latest/userguide/service-quotas.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Accepting and rejecting resource share invitations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ram/latest/userguide/working-with-shared-invitations.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "Shareable AWS resources"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ram/latest/userguide/shareable
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "AWS Resource Access Manager now supports maintaining shares when accounts change organizations"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/02/aws-resource-access-manager
    type: official-blog
    accessed: 2026-11-16
  - id: s7
    title: "VPC sharing - Building a Scalable and Secure Multi-VPC AWS Network Infrastructure"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/building-scalable-secure-multi-vpc-network-infrastructure/amazon-vpc-sharing.html
    type: official-docs
    accessed: 2026-11-16
  - id: s8
    title: "AWS Resource Access Manager FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ram/faqs/
    type: official-docs
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS Resource Access Manager

AWS Resource Access Manager (AWS RAM) lets an account that owns a resource share it with other AWS accounts, with an entire AWS Organizations organization or specific organizational units (OUs), and, for supported resource types, with individual IAM roles and users. Instead of provisioning a duplicate of the same network or infrastructure resource in every account, you create it once in an owning account and share it. The owner keeps full ownership, and the shared resource appears natively in the consumer's console and API results for the originating service.[^s1] RAM itself carries no additional charge; the shared resources are billed by their own services.[^s1][^s8]

## How it works

The unit of sharing is a **resource share**, which bundles three things: one or more resources, one or more principals that receive access, and one **managed permission** per resource type that caps what those principals may do.[^s2] AWS provides at least one AWS managed permission for every shareable type (one is the default), and for some types you can author customer managed permissions with condition keys, for example letting developers allocate addresses from an IPAM pool without seeing other accounts' allocations. Customer managed permissions are Regional and can hold up to five versions.[^s2] Behind the scenes RAM generates and maintains the resource-based policy for each shared resource, merging every share that includes it into one policy.[^s2]

Access in the consuming account is the intersection of two things: the share's managed permission and the identity-based IAM policies that the consumer's own administrators attach to their roles and users. Service control policies in the consuming account still apply.[^s1][^s2] Resource shares are Regional: a Regional resource can only be shared within its Region, and global resources are shared from US East (N. Virginia), us-east-1.[^s1]

**Inside vs. outside an organization.** Once sharing with AWS Organizations is enabled, you can target the whole organization or an OU by ARN without listing accounts, and member accounts get access without an invitation.[^s1][^s4] Sharing with individual accounts outside that scope sends an invitation that the recipient must accept. Invitations for a listed set of types (including VPC subnets, transit gateways, Route 53 Resolver forwarding rules, EC2 Capacity Reservations and Aurora DB clusters) expire after seven days; invitations for all other types expire after 12 hours.[^s4] Since February 2026, administrators can set `RetainSharingOnAccountLeaveOrganization` so shares survive an account moving between organizations (useful during mergers and restructuring), and can enforce it organization-wide with a matching SCP condition key; this is offered in all commercial Regions at no extra cost.[^s6]

## What can be shared

The shareable-resources list spans dozens of services and keeps growing. Common networking examples are VPC subnets, transit gateways, Route 53 Resolver rules, prefix lists, IPAM pools and VPC Lattice service networks; other examples include EC2 Capacity Reservations, Dedicated Hosts and placement groups, License Manager configurations, AWS Private CA authorities, Cloud Map namespaces, Network Firewall policies and Outposts.[^s5] Each type has its own rules: VPC subnets can be shared only with accounts in your own organization, never with IAM roles or users, and default subnets cannot be shared at all, whereas transit gateways and Resolver rules can go to accounts outside the organization.[^s5]

## Quotas and gotchas

Default quotas as of 2026-11 (per Region unless noted): 25,000 resource shares; 5,000 resource associations and 5,000 principal associations per share; 25,000 resource associations and 25,000 principal associations across all shares; 1,500 customer managed permissions (10 per resource type); and 250 pending invitations per sharing account, which only counts invitations to accounts outside the organization. The guide says some of these can be raised through AWS Support but doesn't mark which.[^s3]

Gotchas: resources shared through a hand-written resource-based policy don't get RAM's visibility features until promoted with `PromoteResourceShareCreatedFromPolicy`;[^s1] an organization-scoped share that includes the owner's own account effectively grants all its principals access up to the managed permission, because the generated policy keys on the organization ID;[^s2] and an account removed from the organization loses access obtained through organization sharing unless the retain-sharing option is set.[^s4][^s6]

## When to use it

RAM is the standard way to centralize shared infrastructure in a multi-account landing zone. The canonical pattern is **VPC sharing**: a network account owns the VPC and shares subnets with application accounts, which create their own instances and databases there but cannot see or modify other participants' resources. AWS's multi-VPC whitepaper credits this with fewer VPCs to manage, a clean split between network and application teams, better IPv4 utilization, and no data transfer charge between accounts' instances in the same Availability Zone; the trade-off is that participants share IP space and must coordinate.[^s7] Transit gateways shared through RAM then connect a small number of shared VPCs instead of one VPC per application.[^s7]

## Connections

RAM sits among the **AWS management and governance services** and works hand in hand with **AWS Organizations**, whose organization and OU ARNs it uses as share targets, which makes it a building block of a **multi-account strategy**.[^s1][^s2] Consumer access always depends on **AWS IAM** identity-based policies, and activity is visible through **AWS CloudTrail**.[^s1] Shares are bound to **AWS Regions**, with defaults defined in **AWS RAM quotas**.[^s1][^s3] Typical shared resources include subnets of an **Amazon VPC**, an **AWS Transit Gateway** and **AWS Cloud Map** namespaces.[^s5][^s7]

## Sources
[^s1]: Amazon Web Services, "What is AWS Resource Access Manager?". https://docs.aws.amazon.com/ram/latest/userguide/what-is.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Terms and concepts for AWS RAM". https://docs.aws.amazon.com/ram/latest/userguide/getting-started-terms-and-concepts.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Service quotas for AWS RAM". https://docs.aws.amazon.com/ram/latest/userguide/service-quotas.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Accepting and rejecting resource share invitations". https://docs.aws.amazon.com/ram/latest/userguide/working-with-shared-invitations.html (accessed 2026-11-16)
[^s5]: Amazon Web Services, "Shareable AWS resources". https://docs.aws.amazon.com/ram/latest/userguide/shareable (accessed 2026-11-16)
[^s6]: Amazon Web Services, "AWS Resource Access Manager now supports maintaining shares when accounts change organizations" (What's New, 2026-02-27). https://aws.amazon.com/about-aws/whats-new/2026/02/aws-resource-access-manager (accessed 2026-11-16)
[^s7]: Amazon Web Services, "VPC sharing - Building a Scalable and Secure Multi-VPC AWS Network Infrastructure" (whitepaper). https://docs.aws.amazon.com/whitepapers/latest/building-scalable-secure-multi-vpc-network-infrastructure/amazon-vpc-sharing.html (accessed 2026-11-16)
[^s8]: Amazon Web Services, "AWS Resource Access Manager FAQs". https://aws.amazon.com/ram/faqs/ (accessed 2026-11-16)
