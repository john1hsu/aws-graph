---
id: kt:aws-transform
type: Service
title: AWS Transform
aliases: [Amazon Q Developer transformation capabilities, AWS Transform for .NET, AWS Transform for mainframe, AWS Transform for VMware, AWS Transform custom]
domain: migration-and-hybrid
summary: >-
  AWS's agentic AI service for migration and modernization: specialized AI agents assess
  on-premises estates, plan and execute VMware and other server migrations, port .NET and SQL
  Server workloads, refactor mainframe COBOL, and run organization-wide custom code
  transformations, with humans reviewing and approving each plan.
verified: 2026-11-14
status: current
broader: [kt:aws-migration-and-transfer-services]
related: [kt:vmware-migration-to-aws, kt:seven-rs-migration-strategies, kt:amazon-q-developer]
relations:
  - {predicate: integratesWith, object: kt:aws-transform-mgn}
  - {predicate: integratesWith, object: kt:aws-database-migration-service}
  - {predicate: securedBy, object: kt:aws-kms}
sources:
  - id: s1
    title: "AWS Transform FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/transform/faq
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "AWS Transform — AWS Transform launch guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/transform/latest/launchguide/aws-transform.html
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "Getting started with AWS Transform"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/transform/latest/userguide/getting-started.html
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "AWS Transform pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/transform/pricing
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "AWS Transform is now generally available"
    publisher: Amazon Web Services (AWS Migration & Modernization Blog)
    url: https://aws.amazon.com/blogs/migration-and-modernization/aws-transform-generally-available
    type: official-blog
    accessed: 2026-11-14
  - id: s6
    title: "AWS launches AWS Transform custom to accelerate organization-wide application modernization"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/transform-custom-organization-wide-modernization
    type: official-blog
    accessed: 2026-11-14
created: 2026-11-14
---

# AWS Transform

AWS Transform is an agentic AI service for moving and modernizing infrastructure, applications and code. AWS describes it as a collaborative transformation workbench in which specialized AI agents do the repetitive analysis and conversion work while people review, steer and approve.[^s2] It reached general availability on May 15, 2025, with agents for .NET, mainframe and VMware; those capabilities had been previewed at re:Invent 2024 as Amazon Q Developer transformation capabilities.[^s5] A custom transformation agent followed as generally available on December 1, 2025, launching in US East (N. Virginia).[^s6]

## How it works

Work is organized into jobs. Each job has a plan that the service builds from your stated goals, and an orchestration layer coordinates task-specific agents, for example one that generates network configurations or one that extracts business rules from COBOL. Administrators and approvers can review a plan before any transformation runs, and Windows, mainframe and custom workflows can pause for feedback or requested changes.[^s1] Migration teams share a single collaborative workspace and can ask about waves and per-server status in natural language.[^s1]

The capability areas, as of 2026-11, are:

- **Assessments**: right-sizing, pricing-model comparisons and TCO estimates for on-premises x86 servers. AWS notes the figures are directional, not quotes.[^s1]
- **Migrations**: discovery, wave planning, 7 Rs recommendations, landing zones built with AWS Control Tower, network conversion to VPCs (exportable as CloudFormation, CDK, Terraform or LZA code) and rehosting to Amazon EC2. Sources include VMware, Hyper-V, Nutanix, KVM and bare metal.[^s1][^s2]
- **Windows modernization**: porting .NET Framework and older .NET apps to cross-platform .NET 8 or .NET 10, and converting SQL Server to Aurora PostgreSQL, with AWS DMS Schema Conversion handling storage objects.[^s1]
- **Mainframe modernization**: analysis of COBOL, PL/I and JCL, refactoring to Java (or reimagining as cloud-native), documentation and test generation.[^s2]
- **Custom and continuous modernization**: AWS-managed transformations such as Java, Python, Node.js and AWS SDK upgrades, plus organization-specific ones you describe in natural language, run at scale from the AWS Transform CLI and, for continuous modernization, opening pull requests in GitHub, GitLab or Bitbucket.[^s1][^s2]

The service is reachable from a web console, the CLI, IDE integrations, a Visual Studio extension and MCP.[^s2]

## Setup, security and pricing

You enable AWS Transform in a Region from the console and choose user access through IAM Identity Center, a third-party identity provider, or IAM-only access. That choice is final once the service is enabled. You also pick the encryption key (AWS managed by default, or your own) and can use your own Amazon S3 bucket for artifacts instead of the service-managed one.[^s3] Data in transit uses TLS 1.2 or higher. Content may be used to improve foundation models unless you opt out through an AWS Organizations AI services opt-out policy or IDE settings.[^s1] For migrations, every commercial Region is supported as a target except Middle East (Bahrain) and Middle East (UAE). The launch guide lists US East (N. Virginia) as in scope for FedRAMP.[^s1][^s2]

Pricing as of 2026-11 (no Region-specific prices are published): assessments and the Windows, mainframe and VMware migration agents cost nothing, though AWS warns that future capabilities may be paid. The custom transformation agent and continuous modernization analyses cost $0.035 per agent minute, billed per minute for active agent work only. AWS-managed .NET transformations through the custom agent include up to 50,000 free agent minutes a month. AWS resources you create from Transform output are billed at their standard prices.[^s4]

## When to use it (and when not)

Use AWS Transform for large, repetitive migration or modernization work where planning and code conversion dominate the effort: hundreds of VMs, many .NET services, a COBOL estate, or a dependency upgrade across many repositories. AWS's GA claims include .NET porting up to 4x faster and VMware network conversion up to 80x faster than manual methods. These are vendor figures, so treat them as such.[^s5] For a few servers you want to rehost step by step, the self-directed AWS Transform MGN console is the simpler path. Containerization also needs source code; Transform will not containerize running servers that have none.[^s1]

## Connections

AWS Transform sits under AWS migration and transfer services. Its migration agent orchestrates AWS Transform MGN, the separate block-level replication service (formerly AWS Application Migration Service), and offers either an agentic rehost that sets MGN up for you or the classic self-directed MGN console.[^s1][^s2] It is the planning layer in VMware migration to AWS and recommends strategies from the 7 Rs migration strategies. Its SQL Server conversion builds on AWS Database Migration Service Schema Conversion, artifacts can be encrypted with AWS KMS keys, and its lineage traces back to Amazon Q Developer's transformation features.[^s1][^s3][^s5]

## Sources
[^s1]: Amazon Web Services, "AWS Transform FAQs". https://aws.amazon.com/transform/faq (accessed 2026-11-14)
[^s2]: Amazon Web Services, "AWS Transform — AWS Transform launch guide". https://docs.aws.amazon.com/transform/latest/launchguide/aws-transform.html (accessed 2026-11-14)
[^s3]: Amazon Web Services, "Getting started with AWS Transform". https://docs.aws.amazon.com/transform/latest/userguide/getting-started.html (accessed 2026-11-14)
[^s4]: Amazon Web Services, "AWS Transform pricing". https://aws.amazon.com/transform/pricing (accessed 2026-11-14)
[^s5]: Amazon Web Services, "AWS Transform is now generally available" (AWS Migration & Modernization Blog, 2025-05-15). https://aws.amazon.com/blogs/migration-and-modernization/aws-transform-generally-available (accessed 2026-11-14)
[^s6]: Amazon Web Services, "AWS launches AWS Transform custom to accelerate organization-wide application modernization" (What's New, 2025-12-01). https://aws.amazon.com/about-aws/whats-new/2025/12/transform-custom-organization-wide-modernization (accessed 2026-11-14)
