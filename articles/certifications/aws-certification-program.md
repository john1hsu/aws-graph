---
id: kt:aws-certification-program
type: Concept
title: AWS Certification program
aliases: [AWS Certification, AWS Certifications, AWS Training and Certification exams]
domain: certifications
summary: >-
  AWS's family of proctored, role-based credentials, organised into Business, Foundational, Associate,
  Professional and Specialty levels, with shared rules for pricing, delivery, retakes, three-year
  validity and recertification.
verified: 2026-11-12
status: concept
broader: [kt:amazon-web-services]
related: [kt:aws-skill-builder]
relations:
  - {predicate: hasPart, object: kt:aws-certified-cloud-practitioner}
  - {predicate: hasPart, object: kt:aws-certified-ai-practitioner}
  - {predicate: hasPart, object: kt:aws-certified-solutions-architect-associate}
  - {predicate: hasPart, object: kt:aws-certified-developer-associate}
  - {predicate: hasPart, object: kt:aws-certified-solutions-architect-professional}
sources:
  - id: s1
    title: "AWS Certification Exam Guides"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/examguides/aws-certification-exam-guides.html
    type: official-learning
    accessed: 2026-11-12
  - id: s2
    title: "AWS Certification FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/en/certification/faqs/
    type: official-learning
    accessed: 2026-11-12
  - id: s3
    title: "AWS Recertification | AWS Certification Renewal"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/recertification/
    type: official-learning
    accessed: 2026-11-12
  - id: s4
    title: "AWS Recertification | AWS Certification Information and Policies"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/policies/recertification/
    type: official-learning
    accessed: 2026-11-12
  - id: s5
    title: "Exam Updates, Beta Exams, and New Certifications | Coming Soon to AWS Certification"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/coming-soon/
    type: official-learning
    accessed: 2026-11-12
  - id: s6
    title: "Register now: Exam update and new name for cloud operations certification"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/jp/blogs/training-and-certification/exam-update-and-new-name-for-operations-certification/
    type: official-blog
    accessed: 2026-11-12
created: 2026-11-12
---

# AWS Certification program

The AWS Certification program is the umbrella under which AWS offers its individual credentials. It is organised into five levels: Business, Foundational, Associate, Professional and Specialty.[^s3] Each exam has a published exam guide that describes the target candidate, gives a content outline and lists in-scope AWS services, so the guide, not third-party prep material, defines what an exam tests.[^s1]

## The current portfolio

As of 2026-11, AWS's exam-guide index lists one Business exam (AI Business Strategist, AIB-C01), two Foundational exams (Cloud Practitioner, CLF-C02, and AI Practitioner, AIF-C01), Associate exams for CloudOps Engineer (SOA-C03), Data Engineer (DEA-C01), Developer (DVA-C02/C03), Machine Learning Engineer (MLA-C02) and Solutions Architect (SAA-C03), Professional exams for DevOps Engineer (DOP-C02), Generative AI Developer (AIP-C01) and Solutions Architect (SAP-C02/C03), and two Specialty exams, Advanced Networking (ANS-C01) and Security (SCS-C03).[^s1]

Exam codes change when AWS revises an exam. The index flags two transitions in progress: registration for Developer – Associate DVA-C03 opened on October 27, 2026, and November 30, 2026 is the last day for DVA-C02; Solutions Architect – Professional moves to SAP-C03, with November 16, 2026 the last day for SAP-C02.[^s1] Names change too: in 2025 AWS renamed SysOps Administrator – Associate to CloudOps Engineer – Associate, replacing SOA-C02 with SOA-C03 and adding containers, multi-account and multi-Region operations, and more automation and infrastructure-as-code coverage.[^s6] The last day to sit SOA-C02 was September 29, 2025, and SOA-C03 was first offered on September 30, 2025.[^s5] The new name applies only to people who pass SOA-C03; existing SysOps badges stay valid until they expire.[^s6]

## Pricing, delivery and retakes

As of 2026-11, exam fees are 100 USD for Business and Foundational exams, 150 USD for Associate and 300 USD for Professional and Specialty, before taxes such as VAT; AWS also prices in AUD, EUR, KRW and JPY.[^s2] Pearson VUE delivers every exam, either at a test center or as an online proctored exam.[^s2] There are no prerequisite exams or mandatory training; AWS recommends six months to two years of hands-on AWS experience for the technical levels and six months of professional experience for Business, and candidates must be at least 13 (with guardian consent under 18).[^s2]

After a failed attempt you wait 14 calendar days before retrying, with no cap on attempts but the full fee each time; after passing, you cannot retake the same exam for two years.[^s2] Successful candidates get a digital badge issued through Credly and access to benefits in their AWS Certification Account within five business days, including a 50% discount voucher for a later exam.[^s2][^s4]

## Validity and recertification

A certification is valid for three years from the date it was earned, and keeping it active is the holder's responsibility.[^s4] The standard route is to pass the current version of the same exam; AWS does not accept continuing-education credits as a substitute.[^s4] For Foundational and Associate credentials, passing a qualifying higher-level exam also renews them.[^s4] The renewal page maps those paths: any Associate or Professional exam (or AWS Cloud Quest) renews Cloud Practitioner; Machine Learning Engineer – Associate or Generative AI Developer – Professional renews AI Practitioner; Solutions Architect – Professional renews the Associate; DevOps Engineer – Professional renews Developer and CloudOps/SysOps; and Generative AI Developer – Professional renews Data Engineer and Machine Learning Engineer.[^s3] Professional, Specialty and Business credentials renew only by retaking their own exam or, where offered, by maintaining them through AWS Skill Builder.[^s3]

## Gotchas

Exam versions can change within weeks, so check the exam-guide index for retiring codes before booking and study from the current guide.[^s1] Renewal is not symmetric: a Professional exam renews only the specific lower credentials AWS lists, never other Professional or Specialty ones.[^s3] Retakes after a fail cost the full fee; the 50% voucher comes only from an earlier pass.[^s2][^s4]

## Connections

The program **has part** each certification node in this graph: Cloud Practitioner and AI Practitioner at the Foundational level, Solutions Architect – Associate and Developer – Associate, and Solutions Architect – Professional.[^s1] It sits under Amazon Web Services as AWS's own credentialing scheme and is **related** to AWS Skill Builder, which AWS names as a way to maintain some credentials without a full retake.[^s3] Certification articles' `coveredBy` edges point at these individual exams, whose exam guides are the authority on scope.[^s1]

## Sources
[^s1]: Amazon Web Services, "AWS Certification Exam Guides". https://docs.aws.amazon.com/aws-certification/latest/examguides/aws-certification-exam-guides.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "AWS Certification FAQs". https://aws.amazon.com/en/certification/faqs/ (accessed 2026-11-12)
[^s3]: Amazon Web Services, "AWS Recertification | AWS Certification Renewal". https://aws.amazon.com/certification/recertification/ (accessed 2026-11-12)
[^s4]: Amazon Web Services, "AWS Recertification | AWS Certification Information and Policies". https://aws.amazon.com/certification/policies/recertification/ (accessed 2026-11-12)
[^s5]: Amazon Web Services, "Exam Updates, Beta Exams, and New Certifications | Coming Soon to AWS Certification". https://aws.amazon.com/certification/coming-soon/ (accessed 2026-11-12)
[^s6]: Amazon Web Services, "Register now: Exam update and new name for cloud operations certification". https://aws.amazon.com/jp/blogs/training-and-certification/exam-update-and-new-name-for-operations-certification/ (accessed 2026-11-12)
