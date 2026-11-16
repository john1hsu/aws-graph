---
id: kt:aws-cloud-map
type: Service
title: AWS Cloud Map
aliases: [Cloud Map, Route 53 Auto Naming]
domain: networking-and-content-delivery
summary: >-
  AWS's managed service-discovery registry: you register application resources (IPs, URLs, ARNs) under
  friendly names in namespaces, and clients find healthy endpoints through the DiscoverInstances API or,
  for DNS namespaces, through Route 53 DNS queries.
verified: 2026-11-16
status: current
broader: [kt:aws-networking-and-content-delivery]
related: [kt:amazon-ecs, kt:amazon-eks, kt:container-networking-on-aws, kt:ecs-quotas]
relations:
  - {predicate: dependsOn, object: kt:amazon-route-53}
  - {predicate: integratesWith, object: kt:amazon-ecs}
  - {predicate: integratesWith, object: kt:aws-resource-access-manager}
  - {predicate: governedBy, object: kt:cloud-map-quotas}
  - {predicate: alternativeTo, object: kt:elastic-load-balancing}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "AWS Cloud Map FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/cloud-map/faqs/
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Creating an AWS Cloud Map namespace to group application services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cloud-map/latest/dg/namespaces-values.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "AWS Cloud Map service quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cloud-map/latest/dg/cloud-map-limits.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "AWS Cloud Map pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/cloud-map/pricing
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "Use service discovery to connect Amazon ECS services with DNS names"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-discovery.html
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "Amazon ECS Service Connect configuration overview"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-concepts.md
    type: official-docs
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS Cloud Map

AWS Cloud Map is a resource-discovery service: you give the pieces of an application custom names, register where they live, and Cloud Map keeps those locations current as instances come and go. It grew out of Route 53 Auto Naming (released December 2017), which only automated DNS names for IP-based resources; Cloud Map added a registry that can also hold URLs and ARNs, API-based discovery, attribute filtering and faster propagation, and existing Auto Naming resources were upgraded automatically.[^s1]

## How it works

The model has three levels. A **namespace** groups services and decides how they can be discovered; a **service** is an application component; and the registered **instances** (the FAQ calls them resources) are the infrastructure behind it, such as EC2 instances, ECS tasks, DynamoDB tables, SQS queues or Lambda functions. Instances can carry custom attributes, which clients use to narrow discovery results, and IP-based instances can be health-checked so that unhealthy endpoints drop out of answers.[^s1]

The namespace type is the key design choice. An **HTTP namespace** supports discovery only through the `DiscoverInstances` API. A **private DNS namespace** adds DNS queries from within a VPC and requires a VPC at creation; Cloud Map creates a private Route 53 hosted zone with the namespace's name, holding records named `service-name.namespace-name`. A **public DNS namespace** creates a public hosted zone and must use a registered domain name you own. Namespace creation is asynchronous, so automation should poll `ListOperations`.[^s2] DNS discovery returns IPv4 or IPv6 addresses (optionally with ports), while API discovery can also return URLs and ARNs.[^s1]

## Container integration

Amazon ECS has two ways to use Cloud Map. Classic **ECS service discovery** registers each task as a Cloud Map instance with attributes such as its IP, port and Availability Zone; tasks in `awsvpc` mode can get A or SRV records (AAAA in dual-stack subnets), while `bridge` and `host` modes support only SRV records. DNS records always hold the task's private IP, even in a public namespace, and traffic goes straight to tasks rather than through any load balancer in front of the service.[^s5] **ECS Service Connect** instead uses an HTTP namespace and creates no Route 53 hosted zones; ECS injects a proxy into each task that does round-robin balancing, outlier detection and retries, and a cluster can use a namespace shared from another account through AWS Resource Access Manager. AWS warns against hand-editing the Cloud Map services Service Connect creates.[^s6] On EKS, an open-source Cloud Map provider for the ExternalDNS connector registers pod endpoints as they start and stop.[^s1]

## Quotas, pricing and gotchas

Default quotas (per account, per Region, as of 2026-11): 50 namespaces per Region (adjustable), 2,000 instances per namespace (adjustable), 1,000 instances per service (not adjustable), 30 custom attributes per instance (not adjustable), and `DiscoverInstances` at a steady rate of 1,000 and burst of 2,000 requests (adjustable). Every DNS namespace consumes a Route 53 hosted zone from that quota, and raising instances in DNS namespaces also means raising Route 53 records-per-zone limits, at extra cost.[^s3] The non-adjustable per-service cap is why an ECS service using service discovery is limited to 1,000 tasks.[^s5]

Pricing (as of 2026-11, as published on the pricing page with no Region distinction): $0.10 per registered resource per month and $1.00 per million discovery API calls. DNS namespaces add Route 53 charges, which the page lists as $0.50 per hosted zone per month for the first 25 zones and $0.40 per million queries for the first billion each month; Route 53 health checks are billed under Route 53 pricing.[^s4] ECS container health checks exposed to Cloud Map are free, but Cloud Map resources ECS created for service discovery must be cleaned up manually.[^s5]

Other gotchas: the ECS console's create-service flow supports only private DNS namespaces for service discovery, VPC DNS attributes must be enabled for resolution to work, and when more than eight records are healthy Route 53 returns only a subset of them in each answer.[^s5] For DNS namespaces, the TTL you set controls how long resolvers cache the hosted zone's SOA record, and VPC queries for names with no matching record get `NXDOMAIN` from the private zone.[^s2]

## When to use it (and when not)

Use Cloud Map when services need to find each other by name and endpoints churn faster than static DNS or configuration can track: microservices on ECS, mixed fleets, or non-IP resources like queue or table ARNs. For containers on ECS, Service Connect is the higher-level option, adding a proxy with retries and metrics on top of the same registry.[^s6] If clients only need one stable entry point with layer 7 routing, a load balancer is usually simpler, since direct service-discovery traffic bypasses it.[^s5]

## Connections

Cloud Map sits under **AWS networking and content delivery** and **depends on Amazon Route 53** for its DNS namespaces and health checks.[^s2][^s4] It **integrates with Amazon ECS** (service discovery and Service Connect) and with **AWS Resource Access Manager** for cross-account namespace sharing.[^s5][^s6] It is **governed by Cloud Map quotas**, whose per-service cap also shows up in **ECS quotas**, is **scoped to AWS Regions**, and is an **alternative to Elastic Load Balancing** for internal service-to-service traffic. It is related to **Amazon EKS** through ExternalDNS and to **container networking on AWS** generally.[^s3][^s1]

## Sources

[^s1]: Amazon Web Services, "AWS Cloud Map FAQs". https://aws.amazon.com/cloud-map/faqs/ (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Creating an AWS Cloud Map namespace to group application services". https://docs.aws.amazon.com/cloud-map/latest/dg/namespaces-values.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "AWS Cloud Map service quotas". https://docs.aws.amazon.com/cloud-map/latest/dg/cloud-map-limits.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "AWS Cloud Map pricing". https://aws.amazon.com/cloud-map/pricing (accessed 2026-11-16)
[^s5]: Amazon Web Services, "Use service discovery to connect Amazon ECS services with DNS names". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-discovery.html (accessed 2026-11-16)
[^s6]: Amazon Web Services, "Amazon ECS Service Connect configuration overview". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-concepts.md (accessed 2026-11-16)
