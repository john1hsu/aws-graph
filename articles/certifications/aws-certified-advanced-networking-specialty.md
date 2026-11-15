---
id: kt:aws-certified-advanced-networking-specialty
type: Certification
title: AWS Certified Advanced Networking - Specialty
aliases: [ANS-C01, AWS Advanced Networking Specialty, AWS Certified Advanced Networking Specialty]
domain: certifications
summary: >-
  AWS's specialty-level certification for experienced network engineers, validating the design,
  implementation, operation and security of hybrid and cloud networks on AWS (exam ANS-C01); AWS
  has announced that the last day to take the exam is December 31, 2026.
verified: 2026-11-15
status: current
broader: [kt:aws-certification-program]
related: [kt:aws-networking-and-content-delivery, kt:amazon-vpc, kt:aws-transit-gateway, kt:aws-direct-connect, kt:amazon-route-53, kt:aws-global-accelerator]
relations:
  - {predicate: contrastsWith, object: kt:aws-certified-security-specialty}
  - {predicate: contrastsWith, object: kt:aws-certified-solutions-architect-professional}
sources:
  - id: s1
    title: "AWS Certified Advanced Networking - Specialty (ANS-C01) Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/advanced-networking-specialty-01/advanced-networking-specialty-01.html
    type: official-learning
    accessed: 2026-11-15
  - id: s2
    title: "AWS Certified Advanced Networking - Specialty"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-advanced-networking-specialty
    type: official-learning
    accessed: 2026-11-15
  - id: s3
    title: "ANS-C01 Content Domain 1: Network Design"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/advanced-networking-specialty-01/advanced-networking-specialty-01-domain1.html
    type: official-learning
    accessed: 2026-11-15
  - id: s4
    title: "ANS-C01 Content Domain 4: Network Security, Compliance, and Governance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/advanced-networking-specialty-01/advanced-networking-specialty-01-domain4.html
    type: official-learning
    accessed: 2026-11-15
  - id: s5
    title: "ANS-C01 In-scope AWS services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/advanced-networking-specialty-01/ans-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-15
  - id: s6
    title: "AWS 認定の上位・下位関係を理解して効率的に認定を更新しましょう (Understand AWS certification hierarchy to renew efficiently)"
    publisher: Amazon Web Services (AWS Japan Blog)
    url: https://aws.amazon.com/jp/blogs/news/aws-certification-hierarchy-recertification-guide/
    type: official-blog
    accessed: 2026-11-15
created: 2026-11-15
---

# AWS Certified Advanced Networking - Specialty

AWS Certified Advanced Networking - Specialty is AWS's specialty-level credential for network engineers who design and run hybrid and cloud networks on AWS; the current (and final) exam version is ANS-C01.[^s1][^s2] It validates that a candidate can design and develop hybrid and cloud-based networking solutions, implement core AWS networking services according to best practice, operate and maintain that architecture, automate networking tasks with tooling, and build secure networks with AWS-native constructs.[^s1] The exam guide's target candidate has five or more years of networking experience, including at least two years of cloud and hybrid networking.[^s1] There are no formal prerequisites, though AWS says candidates benefit from holding an Associate or Professional certification first.[^s2]

## Retirement: last exam date December 31, 2026

As of 2026-11, the certification page says the exam is being retired and that December 31, 2026 is the last day to sit it; no new certifications are issued after that, while credentials earned before then stay active for their three-year term.[^s2] An AWS Japan blog post from August 5, 2026 had given an earlier retirement date of August 25, 2026, so the deadline has since been pushed back.[^s6] AWS does not name a successor certification and points networking learners to AWS Skill Builder instead.[^s2] Gotcha: anyone planning to renew by re-passing the exam must do so before the cut-off, because there will be no later version to pass.[^s2]

## Exam format and scoring

The certification page lists 65 questions in 170 minutes for 300 USD (foreign-exchange and other costs may apply), taken at a Pearson VUE test center or as an online proctored exam, in English, Japanese, Korean or Simplified Chinese (as of 2026-11).[^s2] Of the 65 questions, 50 are scored and 15 are unidentified, unscored items; unanswered questions are marked wrong and guessing carries no penalty.[^s1] Besides multiple choice and multiple response, the guide describes matching questions that pair responses with 3–7 prompts and give credit only when every pair is correct.[^s1] Results are pass/fail on a 100–1,000 scaled score with 750 to pass, under a compensatory model where only the overall score counts.[^s1]

## What ANS-C01 covers

Scored content is split into four domains: Network Design (30%), Network Implementation (26%), Network Management and Operation (20%), and Network Security, Compliance, and Governance (24%).[^s1] The design domain spans edge services (CloudFront and Global Accelerator patterns), public, private and hybrid DNS with Route 53 Resolver and DNSSEC, layer 3/4/7 load balancing, network logging and monitoring, BGP-based hybrid routing over Direct Connect and Site-to-Site VPN, and multi-account, multi-Region and multi-VPC connectivity including overlapping CIDRs.[^s3] Its knowledge list goes below the usual cloud abstractions, into layer 1/2 topics such as VLANs, link aggregation, optics and jumbo frames, plus GRE and IPsec tunnels and SD-WAN integration through Transit Gateway Connect.[^s3]

The security domain asks candidates to protect inbound flows with AWS WAF, Shield and Network Firewall, control outbound flows with Network Firewall, proxies or Gateway Load Balancers, analyse VPC Flow Logs and Traffic Mirroring, and encrypt traffic in transit with IPsec, TLS and VPN over Direct Connect, managing certificates with ACM.[^s4] The in-scope service list centres on networking and content delivery (VPC, Transit Gateway, PrivateLink, Direct Connect, Client VPN, ELB, Route 53, CloudFront, Global Accelerator) but also includes AWS RAM, Firewall Manager, Organizations, Control Tower, CloudFormation and container services such as EKS.[^s5]

## Validity and recertification

The credential is valid for three years, and the standard renewal path is passing the latest version of the exam.[^s2] Specialty certifications sit outside the Foundational–Associate–Professional hierarchy: renewing a Professional certification does not renew a Specialty one, and vice versa.[^s6] Any AWS certification earns 50% off the next exam.[^s2]

## When to take it (and when not)

It suits senior network engineers and architects who own hybrid connectivity, DNS, routing and network security on AWS, and who can still sit the exam before the retirement date.[^s1][^s2] Architects who need broad design coverage rather than deep networking are better served by the Solutions Architect track, and anyone starting after 2026 will need another route to show networking depth.[^s2]

## Connections

It belongs to the specialty tier of the AWS Certification program.[^s2][^s6] Its content maps directly onto AWS's networking and content delivery services—Amazon VPC, AWS Transit Gateway, AWS Direct Connect, Amazon Route 53 and AWS Global Accelerator are all named in the domains and service list.[^s3][^s5] It contrasts with Security - Specialty, the other specialty exam, which overlaps on network protection (WAF, Shield, Network Firewall) but centres on identity, detection and data protection, and with Solutions Architect - Professional, which tests broad architecture rather than packet-level and routing depth.[^s4][^s2]

## Sources
[^s1]: Amazon Web Services, "AWS Certified Advanced Networking - Specialty (ANS-C01) Exam Guide". https://docs.aws.amazon.com/aws-certification/latest/advanced-networking-specialty-01/advanced-networking-specialty-01.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "AWS Certified Advanced Networking - Specialty". https://aws.amazon.com/certification/certified-advanced-networking-specialty (accessed 2026-11-15)
[^s3]: Amazon Web Services, "ANS-C01 Content Domain 1: Network Design". https://docs.aws.amazon.com/aws-certification/latest/advanced-networking-specialty-01/advanced-networking-specialty-01-domain1.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "ANS-C01 Content Domain 4: Network Security, Compliance, and Governance". https://docs.aws.amazon.com/aws-certification/latest/advanced-networking-specialty-01/advanced-networking-specialty-01-domain4.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "ANS-C01 In-scope AWS services". https://docs.aws.amazon.com/aws-certification/latest/advanced-networking-specialty-01/ans-01-in-scope-services.html (accessed 2026-11-15)
[^s6]: Amazon Web Services (AWS Japan Blog), "AWS 認定の上位・下位関係を理解して効率的に認定を更新しましょう". https://aws.amazon.com/jp/blogs/news/aws-certification-hierarchy-recertification-guide/ (accessed 2026-11-15)
