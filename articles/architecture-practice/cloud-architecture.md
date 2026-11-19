---
id: kt:cloud-architecture
type: Concept
title: Cloud architecture
aliases: [cloud solution architecture, cloud architecture design, architecting for the cloud]
domain: architecture-practice
summary: >-
  The discipline of deciding how the components of a workload are composed from on-demand, elastic,
  metered cloud resources and how they interact, trading off quality attributes such as reliability,
  cost and performance; on AWS it is codified by the Well-Architected Framework and its design principles.
verified: 2026-11-19
status: concept
broader: [kt:cloud-computing]
related: [kt:amazon-web-services, kt:shared-responsibility-model, kt:event-driven-architecture, kt:multi-region-architecture, kt:cell-based-architecture]
relations:
  - {predicate: hasPart, object: kt:aws-well-architected-framework}
  - {predicate: hasPart, object: kt:aws-cloud-adoption-framework}
  - {predicate: hasPart, object: kt:aws-serverless-services}
sources:
  - id: s1
    title: "The NIST Definition of Cloud Computing (SP 800-145)"
    publisher: National Institute of Standards and Technology
    url: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-145.pdf
    type: standards-body
    accessed: 2026-11-19
  - id: s2
    title: "Definitions - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/definitions.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "General design principles - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/general-design-principles.html
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "Six advantages of cloud computing - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/six-advantages-of-cloud-computing.html
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "AWS Reference Architecture Diagrams"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/reference-architecture-diagrams/
    type: official-docs
    accessed: 2026-11-19
created: 2026-11-19
---

# Cloud architecture

Cloud architecture is the practice of designing how a workload's parts are built from cloud resources and how those parts work together. The AWS Well-Architected Framework gives the vocabulary: a **component** is the code, configuration and AWS resources that together meet a requirement; a **workload** is a set of components that delivers business value; and **architecture** is how those components work together, with their communication and interaction usually the focus of architecture diagrams. A company's full collection of workloads is its **technology portfolio**, and an architecture changes over **milestones** from design through implementation, testing, go-live and production.[^s2]

## What makes it "cloud" architecture

The substrate is what differs from traditional data-center design. NIST SP 800-145 (Mell and Grance, September 2011) defines cloud computing as on-demand network access to a shared pool of configurable resources that can be provisioned and released quickly with minimal management effort, and lists five essential characteristics: on-demand self-service, broad network access, resource pooling (multi-tenant), rapid elasticity and measured service.[^s1] The same publication names three service models (IaaS, PaaS and SaaS), which decide how much of the stack the customer controls, and four deployment models: private, community, public and hybrid cloud.[^s1] An architect's first choices are therefore which service model each component uses and where it runs, because those choices set what the customer must operate and secure.

AWS frames the business case in six advantages: trade fixed expense for variable expense, benefit from economies of scale, stop guessing capacity, increase speed and agility, stop spending money running data centers, and go global in minutes by deploying to multiple Regions.[^s4] Each advantage maps to a design habit: pay-per-use pricing rewards turning resources off, elasticity rewards stateless horizontally scaled tiers, and global Regions make latency and data location explicit design inputs.[^s4]

## How it works on AWS: design principles

The Well-Architected Framework lists six general design principles that apply across all pillars:[^s3]

- **Stop guessing your capacity needs** – scale automatically instead of provisioning for a guessed peak and paying for idle resources.[^s3]
- **Test systems at production scale** – build a production-scale test environment on demand, pay only while it runs, then tear it down.[^s3]
- **Automate with architectural experimentation in mind** – automation makes it cheap to replicate a workload, track and audit changes, and roll back.[^s3]
- **Consider evolutionary architectures** – because testing is cheap and automated, designs can keep changing with business needs instead of being fixed up front.[^s3]
- **Drive architectures using data** – collect data on how architectural choices affect workload behavior and improve on facts.[^s3]
- **Improve through game days** – rehearse production events regularly to find weaknesses and build team experience.[^s3]

## Trade-offs and gotchas

Cloud architecture is mostly about deliberate trade-offs. Well-Architected says pillar trade-offs should follow business context: a development environment might favor cost and sustainability over reliability, while a mission-critical system might accept higher cost for reliability. Security and operational excellence are generally not traded away.[^s2] Common mistakes follow from ignoring the substrate: sizing for peak as if hardware were fixed, treating test environments as permanent, or making manual changes that can't be reproduced or rolled back, all of which work against the design principles above.[^s3] Elasticity also isn't free; measured service means every resource left running shows up on the bill.[^s1][^s4]

## When to use which reference

For the definition of cloud computing itself (service and deployment models), NIST SP 800-145 is the neutral reference.[^s1] For evaluating a specific workload on AWS, use the Well-Architected Framework and its pillars and lenses.[^s2][^s3] For a starting design in a given industry or technology area, AWS publishes a searchable library of reference architecture diagrams, each listing the AWS services it combines, across categories such as data and analytics, serverless and application architecture, hybrid and edge, security and compliance, and industries from financial services to telecom.[^s5] Reference diagrams are examples, not requirements; they still need checking against the workload's own constraints.[^s2][^s5]

## Connections

Cloud architecture sits under **cloud computing** (`kt:cloud-computing`), whose NIST characteristics define the resource model it designs for.[^s1] It contains the **AWS Well-Architected Framework** (`kt:aws-well-architected-framework`), AWS's codified review of architecture quality, and the related **AWS Cloud Adoption Framework** (`kt:aws-cloud-adoption-framework`) for the organizational side of moving to the cloud.[^s2] **AWS serverless services** (`kt:aws-serverless-services`) are one architectural style in it, alongside **event-driven**, **cell-based** and **multi-Region** architectures, each a pattern for one set of trade-offs.[^s3] It is related to **Amazon Web Services** (`kt:amazon-web-services`) as the platform whose six advantages motivate these designs, and to the **shared responsibility model** (`kt:shared-responsibility-model`), because the chosen service model decides which layers the customer secures.[^s1][^s4]

## Sources
[^s1]: Peter Mell and Timothy Grance, "The NIST Definition of Cloud Computing", NIST Special Publication 800-145 (September 2011). https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-145.pdf (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Definitions", AWS Well-Architected Framework. https://docs.aws.amazon.com/wellarchitected/latest/framework/definitions.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "General design principles", AWS Well-Architected Framework. https://docs.aws.amazon.com/wellarchitected/latest/framework/general-design-principles.html (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Six advantages of cloud computing", Overview of Amazon Web Services (whitepaper). https://docs.aws.amazon.com/whitepapers/latest/aws-overview/six-advantages-of-cloud-computing.html (accessed 2026-11-19)
[^s5]: Amazon Web Services, "AWS Reference Architecture Diagrams". https://docs.aws.amazon.com/reference-architecture-diagrams/ (accessed 2026-11-19)
