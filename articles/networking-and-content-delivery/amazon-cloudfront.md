---
id: kt:amazon-cloudfront
type: Service
title: Amazon CloudFront
aliases: [CloudFront, AWS CloudFront]
domain: networking-and-content-delivery
summary: >-
  AWS's global content delivery network (CDN), which caches and serves content and APIs from edge
  points of presence and regional edge caches close to viewers, with origins such as Amazon S3 or
  HTTP servers, edge compute, and pay-as-you-go or flat-rate pricing.
verified: 2026-11-08
status: current
broader: [kt:aws-networking-and-content-delivery]
related: [kt:amazon-route-53]
relations:
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: securedBy, object: kt:aws-waf}
  - {predicate: scopedTo, object: kt:edge-locations}
  - {predicate: governedBy, object: kt:cloudfront-quotas}
  - {predicate: hasPart, object: kt:cloudfront-functions}
sources:
  - id: s1
    title: "How CloudFront delivers content - Amazon CloudFront"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/nl_nl/AmazonCloudFront/latest/DeveloperGuide/HowCloudFrontWorks.html
    type: official-docs
    accessed: 2026-11-08
  - id: s2
    title: "Quotas - Amazon CloudFront Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/cloudfront-limits.md
    type: official-docs
    accessed: 2026-11-08
  - id: s3
    title: "Amazon CloudFront pay-as-you-go pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/cloudfront/pricing/pay-as-you-go
    type: official-docs
    accessed: 2026-11-08
  - id: s4
    title: "Introducing flat-rate pricing plans with no overages"
    publisher: Amazon Web Services (Networking & Content Delivery Blog)
    url: https://aws.amazon.com/blogs/networking-and-content-delivery/introducing-flat-rate-pricing-plans-with-no-overages
    type: official-blog
    accessed: 2026-11-08
  - id: s5
    title: "Restrict access to an Amazon S3 origin - Amazon CloudFront"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html
    type: official-docs
    accessed: 2026-11-08
  - id: s6
    title: "Differences between CloudFront Functions and Lambda@Edge - Amazon CloudFront"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/edge-functions-choosing.md
    type: official-docs
    accessed: 2026-11-08
created: 2026-11-08
---

# Amazon CloudFront

Amazon CloudFront is AWS's content delivery network. You create a *distribution* that points at one or more origins (for example an Amazon S3 bucket or any HTTP server), and CloudFront serves viewers from a global network of points of presence (POPs), caching responses so repeat requests never reach the origin.[^s1] It accelerates static assets, whole websites, video and APIs, and is also the front door for edge security and edge compute.

## How it works

DNS routes each viewer request to the POP that can serve it best, usually the lowest-latency one. On a cache hit the POP answers directly. On a miss, CloudFront applies the distribution's settings, forwards the request toward the origin, and starts streaming the object to the viewer as soon as the first byte arrives, caching it for later requests.[^s1]

Between POPs and origins sits a tier of larger **regional edge caches**. A POP that misses usually asks its nearest regional edge cache first; if that also misses, the object is fetched from the origin and stored in both tiers, so all POPs in a geography share one cache and the origin sees fewer requests. Some traffic skips this tier: proxy methods (PUT, POST, PATCH, OPTIONS, DELETE), requests CloudFront judges dynamic, and requests for an S3 origin in the same Region as the regional cache go straight to the origin. An invalidation clears objects from both tiers.[^s1]

Behaviour is configured per path pattern through *cache behaviors*, which reference cache policies (what goes into the cache key), origin request policies and response headers policies.[^s2]

## Edge compute

CloudFront offers two ways to run code on requests. **CloudFront Functions** run JavaScript in submillisecond time on viewer request and viewer response events, scale to millions of requests per second, and suit cache-key normalisation, header rewrites, redirects and token checks; code is capped at 10 KB and has no network or request-body access. **Lambda@Edge** runs Node.js or Python on all four events (viewer and origin request/response), for up to 30 seconds, with network, file system and body access, but scales to about 10,000 requests per second per Region.[^s6]

## Quotas, pricing and gotchas

Default quotas (as of 2026-11, from the CloudFront Developer Guide): 500 distributions per account, 75 cache behaviors and 100 origins per distribution, 150 Gbps and 250,000 requests per second per distribution, all adjustable on request. The rate quotas do not apply to distributions on flat-rate plans. The largest cacheable GET response is 50 GB and origin response timeout ranges from 1 to 120 seconds (adjustable). Invalidations run at up to 150 paths or tags per second.[^s2]

Pay-as-you-go pricing (as of 2026-11) includes an always-free tier of 1 TB data transfer out, 10 million HTTP(S) requests and 2 million CloudFront Function invocations per month. Beyond that, transfer to viewers in the US, Mexico and Canada costs $0.085 per GB for the next 9 TB, and US HTTPS requests cost $0.0100 per 10,000. Origin fetches from AWS origins are free; invalidations are free for the first 1,000 paths or tags a month, then $0.005 each. Price classes (All, 200, 100) let you exclude costlier geographies at the expense of latency for viewers there.[^s3]

Since November 2025, **flat-rate plans** bundle CloudFront with AWS WAF, DDoS protection, Route 53 DNS, CloudWatch Logs ingestion, edge compute and S3 storage credits: Free ($0, 1 million requests, 100 GB), Pro ($15), Business ($200) and Premium ($1,000) per month, the paid tiers with up to 50 TB of transfer. There are no overage charges; going past the allowance can instead degrade performance, for example by serving from fewer locations. Each plan covers one distribution, and you can mix plans with pay-as-you-go distributions.[^s4]

For private S3 origins, use **origin access control (OAC)**; AWS labels the older origin access identity (OAI) legacy. OAC supports all Regions, including opt-in Regions launched after 2022, objects encrypted with SSE-KMS, and PUT/DELETE requests to S3.[^s5] Placing one distribution in front of another is discouraged, and a chain of more than two returns 403 errors.[^s2]

## When to use it

Use CloudFront whenever viewers are geographically spread and content is cacheable, or when you want TLS termination, WAF filtering and edge logic in front of an origin. For a workload with predictable traffic, a flat-rate plan caps the bill; for spiky or very large traffic, pay-as-you-go with a price class may be cheaper.[^s3][^s4] Pick CloudFront Functions for lightweight per-request tweaks and Lambda@Edge only when you need network calls, the request body or longer runtimes.[^s6]

## Connections

CloudFront sits in AWS's networking and content delivery family and runs on the global network of **edge locations** and regional edge caches.[^s1] Its most common origin is **Amazon S3**, locked down with OAC.[^s5] Flat-rate plans bundle **AWS WAF** protection and **Amazon Route 53** DNS with the distribution.[^s4] **CloudFront Functions** is part of the service, and its limits, like the rest of the service's, are listed on the **CloudFront quotas** page.[^s2][^s6]

## Sources

[^s1]: Amazon Web Services, "How CloudFront delivers content". https://docs.aws.amazon.com/nl_nl/AmazonCloudFront/latest/DeveloperGuide/HowCloudFrontWorks.html (accessed 2026-11-08)
[^s2]: Amazon Web Services, "Quotas - Amazon CloudFront". https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/cloudfront-limits.md (accessed 2026-11-08)
[^s3]: Amazon Web Services, "Amazon CloudFront pay-as-you-go pricing". https://aws.amazon.com/cloudfront/pricing/pay-as-you-go (accessed 2026-11-08)
[^s4]: Amazon Web Services, "Introducing flat-rate pricing plans with no overages", Networking & Content Delivery Blog, 2025-11-18. https://aws.amazon.com/blogs/networking-and-content-delivery/introducing-flat-rate-pricing-plans-with-no-overages (accessed 2026-11-08)
[^s5]: Amazon Web Services, "Restrict access to an Amazon S3 origin". https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html (accessed 2026-11-08)
[^s6]: Amazon Web Services, "Differences between CloudFront Functions and Lambda@Edge". https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/edge-functions-choosing.md (accessed 2026-11-08)
