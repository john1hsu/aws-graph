---
id: kt:amazon-route-53
type: Service
title: Amazon Route 53
aliases: [Route 53, Route53, AWS Route 53]
domain: networking-and-content-delivery
summary: >-
  AWS's managed DNS service: authoritative DNS hosting in public and private hosted zones with
  routing policies (weighted, latency, failover, geolocation and more), health checks, domain
  registration, and resolver services for VPC and hybrid DNS.
verified: 2026-11-09
status: current
broader: [kt:aws-networking-and-content-delivery]
related: [kt:amazon-cloudfront, kt:disaster-recovery-strategies]
relations:
  - {predicate: integratesWith, object: kt:elastic-load-balancing}
  - {predicate: hasPart, object: kt:route-53-resolver}
  - {predicate: governedBy, object: kt:route-53-quotas}
sources:
  - id: s1
    title: "Choosing a routing policy - Amazon Route 53"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "Choosing between alias and non-alias records - Amazon Route 53"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.md
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Quotas - Amazon Route 53"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/DNSLimitations.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Amazon Route 53 pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/route53/pricing/
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "Announcing Amazon Route 53 accelerated recovery for managing public DNS records"
    publisher: AWS Networking & Content Delivery Blog
    url: https://aws.amazon.com/blogs/networking-and-content-delivery/announcing-amazon-route-53-accelerated-recovery-for-managing-public-dns-records
    type: official-blog
    accessed: 2026-11-09
  - id: s6
    title: "Amazon Route 53 Global Resolver is now generally available"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/03/amazon-route-53-global-resolver
    type: official-blog
    accessed: 2026-11-09
created: 2026-11-09
---

# Amazon Route 53

Amazon Route 53 is AWS's managed Domain Name System (DNS) service. It hosts authoritative DNS for your domains in *hosted zones*, answers queries using a choice of routing policies, checks endpoint health so that unhealthy targets drop out of answers, registers domain names, and provides resolver services for DNS inside VPCs and across hybrid networks.[^s1][^s4] Its records can point at AWS resources such as load balancers and CloudFront distributions through *alias records*, a Route 53–specific DNS extension.[^s2]

## How it works

A **public hosted zone** answers queries from the internet; a **private hosted zone** answers only inside the VPCs associated with it.[^s1][^s4] Each record carries a routing policy. Route 53 offers eight: *simple* (one resource), *failover* (active-passive), *geolocation* (by the user's location), *geoproximity* (by resource location, with optional traffic shifting), *latency* (to the AWS Region with the best latency), *IP-based* (by the client's source IP ranges), *multivalue answer* (up to eight healthy records chosen at random) and *weighted* (traffic split in proportions you set). AWS documents every policy except IP-based routing as usable in private hosted zones.[^s1]

**Alias records** let a name resolve directly to an AWS resource — Elastic Load Balancing load balancers, CloudFront distributions, API Gateway APIs, S3 static-website buckets, VPC interface endpoints, Global Accelerator accelerators and more — or to another record in the same zone. Unlike a CNAME, an alias can sit at the zone apex (example.com itself), it tracks the target's changing IP addresses automatically, and it answers only for the record type you created (such as A or AAAA). You can't set a TTL on an alias to an AWS resource; Route 53 uses that resource's default.[^s2]

**Resolver** handles the other direction: recursive DNS for VPCs, with inbound and outbound endpoints and forwarding rules to connect on-premises DNS, plus Resolver DNS Firewall for filtering outbound queries.[^s3][^s4] **Route 53 Global Resolver**, generally available since 9 March 2026 in 30 Regions, adds an internet-reachable anycast resolver for authorized clients outside AWS (offices, remote users). It resolves both public domains and private hosted zone names and filters malicious domains, including DNS tunneling and domain-generation-algorithm threats.[^s6]

## Quotas, pricing and gotchas

Default quotas (as of 2026-11, global): 500 hosted zones per account, 10,000 records per hosted zone, 200 active health checks, 50 traffic policies and 20 domain registrations for new accounts — all adjustable through Service Quotas, which for Route 53 you reach from the US East (N. Virginia) console. Resolver quotas are per account per Region: 4 endpoints, 6 IP addresses per endpoint and 1,000 rules, also adjustable. A single ChangeResourceRecordSets call accepts at most 1,000 record values (UPSERT counts each twice). Only one change request per hosted zone can be in flight; a concurrent one fails with `PriorRequestNotComplete`, so automation needs retries with exponential backoff.[^s3]

Pricing (as of 2026-11, public list prices): a hosted zone costs $0.50 per month for each of the first 25 and $0.10 per month after that; standard queries cost $0.40 per million for the first billion per month, with latency, geo and IP-based queries priced higher. Alias queries to supported AWS resources are free, and private hosted zone queries are free. Basic health checks cost $0.50 per month for AWS endpoints and $0.75 for non-AWS endpoints. Resolver endpoints cost $0.125 per ENI per hour, and Traffic Flow policy records cost $50.00 per month each.[^s4] A CNAME pointing at another Route 53 record is billed as two queries, which is one reason to prefer aliases for AWS targets.[^s2]

The data plane that answers DNS queries is separate from the control plane that changes records, and the control plane historically ran only in us-east-1. Since November 2025, public hosted zones can opt in to **accelerated recovery**, at no extra charge, which fails the control plane over to us-west-2 with a 60-minute recovery time objective. Changes submitted mid-disruption may be stranded and need resubmitting.[^s5] DR designs should therefore rely on health-check-driven failover (a data-plane action) rather than on editing records during an incident.[^s1][^s5]

## When to use it (and when not)

Use Route 53 for domains fronting AWS workloads, especially when you want free, apex-capable aliases to load balancers or CloudFront, or DNS-level traffic steering across Regions.[^s1][^s2] Because answers are cached for the record's TTL, pointing an alias at a load balancer or AWS Global Accelerator accelerator lets AWS change the underlying addresses without any DNS edits on your side.[^s2] Teams standardized on another DNS provider can still point CNAMEs at AWS names, but lose apex aliases and pay for both lookups when the target is also in Route 53.[^s2]

## Connections

Route 53 belongs to AWS's **networking and content delivery** family. Alias records route to **Amazon CloudFront** distributions and **Elastic Load Balancing** load balancers, and alias queries to them carry no query charge.[^s2][^s4] Its failover, latency and weighted policies are the DNS traffic-shifting mechanism behind multi-Region **disaster recovery strategies**, made more dependable by accelerated recovery of the control plane.[^s1][^s5] **Route 53 Resolver** is the part of the service that handles VPC and hybrid DNS, and the service's account and API limits are listed on the **Route 53 quotas** page.[^s3]

## Sources

[^s1]: Amazon Web Services, "Choosing a routing policy - Amazon Route 53". https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html (accessed 2026-11-09)
[^s2]: Amazon Web Services, "Choosing between alias and non-alias records - Amazon Route 53". https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.md (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Quotas - Amazon Route 53". https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/DNSLimitations.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Amazon Route 53 pricing". https://aws.amazon.com/route53/pricing/ (accessed 2026-11-09)
[^s5]: AWS Networking & Content Delivery Blog, "Announcing Amazon Route 53 accelerated recovery for managing public DNS records". https://aws.amazon.com/blogs/networking-and-content-delivery/announcing-amazon-route-53-accelerated-recovery-for-managing-public-dns-records (accessed 2026-11-09)
[^s6]: Amazon Web Services, "Amazon Route 53 Global Resolver is now generally available". https://aws.amazon.com/about-aws/whats-new/2026/03/amazon-route-53-global-resolver (accessed 2026-11-09)
