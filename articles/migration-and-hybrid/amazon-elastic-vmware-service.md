---
id: kt:amazon-elastic-vmware-service
type: Service
title: Amazon Elastic VMware Service
aliases: [Amazon EVS, EVS]
domain: migration-and-hybrid
summary: >-
  An AWS-native service, generally available since August 2025, that deploys a customer-licensed
  VMware Cloud Foundation (VCF) stack onto EC2 bare metal hosts inside the customer's own Amazon VPC,
  so VMware workloads can move to AWS while keeping vSphere tooling and IP addressing.
verified: 2026-11-20
status: current
broader: [kt:aws-migration-and-transfer-services]
related: [kt:vmware-migration-to-aws, kt:aws-direct-connect, kt:aws-site-to-site-vpn]
relations:
  - {predicate: runsOn, object: kt:amazon-ec2}
  - {predicate: dependsOn, object: kt:amazon-vpc}
  - {predicate: dependsOn, object: kt:aws-transit-gateway}
  - {predicate: alternativeTo, object: kt:aws-transform-mgn}
  - {predicate: integratesWith, object: kt:amazon-fsx}
  - {predicate: governedBy, object: kt:service-quotas}
  - {predicate: configuredVia, object: kt:aws-cloudformation}
sources:
  - id: s1
    title: "What is Amazon Elastic VMware Service?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/evs/latest/userguide/what-is-evs.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "AWS announces general availability of Amazon Elastic VMware Service (Amazon EVS)"
    publisher: What's New with AWS
    url: https://aws.amazon.com/about-aws/whats-new/2025/08/aws-general-availability-amazon-elastic-evs
    type: official-blog
    accessed: 2026-11-20
  - id: s3
    title: "Introducing Amazon Elastic VMware Service for running VMware Cloud Foundation on AWS"
    publisher: AWS News Blog
    url: https://aws.amazon.com/blogs/aws/introducing-amazon-elastic-vmware-service-for-running-vmware-cloud-foundation-on-aws
    type: official-blog
    accessed: 2026-11-20
  - id: s4
    title: "Amazon Elastic VMware Service endpoints and quotas"
    publisher: Amazon Web Services (AWS General Reference)
    url: https://docs.aws.amazon.com/general/latest/gr/evs.html
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "Amazon EVS service quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/evs/latest/userguide/service-quotas-evs.html
    type: official-docs
    accessed: 2026-11-20
  - id: s6
    title: "Amazon EVS is now available in additional Regions"
    publisher: What's New with AWS
    url: https://aws.amazon.com/about-aws/whats-new/2026/07/amazon-evs-available-in-additional-regions/
    type: official-blog
    accessed: 2026-11-20
created: 2026-11-20
---

# Amazon Elastic VMware Service

Amazon Elastic VMware Service (Amazon EVS) runs a VMware Cloud Foundation (VCF) environment on EC2 bare metal instances placed directly in a VPC that the customer owns.[^s1] The hosts and the VLAN subnets that connect them sit in your account, next to your other AWS resources.[^s1][^s3] AWS lists its main benefits as license portability, automated VCF deployment, and stretching on-premises networks to AWS without re-IPing workloads. You also keep full control of the VMware stack and its third-party add-ons.[^s1]

**Lifecycle (as of 2026-11):** AWS announced general availability on 5 August 2025. Launch Regions were N. Virginia, Ohio, Oregon, Tokyo, Frankfurt and Ireland.[^s2] Several Region expansions have followed. A July 2026 post added Seoul, Zurich and Stockholm and mentioned support for VCF 9.0 and 9.1.[^s6] The General Reference endpoint table now lists 25 Regions, each with a standard `evs.<region>.amazonaws.com` endpoint, and FIPS endpoints in the four US commercial Regions.[^s4]

## How it works

An EVS deployment has three layers: AWS networking, EC2 bare metal hosts, and the VCF software that EVS installs on them.[^s1]

- **Networking prerequisites.** You need a VPC with enough CIDR space and two Route Server endpoints, each with its own peer. You also plan the VLAN subnets that EVS creates from the CIDR ranges you supply.[^s3][^s1]
- **Environment creation.** In the EVS console, the AWS CLI or AWS CloudFormation (`AWS::EVS::Environment`), you enter your Broadcom-issued Site ID and VCF license keys, the host details, and the DNS details for the management appliances. EVS then provisions the hosts and deploys VCF. AWS describes this as taking a few hours.[^s1][^s3]
- **Day-2 operations.** After deployment you customize and run the environment in vSphere like any VCF site. You can manage it yourself or through an AWS Partner.[^s1]
- **Connectivity and storage.** On-premises vSphere connects over AWS Direct Connect, or over a VPN that terminates on a transit gateway.[^s3] Host-local vSAN is the primary datastore. External NFS or iSCSI storage, such as Amazon FSx for NetApp ONTAP, can add capacity without adding hosts.[^s3]

## Quotas, pricing and gotchas

The General Reference lists two EVS quotas as of 2026-11. Both default to the same value in every supported Region, and both are adjustable:[^s4]

- 3 EVS environments per account per Region
- 5 hosts per EVS environment

Each i4i.metal host consumes 128 vCPUs of the EC2 Running On-Demand Standard quota. If you use Dedicated Hosts, they draw on the EC2 Dedicated i4i Hosts quota instead. Raise these EC2 quotas before you create an environment.[^s4][^s5] HCX internet connectivity also needs an IPAM pool netmask of /28 or larger.[^s5]

At GA, AWS sold EVS on demand or on one- or three-year terms, with no license bundled. Customers bring VCF subscriptions bought from Broadcom.[^s2][^s3] The launch post set a licensing floor of 256 cores, which means at least four 64-core i4i.metal hosts. It also required AWS Business Support on accounts that run EVS.[^s3] Check current terms on the EVS pricing page before sizing; this run did not verify list prices.

Gotchas:

- The default quota of 5 hosts per environment is below what many production clusters need. File the increase request early.[^s4]
- GA launched with a single instance type (i4i.metal) and a single VCF version (5.2.1). Newer VCF releases arrived later, so confirm which combinations your target Region supports.[^s2][^s6]
- Because the hosts live in your account, you are responsible for capacity reservations, VPC design and Route Server setup.[^s3]

## When to use it (and when not)

Choose EVS when you need to leave a data center quickly, already own VCF entitlements, and want to keep the vSphere operating model, NSX networking and existing IP plans. It also suits estates that rely on VMware-specific third-party tools.[^s1][^s2] Choose rehosting onto native EC2 with AWS Transform MGN when the goal is to drop the hypervisor and its licensing. Do not choose EVS just to avoid change: you still operate VCF yourself, and you pay for whole bare metal hosts, not individual VMs.[^s3]

## Connections

EVS sits under **AWS migration and transfer services** as the relocate option for VMware estates, and is one of the paths described in **VMware migration to AWS**.[^s1] It **runs on Amazon EC2** bare metal and **depends on Amazon VPC** for VLAN subnets and Route Server endpoints. Hybrid links to it **depend on AWS Transit Gateway**, through **AWS Direct Connect** or **AWS Site-to-Site VPN**.[^s3] It **integrates with Amazon FSx** for external datastores.[^s3] It is an **alternative to AWS Transform MGN**, which rehosts VMs as native EC2 instances instead.[^s1] It is **governed by** quotas managed in **Service Quotas**,[^s5] and it can be **configured via AWS CloudFormation**.[^s1]

## Sources

[^s1]: Amazon Web Services, "What is Amazon Elastic VMware Service?". https://docs.aws.amazon.com/evs/latest/userguide/what-is-evs.html (accessed 2026-11-20)
[^s2]: What's New with AWS, "AWS announces general availability of Amazon Elastic VMware Service (Amazon EVS)" (2025-08-05). https://aws.amazon.com/about-aws/whats-new/2025/08/aws-general-availability-amazon-elastic-evs (accessed 2026-11-20)
[^s3]: AWS News Blog, "Introducing Amazon Elastic VMware Service for running VMware Cloud Foundation on AWS" (2025-08-05). https://aws.amazon.com/blogs/aws/introducing-amazon-elastic-vmware-service-for-running-vmware-cloud-foundation-on-aws (accessed 2026-11-20)
[^s4]: Amazon Web Services, AWS General Reference, "Amazon Elastic VMware Service endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/evs.html (accessed 2026-11-20)
[^s5]: Amazon Web Services, Amazon EVS User Guide, "Amazon EVS service quotas". https://docs.aws.amazon.com/evs/latest/userguide/service-quotas-evs.html (accessed 2026-11-20)
[^s6]: What's New with AWS, "Amazon EVS is now available in additional Regions" (2026-07-23). https://aws.amazon.com/about-aws/whats-new/2026/07/amazon-evs-available-in-additional-regions/ (accessed 2026-11-20)
