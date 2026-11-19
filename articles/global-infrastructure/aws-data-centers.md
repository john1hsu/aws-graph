---
id: kt:aws-data-centers
type: InfrastructureConcept
title: AWS data centers
aliases: [AWS data center, AWS data center security, AWS data center controls, AWS physical security]
domain: global-infrastructure
summary: >-
  The physical facilities that house AWS infrastructure, grouped into Availability Zones, and the layered
  physical, environmental and operational controls (perimeter, infrastructure, data and environmental
  layers) that AWS runs on its side of the shared responsibility model and attests to through third-party audits.
verified: 2026-11-19
status: concept
broader: [kt:aws-global-infrastructure]
related: [kt:shared-responsibility-model, kt:aws-nitro-system, kt:aws-artifact, kt:sustainability-pillar]
relations:
  - {predicate: enables, object: kt:availability-zones}
  - {predicate: compliesWith, object: kt:aws-compliance-programs}
sources:
  - id: s1
    title: "AWS Data Centers - Amazon Web Services (AWS)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/trust-center/data-center/
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Data Center Controls - Our Controls"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/data-center/controls/?nc1=h_ls
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Perimeter Layer - Amazon Web Services (AWS)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/trust-center/data-center/perimeter-layer/
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "AWS Sustainable Infrastructure - Data Centers"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/sustainability/data-centers/?utm_=
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "AWS Cloud - Amazon Sustainability"
    publisher: Amazon
    url: https://sustainability.aboutamazon.com/products-services/aws-cloud
    type: news
    accessed: 2026-11-19
  - id: s6
    title: "SP 800-88 Rev. 2, Guidelines for Media Sanitization"
    publisher: National Institute of Standards and Technology
    url: https://csrc.nist.gov/pubs/sp/800/88/r2/final
    type: standards-body
    accessed: 2026-11-19
created: 2026-11-19
---

# AWS data centers

AWS data centers are the physical buildings that hold the servers, storage and networking behind every AWS service. Customers never choose or see an individual facility: they choose a Region and an Availability Zone, and each AZ is made of one or more of these data centers. What customers do get is a documented set of physical and environmental controls that AWS operates on its side of the shared responsibility model, plus third-party audit reports that attest to them.[^s1][^s2]

## The four layers

AWS describes data center security as four nested layers.[^s1]

- **Perimeter layer** – where physical security starts. Depending on the site it combines security guards, fencing, camera feeds and intrusion detection.[^s1] Employees and vendors must apply for access with a business justification; an area access manager reviews the request, and access is removed once the work is done. Badges require multi-factor authentication and only open pre-approved areas, forced or propped doors raise alarms, and Security Operations Centers watch access activity around the clock.[^s3]
- **Infrastructure layer** – the building and the plant that keeps it running: backup power, HVAC and fire suppression.[^s1]
- **Data layer** – the most protected layer, because it is the only one that holds customer data. Access is restricted further, privileges are separated between layers, and threat-detection devices and protocols are deployed.[^s1]
- **Environmental layer** – site selection, construction, operations and sustainability, with sites chosen to reduce exposure to flooding, extreme weather and seismic activity.[^s1]

## Controls AWS documents

The controls page groups the operational detail.[^s2] Capacity is designed to an N+1 standard so that remaining sites can absorb load if one fails, AZs are physically separated, and capacity planning is reviewed at least monthly. A business continuity plan is exercised with simulated scenarios. Employee access is least-privilege and time-bound; third-party visitors are requested by an AWS employee, must identify themselves and are escorted. Access to AWS GovCloud (US) data centers is limited to employees validated as US citizens. Physical access is logged and retained, CCTV covers server-room entry points, and access is revoked automatically when an HR record is terminated or a request expires.[^s2]

Operational systems include fully redundant power with backup for critical loads, climate and humidity control, smoke detection with automatic fire suppression, and water-leak detection.[^s2] Storage media that held customer data is classified as Critical, tracked in a central inventory, never leaves AWS control until it is securely decommissioned, and is sanitized using NIST SP 800-88 techniques.[^s2] NIST revised that guideline as SP 800-88 Rev. 2 in September 2025, replacing the 2014 Rev. 1.[^s6]

## Efficiency and sustainability

AWS reports newer designs aimed at AI-density racks: a single-sided cooling layout, configurable liquid-to-chip cooling alongside air cooling, and an In-Row Heat Exchanger that captures heat at the rack. AWS says the new design can cut mechanical energy use by up to 50% at peak cooling compared with the previous design.[^s4] Amazon's sustainability reporting gives a 2025 global power usage effectiveness (PUE) of 1.14 (1.15 in 2024) and a water usage effectiveness of 0.12 litres per kWh of IT load, with a goal of being water positive by 2030 (figures as of 2026-11).[^s5]

## Gotchas and how to use this

- **You can't audit the building yourself.** Assurance comes from third-party reports; external auditors may test controls such as media disposal, camera footage and access devices depending on the compliance program.[^s2] Download those reports rather than asking for a site visit.
- **Physical controls don't make a workload resilient.** N+1 design protects AWS capacity, but a single-AZ deployment still fails with its AZ.[^s2] Spread across AZs.
- **The boundary matters for compliance scoping.** Everything in this article sits on the AWS side of the shared responsibility model; your controls start at the guest OS, data and configuration.[^s1]

## Connections

Data centers are the lowest physical tier of the **AWS global infrastructure**, and they are what make up **Availability Zones**: each AZ is one or more physically separated facilities.[^s1][^s2] The controls described here are AWS's half of the **shared responsibility model** ("security of the cloud"), and their host-level counterpart is the **AWS Nitro System**.[^s1] They are attested by third-party reports under **AWS compliance programs**, which customers obtain through **AWS Artifact**.[^s2] The facility efficiency figures connect to the **sustainability pillar** of the Well-Architected Framework, where customer design choices take over.[^s4][^s5]

## Sources
[^s1]: Amazon Web Services, "AWS Data Centers". https://aws.amazon.com/trust-center/data-center/ (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Data Center Controls - Our Controls". https://aws.amazon.com/compliance/data-center/controls/?nc1=h_ls (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Perimeter Layer". https://aws.amazon.com/trust-center/data-center/perimeter-layer/ (accessed 2026-11-19)
[^s4]: Amazon Web Services, "AWS Sustainable Infrastructure - Data Centers". https://aws.amazon.com/sustainability/data-centers/?utm_= (accessed 2026-11-19)
[^s5]: Amazon, "AWS Cloud - Amazon Sustainability". https://sustainability.aboutamazon.com/products-services/aws-cloud (accessed 2026-11-19)
[^s6]: NIST, "SP 800-88 Rev. 2, Guidelines for Media Sanitization" (September 2025). https://csrc.nist.gov/pubs/sp/800/88/r2/final (accessed 2026-11-19)
