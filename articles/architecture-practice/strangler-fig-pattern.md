---
id: kt:strangler-fig-pattern
type: Pattern
title: Strangler fig pattern
aliases: [strangler fig application, strangler application, strangler pattern]
domain: architecture-practice
summary: >-
  An incremental modernization pattern in which a routing layer is placed in front of a legacy (usually
  monolithic) application and its functionality is replaced piece by piece with new services, until the
  legacy system receives no traffic and can be retired, avoiding a risky big-bang rewrite.
verified: 2026-11-21
status: concept
broader: [kt:cloud-architecture, kt:seven-rs-migration-strategies]
related: [kt:microservices-architecture, kt:event-driven-architecture, kt:aws-migration-hub]
relations:
  - {predicate: dependsOn, object: kt:amazon-api-gateway}
  - {predicate: integratesWith, object: kt:anti-corruption-layer-pattern}
  - {predicate: contrastsWith, object: kt:big-bang-rewrite}
  - {predicate: integratesWith, object: kt:amazon-ecs}
sources:
  - id: s1
    title: "Strangler fig pattern - AWS Prescriptive Guidance (Cloud design patterns, architectures, and implementations)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/strangler-fig.html
    type: official-docs
    accessed: 2026-11-21
  - id: s2
    title: "Strangler fig pattern - AWS Prescriptive Guidance (Decomposing monoliths into microservices)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-decomposing-monoliths/strangler-fig.html
    type: official-docs
    accessed: 2026-11-21
  - id: s3
    title: "The strangler fig pattern - AWS Prescriptive Guidance (Modernizing ASP.NET web services)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-aspnet-web-services/fig-pattern.html
    type: official-docs
    accessed: 2026-11-21
  - id: s4
    title: "Strangler Fig (bliki)"
    publisher: Martin Fowler
    url: https://www.martinfowler.com/bliki/StranglerApplication.html
    type: community
    accessed: 2026-11-21
  - id: s5
    title: "The Strangler Fig application pattern: incremental modernization to microservices"
    publisher: Chris Richardson (microservices.io)
    url: https://microservices.io/post/refactoring/2023/06/21/strangler-fig-application-pattern-incremental-modernization-to-services.md.html
    type: community
    accessed: 2026-11-21
created: 2026-11-21
---

# Strangler fig pattern

The strangler fig pattern modernizes a legacy system gradually instead of replacing it in one cutover. A new application grows around the old one, takes over its behaviour one slice at a time, and the old system is switched off once nothing depends on it.[^s1][^s2] Martin Fowler named it after strangler figs he saw in Queensland rain forests, which germinate in a host tree and eventually stand on their own; his original post was called "Strangler Application" and was later retitled "Strangler Fig" partly to recover the botanical meaning.[^s4] The core argument is that full replacements of serious systems usually take so long that users cannot wait and existing behaviour is hard to specify, so moving value out incrementally lowers risk.[^s4]

## How it works

AWS Prescriptive Guidance frames the pattern as three phases: **transform** (build modernized components in parallel with the legacy app, by porting or rewriting), **coexist** (keep the monolith in place for rollback while an HTTP proxy at its perimeter redirects selected requests), and **eliminate** (retire the old functionality as traffic leaves it).[^s2] The cloud design patterns guide walks through a concrete sequence for an e-commerce monolith with user, cart and account modules:[^s1]

1. Insert a proxy between clients and the monolith that initially forwards every call unchanged.[^s1]
2. Build new features as separate services and route their API paths to them.[^s1]
3. Add an anti-corruption layer (a facade and adapter inside the monolith) so legacy code can call new services without adopting their model.[^s1]
4. Keep data in step with a synchronizing agent fed by a queue while two stores coexist.[^s1]
5. Move existing modules out one at a time, then remove the anti-corruption layer and decommission the monolith.[^s1]

On AWS the proxy is typically Amazon API Gateway, with new services on AWS Lambda, Amazon ECS or Amazon EC2 and their own stores such as Amazon DynamoDB or Amazon RDS.[^s1] For legacy ASP.NET ASMX web services, AWS suggests starting with pass-through proxies, then moving each function to a decoupled service; refactored services can run in Windows containers on Amazon ECS with the modern REST API published through API Gateway.[^s3]

## Gotchas

- **The routing layer is critical path.** A badly designed proxy or facade becomes a single point of failure or a bottleneck; AWS notes that API Gateway, being serverless and Multi-AZ, reduces that risk.[^s1][^s2]
- **You must be able to intercept requests.** The pattern does not fit systems whose backend calls cannot be captured and rerouted.[^s2]
- **The legacy code usually changes too.** The anti-corruption layer needs access to the monolith's source, and the old application is typically modified to call new services, which is hard on very old technology stacks.[^s1][^s5]
- **Data coexistence is tactical.** Synchronizing two stores introduces redundancy and eventual consistency; synchronous calls between old and new parts need timeouts and circuit breakers.[^s1]
- **Plan rollback per slice.** Each refactored service needs its own rollback plan.[^s2]
- **Transitional code is throwaway.** Fowler calls the scaffolding that lets old and new coexist "transitional architecture" and argues its cost is outweighed by the reduced risk.[^s4]
- **Tooling has moved.** AWS Migration Hub Refactor Spaces, which automated the routing and multi-account setup for this pattern, was no longer open to new customers as of 7 November 2025; AWS points to AWS Transform instead (as of 2026-11).[^s1]

## When to use it (and when not)

Use it when a big-bang migration is too risky, the business still needs new features during modernization, and end users must see minimal disruption.[^s1] Start with components that have good test coverage and less technical debt, real scalability needs, or frequent business changes and deployments.[^s3] Richardson adds that it treats modernization as discovery: benefits arrive with the first extracted service, the order can be reprioritized, and early production feedback exposes architecture mistakes.[^s5] For small, low-complexity applications a direct rewrite is often cheaper.[^s1][^s2]

## Connections

The pattern is the usual path from a monolith to a **microservices architecture** and is a form of the refactor strategy among the **7 Rs of migration**.[^s1][^s2] It **depends on** a routing layer such as **Amazon API Gateway**, **integrates with** the anti-corruption layer pattern, and **contrasts with** a big-bang rewrite.[^s1][^s2][^s4] New services often run on **Amazon ECS**, and asynchronous synchronization ties it to **event-driven architecture**.[^s1][^s3] AWS tooling for it has shifted from Refactor Spaces in **AWS Migration Hub** to **AWS Transform**.[^s1]

## Sources

[^s1]: Amazon Web Services, "Strangler fig pattern - AWS Prescriptive Guidance (Cloud design patterns)". https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/strangler-fig.html (accessed 2026-11-21)
[^s2]: Amazon Web Services, "Strangler fig pattern - AWS Prescriptive Guidance (Decomposing monoliths)". https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-decomposing-monoliths/strangler-fig.html (accessed 2026-11-21)
[^s3]: Amazon Web Services, "The strangler fig pattern - AWS Prescriptive Guidance (Modernizing ASP.NET web services)". https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-aspnet-web-services/fig-pattern.html (accessed 2026-11-21)
[^s4]: Martin Fowler, "Strangler Fig". https://www.martinfowler.com/bliki/StranglerApplication.html (accessed 2026-11-21)
[^s5]: Chris Richardson, "The Strangler Fig application pattern: incremental modernization to microservices". https://microservices.io/post/refactoring/2023/06/21/strangler-fig-application-pattern-incremental-modernization-to-services.md.html (accessed 2026-11-21)
