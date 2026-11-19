---
id: kt:microservices-architecture
type: Pattern
title: Microservices architecture
aliases: [microservices, microservices on AWS, microservice-based architecture]
domain: architecture-practice
summary: >-
  An architectural style that splits an application into small, independently deployable services that
  own their data, communicate through well-defined APIs or events and are run by autonomous teams; AWS
  frames it as a reliability and agility choice whose distributed-system costs must be weighed against a
  modular monolith.
verified: 2026-11-19
status: concept
broader: [kt:cloud-architecture, kt:reliability-pillar]
related: [kt:event-driven-architecture, kt:cell-based-architecture, kt:aws-serverless-services, kt:aws-container-services]
relations:
  - {predicate: integratesWith, object: kt:amazon-api-gateway}
  - {predicate: runsOn, object: kt:aws-lambda}
  - {predicate: runsOn, object: kt:amazon-ecs}
  - {predicate: integratesWith, object: kt:aws-cloud-map}
  - {predicate: monitoredBy, object: kt:aws-x-ray}
  - {predicate: integratesWith, object: kt:strangler-fig-pattern}
sources:
  - id: s1
    title: "Implementing Microservices on AWS - AWS Whitepaper"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/microservices-on-aws/microservices-on-aws.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "REL03-BP01 Choose how to segment your workload - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/2025-02-25/framework/rel_service_architecture_monolith_soa_microservice.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Decomposing monoliths into microservices - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-decomposing-monoliths/introduction.html
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "Strangler fig pattern - AWS Prescriptive Guidance (Cloud design patterns)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/strangler-fig.html
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Simplifying operations - Implementing Microservices on AWS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/microservices-on-aws/simplyfing-operations.html
    type: official-docs
    accessed: 2026-11-19
created: 2026-11-19
---

# Microservices architecture

A microservices architecture builds an application from small, loosely coupled services, each exposing a well-defined API and owned end to end by a small autonomous team (AWS's "two-pizza" teams that build and run what they ship).[^s1] AWS describes the style as a blend of agile delivery, service-oriented architecture (SOA), API-first design and CI/CD, often following Twelve-Factor App principles, and groups designs into API-driven, event-driven and data-streaming patterns.[^s1] The Well-Architected Reliability Pillar treats it as a segmentation decision: favour SOA with microservices where practical, and keep even a monolith modular so it can evolve.[^s2]

## How it works

- **Service boundaries follow the business.** AWS Prescriptive Guidance lists six ways to carve a monolith: by business capability, by subdomain, by transaction, service per team, strangler fig, and branch by abstraction.[^s3] Domain-driven design and event storming help find boundaries; decomposing before the domain is understood is costly.[^s4]
- **Each service owns its data.** Guidance favours a data store per service (polyglot persistence), so one service might use DynamoDB, another ElastiCache and a third keep Amazon RDS.[^s3][^s4] When migrating, a shared monolith database is split by business unit, access pattern or data structure.[^s2]
- **Communication is synchronous or asynchronous.** Synchronous calls go through an API front door; Amazon API Gateway provides authorization, rate limiting, caching, monitoring and traffic management, while some teams use an Application or Network Load Balancer instead.[^s5] For slow synchronous dependencies use circuit breakers, and prefer events and queues for asynchronous work.[^s4] AWS Cloud Map provides DNS-based service discovery.[^s2]
- **Compute is containers or serverless.** The AWS whitepaper walks through a container-based reference design and then a serverless one built on Lambda, which it recommends where possible to cut operational effort.[^s1][^s5] Functions are best defined as code with CloudFormation plus AWS SAM, AWS CDK or Terraform rather than in the console.[^s5]
- **Observability is distributed.** Monitoring, logging, tracing and auditing must span services; the Well-Architected guidance points to AWS X-Ray for end-to-end tracing.[^s1][^s2]

## Gotchas and trade-offs

- **It is not free.** AWS lists added network latency, harder debugging and more operational overhead as the price of microservices, and rates this best practice as high effort.[^s2] The whitepaper itself says a monolith may be more appropriate in some cases, and Prescriptive Guidance notes a monolith remains valid when the domain has no clear boundaries.[^s1][^s3]
- **The "Death Star" anti-pattern.** Services so interdependent that one failure cascades across the system defeat the purpose; aim for stateless, loosely coupled services.[^s2]
- **Shared code becomes coupling.** Lambda layers suit shared libraries and helpers, but AWS warns against putting business logic in them because shared code makes independent updates harder.[^s5]
- **Data consistency.** Keeping data in sync across services through queues yields eventual consistency; treat such synchronisation as a tactical, temporary measure during migration.[^s4]
- **Migration tooling has shifted.** The strangler fig guide notes that AWS Migration Hub Refactor Spaces closed to new customers on 7 November 2025 and points to AWS Transform instead (as of 2026-11).[^s4]

## When to use it (and when not)

Choose microservices when independent deployment, team autonomy, per-component scaling and a smaller failure blast radius are worth the operational cost; AWS cites faster delivery, failure isolation and the ability to give components different availability targets.[^s2][^s3] Prioritise decomposing monoliths that have reliability or performance problems or heavy coupling.[^s3] Migrate incrementally with the strangler fig pattern: place a proxy (for example API Gateway) in front of the monolith, build new features as services, use an anti-corruption layer, move services one at a time and finally decommission the monolith; a big-bang rewrite is the risky alternative.[^s4] Small teams with a simple, poorly understood domain are usually better served by a modular monolith.[^s1][^s3]

## Connections

Microservices is a cloud architecture style that the Well-Architected Reliability Pillar addresses directly through workload segmentation.[^s2] Services commonly run on AWS Lambda or Amazon ECS, sit behind Amazon API Gateway, find each other through AWS Cloud Map and are traced with AWS X-Ray.[^s2][^s5] The style overlaps with event-driven architecture for asynchronous integration and pairs with cell-based architecture for blast-radius containment. Teams usually reach it from a monolith via the strangler fig pattern.[^s1][^s4]

## Sources
[^s1]: Amazon Web Services, "Implementing Microservices on AWS - AWS Whitepaper". https://docs.aws.amazon.com/whitepapers/latest/microservices-on-aws/microservices-on-aws.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "REL03-BP01 Choose how to segment your workload - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/2025-02-25/framework/rel_service_architecture_monolith_soa_microservice.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Decomposing monoliths into microservices - AWS Prescriptive Guidance". https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-decomposing-monoliths/introduction.html (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Strangler fig pattern - AWS Prescriptive Guidance". https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/strangler-fig.html (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Simplifying operations - Implementing Microservices on AWS". https://docs.aws.amazon.com/whitepapers/latest/microservices-on-aws/simplyfing-operations.html (accessed 2026-11-19)
