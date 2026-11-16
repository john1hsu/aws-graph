---
id: kt:aws-european-sovereign-cloud
type: InfrastructureConcept
title: AWS European Sovereign Cloud
aliases: [AWS ESC, EUSC, aws-eusc, AWS European Sovereign Cloud partition]
domain: public-sector-and-compliance
summary: >-
  AWS's independent cloud for Europe: a separate partition (aws-eusc) whose first Region, eusc-de-east-1 in
  Brandenburg, Germany, became generally available in January 2026, located entirely in the EU, run by EU-resident
  staff under German-law entities, with its own accounts, IAM, billing and DNS.
verified: 2026-11-16
status: current
broader: [kt:aws-partitions, kt:aws-global-infrastructure]
related: [kt:data-residency-on-aws, kt:aws-regions, kt:aws-compliance-programs, kt:aws-nitro-system, kt:local-zones, kt:aws-organizations, kt:aws-artifact]
relations:
  - {predicate: contrastsWith, object: kt:aws-govcloud-us}
  - {predicate: securedBy, object: kt:iam-identity-center}
  - {predicate: integratesWith, object: kt:aws-direct-connect}
  - {predicate: compliesWith, object: kt:bsi-c5}
sources:
  - id: s1
    title: "Opening the AWS European Sovereign Cloud"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/opening-the-aws-european-sovereign-cloud
    type: official-blog
    accessed: 2026-11-16
  - id: s2
    title: "What is AWS European Sovereign Cloud? - AWS European Sovereign Cloud User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.eu/esc/latest/userguide/introduction.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Introduction - Overview of the AWS European Sovereign Cloud (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/overview-aws-european-sovereign-cloud/introduction.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "AWS European Sovereign Cloud - Frequently Asked Questions"
    publisher: Amazon Web Services
    url: https://aws.eu/faq/
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "Architecting a secure landing zone in the AWS European Sovereign Cloud"
    publisher: Amazon Web Services (AWS Security Blog)
    url: https://aws.amazon.com/blogs/security/architecting-a-secure-landing-zone-in-the-aws-european-sovereign-cloud/
    type: official-blog
    accessed: 2026-11-16
  - id: s6
    title: "AWS European Sovereign Cloud Compliance"
    publisher: Amazon Web Services
    url: https://aws.eu/compliance/
    type: official-docs
    accessed: 2026-11-16
created: 2026-11-16
---

# AWS European Sovereign Cloud

The AWS European Sovereign Cloud (ESC) is an independent cloud for Europe aimed at public-sector bodies and highly regulated industries that have sovereignty requirements beyond ordinary data residency. It runs the same Nitro-based architecture, APIs and security model as the rest of AWS, but its infrastructure sits entirely inside the EU and it is separate from all other AWS Regions.[^s2] AWS traces it to the Digital Sovereignty Pledge of November 2022, announced the plan in October 2023 and committed €7.8 billion to infrastructure, jobs and skills.[^s3] It became generally available to all customers on 14 January 2026 with its first Region, `eusc-de-east-1`, in Brandenburg, Germany, in the `aws-eusc` partition.[^s1]

## How it works

**Governance.** ESC is run through dedicated European legal entities incorporated under German law, led by EU-based managing directors and overseen by an advisory board made up only of EU citizens, including independent members.[^s1] Day-to-day operations, data-center access, technical support and customer service are handled by AWS staff who reside in the EU; AWS says it is moving toward operation exclusively by EU citizens located in the EU.[^s1][^s4]

**Independence.** The Region has multiple Availability Zones and is designed to keep running through a loss of connectivity to the rest of the world, with no critical dependency on non-EU infrastructure. Customers can keep both their content and the metadata they create (roles, permissions, resource labels, configuration) in the EU.[^s2] ESC has its own Route 53, its own Direct Connect points of presence, and name servers on European top-level domains.[^s1][^s4]

**A separate partition.** Because ESC is its own partition, nothing carries over from a commercial AWS account. Existing accounts cannot be used; customers create new ESC accounts, which do not interoperate with other Regions, and no EU residency is needed to sign up.[^s4] Partitions have independent IAM instances, so a customer builds a separate AWS Organization, a separate IAM Identity Center instance (which can still federate to the same corporate identity provider) and a separate log archive inside ESC.[^s5] ARNs begin `arn:aws-eusc:` and service endpoints use `amazonaws.eu`, while IAM service principals still end in `amazonaws.com`; the console lives at `console.amazonaws-eusc.eu`.[^s5][^s1]

## Limits, pricing and gotchas

- **No cross-partition plumbing.** A role in the `aws` partition cannot assume a role in `aws-eusc` or the reverse, and VPC peering, Transit Gateway, AWS RAM, S3 and ECR replication and AMI copy do not cross the boundary. Artifacts must be pushed into ESC with ESC credentials, and CI/CD needs a trust configuration per partition.[^s5]
- **Smaller service catalogue.** The launch post named a starter set of services across compute, containers, databases, storage, networking, security and AI/ML and points to the AWS Capabilities matrix for the current list; at the time of the landing-zone guidance, CloudFront, Shield Advanced, Firewall Manager and Inspector were still planned. Check availability before designing.[^s1][^s5]
- **Billing.** Prices are set in EUR, the contracting entity is Amazon Web Services EMEA SARL, and the ESC payer account is billed separately, so costs do not roll up with commercial accounts.[^s1][^s5] This article states no ESC prices; use the ESC pricing pages for the Region.
- **Key management.** AWS KMS is designed with no operator access; an external key store adds latency, a lower availability commitment and operational work, so AWS suggests keeping it for data whose regulation demands it.[^s4][^s5]

## Compliance

As of 2026-11 the ESC compliance page lists C5, SOC 2 and ISO 9001, 20000, 22301, 27001, 27017, 27018 and 27701 attestations, plus alignment with the ESC Sovereign Reference Framework (ESC-SRF), which covers governance independence, operational control, data residency and technical isolation.[^s6][^s4] Reports, including the ESC-SRF, are obtained through AWS Artifact; the FAQ described the ESC-SRF third-party validation as in progress.[^s4] DORA is listed as a regulation customers remain responsible for meeting.[^s6]

## When to use it (and when not)

For most EU residency rules, choosing one of AWS's existing European Regions plus guardrails is enough: AWS offers eight European Regions, six of them in EU member states.[^s3] ESC is for requirements that extend to who operates the cloud and where its control plane, metadata and support live. The price is a second landing zone, duplicated identity and pipelines, no direct cross-partition networking and a narrower service list.[^s5] AWS plans to extend ESC with sovereign Local Zones in Belgium, the Netherlands and Portugal, and offers Dedicated Local Zones, AI Factories and Outposts at customer-chosen sites.[^s1][^s4]

## Connections

ESC is its own AWS partition, which is why its broader nodes are AWS partitions and the AWS global infrastructure, and why it sits beside AWS Regions rather than inside the commercial set; its build-it-again-per-partition model is the point of contrast with AWS GovCloud (US).[^s5][^s2] It is the strongest option on the data-residency-on-AWS spectrum, above Region choice and Local Zones, and leans on the AWS Nitro System's no-operator-access design.[^s3][^s1] Inside it, customers run a separate AWS Organizations structure and IAM Identity Center instance, connect through sovereign AWS Direct Connect locations, and pull compliance reports such as C5 and the ESC-SRF from AWS Artifact, linking it to AWS compliance programs.[^s5][^s4][^s6]

## Sources
[^s1]: Amazon Web Services (AWS News Blog), "Opening the AWS European Sovereign Cloud". https://aws.amazon.com/blogs/aws/opening-the-aws-european-sovereign-cloud (accessed 2026-11-16)
[^s2]: Amazon Web Services, "What is AWS European Sovereign Cloud? - AWS European Sovereign Cloud User Guide". https://docs.aws.eu/esc/latest/userguide/introduction.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Introduction - Overview of the AWS European Sovereign Cloud". https://docs.aws.amazon.com/whitepapers/latest/overview-aws-european-sovereign-cloud/introduction.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "AWS European Sovereign Cloud - Frequently Asked Questions". https://aws.eu/faq/ (accessed 2026-11-16)
[^s5]: Amazon Web Services (AWS Security Blog), "Architecting a secure landing zone in the AWS European Sovereign Cloud". https://aws.amazon.com/blogs/security/architecting-a-secure-landing-zone-in-the-aws-european-sovereign-cloud/ (accessed 2026-11-16)
[^s6]: Amazon Web Services, "AWS European Sovereign Cloud Compliance". https://aws.eu/compliance/ (accessed 2026-11-16)
