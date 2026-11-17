---
id: kt:immutable-infrastructure
type: Pattern
title: Immutable infrastructure
aliases: [Immutable deployments, Immutable deployment pattern, Immutable servers]
domain: devops-and-iac
summary: >-
  An operating model in which running resources are never patched, reconfigured or upgraded in
  place; every change is shipped by building new resources from a versioned definition, shifting
  traffic to them, and discarding the old ones.
verified: 2026-11-17
status: concept
broader: [kt:devops, kt:infrastructure-as-code]
related: [kt:blue-green-deployments, kt:canary-deployments, kt:cloudformation-drift-detection, kt:aws-elastic-beanstalk, kt:ec2-image-builder]
relations:
  - {predicate: dependsOn, object: kt:amazon-machine-images}
  - {predicate: integratesWith, object: kt:amazon-ec2-auto-scaling}
  - {predicate: configuredVia, object: kt:aws-cloudformation}
  - {predicate: implements, object: kt:aws-well-architected-framework}
  - {predicate: coveredBy, object: kt:aws-certified-devops-engineer-professional}
sources:
  - id: s1
    title: "REL08-BP04 Deploy using immutable infrastructure - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_tracking_change_management_immutable_infrastructure.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Amazon EC2 instance mutability in AMS - AMS Advanced Application Developer's Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/managedservices/latest/appguide/compute-instance-mutability-aog.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "What is Image Builder? - EC2 Image Builder"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "How an instance refresh works in an Auto Scaling group - Amazon EC2 Auto Scaling"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/autoscaling/ec2/userguide/instance-refresh-overview.md
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "Immutable environment updates - AWS Elastic Beanstalk"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environmentmgmt-updates-immutable.html
    type: official-docs
    accessed: 2026-11-17
  - id: s6
    title: "AWS Certified DevOps Engineer - Professional: Exam Guide (DOP-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/devops-engineer-professional-02/devops-engineer-professional-02.pdf
    type: official-learning
    accessed: 2026-11-17
created: 2026-11-17
---

# Immutable infrastructure

Immutable infrastructure is an operating model in which production resources are never changed after they go live: no in-place software updates, security patches or configuration edits. When something needs to change, a new set of resources carrying the change is built and deployed, and the old set is retired.[^s1] The AWS Well-Architected Framework makes this a Reliability best practice (REL08-BP04, "Deploy using immutable infrastructure"), lists in-place changes to running resources as the anti-pattern, and rates the risk of not adopting it as medium.[^s1] The opposite, **mutable** model deploys updates onto existing instances, typically mixing manually pushed changes with infrastructure as code, and does not depend on producing new machine images.[^s2]

## How it works

The unit of change is a new artifact, not a command run on a server. For EC2 fleets that artifact is usually a pre-baked Amazon Machine Image (AMI): instances are launched from an image that already contains the required software, and an update means terminating instances and replacing them with ones launched from an updated AMI, usually on a rolling basis so some capacity stays in service.[^s2] The new resources are stood up alongside the existing ones, validated automatically, and then receive traffic gradually; the same flow applies whether the change is application code, an OS patch, a configuration tweak or an infrastructure change.[^s1]

Well-Architected's implementation steps are: forbid in-place modification (using IAM to control who and what may touch resources); automate builds and deployments with pre-baked images from EC2 Image Builder and with IaC tools such as AWS CloudFormation, AWS SAM, AWS CDK or AWS Cloud Control API; limit user impact with canary or blue/green releases; and detect drift, for example with CloudFormation drift detection, because any unmanaged change violates the model.[^s1]

## AWS building blocks

- **EC2 Image Builder** automates building, testing and distributing AMIs and container images through scheduled pipelines; it distributes an image only after every configured test passes, which makes it a natural "bakery" for immutable fleets. The service itself is free; you pay for the EC2 instances, EBS snapshots, S3 logs and other resources it uses (as of 2026-11).[^s3]
- **EC2 Auto Scaling instance refresh** rolls a new launch template (for example a new AMI) across a group. Minimum and maximum healthy percentages control batch size; setting both to 100% launches each replacement before terminating an old instance. Checkpoints, bake time and skip matching are optional.[^s4] A refresh can actively replace instances for at most 14 days, and it fails if blocked for an hour by unhealthy, standby or scale-in-protected instances.[^s4]
- **AWS Elastic Beanstalk immutable updates** launch a temporary Auto Scaling group behind the environment's load balancer, start with one instance, scale it to match the original group once health checks pass, then move the new instances in and terminate the old ones. A failure is undone by deleting the temporary group, so the fleet is never left half-deployed, unlike a failed rolling update.[^s5]

## Benefits, costs and gotchas

AWS lists the payoffs as consistency across environments, less configuration drift, atomic deployments that either fully succeed or change nothing, no upgrade logic to maintain, safer rollback because the previous version is untouched, and a smaller attack surface, since remote access such as SSH can be turned off.[^s1]

The costs are real, though:

- **Temporary double capacity.** Beanstalk's immutable updates briefly double the instance count, so large environments or accounts near their On-Demand instance quota may need rolling updates instead.[^s5]
- **Lost local state.** Replacing every instance resets accumulated EC2 burst credit balances, and anything stored only on the instance disappears.[^s5]
- **Hidden mutability.** Instance refresh's skip matching compares only launch template versions and AMI IDs; code pulled by a user-data script is invisible to it, so AWS advises turning skip matching off in that setup.[^s4]
- **Configuration coupling.** Beanstalk rejects an immutable update combined with resource changes such as load balancer settings; those must go in a separate update or through a blue/green swap.[^s5]

Choose immutability when repeatable environments, fast rollback and drift-free fleets matter more than build time and temporary extra capacity. Mutable, in-place patching remains common for long-lived, stateful hosts.[^s2][^s1]

## Connections

Immutable infrastructure is a **DevOps** practice that **infrastructure as code** makes practical, and AWS **CloudFormation** templates are a usual way to **configure** it.[^s1] It **depends on** versioned **Amazon Machine Images**, often produced by **EC2 Image Builder**, and **integrates with Amazon EC2 Auto Scaling** for replacing fleets.[^s2][^s3][^s4] **Blue/green** and **canary** deployments are the release patterns that move traffic onto the new resources, **AWS Elastic Beanstalk** packages the pattern as an update policy, and **CloudFormation drift detection** catches out-of-band edits that break it.[^s1][^s5] It **implements** REL08-BP04 of the **AWS Well-Architected Framework**.[^s1] The DOP-C02 exam guide for **AWS Certified DevOps Engineer – Professional** lists mutable versus immutable deployment patterns as knowledge under Task 1.4 on deployment strategies, so the topic is **covered by** that certification.[^s6]

## Sources
[^s1]: Amazon Web Services, "REL08-BP04 Deploy using immutable infrastructure - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_tracking_change_management_immutable_infrastructure.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Amazon EC2 instance mutability in AMS". https://docs.aws.amazon.com/managedservices/latest/appguide/compute-instance-mutability-aog.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "What is Image Builder? - EC2 Image Builder". https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html (accessed 2026-11-17)
[^s4]: Amazon Web Services, "How an instance refresh works in an Auto Scaling group". https://docs.aws.amazon.com/autoscaling/ec2/userguide/instance-refresh-overview.md (accessed 2026-11-17)
[^s5]: Amazon Web Services, "Immutable environment updates - AWS Elastic Beanstalk". https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environmentmgmt-updates-immutable.html (accessed 2026-11-17)
[^s6]: Amazon Web Services, "AWS Certified DevOps Engineer - Professional: Exam Guide (DOP-C02)". https://docs.aws.amazon.com/pdfs/aws-certification/latest/devops-engineer-professional-02/devops-engineer-professional-02.pdf (accessed 2026-11-17)
