# Article schema

Each article is one **node** in the graph: `articles/<domain>/<slug>.md`, where the filename equals the id's slug and `<domain>` is a key in `topics/seeds.yaml`.

```markdown
---
id: kt:amazon-sqs                      # stable node id: kt: + lowercase-hyphen slug
type: Service                          # a class from ontology/ontology.ttl
title: Amazon SQS
aliases: [Amazon Simple Queue Service, SQS]   # other names; used for duplicate detection
domain: serverless-and-integration     # a domain key from topics/seeds.yaml
summary: >-                            # 1–2 sentence definition (becomes skos:definition)
  AWS's fully managed message queue for decoupling producers and consumers, with standard and FIFO queue types.
verified: 2026-11-08                   # date the content was last verified against the cited sources
status: current                        # current | preview | closed-to-new-customers | deprecated | retired | concept
broader: [kt:application-integration]
related: [kt:amazon-sns, kt:amazon-eventbridge]
relations:                             # typed edges; predicates must exist in the ontology
  - {predicate: alternativeTo, object: kt:amazon-kinesis-data-streams}
  - {predicate: governedBy, object: kt:sqs-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:                               # >= 3 total, >= 2 strong, >= 1 official AWS
  - id: s1
    title: "What is Amazon Simple Queue Service?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html
    type: official-docs
    accessed: 2026-11-08
created: 2026-11-08
---

# Amazon SQS

Prose with inline footnote citations after each claim.[^s1]

## How it works
...

## Quotas, pricing and gotchas
Concrete quotas and prices, each dated ("as of 2026-11") and, if they vary, with the Region they apply to.[^s1]

## When to use it (and when not)
Trade-offs vs. alternatives in the graph.[^s2]

## Connections
How this node relates to its neighbours, in prose, with citations.[^s3]

## Sources
[^s1]: Amazon Web Services, "What is Amazon Simple Queue Service?". https://docs.aws.amazon.com/... (accessed 2026-11-08)
```

## Rules enforced by `scripts/kg.py check`

- Required fields: `id, type, title, domain, summary, sources, created, verified, status`.
- `verified` is a real date, not in the future; `status` is one of the allowed values; a `deprecated`/`retired` node must have a `replacedBy` relation (its migration path).
- `type` must be a class, and every `relations.predicate` a property, in `ontology/ontology.ttl`.
- Every edge target is a `kt:` id. Targets with no article yet become **frontier** nodes (`graph/frontier.md`).
- Citations: at least 3 sources; at least 2 of a strong type; at least 1 official AWS source (`official-docs` or `official-learning`) unless the node is an `ExternalConcept`; https only; no duplicate URLs; every source cited in the body and defined in `## Sources`; every substantive paragraph cited.
- Body of at least 350 words.
- Uniqueness: no duplicate ids, titles or aliases; no near-paraphrase titles; no bodies that overlap another article above the similarity threshold.

## Freshness

AWS has no named releases; it ships continuously and announces the biggest changes at re:Invent each December. So each node records the date it was last `verified`, and `python3 scripts/kg.py stale` treats a node as stale when that date is older than 180 days, or older than the `--since` date the run passes (the last day of the most recent re:Invent that has ended). Refreshing a node re-opens its sources and moves `verified` to today.

## Naming

Use AWS's official service name as the `title` (e.g. "Amazon SageMaker AI", "Amazon Data Firehose") and put abbreviations and former names in `aliases` ("SageMaker", "Kinesis Data Firehose"). AWS renames services often; a rename never justifies a second node.

## Source types

| type | strong? | examples |
|---|---|---|
| official-docs | yes | docs.aws.amazon.com (user and developer guides, API references, service quotas pages), Well-Architected Framework and lenses, AWS Prescriptive Guidance, AWS whitepapers, service FAQ and pricing pages on aws.amazon.com, AWS Services in Scope pages |
| official-learning | yes | AWS Skill Builder courses, AWS certification exam guides on aws.amazon.com/certification |
| official-blog | yes | AWS News Blog, "What's New with AWS" posts, AWS Architecture / Security / Compute / Database blogs |
| standards-body | yes | IETF RFCs, NIST, ISO, CNCF and OCI specifications |
| government | yes | fedramp.gov marketplace, DISA Cloud Computing SRG, GSA |
| peer-reviewed / academic / textbook | yes | papers (e.g. Dynamo, Firecracker at NSDI), university material |
| partner | no | recognized AWS Partner write-ups |
| community | no | AWS re:Post, AWS Community Builder and Hero blogs |
| news | no | reputable press, for announcements only |

Never cite Wikipedia, content farms, exam-dump or "practice question" sites, or AI-generated pages.
