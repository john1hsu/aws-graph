---
id: kt:static-stability
type: Pattern
title: Static stability
aliases: [statically stable design, statically stable architecture]
domain: architecture-practice
summary: >-
  A resilience pattern in which a workload keeps running in the same mode during a failure as in normal
  operation, because capacity, configuration and resources are provisioned in advance and recovery relies on
  the data plane rather than on control plane actions such as launching instances or editing DNS records.
verified: 2026-11-17
status: concept
broader: [kt:reliability-pillar]
related: [kt:availability-zones, kt:cell-based-architecture, kt:multi-region-architecture, kt:disaster-recovery-strategies]
relations:
  - {predicate: integratesWith, object: kt:amazon-application-recovery-controller}
  - {predicate: contrastsWith, object: kt:amazon-ec2-auto-scaling}
  - {predicate: usedBy, object: kt:aws-lambda}
  - {predicate: mitigates, object: kt:bimodal-behavior}
  - {predicate: partOf, object: kt:control-plane-and-data-plane}
sources:
  - id: s1
    title: "REL11-BP05 Use static stability to prevent bimodal behavior - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_withstand_component_failures_static_stability.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "REL11-BP04 Rely on the data plane and not the control plane during recovery - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_withstand_component_failures_avoid_control_plane.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Static stability - AWS Fault Isolation Boundaries (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/static-stability.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "Best practices for zonal shifts in ARC - Amazon Application Recovery Controller (ARC)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/r53recovery/latest/dg/route53-arc-best-practices.zonal-shifts.html
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "AWS Lambda: Resilience under-the-hood (AWS Compute Blog)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/compute/aws-lambda-resilience-under-the-hood/
    type: official-blog
    accessed: 2026-11-17
created: 2026-11-17
---

# Static stability

Static stability is the property of a workload that behaves the same way when something fails as it does on a normal day. The AWS Well-Architected Framework frames it as avoiding *bimodal behavior*: a system that has one mode for steady state and a different mode for failure (for example, "launch replacement capacity in another Availability Zone") is bimodal, while a statically stable system already has that capacity running.[^s1] AWS's Fault Isolation Boundaries whitepaper describes the same idea as a data plane that keeps working without needing the control plane for configuration or recovery, degrading gracefully instead of reconfiguring itself mid-incident.[^s3]

## How it works

AWS separates every service into a **control plane** (the administrative create, read, update, delete and list APIs, often complex multi-step orchestrations) and a **data plane** (the part that serves day-to-day traffic). Control planes are more likely to be affected during an impairment, so recovery that depends on them tends to take longer.[^s2] Static stability applies that split to your own workload. The whitepaper describes four recurring techniques:[^s3]

- **Pre-provisioning.** Capacity exists before the failure. If a fleet spread over three Availability Zones loses one zone, it loses a third of its capacity; rather than waiting for Auto Scaling to replace it through the EC2 control plane, the remaining zones already carry enough instances to absorb the load. The same applies to non-compute resources such as accounts, buckets, IAM roles and queues.[^s3]
- **Maintaining existing state.** When a dependency that supplies configuration is down, the component keeps running on its last known good state. EC2 itself does this: running instances keep a read-only copy of their configuration in the data plane.[^s3]
- **Independent startup.** A service can start (possibly degraded) even when its dependencies are unavailable, avoiding circular dependencies and crash loops.[^s3]
- **Asynchronous interaction.** Control plane updates are pushed to the data plane through bounded queues rather than synchronous calls that fail when the dependency fails.[^s3]

Well-Architected adds a caching example: if a network blip makes every client refresh configuration at once, the extra load can trigger a second failure. A statically stable client refreshes on a fixed schedule (constant work) and, if a refresh fails, keeps using the cached value and raises an alarm.[^s1]

## Data plane versus control plane during recovery

REL11-BP04 lists swaps that turn a control plane recovery step into a data plane one: pre-scaled EC2 capacity instead of scaling out with Auto Scaling, Lambda's built-in scaling instead of launching instances, over-provisioned Kubernetes nodes so that recovery only adds pods, and Amazon Application Recovery Controller (ARC) traffic shifting instead of editing Route 53 records.[^s2] The Route 53 case is instructive: its data plane (answering queries and evaluating health checks) is globally distributed and carries a 100% availability SLA, but its management APIs and console run in US East (N. Virginia) and are outside that SLA, so record changes should not be on the recovery path.[^s2] Creating a DynamoDB table, load balancer, API Gateway API or Lambda function is likewise a control plane action and belongs before the event, not during it.[^s2]

## Costs and gotchas

- **You pay for idle headroom.** Running full-load capacity in each zone costs more than provisioning lean and launching on failure, but the cheaper approach is less effective in a zone- or Region-wide event because it depends on a control plane and on spare capacity being available in the unaffected zones.[^s1] Adding zones reduces the extra compute each zone must hold.[^s1]
- **Not just compute.** A common anti-pattern is applying static stability only to instances. Read replicas should be sized so that the replicas left after losing a zone or Region still meet total demand, and S3 One Zone-IA is not statically stable because its data becomes unreachable when that zone is lost.[^s1]
- **Zonal shift needs headroom.** ARC's zonal shift removes an AZ's capacity from service, so AWS recommends pre-scaling compute so peak traffic can still be served when one of the typical three replicas is offline.[^s4]
- **Risk level.** Well-Architected rates both REL11-BP04 and REL11-BP05 as medium risk if not established.[^s1][^s2]

## When to use it (and when not)

Use static stability for workloads whose recovery time objective cannot tolerate waiting for capacity to launch, and wherever an AZ or Regional impairment is in scope. AWS builds its own services this way: Lambda pre-provisions capacity in every AZ so the healthy zones can absorb an impaired zone's traffic without scaling up, a cost trade-off made in favour of its availability commitment.[^s5] For less critical workloads, scaling on demand after a failure may be an acceptable, cheaper compromise, as long as the team accepts the dependency on control planes and on available capacity.[^s1]

## Connections

Static stability is a practice of the Reliability Pillar (REL11) and complements cell-based architecture, which limits blast radius while static stability keeps each surviving unit serving.[^s1][^s3] It depends on spreading workloads across Availability Zones, and at Region scale it underpins warm-standby and active-active designs in multi-Region architecture and disaster recovery strategies.[^s1][^s2] Amazon Application Recovery Controller provides the data plane traffic-shifting it relies on, whereas reactive scaling with Amazon EC2 Auto Scaling is the control plane approach it contrasts with; AWS Lambda is an AWS service designed this way.[^s2][^s4][^s5]

## Sources

[^s1]: Amazon Web Services, "REL11-BP05 Use static stability to prevent bimodal behavior - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_withstand_component_failures_static_stability.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "REL11-BP04 Rely on the data plane and not the control plane during recovery - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_withstand_component_failures_avoid_control_plane.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Static stability - AWS Fault Isolation Boundaries". https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/static-stability.html (accessed 2026-11-17)
[^s4]: Amazon Web Services, "Best practices for zonal shifts in ARC - Amazon Application Recovery Controller (ARC)". https://docs.aws.amazon.com/r53recovery/latest/dg/route53-arc-best-practices.zonal-shifts.html (accessed 2026-11-17)
[^s5]: Adrian Hornsby and Marcia Villalba, "AWS Lambda: Resilience under-the-hood", AWS Compute Blog, 2023-01-23. https://aws.amazon.com/blogs/compute/aws-lambda-resilience-under-the-hood/ (accessed 2026-11-17)
