---
id: kt:dynamodb-global-tables
type: Feature
title: Amazon DynamoDB global tables
aliases: [DynamoDB global tables, DynamoDB multi-Region replication, MRSC global tables, MREC global tables]
domain: databases
summary: >-
  A DynamoDB feature that keeps replica tables in several AWS Regions (and optionally several
  accounts) in sync as one multi-active table, with either eventually consistent (MREC) or
  strongly consistent (MRSC) multi-Region replication and a 99.999% availability SLA.
verified: 2026-11-12
status: current
broader: [kt:amazon-dynamodb]
related: [kt:disaster-recovery-strategies, kt:multi-region-architecture, kt:aurora-global-database]
relations:
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: configuredVia, object: kt:aws-cloudformation}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: contrastsWith, object: kt:aws-backup}
sources:
  - id: s1
    title: "How DynamoDB global tables work"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/V2globaltables_HowItWorks.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Understanding Amazon DynamoDB billing for global tables"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/global-tables-billing.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Global tables FAQ - Using Amazon DynamoDB global tables"
    publisher: Amazon Web Services (AWS Prescriptive Guidance)
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/dynamodb-global-tables/faq.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "Amazon DynamoDB global tables now support replication across multiple AWS accounts"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/02/dynamodb-gt-multi-account
    type: official-blog
    accessed: 2026-11-12
  - id: s5
    title: "Amazon DynamoDB global tables now support replication across AWS accounts"
    publisher: Amazon Web Services (AWS Database Blog)
    url: https://aws.amazon.com/blogs/database/amazon-dynamodb-global-tables-now-support-replication-across-aws-accounts
    type: official-blog
    accessed: 2026-11-12
  - id: s6
    title: "Disassociating a global table replica"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/globaltables-disassociate.html
    type: official-docs
    accessed: 2026-11-12
created: 2026-11-12
---

# Amazon DynamoDB global tables

Global tables are DynamoDB's built-in multi-Region replication. A global table is a set of two or more **replica tables**, at most one per Region, that share a table name, key schema and item data; a write to any replica is replicated to the others automatically.[^s1] Applications keep using the normal DynamoDB API, so no code changes are needed to read and write locally in each Region.[^s3] Global tables carry a 99.999% availability SLA, against 99.99% for a single-Region table.[^s1] The current version is 2019.11.21; the 2017.11.29 version is legacy and AWS says to use the current one whenever possible.[^s1]

## How it works

Each global table picks one consistency mode at creation, and the mode cannot be changed later.[^s1]

- **Multi-Region eventual consistency (MREC)**, the default, replicates asynchronously, typically within a second or less, so the recovery point objective equals the replication delay. You create one by adding a replica to an existing table, which AWS says does not affect the performance of existing tables. Replicas can live in any Region where DynamoDB runs. Concurrent updates to the same item are resolved per item by **last writer wins** on an internal timestamp.[^s1]
- **Multi-Region strong consistency (MRSC)** acknowledges a write only after it is replicated synchronously to at least one other Region, so strongly consistent reads on any replica return the latest data and the RPO is zero. The cost is higher write and read latency, set by inter-Region round trips. An MRSC table spans exactly three Regions: three replicas, or two replicas plus a DynamoDB-managed **witness** that stores data but serves no reads or writes. It must be created from an empty table, and replicas cannot be added afterwards. Writes that collide with an in-flight write elsewhere fail with a retryable `ReplicatedWriteConflictException`.[^s1]

MREC replicas replicate through DynamoDB Streams, which are always on and cannot be disabled; per-item ordering is preserved. MRSC does not use Streams for replication.[^s1][^s3] Capacity mode, write capacity, GSI definitions, encryption type and TTL are always synchronized across replicas, while read capacity and table class can be overridden per replica. Point-in-time recovery, deletion protection, tags and resource policies are never synchronized.[^s1]

Since February 2026, MREC global tables can also replicate **across AWS accounts**, in all Regions, at standard global-table pricing.[^s4][^s5] Each replica sits in its own account and Region, permissions are granted through resource-based policies on the tables, and same-Region replication is not supported.[^s5] MREC replicas can also be **disassociated**: replication stops and the replica becomes a standalone regional table that keeps its data, name and ARN. It is irreversible, and disassociation protection is on by default.[^s6]

## Quotas, pricing and gotchas

Billing (as of 2026-11): every write is charged as a **replicated write unit** (rWRU on demand, rWCU provisioned) per 1 KB in every Region that holds a replica, priced the same as ordinary write units. GSI updates are billed as normal write units in each Region. DynamoDB does not charge cross-Region data transfer for replication between a global table's Regions, the rates are the same for MRSC and MREC, and replication to an MRSC witness is free.[^s2] Reserved capacity does not apply to replicated write units, and adding a Region is billed like a table restore based on table size.[^s3]

Gotchas, all from AWS's documentation:

- MREC transactions are atomic only in the Region that ran them, so other Regions can briefly see partial results. MRSC rejects transactions entirely and also lacks TTL and LSIs; its GSI reads are eventually consistent.[^s1]
- Writes to replicas bypass DAX, so a DAX cache can serve stale data until its TTL expires.[^s1][^s3]
- Replicated writes and replicated TTL deletes consume write capacity, so provisioned replicas can throttle.[^s1]
- A table used to seed a replica cannot be deleted for 24 hours, and replicas in a disabled Region become single-Region tables after 20 hours.[^s1]
- Manage each global table from one CloudFormation stack in one Region using `AWS::DynamoDB::GlobalTable`.[^s3]

## When to use it (and when not)

Choose MREC when lower latency matters more than reading the very latest cross-Region write and a small non-zero RPO is acceptable; choose MRSC when every Region must see the latest data or the RPO must be zero.[^s1] Multi-account replication suits account-level isolation, data-perimeter guardrails and disaster recovery across organizational boundaries.[^s4][^s5] Global tables are not a backup: AWS advises point-in-time recovery for undoing bad writes and AWS Backup for compliance snapshots, because a mistaken write replicates everywhere.[^s3]

## Connections

- **Broader:** Amazon DynamoDB, of which this is a feature.[^s1]
- **Scoped to AWS Regions:** one replica per Region, with MRSC limited to a fixed list of Regions.[^s1]
- **Monitored by Amazon CloudWatch** through the `ReplicationLatency` metric, published for MREC only.[^s1]
- **Configured via AWS CloudFormation** with the `AWS::DynamoDB::GlobalTable` resource.[^s3][^s6]
- **Integrates with AWS Organizations** multi-account strategies via cross-account replicas.[^s4]
- **Contrasts with AWS Backup:** replication gives Region resilience, but backups are still needed for logical recovery.[^s3]
- **Related:** disaster recovery strategies and multi-Region architecture (multi-active patterns), and Aurora Global Database as the relational counterpart.[^s1][^s5]

## Sources
[^s1]: Amazon Web Services, "How DynamoDB global tables work". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/V2globaltables_HowItWorks.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Understanding Amazon DynamoDB billing for global tables". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/global-tables-billing.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, AWS Prescriptive Guidance, "Global tables FAQ". https://docs.aws.amazon.com/prescriptive-guidance/latest/dynamodb-global-tables/faq.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, What's New, "Amazon DynamoDB global tables now support replication across multiple AWS accounts" (2026-02-03). https://aws.amazon.com/about-aws/whats-new/2026/02/dynamodb-gt-multi-account (accessed 2026-11-12)
[^s5]: Amazon Web Services, AWS Database Blog, "Amazon DynamoDB global tables now support replication across AWS accounts" (2026-02-03). https://aws.amazon.com/blogs/database/amazon-dynamodb-global-tables-now-support-replication-across-aws-accounts (accessed 2026-11-12)
[^s6]: Amazon Web Services, "Disassociating a global table replica". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/globaltables-disassociate.html (accessed 2026-11-12)
