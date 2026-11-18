---
id: kt:ec2-instance-metadata-service
type: Feature
title: Amazon EC2 Instance Metadata Service (IMDS)
aliases: [IMDS, IMDSv2, IMDSv1, EC2 instance metadata, Instance Metadata Service Version 2, EC2 metadata service]
domain: compute
summary: >-
  The link-local HTTP endpoint (169.254.169.254, or fd00:ec2::254 over IPv6) that every Amazon EC2
  instance uses to read its own metadata, user data and IAM role credentials; IMDSv2 adds
  session tokens obtained with a PUT request to defend against SSRF and open-proxy attacks.
verified: 2026-11-18
status: current
broader: [kt:amazon-ec2]
related: [kt:iam-roles, kt:aws-sts, kt:amazon-machine-images, kt:amazon-eks]
relations:
  - {predicate: governedBy, object: kt:service-control-policies}
  - {predicate: configuredVia, object: kt:declarative-policies}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: dependsOn, object: kt:aws-nitro-system}
sources:
  - id: s1
    title: "Use the Instance Metadata Service to access instance metadata"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Access instance metadata for an EC2 instance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.md
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Configure the Instance Metadata Service options"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-options.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Transition to using Instance Metadata Service Version 2"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-metadata-transition-to-version-2.md
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Add defense in depth against open firewalls, reverse proxies, and SSRF vulnerabilities with enhancements to the EC2 Instance Metadata Service"
    publisher: Amazon Web Services (AWS Security Blog)
    url: https://aws.amazon.com/blogs/security/defense-in-depth-open-firewalls-reverse-proxies-ssrf-vulnerabilities-ec2-instance-metadata-service/
    type: official-blog
    accessed: 2026-11-18
  - id: s6
    title: "Amazon EC2 Instance Metadata Service IMDSv2 by default"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/amazon-ec2-instance-metadata-service-imdsv2-by-default
    type: official-blog
    accessed: 2026-11-18
  - id: s7
    title: "Set IMDSv2 as default for all new instance launches in your account"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2024/03/set-imdsv2-default-new-instance-launches
    type: official-blog
    accessed: 2026-11-18
created: 2026-11-18
---

# Amazon EC2 Instance Metadata Service (IMDS)

The Instance Metadata Service is how software running on an Amazon EC2 instance learns about the instance it is on: its AMI ID, instance type, private IP, network and placement details, security groups, optionally its tags, and the temporary credentials of the IAM role attached to it.[^s1][^s2] It is reached over HTTP at the link-local address `169.254.169.254`, or at `[fd00:ec2::254]` once the IPv6 endpoint is enabled, and those addresses answer only from inside the instance.[^s2] Besides `meta-data/`, the same endpoint serves `dynamic/` data such as the signed instance identity document, and `user-data`, the opaque launch payload that cloud-init or EC2Launch interpret.[^s2] Requests to IMDS are not billed.[^s2]

## IMDSv1 vs. IMDSv2

There are two ways to call the service. IMDSv1 is plain request/response: any GET to the address returns data. IMDSv2 is session-oriented: the caller first sends a `PUT` to `/latest/api/token` with a TTL header, receives a token, and then sends it in the `X-aws-ec2-metadata-token` header on each GET.[^s1] Token lifetimes range from one second to six hours, and a token is tied to the instance that issued it.[^s1] When tokens are set to required, a request with a missing or expired token gets `401 Unauthorized`.[^s1]

The design targets four common misconfigurations. Most open web application firewalls don't forward `PUT`; IMDSv2 refuses to issue tokens for requests that carry an `X-Forwarded-For` header, which reverse proxies usually add; a secret token defeats most server-side request forgery (SSRF) flaws even where an attacker controls headers; and the token response is sent with an IP TTL (hop limit) of 1, so it can't travel past a misconfigured router, NAT or tunnel.[^s5][^s1] AWS published the design in November 2019.[^s5]

## Configuration options and precedence

Each instance has five metadata options: IMDS on or off; the IPv6 endpoint on or off (Nitro-based instances in IPv6-capable subnets only); metadata version (`httpTokens` = `optional` allows v1 and v2, `required` allows v2 only); the PUT response hop limit, from 1 to 64; and whether tags are exposed in metadata.[^s3] Values can come from three places, evaluated per option: settings given at launch (or in a launch template) win, then the account's per-Region defaults, then the AMI, which only influences the version and hop limit.[^s3] An AMI registered with `ImdsSupport` set to `v2.0` yields token-required and a hop limit of 2; Amazon Linux 2023 ships that way.[^s3][^s4] All options can be changed later on a running or stopped instance unless an IAM policy or SCP forbids it, and requiring IMDSv2 takes effect without a restart.[^s3][^s4]

## Quotas and gotchas

As documented on 2026-11-18 (the docs state them per instance rather than per Region, and list no way to raise them):[^s2]

- Traffic to link-local services, which covers IMDS together with Route 53 Resolver DNS, Amazon Time Sync NTP and Windows licensing, shares a limit of **1024 packets per second** per instance.[^s2]
- IMDS also throttles per instance and caps simultaneous connections; cache role credentials until close to expiry rather than fetching them per request, and retry with exponential backoff.[^s2]
- Containers count as an extra network hop. With a hop limit of 1, SDKs inside a container may get no IMDSv2 response or fall back slowly to IMDSv1; AWS suggests passing settings to the container directly or raising the hop limit to 2.[^s2]
- User data is capped at 16 KB raw, before base64 encoding, is not copied into AMIs made from the instance, and is not rerun when changed on a stopped Linux instance.[^s2]
- Turning IMDS off or requiring v2 breaks any agent that still relies on v1, so test first.[^s3]

## Moving a fleet to IMDSv2

AWS's recommended path has five steps: find instances still allowing v1, update software, require v2 on existing and new instances, make v2 the default, then enforce it.[^s4] The CloudWatch metrics `ImdsV1Enabled`, `MetadataNoToken` (v1 calls still succeeding) and `MetadataNoTokenRejected` (v1 calls refused after the switch) measure progress, and the open-source IMDS Packet Analyzer pinpoints which process is making v1 calls.[^s4] Since 2024-03-25 you can set IMDSv2-only as the account default per Region, including in AWS GovCloud (US); existing instances are unaffected.[^s7] Account-level enforcement goes further: launches configured with v1 fail, and v1 cannot be re-enabled on instances that already disabled it.[^s4] AWS also moved console Quick Start launches to IMDSv2-only in November 2023 and said newly released instance types would default to it from mid-2024.[^s6]

## Connections

IMDS is a feature of **Amazon EC2** and is how an instance receives temporary **AWS STS** credentials for its attached **IAM role**.[^s1][^s4] Its version and hop-limit defaults can come from the **Amazon Machine Image**.[^s3] Organizations govern it with **service control policies** and IAM policies using the condition keys `ec2:MetadataHttpTokens`, `ec2:MetadataHttpPutResponseHopLimit` and `ec2:MetadataHttpEndpoint`; adding `ec2:RoleDelivery` = `2.0` makes credentials obtained over IMDSv1 unusable for API calls.[^s4] AWS Organizations **declarative policies** can set the defaults and enforcement across every account.[^s4] Adoption is **monitored by** **Amazon CloudWatch**.[^s4] The IPv6 endpoint **depends on** the **AWS Nitro System**, and the hop-limit behaviour matters for container hosts such as **Amazon EKS** worker nodes.[^s2][^s3]

## Sources
[^s1]: Amazon Web Services, "Use the Instance Metadata Service to access instance metadata". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Access instance metadata for an EC2 instance". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.md (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Configure the Instance Metadata Service options". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-options.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Transition to using Instance Metadata Service Version 2". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-metadata-transition-to-version-2.md (accessed 2026-11-18)
[^s5]: Amazon Web Services, AWS Security Blog, "Add defense in depth against open firewalls, reverse proxies, and SSRF vulnerabilities with enhancements to the EC2 Instance Metadata Service" (2019-11-19). https://aws.amazon.com/blogs/security/defense-in-depth-open-firewalls-reverse-proxies-ssrf-vulnerabilities-ec2-instance-metadata-service/ (accessed 2026-11-18)
[^s6]: Amazon Web Services, AWS News Blog, "Amazon EC2 Instance Metadata Service IMDSv2 by default" (2023-11-06). https://aws.amazon.com/blogs/aws/amazon-ec2-instance-metadata-service-imdsv2-by-default (accessed 2026-11-18)
[^s7]: Amazon Web Services, What's New with AWS, "Set IMDSv2 as default for all new instance launches in your account" (2024-03-25). https://aws.amazon.com/about-aws/whats-new/2024/03/set-imdsv2-default-new-instance-launches (accessed 2026-11-18)
