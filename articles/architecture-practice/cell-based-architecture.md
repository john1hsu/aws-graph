---
id: kt:cell-based-architecture
type: Pattern
title: Cell-based architecture
aliases: [cellular architecture, cell-based design, bulkhead architecture]
domain: architecture-practice
summary: >-
  A resilience pattern that splits a workload into multiple isolated, identical, fixed-size copies called
  cells, each serving a subset of customers chosen by a partition key behind a thin cell router, so that a
  failure, bad deployment or poison-pill request is contained to one cell rather than the whole fleet.
verified: 2026-11-16
status: concept
broader: [kt:reliability-pillar]
related: [kt:multi-region-architecture, kt:availability-zones, kt:amazon-route-53, kt:amazon-dynamodb, kt:multi-account-strategy]
relations:
  - {predicate: integratesWith, object: kt:shuffle-sharding}
  - {predicate: integratesWith, object: kt:static-stability}
  - {predicate: integratesWith, object: kt:amazon-api-gateway}
  - {predicate: integratesWith, object: kt:amazon-application-recovery-controller}
  - {predicate: governedBy, object: kt:service-quotas}
sources:
  - id: s1
    title: "What is a cell-based architecture? - Reducing the Scope of Impact with Cell-Based Architecture"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/what-is-a-cell-based-architecture.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Why use a cell-based architecture? - Reducing the Scope of Impact with Cell-Based Architecture"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/why-to-use-a-cell-based-architecture.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Reducing the Scope of Impact with Cell-Based Architecture (AWS Well-Architected whitepaper, PDF)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/reducing-scope-of-impact-with-cell-based-architecture.pdf
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "REL10-BP03 Use bulkhead architectures to limit scope of impact - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_fault_isolation_use_bulkhead.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "Shuffle Sharding: Massive and Magical Fault Isolation"
    publisher: AWS Architecture Blog
    url: https://aws.amazon.com/blogs/architecture/shuffle-sharding-massive-and-magical-fault-isolation/
    type: official-blog
    accessed: 2026-11-16
created: 2026-11-16
---

# Cell-based architecture

A cell-based architecture divides a workload into several isolated copies of itself, called cells. Each cell is a complete instance of the workload with everything it needs to run on its own; it shares no state with other cells and serves only a subset of the overall requests. The idea comes from the watertight bulkheads in a ship's hull: a flood (or a bad software update, or a poison-pill request) stays in one compartment. AWS's example is a workload split into 10 cells: if one cell fails, 90% of requests are unaffected.[^s1] AWS describes the approach in its Well-Architected whitepaper *Reducing the Scope of Impact with Cell-Based Architecture* (published September 2023), which applies the fault-isolation thinking behind Availability Zones and Regions to an individual workload.[^s3]

## How it works

- **Partition key.** Requests are assigned to cells by a key that matches the natural "grain" of the service, such as customer ID or resource ID. The key must appear in every request or be derivable from it, and a customer should stay pinned to one cell for the lifetime of its resources.[^s4]
- **Cell router.** A shared routing layer presents one endpoint to clients and forwards each request to its cell. Because it cannot itself be split into cells, it should be the thinnest possible layer: horizontally scalable, with no business logic.[^s1][^s4] The whitepaper describes router options including Route 53 DNS records per tenant, API Gateway backed by a DynamoDB lookup table, a compute fleet that loads the cell map from S3 into memory, and routers that consume from SQS or MSK for non-HTTP traffic.[^s3]
- **Mapping algorithms.** Full key-to-cell mapping tables give the most control but carry large state; range-based mapping is lighter but risks hot cells; hash-modulo mapping spreads keys evenly but reshuffles almost everything when the cell count changes; consistent hashing maps keys to many logical buckets and buckets to cells, so cells can be added without a full rebalance. Whatever the algorithm, an override table lets operators pin specific keys to specific cells.[^s3]
- **Control plane and data plane.** A control plane provisions and retires cells, places new tenants and migrates existing ones; the router and cells form the data plane. Following static stability, the data plane should keep serving from its last known cell map even if the control plane is down.[^s3]

## Sizing, quotas and gotchas

Each cell has a defined maximum size found by load testing to its breaking point; the workload grows by adding cells, never by enlarging one, and letting cells grow unbounded is an explicit anti-pattern.[^s4] Smaller cells shrink the blast radius (100 cells means about 1% of customers per cell) and are cheaper to test at full scale, but they add operational overhead and idle capacity; larger cells use capacity more efficiently.[^s3] Because each cell is bounded, scaling out stays within known service, instance and account quotas rather than discovering them in production; AWS encourages a separate AWS account per cell.[^s2][^s3] Other anti-patterns are deploying to all cells at once, sharing state or components between cells, and putting complex logic in the router.[^s4] Customers who outgrow a cell need an online migration path, which the whitepaper advises building from day one, and observability (metrics, logs, dashboards and alarms) has to be broken out per cell.[^s3]

## When to use it (and when not)

Cells pay off where downtime is very costly: critical financial services, "too big to fail" ultra-scale services, and multi-tenant SaaS where some tenants need a dedicated cell. AWS frames the decision as whether it is better for every customer to see a small failure rate or for a small share of customers to see a total outage.[^s3] The benefits include a smaller scope of impact, higher testability, longer mean time between failures, faster recovery and an extra dimension for phased deployments in which the first cell acts as a canary.[^s2] The costs are higher architectural complexity, specialized tooling and the routing layer itself; the whitepaper notes that cells do not necessarily multiply host count, since 30 hosts can be regrouped into cells behind a router.[^s1][^s3] Well-Architected rates the risk of not using bulkhead architectures as high, but still treats multiple Availability Zones as the first layer of resilience and multi-Region designs as rarely required.[^s4]

## Connections

Cell-based architecture is the detailed form of the Reliability Pillar's bulkhead best practice (REL10-BP03).[^s4] Cells can be zonal, Regional or global, so they combine with Availability Zones and multi-Region architecture, and Amazon Application Recovery Controller can help evacuate traffic during AZ, Region or gray failures.[^s1][^s3] Typical router building blocks are Amazon Route 53, Amazon API Gateway and Amazon DynamoDB; an account per cell ties the pattern to a multi-account strategy, and bounded cells keep growth inside service quotas.[^s3] Shuffle sharding is a related isolation technique: instead of fixed groups (eight instances in four shards of two, where one bad client hurts a quarter of customers), each customer gets a pseudo-random combination of instances, so few customers share an entire shard.[^s5]

## Sources
[^s1]: Amazon Web Services, "What is a cell-based architecture?", Reducing the Scope of Impact with Cell-Based Architecture. https://docs.aws.amazon.com/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/what-is-a-cell-based-architecture.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Why use a cell-based architecture?", Reducing the Scope of Impact with Cell-Based Architecture. https://docs.aws.amazon.com/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/why-to-use-a-cell-based-architecture.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Reducing the Scope of Impact with Cell-Based Architecture" (AWS Well-Architected whitepaper, PDF). https://docs.aws.amazon.com/pdfs/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/reducing-scope-of-impact-with-cell-based-architecture.pdf (accessed 2026-11-16)
[^s4]: Amazon Web Services, "REL10-BP03 Use bulkhead architectures to limit scope of impact", AWS Well-Architected Framework. https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_fault_isolation_use_bulkhead.html (accessed 2026-11-16)
[^s5]: Colm MacCárthaigh, "Shuffle Sharding: Massive and Magical Fault Isolation", AWS Architecture Blog (2014). https://aws.amazon.com/blogs/architecture/shuffle-sharding-massive-and-magical-fault-isolation/ (accessed 2026-11-16)
