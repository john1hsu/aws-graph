---
id: kt:aws-firewall-manager
type: Service
title: AWS Firewall Manager
aliases: [Firewall Manager, FMS, AWS FMS]
domain: security
summary: >-
  AWS's organization-wide firewall policy service: an administrator account in AWS Organizations
  defines AWS WAF, Shield Advanced, security group, network ACL, Network Firewall, DNS Firewall and
  third-party firewall policies once, and Firewall Manager applies them to in-scope accounts and
  resources per Region, flagging or remediating drift. AWS has announced Network Security Manager as
  its eventual replacement.
verified: 2026-11-16
status: current
broader: [kt:aws-security-services]
related: [kt:aws-waf, kt:aws-shield, kt:multi-account-strategy, kt:amazon-vpc]
relations:
  - {predicate: dependsOn, object: kt:aws-organizations}
  - {predicate: dependsOn, object: kt:aws-config}
  - {predicate: integratesWith, object: kt:aws-resource-access-manager}
  - {predicate: integratesWith, object: kt:aws-network-firewall}
  - {predicate: replacedBy, object: kt:aws-network-security-manager}
  - {predicate: governedBy, object: kt:firewall-manager-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "AWS Firewall Manager FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/firewall-manager/faqs
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "AWS Firewall Manager prerequisites"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/waf/latest/developerguide/fms-prereq.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "AWS Firewall Manager quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/waf/latest/developerguide/fms-limits.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "AWS Firewall Manager pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/firewall-manager/pricing
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "AWS Network Security Manager and AWS Firewall Manager"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/network-security-manager/latest/devguide/firewall-manager.html
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "AWS Network Security Manager is now generally available in US East (N. Virginia) Region"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/network-security-manager-us-east-va/
    type: official-blog
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS Firewall Manager

AWS Firewall Manager is a security management service for centrally configuring and managing firewall rules across the accounts and applications in an AWS Organizations organization. Its job is to keep newly created resources compliant by enforcing a common set of security rules automatically, rather than relying on each account team to attach protections by hand.[^s1] It is the central policy layer above individual firewall services, not a firewall itself: the actual filtering is done by the services it configures.

## How it works

Firewall Manager supports policies for AWS WAF, AWS Shield Advanced, VPC security groups, AWS Network Firewall, Amazon Route 53 Resolver DNS Firewall and third-party firewalls from AWS Marketplace, and it can also manage network ACLs for VPC subnets.[^s1] A policy names the protection (for example a set of WAF rule groups) and a scope: accounts, organizational units, resource types, tags or resource sets. Each policy runs in one of two modes. With automatic remediation, Firewall Manager watches for policy drift and applies the rules to non-compliant resources; in manual mode it creates the policy and rules in each account but only reports compliance, leaving enforcement to you.[^s1]

Setup follows a fixed order. You join AWS Organizations, designate a Firewall Manager default administrator account, enable AWS Config, subscribe to any Marketplace firewalls you plan to use, enable AWS RAM resource sharing for Network Firewall and DNS Firewall policies, and opt in to any Regions that are disabled by default.[^s2] The FAQ adds that the organization must have all features enabled and that AWS Config must be on in each member account.[^s1]

## Quotas, pricing and gotchas

Policies are Regional: a policy can only include resources in its own Region, so a multi-Region estate needs a policy per Region.[^s1] As of 2026-11, the default quota is 50 Firewall Manager policies per organization (adjustable; the Global scope and US East (N. Virginia) share this limit), and a policy can cover up to 2,500 accounts when it uses no explicit include or exclude lists, but only 200 when it does (both adjustable). An organization can have at most 10 Firewall Manager administrators, one default plus up to nine more, and that limit is fixed.[^s3] AWS WAF policies are also capped at a fixed 5,000 web ACL capacity units for their rule groups.[^s3]

Firewall Manager charges a monthly fee for each protection policy in each Region. As of 2026-11, the pricing page's examples use $100 per policy per month, while noting that some Regions charge more than $100. Shield Advanced subscribers pay no policy fee.[^s4] The bill does not stop at that fee. Firewall Manager creates two AWS Config rules per policy per account, so Config charges for configuration items and rule evaluations scale with the number of accounts. The WAF web ACLs, Network Firewall endpoints or DNS Firewall queries it deploys are billed at their own rates.[^s4]

## Lifecycle: Network Security Manager

AWS Network Security Manager became generally available in US East (N. Virginia) on September 24, 2026. It also deploys organization-wide protections, with drift detection and remediation, and at launch it supports AWS WAF and Shield Advanced, with Network Firewall support announced as coming later.[^s6] AWS documentation states that Network Security Manager is replacing Firewall Manager, which will eventually stop being sold and then stop operating; no timeline has been published.[^s5] Until then the two services run side by side and independently. Existing Firewall Manager policies and the firewalls they created keep working, even after Firewall Manager is retired, unless you terminate them. Where a Network Security Manager policy covers the same resources, Network Security Manager wins during remediation. A migration path that copies and translates Firewall Manager policies is planned but not yet available.[^s5]

## When to use it (and when not)

Use Firewall Manager when a central security team must guarantee baseline WAF, Shield Advanced, security group or Network Firewall controls across many accounts, including accounts created later. That is the problem it solves that per-account WAF or security group configuration does not.[^s1] For a single account, or a handful of resources, attaching WAF web ACLs directly is simpler and avoids the per-policy and AWS Config charges.[^s4] New organization-wide WAF or Shield Advanced deployments should weigh Network Security Manager, given AWS's stated direction. Firewall Manager remains the option for policy types the newer service does not yet cover at launch, such as security groups, network ACLs and DNS Firewall.[^s5][^s6]

## Connections

Firewall Manager depends on AWS Organizations for its account structure and on AWS Config for the compliance rules it evaluates. It integrates with AWS RAM to share Network Firewall and DNS Firewall configuration across accounts.[^s2] It centrally manages AWS WAF, AWS Shield Advanced, VPC security groups and AWS Network Firewall, which makes it a natural fit in a multi-account strategy.[^s1] Its quotas form a separate limit node.[^s3] Its policies are scoped to individual AWS Regions.[^s1] AWS has named Network Security Manager as its replacement.[^s5]

## Sources

[^s1]: Amazon Web Services, "AWS Firewall Manager FAQs". https://aws.amazon.com/firewall-manager/faqs (accessed 2026-11-16)
[^s2]: Amazon Web Services, "AWS Firewall Manager prerequisites". https://docs.aws.amazon.com/waf/latest/developerguide/fms-prereq.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "AWS Firewall Manager quotas". https://docs.aws.amazon.com/waf/latest/developerguide/fms-limits.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "AWS Firewall Manager pricing". https://aws.amazon.com/firewall-manager/pricing (accessed 2026-11-16)
[^s5]: Amazon Web Services, "AWS Network Security Manager and AWS Firewall Manager". https://docs.aws.amazon.com/network-security-manager/latest/devguide/firewall-manager.html (accessed 2026-11-16)
[^s6]: Amazon Web Services, "AWS Network Security Manager is now generally available in US East (N. Virginia) Region". https://aws.amazon.com/about-aws/whats-new/2026/09/network-security-manager-us-east-va/ (accessed 2026-11-16)
