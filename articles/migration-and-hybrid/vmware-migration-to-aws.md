---
id: kt:vmware-migration-to-aws
type: Concept
title: VMware migration to AWS
aliases: [Migrating VMware workloads to AWS, VMware to AWS migration, VMware exit to AWS, Transitioning from VMware to the AWS Cloud]
domain: migration-and-hybrid
summary: >-
  The set of paths AWS offers for moving VMware vSphere workloads to the AWS Cloud: rehosting
  VMs onto Amazon EC2 (planned with AWS Transform and executed by AWS Transform MGN), running
  VMware Cloud Foundation unchanged on Amazon Elastic VMware Service, or modernizing onto
  cloud-native services.
verified: 2026-11-14
status: concept
broader: [kt:aws-migration-and-transfer-services, kt:seven-rs-migration-strategies]
related: [kt:aws-migration-acceleration-program, kt:aws-transform, kt:aws-outposts]
relations:
  - {predicate: dependsOn, object: kt:aws-transform-mgn}
  - {predicate: dependsOn, object: kt:amazon-elastic-vmware-service}
  - {predicate: dependsOn, object: kt:aws-transit-gateway}
sources:
  - id: s1
    title: "Transitioning from VMware to the AWS Cloud — Introduction"
    publisher: Amazon Web Services (AWS Prescriptive Guidance)
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/transitioning-from-vmware-to-the-aws-cloud/introduction.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "What are VMware alternatives?"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/what-is/vmware-alternatives/
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "What is Amazon Elastic VMware Service?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/evs/latest/userguide/what-is-evs.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Concepts and components of Amazon EVS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/evs/latest/userguide/concepts.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "Amazon EVS FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/evs/faqs
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "AWS Transform for VMware (AWS Transform User Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/transform/latest/userguide/transform-app-vmware
    type: official-docs
    accessed: 2026-11-14
created: 2026-11-14
---

# VMware migration to AWS

VMware migration to AWS is the practice of moving workloads that run on VMware vSphere — usually in an on-premises data center — onto AWS. AWS Prescriptive Guidance frames it as three stages: analyse current on-premises operations to set a baseline, build a migration blueprint that keeps the business running and secure during the move, then evolve IT operations toward cloud-optimized practices.[^s1] It is not a single service but a choice among several destinations, each mapping onto one of the 7 Rs (rehost, relocate, replatform, refactor).[^s2]

## The main paths

- **Rehost onto Amazon EC2.** Virtual machines are replicated at the block level and relaunched as EC2 instances, which removes hypervisor licensing and keeps application changes minimal; AWS suggests this as a common first phase before modernizing later.[^s2]
- **Relocate onto Amazon EVS.** Amazon Elastic VMware Service deploys a VMware Cloud Foundation (VCF) environment directly on EC2 bare metal instances inside your own Amazon VPC, so teams keep vCenter, vSAN and NSX, can extend on-premises networks without changing IP addresses, and keep existing runbooks.[^s3][^s2] AWS positions it for organizations with near-term license renewals or deeply integrated VMware estates.[^s2]
- **Modernize to cloud-native services.** Replatform or refactor onto containers (Amazon ECS or EKS), managed databases (Amazon RDS or Aurora), AWS Lambda, or Amazon WorkSpaces for virtual desktops — slower, but the most efficient long term.[^s2]
- **Hybrid and other hypervisors.** AWS Outposts for workloads that must stay on premises, or partner platforms such as Nutanix Cloud Clusters on AWS and Red Hat OpenShift Service on AWS.[^s2]

## Rehosting with AWS Transform

AWS Transform for VMware is an agentic-AI, conversational service that walks a team through four stages: discovery (via AWS Application Discovery Service collectors, the open-source Export for vCenter tool, or imported inventory data), migration planning that groups applications and sequences waves, network migration that translates the source network into an Amazon VPC design, and server rehosting onto EC2 with guided testing and cutover.[^s6] Its supported operating systems follow the AWS Application Migration Service list, the replication engine now named AWS Transform MGN.[^s6] It is not VMware-only: Hyper-V and other virtual or bare metal servers are also supported sources, and the target is always EC2.[^s6]

## Running VCF on Amazon EVS

An EVS *environment* is a logical container holding one consolidated VCF domain and maps to one SDDC Manager appliance; each *host* is an ESX host on an EC2 bare metal instance whose local NVMe instance store backs vSAN.[^s4] Workloads move in with VMware HCX, which needs connectivity over AWS Direct Connect with an associated transit gateway, or a Site-to-Site VPN attachment to a transit gateway.[^s4] As of 2026-11 the EVS FAQ lists i4i.metal and i7i.metal-24xl hosts, VCF versions 9.1, 9.0, 5.2.2 and 5.2.1, a maximum of 32 nodes (with Broadcom recommending at least 4 for production), and availability in 22 Regions.[^s5] Customers bring their own VCF licence bought from Broadcom; AWS bills hourly for EC2 hosts, VPC Route Server endpoints and the EVS control plane, with hosts available on demand or on 1- or 3-year terms.[^s5] AWS supports EVS including VCF, and customers may also go to Broadcom for VCF issues.[^s5]

## Gotchas

- **VMware Cloud on AWS is no longer sold by AWS.** Since April 30, 2024, AWS and its channel partners stopped reselling it; it remains available from Broadcom.[^s1]
- **EVS host storage is ephemeral.** Stopping or terminating an EVS host instance without first decommissioning it in VCF can lose vSAN data.[^s4]
- **Security groups don't filter VLAN subnets.** EC2 security group rules are not enforced on EVS network interfaces in VLAN subnets; use network ACLs instead.[^s4]
- **Plan VLAN CIDRs up front.** EVS creates VLAN subnets at environment creation; they cannot be changed or added later, must be /28 to /24, and must not overlap VPC CIDRs.[^s4]

## When to choose which

Choose EVS when the goal is a fast data-center exit with the VMware operating model intact, or when licence timing forces a move before applications can change.[^s2][^s3] Choose rehosting to EC2 with AWS Transform when you want to drop the hypervisor layer and its licences and are comfortable operating EC2 natively.[^s2][^s6] Reserve refactoring for applications whose business value justifies the effort.[^s2] Many estates use more than one path, deciding per workload during portfolio assessment.[^s1]

## Connections

- **Broader:** VMware migration is a scenario within AWS migration and transfer services (`kt:aws-migration-and-transfer-services`) and applies the 7 Rs (`kt:seven-rs-migration-strategies`) — rehost, relocate or refactor.[^s2]
- **dependsOn:** rehosting relies on AWS Transform MGN (`kt:aws-transform-mgn`) for replication and cutover;[^s6] relocation relies on Amazon Elastic VMware Service (`kt:amazon-elastic-vmware-service`);[^s3] and HCX migration into EVS needs AWS Transit Gateway (`kt:aws-transit-gateway`) with Direct Connect or VPN.[^s4]
- **Related:** AWS Transform (`kt:aws-transform`) plans the rehost;[^s6] the Migration Acceleration Program (`kt:aws-migration-acceleration-program`) and the Optimization and Licensing Assessment help fund and size the move;[^s2] AWS Outposts (`kt:aws-outposts`) covers workloads that must stay on premises.[^s2]

## Sources

[^s1]: Amazon Web Services, AWS Prescriptive Guidance, "Transitioning from VMware to the AWS Cloud — Introduction". https://docs.aws.amazon.com/prescriptive-guidance/latest/transitioning-from-vmware-to-the-aws-cloud/introduction.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "What are VMware alternatives?". https://aws.amazon.com/what-is/vmware-alternatives/ (accessed 2026-11-14)
[^s3]: Amazon Web Services, Amazon EVS User Guide, "What is Amazon Elastic VMware Service?". https://docs.aws.amazon.com/evs/latest/userguide/what-is-evs.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, Amazon EVS User Guide, "Concepts and components of Amazon EVS". https://docs.aws.amazon.com/evs/latest/userguide/concepts.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, "Amazon EVS FAQs". https://aws.amazon.com/evs/faqs (accessed 2026-11-14)
[^s6]: Amazon Web Services, AWS Transform User Guide, "AWS Transform for VMware". https://docs.aws.amazon.com/transform/latest/userguide/transform-app-vmware (accessed 2026-11-14)
