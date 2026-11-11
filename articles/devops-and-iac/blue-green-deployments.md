---
id: kt:blue-green-deployments
type: Pattern
title: Blue/green deployments
aliases: [Blue-green deployment, Blue/green deployment, Red/black deployment]
domain: devops-and-iac
summary: >-
  A release pattern that runs the current version (blue) and the new version (green) as two
  separate environments and moves traffic from blue to green only after green is validated,
  keeping blue available for a fast rollback.
verified: 2026-11-11
status: concept
broader: [kt:ci-cd]
related: [kt:aws-codedeploy, kt:amazon-ecs, kt:amazon-rds, kt:aws-lambda, kt:immutable-infrastructure]
relations:
  - {predicate: dependsOn, object: kt:elastic-load-balancing}
  - {predicate: integratesWith, object: kt:amazon-route-53}
  - {predicate: configuredVia, object: kt:aws-cloudformation}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: contrastsWith, object: kt:canary-deployments}
  - {predicate: alternativeTo, object: kt:rolling-deployments}
sources:
  - id: s1
    title: "Introduction - Blue/Green Deployments on AWS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/blue-green-deployments/introduction.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Services for blue/green deployments - Blue/Green Deployments on AWS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/blue-green-deployments/services-for-bluegreen-deployments.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "When Blue/Green Deployments Are Not Recommended - Blue/Green Deployments on AWS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/blue-green-deployments/when-bluegreen-deployments-are-not-recommended.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Amazon ECS blue/green deployments"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-blue-green.html
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Working with deployment configurations in CodeDeploy"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "Overview of Amazon RDS Blue/Green Deployments"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/blue-green-deployments-overview.html
    type: official-docs
    accessed: 2026-11-11
created: 2026-11-11
---

# Blue/green deployments

A blue/green deployment releases a new application version by running two environments side by side and shifting traffic between them. **Blue** is the environment serving production today; **green** is a separately provisioned environment carrying the new version. Once green is staged and tested, traffic moves to it; if something goes wrong, traffic goes back to blue.[^s1] Because green lives on its own resources, standing it up does not disturb blue, and rollback is a routing change rather than a redeploy of an older build, which also leaves the faulty version in place for debugging.[^s1]

## How it works

The core design decision is the **environment boundary**: which components are duplicated per colour and where the traffic switch happens.[^s1] On AWS the switch is usually made at one of three layers.[^s2]

- **DNS** — Amazon Route 53 updates records in a hosted zone (for example weighted records) to send users to green. Short TTLs help, but resolver caching means a period of mixed traffic.[^s2]
- **Load balancer** — Elastic Load Balancing moves traffic between target groups or listeners, with health checks deciding whether targets receive requests.[^s2]
- **Platform-managed** — AWS Elastic Beanstalk can swap environment URLs; AWS CloudFormation can provision both environments from templates; AWS CodeDeploy automates blue/green for EC2, Lambda and ECS, with Amazon CloudWatch alarms watching deployment health.[^s2]

The shift does not have to be all-or-nothing. Green can receive test traffic or a small slice of real traffic first (a canary), which limits the blast radius to that slice and that time window.[^s1] Capacity can follow traffic too: scale green out as it takes load, scale blue in as it loses load, and decommission blue after a successful release.[^s1]

## AWS implementations

**Amazon ECS (built-in).** ECS now runs blue/green natively for services fronted by Elastic Load Balancing, Service Connect or VPC Lattice; headless services get task replacement without traffic shifting.[^s4] Deployments move through lifecycle stages such as `TEST_TRAFFIC_SHIFT` and `PRODUCTION_TRAFFIC_SHIFT`, where a Lambda hook can run automated tests or a pause hook can hold until `ContinueServiceDeployment` is called. After the production shift, a configurable **bake time** keeps blue running so rollback stays instant.[^s4] With a Network Load Balancer, each traffic-shift stage takes roughly 10 minutes longer while ECS confirms the shift is safe.[^s4]

**AWS CodeDeploy.** For ECS and Lambda, predefined deployment configurations choose canary, linear or all-at-once shifting — for example `ECSCanary10Percent5Minutes` (10% first, the remaining 90% five minutes later) or `LambdaLinear10PercentEvery1Minute`.[^s5] Custom canary and linear configurations are allowed, except for CloudFormation-managed ECS blue/green deployments, and an ECS service behind a Network Load Balancer supports only `ECSAllAtOnce` (as of 2026-11).[^s5] For EC2, blue/green rules such as `OneAtATime` or `HalfAtATime` govern how traffic is rerouted to the replacement instances.[^s5]

**Amazon RDS Blue/Green Deployments.** For the data tier, RDS for MySQL, MariaDB and PostgreSQL can clone a production topology into a green copy kept in sync by replication, where you apply engine upgrades or parameter changes. Switchover usually completes in under a minute without data loss or application changes, because endpoints move to the new environment; Aurora documents its own equivalent.[^s6] Green is read-only by default, and writing to it risks replication conflicts.[^s6]

## Gotchas and when not to use it

- **Double capacity during the overlap.** Running both revisions at once can temporarily roughly double resource use.[^s4]
- **Shared data stores.** The whitepaper advises against blue/green when schema changes cannot be decoupled from code, or when blue and green cannot share a data store because of data locality or isolation; two-way synchronisation then adds more risk than it removes.[^s3]
- **"Deployment-aware" applications.** Feature flags or sync routines that exist only to survive the cutover add complexity and undercut immutable infrastructure.[^s3]
- **Vendor upgrade paths.** Commercial software whose vendor prescribes an in-place upgrade procedure is a poor fit; simulating blue/green around it can cancel the benefits.[^s3]

Choose blue/green when fast, low-risk rollback is worth paying for temporary duplicate capacity; choose rolling or in-place updates when capacity is tight and the application tolerates mixed versions.[^s1][^s4]

## Connections

Blue/green is a release technique within **CI/CD**, and pipelines commonly hand the cutover to **AWS CodeDeploy** or to **Amazon ECS**'s native strategy, while **AWS Lambda** versions can shift traffic through CodeDeploy configurations.[^s4][^s5] It **depends on** a routing layer — **Elastic Load Balancing** or **Amazon Route 53** — to move traffic, can be **configured via AWS CloudFormation**, and is **monitored by Amazon CloudWatch** alarms that trigger rollback.[^s2] **Amazon RDS** applies the same idea to databases.[^s6] It **contrasts with** canary deployments (a traffic-shifting style that blue/green can use but which does not require a full duplicate environment) and is an **alternative to** rolling deployments; it builds on the principle of **immutable infrastructure**.[^s1][^s3]

## Sources
[^s1]: Amazon Web Services, "Introduction - Blue/Green Deployments on AWS". https://docs.aws.amazon.com/whitepapers/latest/blue-green-deployments/introduction.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Services for blue/green deployments - Blue/Green Deployments on AWS". https://docs.aws.amazon.com/whitepapers/latest/blue-green-deployments/services-for-bluegreen-deployments.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "When Blue/Green Deployments Are Not Recommended". https://docs.aws.amazon.com/whitepapers/latest/blue-green-deployments/when-bluegreen-deployments-are-not-recommended.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Amazon ECS blue/green deployments". https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-blue-green.html (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Working with deployment configurations in CodeDeploy". https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html (accessed 2026-11-11)
[^s6]: Amazon Web Services, "Overview of Amazon RDS Blue/Green Deployments". https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/blue-green-deployments-overview.html (accessed 2026-11-11)
