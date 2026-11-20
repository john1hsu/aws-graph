---
id: kt:dynamodb-quotas
type: Limit
title: Amazon DynamoDB quotas
aliases: [DynamoDB quotas, DynamoDB limits, DynamoDB service quotas, DynamoDB service limits, Amazon DynamoDB service limits]
domain: databases
summary: >-
  The per-account, per-Region throughput, table, index, stream, global-table and data-movement
  quotas that bound Amazon DynamoDB; throughput and table counts are starting defaults that can be
  raised (throughput requests are auto-approved through Service Quotas), while index structure,
  vector-index and stream-reader limits are largely fixed.
verified: 2026-11-20
status: current
broader: [kt:amazon-dynamodb]
related: [kt:dynamodb-global-tables, kt:dynamodb-partition-key-design, kt:amazon-dynamodb-accelerator]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: contrastsWith, object: kt:aurora-quotas}
sources:
  - id: s1
    title: "Quotas in Amazon DynamoDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ServiceQuotas.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Amazon DynamoDB endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/ddb.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "3- Account limits exceeded - Amazon DynamoDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/throttling-account-limit-exceeded-mitigation.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "DynamoDB maximum throughput for on-demand tables"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/on-demand-capacity-mode-max-throughput.html
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "Amazon DynamoDB now supports auto-approval of quota adjustments"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/02/amazon-dynamodb-supports-auto-approval-quota-adjustments
    type: official-blog
    accessed: 2026-11-20
  - id: s6
    title: "Amazon DynamoDB now supports more frequent throughput mode updates from provisioned to on-demand capacity"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/08/amazon-dynamodb-more-frequent-throughput-mode-updates
    type: official-blog
    accessed: 2026-11-20
created: 2026-11-20
---

# Amazon DynamoDB quotas

DynamoDB quotas (AWS's former term was "limits") are set per AWS account and per Region unless a quota says otherwise, and every size in them uses binary units, so 1 KB is 1,024 bytes.[^s1] They fall into two groups. **Throughput and count quotas** are starting points: AWS describes the throughput defaults as initial values rather than maximums, and most can be raised through Service Quotas. **Structural limits** on indexes, vector indexes and stream readers are mostly fixed by design and shape the data model instead.[^s1]

## Throughput quotas (as of 2026-11, every commercial Region)

- **Per table:** 40,000 read units and 40,000 write units, in both on-demand (request units) and provisioned (capacity units) mode. Adjustable.[^s1]
- **Per account, provisioned only:** 80,000 RCUs and 80,000 WCUs summed across all provisioned tables and GSIs in the Region. Adjustable. On-demand tables have no account-level throughput quota, only the per-table one.[^s1][^s2]
- **Minimum per provisioned table or GSI:** 1 RCU and 1 WCU.[^s1]
- **Provisioned decreases:** each UTC day starts with 4 decreases, and one more is earned per hour up to 4 banked, for a ceiling of 27 per day. The table and each GSI have their own budget. Increases have no count limit, but AWS warns that it does not allow very rapid increases.[^s1]
- **Mode switches:** since August 2025 a table can move from provisioned to on-demand up to four times in a rolling 24 hours; before that, once per 24 hours.[^s6]
- **Reserved capacity:** up to 1,000,000 provisioned capacity units (RCUs plus WCUs) per account, adjustable.[^s1]
- **Control plane:** 500 concurrent control-plane operations (such as creating, updating or deleting tables), adjustable.[^s2]

When a table exceeds the per-table account quota, DynamoDB throttles with a reason of type `AccountLimitExceeded`, split into table and index, read and write variants (for example `TableWriteAccountLimitExceeded`). The CloudWatch metrics `ReadAccountLimitThrottleEvents` and `WriteAccountLimitThrottleEvents` separate this from ordinary provisioned-capacity throttling. The per-table quota codes are L-CF0CBE56 (reads) and L-AB614373 (writes).[^s3]

## Tables, indexes and vector indexes

Each account starts with 2,500 tables per Region. The account team can raise that to 10,000; beyond that AWS points to using more accounts. Table size itself has no practical limit.[^s1] A table can have 5 local secondary indexes (fixed) and 20 GSIs (raise through AWS Support). Creating a GSI on a base table larger than 50 TB needs allowlisting. Across all LSIs and GSIs of one table, at most 100 attributes can be projected with `INCLUDE`; `KEYS_ONLY` and `ALL` projections do not count toward that total.[^s1]

Vector indexes count toward the GSI quota: 5 per table by default, raisable to 20. Their fixed limits are 4,096 dimensions, a TopK of 100 per search, one partition key, 18 inline filters and one concurrent create or delete per table. Per-partition-key search (1 GBps) and write (10 MBps) rates can be raised through Support.[^s1]

## Streams, global tables and data movement

- **DynamoDB Streams:** up to two simultaneous readers per shard on a single-Region table, and AWS advises a single reader for global tables; extra readers get throttled. A provisioned table with Streams enabled is held to 40,000 WCUs.[^s1]
- **Global tables:** per-table throughput defaults match the 40,000 single-table quota for both multi-Region eventual (MREC) and strong (MRSC) consistency. The quotas page lists a default of 400 MRSC global tables. New replicas can backfill 10 TB per account, per Region, per day, and adding a replica requires matching table-level write quotas in each Region.[^s1]
- **Import from S3:** 50 concurrent jobs, up to 15 TB of source data in us-east-1, us-west-2 and eu-west-1, and 1 TB elsewhere; at most 50,000 S3 objects per job.[^s1]
- **Export to S3:** 300 concurrent full or incremental exports or 100 TB in flight. An incremental export window must be 15 minutes to 24 hours.[^s1]
- **Restores:** 50 concurrent restores totalling 50 TB through DynamoDB backups or PITR, but 25 TB through AWS Backup.[^s1]

## Raising quotas and gotchas

Since February 2025, requests to raise the account-level and table-level throughput quotas through Service Quotas are auto-approved within minutes in commercial and GovCloud (US) Regions. Before that, AWS Support reviewed each one.[^s5] AWS's throttling guide still allows 24 to 48 hours for an increase. It also suggests checking first whether retries already absorb occasional throttling, and trimming GSI projections, before you ask.[^s3]

The quotas do not cap spend. On-demand tables can set their own **maximum read or write throughput**, as low as 1 unit per second, per table and per GSI. DynamoDB treats it as a best-effort target, so bursts can briefly exceed it. Requests over it get a `ThrottlingException`. On global tables, the setting on one replica applies to all of them.[^s4] Raising the per-table quota on a global table in only one Region does not help, because replicas need the same write quota.[^s1]

## When this matters

Hot-partition throttling is a key-design problem, not a quota problem. Account-limit throttling, by contrast, means the workload has outgrown the Region's defaults, and a quota request is the fix.[^s3] Plan for the 40,000-unit per-table default before a load test or bulk import. Set an on-demand maximum when the risk is a runaway cost rather than throttling.[^s3][^s4]

## Connections

These quotas are part of **Amazon DynamoDB** (`broader`) and are **configured via Service Quotas**, where throughput increases are auto-approved.[^s5] They are **scoped to AWS Regions**: every default applies per account per Region.[^s1] Account-limit throttling is **monitored by Amazon CloudWatch** metrics.[^s3] They relate to **DynamoDB global tables** (matching replica quotas and the reader limit on streams), **partition key design** (the other source of throttling) and **DAX**, whose cluster limits are in the same General Reference page.[^s1][^s2] They **contrast with Amazon Aurora quotas**, where the fixed limits come from engine constraints rather than API and index design.

## Sources
[^s1]: Amazon Web Services, "Quotas in Amazon DynamoDB". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ServiceQuotas.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "Amazon DynamoDB endpoints and quotas" (AWS General Reference). https://docs.aws.amazon.com/general/latest/gr/ddb.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "3- Account limits exceeded" (DynamoDB Developer Guide). https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/throttling-account-limit-exceeded-mitigation.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "DynamoDB maximum throughput for on-demand tables". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/on-demand-capacity-mode-max-throughput.html (accessed 2026-11-20)
[^s5]: Amazon Web Services, What's New, "Amazon DynamoDB now supports auto-approval of quota adjustments" (2025-02-11). https://aws.amazon.com/about-aws/whats-new/2025/02/amazon-dynamodb-supports-auto-approval-quota-adjustments (accessed 2026-11-20)
[^s6]: Amazon Web Services, What's New, "Amazon DynamoDB now supports more frequent throughput mode updates from provisioned to on-demand capacity" (2025-08-13). https://aws.amazon.com/about-aws/whats-new/2025/08/amazon-dynamodb-more-frequent-throughput-mode-updates (accessed 2026-11-20)
