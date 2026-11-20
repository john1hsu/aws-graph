---
id: kt:aws-well-architected-tool
type: Service
title: AWS Well-Architected Tool
aliases: [AWS WA Tool, Well-Architected Tool, WA Tool]
domain: architecture-practice
summary: >-
  AWS's console service and API for running Well-Architected reviews: you define a workload, answer a
  lens's questions, and get high- and medium-risk issues, an improvement plan, milestones and reports.
verified: 2026-11-20
status: current
broader: [kt:aws-management-and-governance-services]
related: [kt:aws-well-architected-framework, kt:aws-well-architected-agent, kt:aws-organizations]
relations:
  - {predicate: implements, object: kt:aws-well-architected-framework}
  - {predicate: integratesWith, object: kt:aws-trusted-advisor}
  - {predicate: integratesWith, object: kt:aws-service-catalog-appregistry}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: scopedTo, object: kt:aws-govcloud-us}
sources:
  - id: s1
    title: "What is AWS Well-Architected Tool?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/userguide/tool.md
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "AWS Well-Architected Tool features"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/well-architected-tool/features
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Tutorial: Document an AWS Well-Architected Tool workload"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/userguide/tutorial.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "Milestones - AWS Well-Architected"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/userguide/milestones.html
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "Release notes - AWS Well-Architected"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/userguide/release-notes.html
    type: official-docs
    accessed: 2026-11-20
  - id: s6
    title: "Announcing AWS Well-Architected Agent, an AI-powered intelligence to optimize your cloud environment (preview)"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/announcing-aws-well-architected-agent-an-ai-powered-intelligence-to-optimize-your-cloud-environment-preview/
    type: official-blog
    accessed: 2026-11-20
created: 2026-11-20
---

# AWS Well-Architected Tool

The AWS Well-Architected Tool (WA Tool) is the AWS service that turns architectural guidance into a repeatable review process. AWS describes it as a consistent way to measure an architecture against AWS best practices, document the decisions behind it, and get recommendations for making the workload more reliable, secure, efficient and cost-effective.[^s1] Its intended users span CTOs, architects, developers and operations staff.[^s1] It has been generally available since November 2018.[^s5]

## How a review works

The unit of review is a **workload**. You define one with a name, description, review owner, environment and the AWS (and non-AWS) Regions it runs in, optionally adding accounts, an application, an architecture diagram, industry, a Trusted Advisor or Jira setting, tags and a profile.[^s3] You then pick one or more **lenses**; the core Well-Architected Framework lens is applied automatically.[^s3]

Answering the review means selecting, for each question, the best practices the workload already follows. Questions or individual best practices can be marked not applicable (AWS recommends leaving a note explaining why), and you can save and come back at any time.[^s3] From the gaps, the tool identifies **high- and medium-risk issues** against each lens and builds an **improvement plan** listing what to fix.[^s3] You can generate a PDF report per lens that shows the workload state, the number of risks and suggested improvements.[^s3]

A **milestone** records the workload's state at a point in time. AWS suggests saving the first one once every question is answered, then another each time you act on the improvement plan, so you can compare risk counts over time; the tutorial's example drops from three high-risk issues to one.[^s4][^s3]

## Features beyond the basic review

- **Lens Catalog and custom lenses:** AWS-authored lenses from the catalog, plus custom lenses in which you define your own pillars, questions, best practices and improvement plans.[^s2][^s1]
- **Profiles:** a set of declared business goals; applying one to a workload produces a prioritized list of questions to answer first.[^s2]
- **Review templates:** pre-filled common answers you reuse across many workloads and teams.[^s2]
- **Sharing:** workloads and custom lenses can be shared with up to 300 individual IAM users or accounts, or across an organization in AWS Organizations (per AWS's features page, as of 2026-11).[^s2]
- **Integrations and APIs:** Trusted Advisor and Service Catalog AppRegistry help surface evidence for answering questions; a Jira connector, EventBridge events, tagging and a dashboard are documented, and APIs let you pull review data into existing governance workflows.[^s1][^s2]

These arrived incrementally: the Trusted Advisor and AppRegistry enhancement in May 2023, profiles in June 2023, review templates in October 2023, the Lens Catalog in November 2023 and the Jira connector in April 2024.[^s5]

## Gotchas

The tool records what reviewers *say*; answers are self-attested, so a review is only as good as the evidence behind it. Linking Trusted Advisor or AppRegistry narrows that gap but does not answer the questions for you.[^s1] AWS's features page states that the tool is available in the AWS GovCloud (US) Region.[^s2] Its service quotas (workloads, milestones, lenses, shares) are published in the AWS General Reference; check them there for the current limits rather than relying on figures copied elsewhere.

## What changed (2026-10/11)

On 1 October 2026 AWS launched the **AWS Well-Architected Agent** in preview. Instead of answering questions manually, you create an agent profile (accounts or Regions, pillars and goals) and give it read-only roles; it analyses configurations, metrics and topology against best practices for 65+ services and returns resource-, application- and architecture-level recommendations, typically within 24 hours, and it can review Terraform, CloudFormation or CDK projects before deployment.[^s6] It is delivered by AWS Support, its console is in US East (N. Virginia), US East (Ohio) and US West (Oregon), and workloads can be onboarded from any commercial Region.[^s6] A 16 November 2026 update added AWS Organizations integration and profile sharing; the release notes list Business+, Enterprise On-Ramp, Enterprise Support or Unified Operations plans as prerequisites.[^s5] AWS says the WA Tool remains available for manual reviews with user-defined lenses.[^s6]

## When to use it (and when not)

Use the WA Tool when you want a documented, comparable record of design reviews: before launch, after major changes, or across a portfolio of workloads with shared templates and profiles.[^s2][^s4] Consider the Well-Architected Agent if you want automated, evidence-based findings from live resources and have a qualifying Support plan, accepting that it is in preview.[^s6]

## Connections

The tool **implements** the AWS Well-Architected Framework: its default lens is the framework, and its risks map to the framework's best practices.[^s3] It **integrates with** AWS Trusted Advisor, Service Catalog AppRegistry and Amazon EventBridge, and shares workloads through AWS Organizations.[^s1][^s2] It is **related** to the AWS Well-Architected Agent, the AI-driven preview offering that automates analysis alongside it.[^s6] Its features page places it in AWS GovCloud (US), hence the **scopedTo** edge.[^s2]

## Sources
[^s1]: Amazon Web Services, "What is AWS Well-Architected Tool?". https://docs.aws.amazon.com/wellarchitected/latest/userguide/tool.md (accessed 2026-11-20)
[^s2]: Amazon Web Services, "AWS Well-Architected Tool features". https://aws.amazon.com/well-architected-tool/features (accessed 2026-11-20)
[^s3]: Amazon Web Services, "Tutorial: Document an AWS Well-Architected Tool workload". https://docs.aws.amazon.com/wellarchitected/latest/userguide/tutorial.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "Milestones - AWS Well-Architected". https://docs.aws.amazon.com/wellarchitected/latest/userguide/milestones.html (accessed 2026-11-20)
[^s5]: Amazon Web Services, "Release notes - AWS Well-Architected". https://docs.aws.amazon.com/wellarchitected/latest/userguide/release-notes.html (accessed 2026-11-20)
[^s6]: Amazon Web Services, "Announcing AWS Well-Architected Agent, an AI-powered intelligence to optimize your cloud environment (preview)" (AWS News Blog, 2026-10-01). https://aws.amazon.com/blogs/aws/announcing-aws-well-architected-agent-an-ai-powered-intelligence-to-optimize-your-cloud-environment-preview/ (accessed 2026-11-20)
