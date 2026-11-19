---
id: kt:aws-network-firewall
type: Service
title: AWS Network Firewall
aliases: [Network Firewall, AWS NFW, ANFW]
domain: security
summary: >-
  AWS's managed, VPC-level network firewall and intrusion prevention service: traffic routed through
  zonal firewall endpoints passes a stateless rules engine and a Suricata-compatible stateful engine
  for Layer 3–7 filtering, domain filtering, TLS inspection and managed threat signatures, scaling to
  100 Gbps per Availability Zone on Gateway Load Balancer.
verified: 2026-11-19
status: current
broader: [kt:aws-security-services]
related: [kt:amazon-vpc, kt:aws-waf, kt:security-groups-vs-network-acls]
relations:
  - {predicate: runsOn, object: kt:gateway-load-balancer}
  - {predicate: integratesWith, object: kt:aws-transit-gateway}
  - {predicate: integratesWith, object: kt:aws-firewall-manager}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: governedBy, object: kt:network-firewall-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "AWS Network Firewall FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/network-firewall/faqs
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "How AWS Network Firewall filters network traffic"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/network-firewall/latest/developerguide/firewall-policy-processing.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "AWS Network Firewall quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/network-firewall/latest/developerguide/quotas.html
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "AWS Network Firewall pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/network-firewall/pricing/
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "AWS Network Firewall announces new price reductions"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/02/aws-network-firewall-new-price-reduction/
    type: official-blog
    accessed: 2026-11-19
  - id: s6
    title: "AWS Network Firewall updates default drop action to improve connection reliability"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/jp/about-aws/whats-new/2026/06/aws-network-firewall-updates-default-drop-action/
    type: official-blog
    accessed: 2026-11-19
  - id: s7
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-19
  - id: s8
    title: "In-scope AWS services – AWS Certified Solutions Architect – Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-19
created: 2026-11-19
---

# AWS Network Firewall

AWS Network Firewall is a managed network security service for Amazon VPCs. It combines a stateful firewall, stateless access-control rules, signature-based intrusion prevention and filtering of known-bad domains and URLs, while AWS runs, scales and patches the underlying infrastructure.[^s1] It fills the gap between per-resource controls such as security groups and application-layer filtering such as AWS WAF: it inspects every flow routed through it at Layers 3–7, across a whole VPC or a whole network.[^s1]

## How it works

You create a **firewall** in a VPC and give it a dedicated subnet (at least /28) in each Availability Zone you want to protect; Network Firewall places a **firewall endpoint** there, and only traffic that your route tables send to that endpoint is inspected.[^s1] The endpoint is delivered like a PrivateLink endpoint service and is built on Gateway Load Balancer, which is why its network interface shows the type `gateway_load_balancer_endpoint`.[^s1] Two deployment models are common: **distributed**, with a firewall in each VPC, and **centralized**, with an inspection VPC behind AWS Transit Gateway. The native Transit Gateway attachment keeps routing symmetric to the same zonal endpoint and can inspect traffic between attached VPCs as well as traffic from internet gateways, NAT gateways, VPN, Direct Connect and PrivateLink.[^s1]

Each firewall uses exactly one **firewall policy**, which references rule groups.[^s3] Packets first hit the **stateless engine**, which evaluates rule groups and then rules in ascending priority order; a match can pass, drop or forward the packet to the stateful engine. If nothing matches, the policy's default stateless action applies, with a separate default for UDP fragments (fragments of other protocols are silently dropped).[^s2] Forwarded packets go to the **stateful engine**, which evaluates flows against Suricata-compatible rules, can inspect payloads as well as headers, and by default applies *action order* (pass rules, then drop, then alert).[^s2] Alerts are only recorded if logging is configured; logs can go to Amazon S3, Amazon Data Firehose or CloudWatch Logs, and per-endpoint CloudWatch metrics count received, passed and dropped packets.[^s1][^s2]

Optional layers include **TLS inspection** (decrypt, inspect and re-encrypt; TLS 1.1–1.3 are supported, but encrypted client hello and encrypted SNI are not), AWS managed rule groups for domains, IPs and threat signatures, an **active threat defense** group built on Amazon threat intelligence, and partner rule groups from AWS Marketplace.[^s1]

## Quotas, pricing and gotchas

As of 2026-11, quotas are per account per Region. Adjustable defaults include 5 firewalls, 20 firewall policies, 50 stateful and 50 stateless rule groups, and 30,000 stateful rules per firewall policy. Fixed limits include 20 rule groups of each kind per policy, a capacity of 30,000 per rule group, one TLS inspection configuration per policy, and 100 Gbps of throughput per Availability Zone across a firewall's endpoints, beyond which traffic is dropped.[^s3] The FAQ warns that an oversubscribed endpoint degrades performance and recommends load-testing with your own rule set.[^s1] AWS's SLA targets 99.99% uptime.[^s1]

As of 2026-11 in US East (N. Virginia), the pricing page's examples use $0.395 per firewall-endpoint hour per Availability Zone, $0.158 per hour for each secondary endpoint, and $0.065 per GB processed. A NAT gateway on the same path has its hourly and per-GB charges waived up to the firewall's usage.[^s4] On February 6, 2026, AWS extended that NAT gateway discount to secondary endpoints and removed the extra per-GB surcharge for TLS advanced inspection.[^s5] Rates vary by Region, and active threat defense adds its own per-GB charge.[^s1][^s4]

Stateful inspection relies on both directions of a flow reaching the same zonal endpoint; in centralized designs the native Transit Gateway attachment maintains that symmetric routing for you, while manual attachments leave it to your route tables.[^s1] Another is drop behaviour on established flows. On June 22, 2026, AWS changed the default stateful action for new policies to "Application drop established (server-directed only)", because the earlier bidirectional default could drop legitimate server-to-client packets such as TCP keepalives and resets, causing intermittent errors. Existing policies are not changed automatically.[^s6] Network Firewall is also not a DDoS service. AWS points to Shield Advanced for volumetric attacks.[^s1]

## When to use it (and when not)

Use Network Firewall when you need centrally managed egress filtering (for example domain allow-lists), east-west inspection between VPCs, or IDS/IPS signatures in front of whole subnets. It is meant to run alongside security groups and AWS WAF, not replace them.[^s1] Network Firewall does not mitigate volumetric DDoS, and teams standardized on a third-party firewall can run AWS Marketplace appliances alongside it or instead.[^s1] In multi-account estates, AWS Firewall Manager deploys and governs Network Firewall policies across accounts, because the service itself is Regional.[^s1]

## Connections

Network Firewall belongs to the **AWS security services** family (**broader**). It protects **Amazon VPC** traffic and is **related to** AWS WAF and to **security groups vs. network ACLs**, the controls it complements.[^s1] It **runs on Gateway Load Balancer** and **integrates with AWS Transit Gateway** for centralized inspection and with **AWS Firewall Manager** for multi-account policy.[^s1] It is **monitored by Amazon CloudWatch** metrics and logs.[^s1] Its limits form the separate **Network Firewall quotas** node, which is **scoped to AWS Regions**.[^s3] AWS's FedRAMP services-in-scope list marks it for both the East/West and GovCloud (US) authorizations (**compliesWith** FedRAMP on AWS).[^s7] It is listed under Security, Identity, and Compliance in the in-scope services for the Solutions Architect – Associate (SAA-C03) exam (**coveredBy**).[^s8]

## Sources

[^s1]: Amazon Web Services, "AWS Network Firewall FAQs". https://aws.amazon.com/network-firewall/faqs (accessed 2026-11-19)
[^s2]: Amazon Web Services, "How AWS Network Firewall filters network traffic". https://docs.aws.amazon.com/network-firewall/latest/developerguide/firewall-policy-processing.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "AWS Network Firewall quotas". https://docs.aws.amazon.com/network-firewall/latest/developerguide/quotas.html (accessed 2026-11-19)
[^s4]: Amazon Web Services, "AWS Network Firewall pricing". https://aws.amazon.com/network-firewall/pricing/ (accessed 2026-11-19)
[^s5]: Amazon Web Services, "AWS Network Firewall announces new price reductions". https://aws.amazon.com/about-aws/whats-new/2026/02/aws-network-firewall-new-price-reduction/ (accessed 2026-11-19)
[^s6]: Amazon Web Services, "AWS Network Firewall updates default drop action to improve connection reliability" (What's New, Japanese edition). https://aws.amazon.com/jp/about-aws/whats-new/2026/06/aws-network-firewall-updates-default-drop-action/ (accessed 2026-11-19)
[^s7]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-19)
[^s8]: Amazon Web Services, "In-scope AWS services – AWS Certified Solutions Architect – Associate (SAA-C03)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-19)
