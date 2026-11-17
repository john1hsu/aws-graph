---
id: kt:ec2-image-builder
type: Service
title: EC2 Image Builder
aliases: [Amazon EC2 Image Builder, AWS Image Builder, Image Builder]
domain: compute
summary: >-
  A fully managed AWS service that builds, tests and distributes custom, patched AMIs and container images
  through versioned recipes, YAML components and scheduled pipelines, charging nothing itself beyond the
  EC2, EBS, ECR, Inspector and other resources a build uses.
verified: 2026-11-17
status: current
broader: [kt:amazon-ec2]
related: [kt:amazon-machine-images, kt:immutable-infrastructure, kt:amazon-ecr, kt:aws-systems-manager]
relations:
  - {predicate: integratesWith, object: kt:amazon-inspector}
  - {predicate: integratesWith, object: kt:aws-resource-access-manager}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: governedBy, object: kt:ec2-image-builder-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-devops-engineer-professional}
sources:
  - id: s1
    title: "What is Image Builder? - EC2 Image Builder"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "How EC2 Image Builder works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/imagebuilder/latest/userguide/how-image-builder-works.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "EC2 Image Builder endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/imagebuilder.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "EC2 Image Builder FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/image-builder/faqs
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "Amazon Inspector integration in Image Builder"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/imagebuilder/latest/userguide/integ-inspector.html
    type: official-docs
    accessed: 2026-11-17
  - id: s6
    title: "Announcing flexible AMI distribution capabilities for EC2 Image Builder"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/announcing-flexible-ami-distribution-ec2-image-builder
    type: official-blog
    accessed: 2026-11-17
  - id: s7
    title: "EC2 Image Builder enhances lifecycle policies with wildcard support and simplified IAM"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/02/ec2-image-builder-lifecycle-enhancements
    type: official-blog
    accessed: 2026-11-17
  - id: s8
    title: "Domain 1: SDLC Automation - AWS Certified DevOps Engineer - Professional (DOP-C02) exam guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/userguide/devops-engineer-professional-02-domain1.html
    type: official-learning
    accessed: 2026-11-17
created: 2026-11-17
---

# EC2 Image Builder

EC2 Image Builder is a fully managed service for creating, maintaining and distributing customized server images, both Amazon Machine Images (AMIs) and Docker container images stored in Amazon ECR.[^s1] You define what the image should contain once, and Image Builder runs the build, tests the result and copies it where it is needed, either on demand or on a schedule that keeps images patched; the images it produces are owned by your account.[^s1] Supported AMI operating systems span Amazon Linux 2 and 2023, Red Hat Enterprise Linux, SUSE Linux Enterprise Server, Ubuntu LTS releases, Windows Server 2012 R2 through 2025, and macOS.[^s1]

## How it works

An image is assembled from a handful of versioned resources:[^s1][^s2]

- **Components** are YAML (or JSON) documents of build or test steps, run on the instance by the AWS Task Orchestrator and Executor (AWSTOE), which can also run on its own outside Image Builder. AWS publishes managed components, and you can write your own.[^s1][^s2]
- **Recipes** pair a base image with components. An image recipe produces an AMI; a container recipe adds a Dockerfile template and a target ECR repository. Recipes and components use semantic versioning, and a recipe can track the latest base OS version automatically.[^s2]
- **Infrastructure configuration** sets where builds run: instance types, subnet, security groups, instance profile, logging and SNS notifications.[^s1]
- **Distribution configuration** lists the target Regions, launch permissions, KMS encryption and optional launch-template updates.[^s1][^s2]
- **Image pipelines** tie these together and run manually or on a schedule.[^s2]

Each run has a build stage, where a temporary EC2 instance in your account applies the build components and then validates them, and a test stage, where an instance launched from the new image runs the test components.[^s1][^s2] Image Builder distributes an image only after every configured test passes, then deletes the temporary build and test resources; failed build instances are terminated by default unless you choose to keep them for troubleshooting.[^s1][^s2] Creating a pipeline creates nothing billable on its own; resources appear only when a build actually runs.[^s2]

## Security, governance and distribution

For hardening, AWS provides Amazon-managed STIG components at no extra charge, and CIS hardening components can be subscribed to in AWS Marketplace, though container recipes do not support the CIS ones.[^s2] AWS notes that these settings are guidance and do not by themselves make a workload compliant.[^s4] Amazon Inspector can scan images for CVEs: by default it scans the test instance (which requires Inspector to be activated for EC2), while an on-demand AMI scan can fail the image when a finding exceeds a severity you set, and a Critical finding always fails it.[^s5] Image Builder only checks Inspector activation when a pipeline or image is created or updated, so deactivating Inspector later silently drops findings rather than failing builds.[^s5]

Components, recipes and images can be shared through AWS Resource Access Manager, and AWS Organizations policies can restrict member accounts to launching approved AMIs.[^s1][^s2] Launch permissions set in a distribution configuration propagate to the AMI copies in every distribution Region.[^s2] Since November 2025 you can also distribute an existing AMI without running a full pipeline, retry failed distributions, and build distribution workflows with approval pauses (for example, test Region first, then production), at no extra cost in all Regions including China and GovCloud (US).[^s6]

## Quotas, pricing and gotchas

Image Builder itself is free; you pay for the EC2 instances that build and test, EBS snapshots behind AMIs, S3 logs, ECR storage and transfer, and Amazon Inspector scans.[^s1][^s4] As of 2026-11, default per-Region quotas include 75 image pipelines, 100 concurrent builds, 20 components per image recipe, a 64 KB component size, 1,000 each of components and image recipes, and 50 lifecycle policies; all of the service's quotas are adjustable.[^s3]

Scheduled pipelines keep producing AMIs and snapshots, which accumulate costs. Lifecycle policies deprecate or delete old images and their resources, and since February 2026 one policy can match many recipes through wildcard patterns, including recipes created later.[^s7] Resource names are normalized for ARNs (lowercased, with spaces and underscores turned into hyphens), so names that differ only in case or separators collide.[^s2] If a build fails, look for leftover temporary resources.[^s2]

## When to use it (and when not)

Use Image Builder to run a "golden image" bakery: regularly patched, hardened, tested AMIs or container images rolled out to many Regions and accounts, which is the backbone of immutable deployments.[^s1][^s4] A hand-captured AMI is fine for a one-off server but drifts as patches land. Teams already invested in a third-party image tool can keep it, but Image Builder adds native testing, Inspector scanning, RAM sharing and Organizations-aware distribution with no charge for the service itself.[^s1][^s5]

## Connections

Image Builder sits **within** the Amazon EC2 family and produces **Amazon Machine Images** and **Amazon ECR** container images.[^s1] It **integrates with Amazon Inspector** for vulnerability scanning, **AWS Resource Access Manager** for sharing, and **AWS Organizations** for approved-AMI governance; AMIs it distributes can be **secured by AWS KMS** keys.[^s1][^s5] **AWS Systems Manager** supports enhanced image metadata during builds, and the service is a common engine for **immutable infrastructure**.[^s2] Its limits are **governed by** the quotas above.[^s3] The DOP-C02 exam guide for **AWS Certified DevOps Engineer – Professional** names EC2 Image Builder in Task 1.3 (automating instance and container image builds) and Task 1.4 (tools for distributing code).[^s8]

## Sources
[^s1]: Amazon Web Services, "What is Image Builder?", EC2 Image Builder User Guide. https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "How EC2 Image Builder works", EC2 Image Builder User Guide. https://docs.aws.amazon.com/imagebuilder/latest/userguide/how-image-builder-works.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "EC2 Image Builder endpoints and quotas", AWS General Reference. https://docs.aws.amazon.com/general/latest/gr/imagebuilder.html (accessed 2026-11-17)
[^s4]: Amazon Web Services, "EC2 Image Builder FAQs". https://aws.amazon.com/image-builder/faqs (accessed 2026-11-17)
[^s5]: Amazon Web Services, "Amazon Inspector integration in Image Builder", EC2 Image Builder User Guide. https://docs.aws.amazon.com/imagebuilder/latest/userguide/integ-inspector.html (accessed 2026-11-17)
[^s6]: Amazon Web Services, "Announcing flexible AMI distribution capabilities for EC2 Image Builder" (What's New, 2025-11-21). https://aws.amazon.com/about-aws/whats-new/2025/11/announcing-flexible-ami-distribution-ec2-image-builder (accessed 2026-11-17)
[^s7]: Amazon Web Services, "EC2 Image Builder enhances lifecycle policies with wildcard support and simplified IAM" (What's New, 2026-02-27). https://aws.amazon.com/about-aws/whats-new/2026/02/ec2-image-builder-lifecycle-enhancements (accessed 2026-11-17)
[^s8]: Amazon Web Services, "Domain 1: SDLC Automation", AWS Certified DevOps Engineer – Professional (DOP-C02) exam guide. https://docs.aws.amazon.com/aws-certification/latest/userguide/devops-engineer-professional-02-domain1.html (accessed 2026-11-17)
