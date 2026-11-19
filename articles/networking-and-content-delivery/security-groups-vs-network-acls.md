---
id: kt:security-groups-vs-network-acls
type: Concept
title: Security groups vs network ACLs
aliases: [Security groups and network ACLs, Security groups vs NACLs, SG vs NACL, VPC security groups, Network access control lists]
domain: networking-and-content-delivery
summary: >-
  The two built-in traffic filters of an Amazon VPC: security groups are stateful, allow-only
  firewalls attached to resources, while network ACLs are stateless, ordered allow/deny rule
  lists attached to subnets. AWS treats security groups as the primary control and network ACLs
  as an optional, coarse-grained second layer.
verified: 2026-11-19
status: concept
broader: [kt:amazon-vpc]
related: [kt:subnets-and-route-tables, kt:security-pillar]
relations:
  - {predicate: partOf, object: kt:amazon-vpc}
  - {predicate: governedBy, object: kt:vpc-quotas}
  - {predicate: contrastsWith, object: kt:aws-network-firewall}
  - {predicate: integratesWith, object: kt:aws-shield}
  - {predicate: usedBy, object: kt:amazon-ec2}
sources:
  - id: s1
    title: "Infrastructure security in Amazon VPC - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/userguide/infrastructure-security.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Control traffic to your AWS resources using security groups - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Security group rules - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/userguide/security-group-rules.html
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "Control subnet traffic with network access control lists - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.md
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Amazon VPC quotas - Amazon Virtual Private Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Appendix_Limits.html
    type: official-docs
    accessed: 2026-11-19
  - id: s6
    title: "Security groups and network ACLs (BP5) - AWS Best Practices for DDoS Resiliency"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-best-practices-ddos-resiliency/security-groups-and-network-acls-bp5.html
    type: official-docs
    accessed: 2026-11-19
created: 2026-11-19
---

# Security groups vs network ACLs

Every Amazon VPC gives you two native packet filters. A **security group** controls the traffic that may reach and leave the resources it is associated with, such as an EC2 instance's network interface. A **network access control list (network ACL, NACL)** controls traffic entering and leaving a subnet. AWS says you can secure instances with security groups alone, and adds network ACLs as an optional extra layer of defense.[^s1][^s2] Neither carries an additional charge.[^s6]

## How they differ

AWS's own comparison comes down to five differences:[^s1]

| | Security group | Network ACL |
|---|---|---|
| Level | Instance (resource) level | Subnet level [^s1] |
| Applies to | Every resource associated with the group | Every instance in the associated subnets [^s1] |
| Rule types | Allow only | Allow and deny [^s1] |
| Evaluation | All rules are evaluated before a decision | Rules evaluated in ascending number order until one matches [^s1] |
| Return traffic | Allowed automatically (stateful) | Must be explicitly allowed (stateless) [^s1] |

**Security groups.** Each VPC comes with a default security group, and you can create more. Inbound rules name a source, protocol and port range; outbound rules name a destination. A resource can carry several groups, whose rules are combined into one set.[^s2][^s3] Responses to allowed inbound traffic may leave regardless of outbound rules.[^s2] A newly created group has no inbound rules, so nothing gets in until you add one, and a single outbound rule allowing all traffic, which you can remove.[^s3] A rule can reference another security group instead of a CIDR. This allows traffic from the private IP addresses of the network interfaces in that group, without copying its rules. Inbound references work within the VPC or across VPC peering or a transit gateway; outbound references only within the VPC or across peering.[^s3] Rule edits apply automatically to every associated resource.[^s3]

**Network ACLs.** Every subnet must be associated with exactly one network ACL; without an explicit association it uses the VPC's default ACL. One ACL can serve many subnets, and associating a new one replaces the old association.[^s4] Rules are numbered 1–32766 and checked from the lowest number up; the first match is applied and evaluation stops, so AWS recommends numbering in gaps of 10 or 100 to leave room for inserts. Rules are evaluated only as traffic crosses the subnet boundary, not for traffic routed inside a subnet. Because the ACL is stateless, the return leg of every allowed flow needs its own rule.[^s4]

## Quotas and gotchas

Default Amazon VPC quotas as of 2026-11 (per account, per Region; all adjustable):[^s5]

- Security groups per Region: 2,500.[^s5]
- Inbound or outbound rules per security group: 60, enforced separately for inbound and outbound and for IPv4 and IPv6.[^s5]
- Security groups per network interface: 5, adjustable up to 16. Rules per group multiplied by groups per interface cannot exceed 1,000.[^s5]
- Network ACLs per VPC: 200.[^s5]
- Rules per network ACL: 20, raisable to 40 inbound and 40 outbound, though AWS warns network performance might be affected.[^s5]

A rule that references a security group counts as one rule, but a customer-managed prefix list counts as its maximum size.[^s3]

Neither filter sees everything. Both ignore traffic to and from Amazon DNS, Amazon DHCP, EC2 instance metadata, ECS task metadata endpoints, Windows license activation, the Amazon Time Sync Service and the reserved addresses of the default VPC router.[^s2][^s4] Neither can block DNS queries to the Route 53 Resolver; AWS points to Route 53 Resolver DNS Firewall for that.[^s3][^s4] When traffic between subnets passes through a middlebox appliance, security group references don't work; rules must use the peer's private IP or subnet CIDR instead.[^s3]

## When to use which

AWS recommends security groups as the primary mechanism. Keep the number of groups small, restrict SSH (22) and RDP (3389) to known ranges rather than 0.0.0.0/0, avoid wide port ranges, and limit who can modify groups through IAM.[^s1][^s2] Add network ACLs for stateless, coarse-grained guard rails at the subnet edge. Examples are explicitly denying a subset of traffic, or backstopping an instance launched with the wrong security group.[^s1] For DDoS resilience, AWS suggests ACL deny rules for known-bad CIDRs or protocols, such as blocking all UDP for a TCP-only application.[^s6]

## Connections

Both filters are **part of Amazon VPC**, and their limits are **governed by VPC quotas**.[^s1][^s5] They're **used by Amazon EC2** and other resources with network interfaces in a VPC.[^s2] They **contrast with AWS Network Firewall**, a separate firewall service you deploy, whereas these two filters are built into every VPC at no extra charge.[^s6] ACLs **integrate with AWS Shield** Advanced: for a protected Elastic IP, Shield reads the subnet's network ACL and enforces it at the AWS network border.[^s6] They relate to **subnets and route tables**, the subnet boundary that ACLs guard,[^s4] and to the Well-Architected **Security pillar**'s defense-in-depth guidance.[^s1]

## Sources
[^s1]: Amazon Web Services, "Infrastructure security in Amazon VPC - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/userguide/infrastructure-security.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Control traffic to your AWS resources using security groups - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Security group rules - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/userguide/security-group-rules.html (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Control subnet traffic with network access control lists - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.md (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Amazon VPC quotas - Amazon Virtual Private Cloud". https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Appendix_Limits.html (accessed 2026-11-19)
[^s6]: Amazon Web Services, "Security groups and network ACLs (BP5) - AWS Best Practices for DDoS Resiliency". https://docs.aws.amazon.com/whitepapers/latest/aws-best-practices-ddos-resiliency/security-groups-and-network-acls-bp5.html (accessed 2026-11-19)
