---
id: kt:control-plane-and-data-plane
type: Concept
title: Control plane and data plane
aliases: [control plane vs data plane, control planes and data planes, data plane and control plane]
domain: global-infrastructure
summary: >-
  The split AWS draws inside every service between the control plane (the administrative APIs that create,
  describe, update, delete and list resources) and the data plane (the part that delivers the service's
  primary function), which determines how services fail and what a workload should rely on during recovery.
verified: 2026-11-18
status: concept
broader: [kt:aws-global-infrastructure, kt:reliability-pillar]
related: [kt:availability-zones, kt:aws-partitions, kt:amazon-route-53, kt:amazon-application-recovery-controller, kt:multi-region-architecture]
relations:
  - {predicate: enables, object: kt:static-stability}
sources:
  - id: s1
    title: "Control planes and data planes - AWS Fault Isolation Boundaries (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/control-planes-and-data-planes.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Fault tolerance and fault isolation - Availability and Beyond: Understanding and Improving the Resilience of Distributed Systems on AWS (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/availability-and-beyond-improving-resilience/fault-tolerance-and-fault-isolation.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "REL11-BP04 Rely on the data plane and not the control plane during recovery - Reliability Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/rel_withstand_component_failures_avoid_control_plane.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Global services - AWS Fault Isolation Boundaries (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/global-services.html
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Simplified AWS service information for programmatic access - Service Authorization Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/service-authorization/latest/reference/service-reference.md
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "Enabling accelerated recovery for managing public DNS records - Amazon Route 53 Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/accelerated-recovery.html
    type: official-docs
    accessed: 2026-11-18
created: 2026-11-18
---

# Control plane and data plane

AWS describes each of its services as two distinct parts. The **control plane** is the set of administrative and configuration APIs used to create, read or describe, update, delete and list (CRUDL) resources; the **data plane** is whatever delivers the service's primary function. The terms come from network routers, where the data plane forwards packets according to rules and the control plane computes and distributes those rules.[^s1] AWS keeps the two separate for performance, scalability and availability, and the distinction shapes both how AWS services fail and how AWS tells customers to design recovery.[^s1]

## How it works

The whitepaper *AWS Fault Isolation Boundaries* gives paired examples: launching an EC2 instance is a control plane action while the running instance is the data plane; creating and managing an EBS volume versus reading and writing it; creating an S3 bucket versus getting and putting objects; and managing Route 53 records versus answering DNS queries.[^s1]

The two halves differ sharply in complexity. A control plane is a workflow orchestrator with business logic and many dependencies. Launching one EC2 instance means finding a host with capacity, allocating network interfaces, preparing an EBS volume, generating IAM credentials, adding security group rules and propagating network configuration to other hosts, and a failure in any step fails the whole request.[^s1] Data planes are deliberately simpler, with fewer dependencies and higher request volumes.[^s1][^s2] The *Availability and Beyond* whitepaper draws the consequence: control planes, having more moving parts, are statistically more likely to fail and have lower availability than data planes.[^s2] Broad failure events are likewise less likely to hit a data plane, so a dependency on a data plane is the safer bet.[^s1]

## Global services: one Region for the control plane

The split matters most for global services, which host their control plane in a single Region while their data plane is distributed. As of 2026-11, AWS lists the control planes of IAM, AWS Organizations, AWS Account Management, Route 53, CloudFront and Shield Advanced in us-east-1, and those of Amazon Application Recovery Controller (ARC), AWS Global Accelerator and AWS Network Manager in us-west-2.[^s4] Some ordinary Regional operations also reach across: S3 bucket-level APIs such as `CreateBucket` and `PutBucketPolicy` depend on us-east-1, and services such as ELB, RDS and ElastiCache rely on the Route 53 control plane when they create DNS records.[^s4] The AWS Management Console for most global services is hosted in the same Region as the control plane, so console access is a control plane dependency too.[^s4]

## Designing around the split

- **Recover with the data plane.** Well-Architected best practice REL11-BP04 says to use as few control plane operations as possible to recover, rescale, fail over or heal a workload, and rates not doing so as medium risk because it can lengthen RTO and MTTR.[^s3] Its substitutions: pre-scaled EC2 capacity instead of Auto Scaling, Lambda's built-in scaling instead of launching instances, adding pods to over-provisioned Kubernetes nodes instead of adding nodes, and ARC traffic shifting instead of editing Route 53 records.[^s3]
- **Create resources ahead of time.** New Lambda functions, load balancers, API Gateway APIs and DynamoDB tables are control plane actions, so they belong before an incident, with failover rehearsed.[^s3]
- **Don't call a control plane from your data plane.** *Availability and Beyond* states this as a rule, especially during recovery.[^s2]
- **Use Regional endpoints.** AWS STS, the console and sign-in offer a global endpoint hosted in one Region as well as Regional endpoints; the Regional endpoint avoids an unintended cross-Region dependency.[^s4]
- **Know which calls are which.** The service reference JSON behind the Service Authorization Reference tags each IAM action with an `IsDataPlane` property derived from CloudTrail event categories; S3 `GetObject` is true and `CreateBucket` is false, and actions not yet classified omit the field.[^s5]

## Gotchas and recent change

Route 53 is the standard example: its data plane, which answers queries and runs health checks, is globally distributed with a 100% availability SLA, while its management APIs and console run in us-east-1 and are not covered by that SLA.[^s3] Since December 2025, Route 53 *accelerated recovery* narrows that gap for public hosted zones: when enabled in advance, Route 53 keeps a copy of the zone in US West (Oregon) and, about 60 minutes after an extended us-east-1 impairment is detected, routes a limited set of record-management APIs there.[^s4][^s6] It does not cover private hosted zones, and hosted zones cannot be created or deleted during failover, so it complements rather than replaces data plane failover.[^s6]

## Connections

The concept sits under **AWS global infrastructure**, since it explains how global services behave within an **AWS partition** and why control planes concentrate in us-east-1 and us-west-2.[^s4] It underpins the **Reliability Pillar** and **static stability**, which pre-provisions capacity so recovery needs only data plane actions, applied across **Availability Zones** and in **multi-Region architecture**.[^s2][^s3] **Amazon Route 53** illustrates the split, and **Amazon Application Recovery Controller** is the AWS tool for shifting traffic through data plane operations instead.[^s3][^s4]

## Sources

[^s1]: Amazon Web Services, "Control planes and data planes - AWS Fault Isolation Boundaries". https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/control-planes-and-data-planes.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Fault tolerance and fault isolation - Availability and Beyond: Understanding and Improving the Resilience of Distributed Systems on AWS". https://docs.aws.amazon.com/whitepapers/latest/availability-and-beyond-improving-resilience/fault-tolerance-and-fault-isolation.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "REL11-BP04 Rely on the data plane and not the control plane during recovery - Reliability Pillar". https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/rel_withstand_component_failures_avoid_control_plane.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Global services - AWS Fault Isolation Boundaries". https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/global-services.html (accessed 2026-11-18)
[^s5]: Amazon Web Services, "Simplified AWS service information for programmatic access - Service Authorization Reference". https://docs.aws.amazon.com/service-authorization/latest/reference/service-reference.md (accessed 2026-11-18)
[^s6]: Amazon Web Services, "Enabling accelerated recovery for managing public DNS records - Amazon Route 53 Developer Guide". https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/accelerated-recovery.html (accessed 2026-11-18)
