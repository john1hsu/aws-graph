---
id: kt:shuffle-sharding
type: Pattern
title: Shuffle sharding
aliases: [shuffle-sharding, shuffle shards, combinatorial sharding]
domain: architecture-practice
summary: >-
  A fault-isolation pattern in which each customer or resource is assigned its own pseudo-random combination
  of workers (a virtual "shuffle shard") instead of one fixed shard, so a poison-pill request or flood aimed at
  one customer degrades only its combination while other customers keep at least some healthy workers.
verified: 2026-11-18
status: concept
broader: [kt:reliability-pillar]
related: [kt:cell-based-architecture, kt:static-stability, kt:availability-zones, kt:aws-well-architected-framework]
relations:
  - {predicate: integratesWith, object: kt:amazon-route-53}
  - {predicate: integratesWith, object: kt:aws-shield}
  - {predicate: dependsOn, object: kt:retry-with-backoff}
sources:
  - id: s1
    title: "Workload isolation using shuffle-sharding (Amazon Builders' Library)"
    publisher: Amazon Web Services
    url: https://builder.aws.com/content/3F06NpJ8YeoIGP8VHTw4n81pFn8/workload-isolation-using-shuffle-sharding
    type: official-blog
    accessed: 2026-11-18
  - id: s2
    title: "Shuffle Sharding: Massive and Magical Fault Isolation"
    publisher: AWS Architecture Blog
    url: https://aws.amazon.com/blogs/architecture/shuffle-sharding-massive-and-magical-fault-isolation/
    type: official-blog
    accessed: 2026-11-18
  - id: s3
    title: "FAQ - Reducing the Scope of Impact with Cell-Based Architecture"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/faq.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Improving reliability with fault isolation and shuffle sharding (AWS re:Invent 2021, ARC319-R2, slides)"
    publisher: Amazon Web Services
    url: https://d1.awsstatic.com/events/reinvent/2021/Improving_reliability_with_fault_isolation_and_shuffle_sharding_REPEAT_ARC319-R2.pdf
    type: official-learning
    accessed: 2026-11-18
  - id: s5
    title: "REL10-BP03 Use bulkhead architectures to limit scope of impact - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_fault_isolation_use_bulkhead.html
    type: official-docs
    accessed: 2026-11-18
created: 2026-11-18
---

# Shuffle sharding

Shuffle sharding is a way of arranging an existing fleet of workers so that one misbehaving customer cannot take down everyone else. Instead of dividing workers into a few fixed shards, the service creates many *virtual* shards, each a different combination of workers, and gives every customer or resource its own combination. A problem that saturates or crashes the workers in one combination leaves every other combination with at least some healthy workers.[^s1] AWS developed the technique in the early days of Amazon Route 53, when hosting customer domains meant absorbing frequent DDoS attacks and buying enough scrubbing capacity for every domain would have cost tens of millions of dollars.[^s1] Colm MacCárthaigh first described it publicly on the AWS Architecture Blog in 2014 and expanded on it in the Amazon Builders' Library in 2019.[^s2][^s1]

## How it works

The standard illustration uses eight workers. With no sharding, a poison-pill request or request flood can cascade through all eight, so the scope of impact is 100%. Ordinary sharding into four shards of two limits a bad customer to one shard, about 25% of the service.[^s1][^s4] With shuffle sharding, each customer still gets two workers, but any two of the eight; that yields 28 possible combinations, so the impact of one bad customer drops to about 1/28 (roughly 3.6%), seven times better than plain sharding with the same hardware.[^s1][^s4] The AWS re:Invent session on the topic generalizes this: scope of impact for sharding is 1 divided by the number of shards, while for shuffle sharding it is 1 divided by the number of combinations, N!/(S!(N−S)!) for N workers and shard size S.[^s4] Because combinations grow combinatorially, adding workers or making shards slightly larger improves isolation rapidly; with enough workers there can be more shuffle shards than customers, so each customer can be fully isolated.[^s1]

Shards can be assigned statelessly, by hashing a customer or object identifier into a combination so clients can compute it themselves, or statefully, by randomly assigning shards and checking each new one against earlier ones to cap overlap. Both approaches in Route 53's open-source Infima library can spread a shard's endpoints across Availability Zones.[^s2][^s1]

## The Route 53 example and gotchas

Route 53 uses 2,048 virtual name servers and assigns each hosted domain a shuffle shard of four, giving roughly 730 billion possible combinations, and ensures no two customer domains share more than two name servers. When a domain is attacked, its four name servers spike while other domains see no noticeable effect, and the targeted domain can be moved onto dedicated attack capacity backed by AWS Shield scrubbers.[^s1]

The pattern only works if clients are fault tolerant: a caller whose shard partly overlaps a degraded one must retry against the other workers in its combination. The Architecture Blog notes that the retry count sets how many workers a shard can usefully hold (three retries make four-worker shards practical), and that retry behaviour has to be carefully tested for partial degradation.[^s2][^s1] Shuffle sharding usually adds no cost because it rearranges existing capacity, but it is harder to apply to stateful components.[^s1][^s3]

## When to use it (and when not)

Use shuffle sharding for multi-tenant, stateless or easily replicated front-end fleets (DNS, API front doors, request routers) where one tenant's traffic could poison shared workers and clients can retry. AWS reports it is built into many of its systems, sometimes recursively, sharding at several layers to isolate a customer's own customers.[^s1] It complements rather than replaces cell-based architecture: cells must not share state, so shuffle sharding may be used inside a cell but, by definition, not across cells.[^s3] Where a workload cannot tolerate overlap at all, or clients cannot retry, fixed shards or dedicated cells are the safer choice.[^s3][^s2]

## Connections

Shuffle sharding is a refinement of the bulkhead idea behind the Reliability Pillar's fault-isolation best practice REL10-BP03, which AWS rates high risk if not established and which links to the Builders' Library talk on shuffle sharding.[^s5] It is the sibling of **cell-based architecture** within the Well-Architected guidance on reducing scope of impact.[^s3] Its best-known implementation is in **Amazon Route 53**, where it works alongside **AWS Shield** traffic scrubbing.[^s1] It spreads shards across **Availability Zones**, and it **depends on** client **retries** to route around degraded workers.[^s2][^s1] Static stability is a neighbouring resilience pattern in the same domain: both aim to keep healthy customers served while part of the system is impaired.

## Sources
[^s1]: Colm MacCárthaigh, "Workload isolation using shuffle-sharding", Amazon Builders' Library (2019). https://builder.aws.com/content/3F06NpJ8YeoIGP8VHTw4n81pFn8/workload-isolation-using-shuffle-sharding (accessed 2026-11-18)
[^s2]: Colm MacCárthaigh, "Shuffle Sharding: Massive and Magical Fault Isolation", AWS Architecture Blog (2014). https://aws.amazon.com/blogs/architecture/shuffle-sharding-massive-and-magical-fault-isolation/ (accessed 2026-11-18)
[^s3]: Amazon Web Services, "FAQ", Reducing the Scope of Impact with Cell-Based Architecture. https://docs.aws.amazon.com/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/faq.html (accessed 2026-11-18)
[^s4]: Jon Steele and Mahanth Jayadeva, "Improving reliability with fault isolation and shuffle sharding" (AWS re:Invent 2021, ARC319-R2, slides). https://d1.awsstatic.com/events/reinvent/2021/Improving_reliability_with_fault_isolation_and_shuffle_sharding_REPEAT_ARC319-R2.pdf (accessed 2026-11-18)
[^s5]: Amazon Web Services, "REL10-BP03 Use bulkhead architectures to limit scope of impact", AWS Well-Architected Framework. https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_fault_isolation_use_bulkhead.html (accessed 2026-11-18)
