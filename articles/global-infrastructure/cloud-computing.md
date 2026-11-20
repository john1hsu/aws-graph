---
id: kt:cloud-computing
type: Concept
title: Cloud computing
aliases: [the cloud, cloud services]
domain: global-infrastructure
summary: >-
  A model for on-demand, network-based access to a shared, elastic pool of metered computing resources
  that are provisioned and released with minimal provider interaction; NIST defines it by five essential
  characteristics, three service models (IaaS, PaaS, SaaS) and four deployment models.
verified: 2026-11-20
status: concept
broader: [kt:distributed-computing]
related: [kt:amazon-web-services, kt:shared-responsibility-model, kt:aws-pricing-fundamentals, kt:aws-outposts]
relations:
  - {predicate: hasPart, object: kt:cloud-deployment-models}
sources:
  - id: s1
    title: "The NIST Definition of Cloud Computing (SP 800-145)"
    publisher: National Institute of Standards and Technology
    url: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-145.pdf
    type: standards-body
    accessed: 2026-11-20
  - id: s2
    title: "What is cloud computing? - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/what-is-cloud-computing.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Types of cloud computing - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-technical-content/latest/aws-overview/types-of-cloud-computing.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "What is Cloud Computing? - Cloud Computing Services, Benefits, and Types"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/what-is-cloud-computing/
    type: official-docs
    accessed: 2026-11-20
created: 2026-11-20
---

# Cloud computing

Cloud computing is a way of consuming IT rather than a product. The reference definition comes from NIST
Special Publication 800-145 (Mell and Grance, September 2011), which describes it as a model for
ubiquitous, convenient, on-demand network access to a shared pool of configurable resources (networks,
servers, storage, applications and services) that can be provisioned and released quickly with minimal
management effort or provider interaction.[^s1] AWS's own wording is shorter: on-demand delivery of
compute, database, storage, applications and other IT resources over the internet with pay-as-you-go
pricing.[^s2] In practice that means renting capacity from a provider that owns and maintains the
network-connected hardware, instead of buying and running servers yourself.[^s2][^s4]

## How it works: the five characteristics

NIST says a service is cloud computing when it shows five essential characteristics.[^s1]
**On-demand self-service**: consumers provision server time or storage themselves, without a human at the
provider. **Broad network access**: capabilities are reached over the network through standard
mechanisms from phones, laptops or workstations. **Resource pooling**: the provider serves many
consumers from shared, multi-tenant resources assigned dynamically, and the customer usually does not
know the exact physical location. **Rapid elasticity**: capacity scales out and in quickly, often
automatically, and looks unlimited to the consumer. **Measured service**: usage is metered so it can be
monitored, controlled and reported.[^s1] The metering is what makes pay-per-use billing possible; AWS
stresses provisioning exactly the right type and size of resource, almost instantly, and paying only for
what you use.[^s2]

## Service models: who controls what

The three service models differ in how much of the stack the customer manages.[^s1]

- **IaaS**: the consumer provisions processing, storage and networks and can run arbitrary software,
  controlling operating systems, storage and applications but not the underlying infrastructure.[^s1]
  AWS calls this the basic building blocks with the most control over resources.[^s4]
- **PaaS**: the consumer deploys applications built with the provider's supported languages and tools;
  the provider runs servers, operating systems and storage.[^s1][^s4]
- **SaaS**: the consumer uses a finished application, typically through a browser, and at most adjusts
  user-specific settings; web-based email is AWS's example.[^s1][^s4]

## Deployment models

NIST names four deployment models: private (one organization), community (a group with shared concerns
such as compliance), public (open to the general public on the provider's premises) and hybrid (two or
more distinct clouds bound together so data and applications can move between them, for example cloud
bursting).[^s1] AWS's overview whitepaper uses a simpler three-way split: **cloud** (an application fully
deployed in the cloud, built there or migrated), **hybrid** (cloud resources connected to existing
resources outside it, most often on-premises infrastructure) and **on-premises / private cloud**
(virtualization and resource-management tools in your own data center, which AWS notes delivers few
cloud benefits and resembles legacy IT).[^s3]

## Benefits, gotchas and when it fits

AWS frames the benefits as agility (resources in minutes, cheaper experiments), elasticity (scale with
demand instead of provisioning for peaks), cost savings (variable instead of fixed cost) and deploying
to several Regions in minutes to cut latency.[^s4] Typical uses it lists include backup and disaster
recovery, email, virtual desktops, development and test, big data analytics and customer-facing web
applications.[^s4] Gotchas follow from the same traits: metered billing means idle resources keep
costing money, and multi-tenant resource pooling means you usually do not know the exact physical
location, so data residency must be addressed by choosing where workloads run.[^s1] Workloads that
need dedicated on-site hardware may still sit on-premises or in a hybrid design.[^s3]

## Connections

**Amazon Web Services** (`kt:amazon-web-services`) is one public-cloud provider of all three service
models; its services are delivered through the self-service, metered model described here.[^s2][^s4]
**Cloud architecture** (`kt:cloud-architecture`) is the design discipline for systems built on this
model. The IaaS/PaaS/SaaS split in control maps directly onto the **shared responsibility model**
(`kt:shared-responsibility-model`): the more of the stack the provider manages, the less the customer controls and must look after.[^s1]
The measured-service characteristic underlies **AWS pricing fundamentals**
(`kt:aws-pricing-fundamentals`),[^s2] the hybrid deployment model is where **AWS Outposts**
(`kt:aws-outposts`) fits,[^s3] and **cloud deployment models** (`kt:cloud-deployment-models`) can be
explored in more depth as their own node. Cloud computing is itself a form of **distributed computing**
(`kt:distributed-computing`) delivered over a network.[^s1]

## Sources
[^s1]: National Institute of Standards and Technology, "The NIST Definition of Cloud Computing" (SP 800-145). https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-145.pdf (accessed 2026-11-20)
[^s2]: Amazon Web Services, "What is cloud computing? - Overview of Amazon Web Services". https://docs.aws.amazon.com/whitepapers/latest/aws-overview/what-is-cloud-computing.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "Types of cloud computing - Overview of Amazon Web Services". https://docs.aws.amazon.com/aws-technical-content/latest/aws-overview/types-of-cloud-computing.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "What is Cloud Computing? - Cloud Computing Services, Benefits, and Types". https://aws.amazon.com/what-is-cloud-computing/ (accessed 2026-11-20)
