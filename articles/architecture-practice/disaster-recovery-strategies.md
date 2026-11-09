---
id: kt:disaster-recovery-strategies
type: Pattern
title: Disaster recovery strategies on AWS
aliases: [DR strategies, AWS disaster recovery strategies, backup and restore / pilot light / warm standby / multi-site active-active]
domain: architecture-practice
summary: >-
  The four AWS-defined patterns for recovering a workload in another location after a disaster —
  backup and restore, pilot light, warm standby and multi-site active/active — which trade cost
  and complexity against recovery time (RTO) and recovery point (RPO) objectives.
verified: 2026-11-09
status: concept
broader: [kt:reliability-pillar, kt:aws-well-architected-framework]
related: [kt:aws-regions, kt:aws-elastic-disaster-recovery, kt:aws-backup]
relations:
  - {predicate: dependsOn, object: kt:aws-cloudformation}
  - {predicate: integratesWith, object: kt:amazon-route-53}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "Disaster recovery options in the cloud - Disaster Recovery of Workloads on AWS: Recovery in the Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "REL13-BP02 Use defined recovery strategies to meet the recovery objectives - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_planning_for_recovery_disaster_recovery.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Disaster Recovery (DR) Architecture on AWS, Part III: Pilot Light and Warm Standby"
    publisher: AWS Architecture Blog
    url: https://aws.amazon.com/blogs/architecture/disaster-recovery-dr-architecture-on-aws-part-iii-pilot-light-and-warm-standby
    type: official-blog
    accessed: 2026-11-09
  - id: s4
    title: "AWS Elastic Disaster Recovery FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/disaster-recovery/faqs/
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "Content Domain 2: Design Resilient Architectures - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03-domain2.html
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# Disaster recovery strategies on AWS

AWS describes four reusable disaster recovery (DR) patterns for bringing a workload back in a recovery site, usually a second AWS Region: **backup and restore**, **pilot light**, **warm standby** and **multi-site active/active**. They are ordered from lowest to highest cost and complexity, and correspondingly from the slowest to the fastest recovery.[^s1] The Well-Architected Reliability pillar (best practice REL13-BP02) asks that every workload have one of these strategies chosen to meet its recovery time objective (RTO) and recovery point objective (RPO), implemented ahead of time and tested, and rates the risk of skipping this as high.[^s2]

## How the four strategies work

The Well-Architected guidance gives indicative recovery targets for each pattern in a multi-Region setting:[^s2]

| Strategy[^s2] | Indicative RPO | Indicative RTO | What runs in the recovery Region |
|---|---|---|---|
| Backup and restore | Hours (minutes with point-in-time recovery) | 24 hours or less | Only backups; infrastructure, code and data are restored on demand |
| Pilot light | Minutes | Tens of minutes | Replicated data stores and core infrastructure; compute is switched off |
| Warm standby | Seconds | Minutes | A scaled-down but fully working copy that is scaled up on failover |
| Multi-site active/active | Near zero | Potentially zero | The full workload, serving live traffic from several Regions |

**Backup and restore** keeps nothing running: the recovery point depends on how often you back up, and the recovery time includes redeploying infrastructure, so without infrastructure as code it may miss its RTO.[^s1] **Pilot light** keeps data replicating and core pieces such as the VPC, load balancers and Auto Scaling groups in place, but no application instances run.[^s3] The key difference from **warm standby** is that pilot light cannot serve any request until resources are started, while warm standby can take traffic immediately at reduced capacity.[^s1] A warm standby provisioned for full production load, so that it does not rely on scaling, is called **hot standby**.[^s1][^s2] **Multi-site active/active** has no failover event in the usual sense; traffic is simply drained from the failed Region, but it requires cross-Region data synchronization and a way to handle write conflicts.[^s1][^s2]

## Gotchas

- **Replication is not a backup.** Continuous replication copies corruption and malicious deletes, so every strategy still needs point-in-time backups; even active/active falls back to a backup (and a non-zero recovery point) after data corruption.[^s1][^s3] AWS suggests S3 versioning and cross-account backup copies with AWS Backup to guard against deletes and account compromise.[^s1]
- **Fail over with data-plane operations.** Control planes typically have lower availability goals than data planes; AWS recommends Amazon Application Recovery Controller's data-plane API for rerouting traffic rather than editing weighted Route 53 records during an event.[^s1][^s3]
- **Be wary of fully automatic failover.** False alarms cause real availability and data loss, so AWS often prefers a manually initiated but fully scripted "push of a button" failover.[^s2][^s3] Detection should use error rates, latency, synthetic tests and business KPIs, not just liveness pings.[^s3]
- **Plan failback.** Re-synchronizing data back to the primary Region is often the hardest step; some organizations rotate primary and recovery Regions on a schedule, for example every three months.[^s2]
- **Reserve capacity.** For pilot light and warm standby, EC2 Capacity Reservations and Lambda provisioned concurrency help guarantee capacity when scaling up.[^s2]
- **Test regularly.** AWS calls regular DR testing critical; warm standby makes continuous testing easier, and periodic scheduled restores prove backups actually work.[^s1]

## When to use which

If your definition of "disaster" is losing a data center and the workload is already highly available across Availability Zones, backup and restore may be enough; if it is losing a whole Region, or regulation requires it, use pilot light, warm standby or active/active.[^s1] AWS advises choosing the cheapest strategy that meets RTO/RPO rather than over-engineering, and reusing the same pattern for workloads with similar objectives.[^s2] If you are going to pay for a full second-Region stack anyway, AWS says it makes sense to run it active/active; otherwise warm standby is more economical and simpler to operate.[^s1]

AWS Elastic Disaster Recovery is a managed, cheaper alternative to a warm standby for server-based workloads: it continuously replicates source servers' block storage into a low-cost staging area (without snapshots) and launches converted recovery instances on demand, aiming at RPOs of seconds and RTOs of minutes; drills run without interrupting replication.[^s2][^s4] Managed data services cover the data tier: Aurora global database can promote a secondary Region to read/write in under a minute, while promoting a non-Aurora RDS read replica takes a few minutes and includes a reboot.[^s1]

## Connections

These strategies are the implementation side of the `kt:reliability-pillar` of the `kt:aws-well-architected-framework` (REL13-BP02).[^s2] They are usually built across `kt:aws-regions`, with `kt:aws-backup` copying backups between Regions and accounts.[^s1] They depend on infrastructure as code: AWS shows a single `kt:aws-cloudformation` template with an active/passive parameter that sets desired capacity to zero in the standby Region.[^s3] Traffic is redirected with `kt:amazon-route-53` health checks and Application Recovery Controller.[^s3] `kt:aws-elastic-disaster-recovery` packages the replication and recovery orchestration as a service.[^s2] The SAA-C03 exam guide lists DR strategies (backup and restore, pilot light, warm standby, active-active failover, RPO and RTO) under Task 2.2, with the skill of selecting one for business requirements.[^s5]

## Sources
[^s1]: Amazon Web Services, "Disaster recovery options in the cloud - Disaster Recovery of Workloads on AWS: Recovery in the Cloud". https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html (accessed 2026-11-09)
[^s2]: Amazon Web Services, "REL13-BP02 Use defined recovery strategies to meet the recovery objectives - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_planning_for_recovery_disaster_recovery.html (accessed 2026-11-09)
[^s3]: Seth Eliot, AWS Architecture Blog, "Disaster Recovery (DR) Architecture on AWS, Part III: Pilot Light and Warm Standby" (2021-05-14). https://aws.amazon.com/blogs/architecture/disaster-recovery-dr-architecture-on-aws-part-iii-pilot-light-and-warm-standby (accessed 2026-11-09)
[^s4]: Amazon Web Services, "AWS Elastic Disaster Recovery FAQs". https://aws.amazon.com/disaster-recovery/faqs/ (accessed 2026-11-09)
[^s5]: Amazon Web Services, "Content Domain 2: Design Resilient Architectures - AWS Certified Solutions Architect - Associate (SAA-C03)". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03-domain2.html (accessed 2026-11-09)
