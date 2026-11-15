---
id: kt:aws-developer-tools
type: Concept
title: AWS developer tools
aliases: [AWS Developer Tools category, AWS developer tools services, AWS Code services, AWS Code* services, developer tools on AWS]
domain: devops-and-iac
summary: >-
  The AWS service category for writing, storing, building, testing, releasing and debugging software:
  source and artifact repositories (CodeCommit, CodeArtifact), CI/CD (CodeBuild, CodePipeline, CodeDeploy),
  browser and AI coding aids (CloudShell, Amazon Q Developer, Infrastructure Composer) and troubleshooting
  and resilience testing (X-Ray, Fault Injection Service).
verified: 2026-11-15
status: concept
broader: [kt:amazon-web-services]
related: [kt:ci-cd, kt:infrastructure-as-code, kt:devops, kt:aws-management-and-governance-services]
relations:
  - {predicate: hasPart, object: kt:aws-codepipeline}
  - {predicate: hasPart, object: kt:aws-codebuild}
  - {predicate: hasPart, object: kt:aws-codedeploy}
  - {predicate: hasPart, object: kt:aws-codecommit}
  - {predicate: hasPart, object: kt:aws-x-ray}
  - {predicate: hasPart, object: kt:amazon-q-developer}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Developer tools - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/developer-tools
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "AWS CodeCommit Returns to General Availability"
    publisher: Amazon Web Services (AWS DevOps & Developer Productivity Blog)
    url: https://aws.amazon.com/blogs/devops/aws-codecommit-returns-to-general-availability
    type: official-blog
    accessed: 2026-11-15
  - id: s3
    title: "Document history for the AWS Cloud9 User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cloud9/latest/user-guide/history.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "How to migrate from CodeCatalyst - Amazon CodeCatalyst"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/codecatalyst/latest/userguide/migration.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "Development lifecycle - DevOps Guidance"
    publisher: Amazon Web Services (AWS Well-Architected)
    url: https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/development-lifecycle.html
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "Content Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html
    type: official-learning
    accessed: 2026-11-15
  - id: s7
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-15
created: 2026-11-15
---

# AWS developer tools

"Developer tools" is one of the service categories AWS uses to organize its portfolio. It groups the managed services that help teams write code, keep it under version control, store packages, build and test it, release it, and then debug and stress it in production. The AWS overview whitepaper lists thirteen entries in the category: AWS Infrastructure Composer, AWS Cloud9, AWS CloudShell, AWS CodeArtifact, AWS CodeBuild, Amazon CodeCatalyst, AWS CodeCommit, AWS CodeDeploy, AWS CodePipeline, Amazon Corretto, AWS Fault Injection Service, Amazon Q Developer and AWS X-Ray.[^s1] This node is the category itself; each service has (or will get) its own node.

## How the pieces fit together

The category maps closely onto a software delivery pipeline. **Source:** CodeCommit hosts private Git repositories, and CodeArtifact is a managed repository for the software packages a build consumes and publishes.[^s1] **Build and test:** CodeBuild is a fully managed build service that compiles code, runs tests and produces deployable packages.[^s1] **Release:** CodePipeline is the continuous-delivery orchestrator that models release stages, and CodeDeploy automates deployments to EC2 instances, on-premises servers and other targets.[^s1] **Authoring:** CloudShell is a browser-based shell for working with AWS resources, Infrastructure Composer lets you visually compose serverless applications backed by infrastructure as code, Amazon Corretto is a no-cost OpenJDK distribution, and Amazon Q Developer (formerly Amazon CodeWhisperer) is the generative-AI assistant for coding, upgrades, troubleshooting and security scanning.[^s1] **Operate and harden:** X-Ray traces requests through distributed applications such as microservices, and Fault Injection Service runs controlled fault-injection experiments to test resilience.[^s1]

AWS's Well-Architected DevOps Guidance describes the same territory as a "development lifecycle" practice area with eight capabilities — local development, software component management, everything as code, code review, cryptographic signing, continuous integration, continuous delivery and advanced deployment strategies — and points to the AWS Deployment Pipeline Reference Architecture as a worked implementation.[^s5] The developer-tools services are AWS's native building blocks for those capabilities, though the guidance is tool-agnostic.

## Lifecycle changes and gotchas (as of 2026-11)

The category has been unusually volatile, and the overview whitepaper still lists services without flagging their lifecycle status, so check each service's own documentation before choosing it.[^s1]

- **AWS CodeCommit** was de-emphasized in July 2024, but on 24 November 2025 AWS returned it to full general availability and reopened sign-ups to new customers. AWS said customers valued its IAM, VPC endpoint, CloudTrail and CodePipeline/CodeBuild integration, kept pricing and the 99.9% uptime SLA unchanged, and published a roadmap including Git LFS support (planned for Q1 2026) and Region expansion starting Q3 2026.[^s2]
- **AWS Cloud9** has been closed to new customers since 25 July 2024; existing customers can keep using it, and AWS's migration guidance points to the AWS IDE Toolkits and AWS CloudShell instead.[^s3]
- **Amazon CodeCatalyst** closed to new customers on 7 November 2025. Existing spaces keep working and receive security and availability investment but no new features, and new spaces cannot be created; AWS recommends GitLab Duo with Amazon Q as the alternative and documents how to mirror repositories to GitLab or GitHub.[^s4]

## When to use them (and when not)

Use the native developer tools when you want CI/CD that stays inside your AWS accounts — IAM-scoped access, private networking, CloudTrail audit and pay-per-use pricing without managing build servers. Teams already standardized on GitHub, GitLab or another platform commonly keep source and code review there and use CodeBuild, CodePipeline and CodeDeploy only for the AWS-facing build and deploy stages; nothing in the DevOps Guidance requires an all-AWS toolchain.[^s5] The guidance's "everything as code" capability applies to the pipeline and infrastructure definitions too, not only application code.[^s5]

## Exam relevance

The AWS Certified Cloud Practitioner (CLF-C02) exam guide expects candidates to know developer tool services and capabilities, naming CodeBuild, CodePipeline and X-Ray, and to identify tools to develop, deploy and troubleshoot applications.[^s6] Its in-scope list for the Developer Tools category is the AWS CLI, CodeBuild, CodePipeline and X-Ray.[^s7]

## Connections

The category is a part of Amazon Web Services and **has parts** including CodePipeline, CodeBuild, CodeDeploy, CodeCommit, X-Ray and Amazon Q Developer, all listed in the AWS overview.[^s1] It is closely **related** to the CI/CD, infrastructure as code and DevOps practice nodes, because those are the practices the tools implement in the DevOps Guidance development lifecycle.[^s5] It sits next to the management and governance category, which covers operating rather than building workloads.[^s1] It is **covered by** the Cloud Practitioner exam through Task Statement 3.8.[^s6]

## Sources
[^s1]: Amazon Web Services, "Developer tools - Overview of Amazon Web Services". https://docs.aws.amazon.com/whitepapers/latest/aws-overview/developer-tools (accessed 2026-11-15)
[^s2]: Amazon Web Services, "AWS CodeCommit Returns to General Availability", AWS DevOps & Developer Productivity Blog, 2025-11-24. https://aws.amazon.com/blogs/devops/aws-codecommit-returns-to-general-availability (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Document history for the AWS Cloud9 User Guide". https://docs.aws.amazon.com/cloud9/latest/user-guide/history.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "How to migrate from CodeCatalyst". https://docs.aws.amazon.com/codecatalyst/latest/userguide/migration.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "Development lifecycle - DevOps Guidance", AWS Well-Architected. https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/development-lifecycle.html (accessed 2026-11-15)
[^s6]: Amazon Web Services, "Content Domain 3: Cloud Technology and Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain3.html (accessed 2026-11-15)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/clf-02-in-scope-services.html (accessed 2026-11-15)
