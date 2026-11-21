---
id: kt:amazon-sagemaker-unified-studio
type: Service
title: Amazon SageMaker Unified Studio
aliases: [SageMaker Unified Studio, SMUS]
domain: analytics
summary: >-
  The single web-based development environment of the next generation of Amazon SageMaker, where
  teams work in shared projects to query, prepare, govern and model data and build generative AI
  applications using AWS analytics, ML and Amazon Bedrock tools, with governance from the
  DataZone-based SageMaker Catalog.
verified: 2026-11-21
status: current
broader: [kt:aws-analytics-services]
related: [kt:amazon-sagemaker-ai, kt:amazon-bedrock, kt:data-lake-on-aws, kt:amazon-q-developer]
relations:
  - {predicate: dependsOn, object: kt:amazon-datazone}
  - {predicate: integratesWith, object: kt:amazon-redshift}
  - {predicate: integratesWith, object: kt:amazon-emr}
  - {predicate: integratesWith, object: kt:aws-glue}
  - {predicate: securedBy, object: kt:iam-identity-center}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is Amazon SageMaker Unified Studio? - Amazon SageMaker Unified Studio"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/sagemaker-unified-studio/latest/userguide/what-is-sagemaker-unified-studio.html
    type: official-docs
    accessed: 2026-11-21
  - id: s2
    title: "Amazon SageMaker Unified Studio terminology and concepts - Amazon SageMaker Unified Studio"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/sagemaker-unified-studio/latest/adminguide/concepts.html
    type: official-docs
    accessed: 2026-11-21
  - id: s3
    title: "Projects - Amazon SageMaker Unified Studio"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/sagemaker-unified-studio/latest/userguide/projects.html
    type: official-docs
    accessed: 2026-11-21
  - id: s4
    title: "SageMaker pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/sagemaker/pricing/
    type: official-docs
    accessed: 2026-11-21
  - id: s5
    title: "Amazon SageMaker Unified Studio is now generally available"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/03/amazon-sagemaker-unified-studio-generally-available
    type: official-blog
    accessed: 2026-11-21
  - id: s6
    title: "Collaborate and build faster with Amazon SageMaker Unified Studio, now generally available"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/collaborate-and-build-faster-with-amazon-sagemaker-unified-studio-now-generally-available
    type: official-blog
    accessed: 2026-11-21
  - id: s7
    title: "Amazon SageMaker Unified Studio adds getting started tutorials and in-product release notes"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/05/smus-getting-started
    type: official-blog
    accessed: 2026-11-21
created: 2026-11-21
---

# Amazon SageMaker Unified Studio

Amazon SageMaker Unified Studio is the development environment at the center of the next generation of Amazon SageMaker. It gathers AWS data, analytics, AI and machine learning services behind one interface, so a team can build, deploy, run and monitor data and ML workflows without hopping between consoles. Administrators manage users, groups and shared resources; everyone else works inside projects where data, compute and code are shared.[^s1] It was previewed at re:Invent 2024 and became generally available on March 13, 2025.[^s5][^s6] Despite the name, it is a different product from Amazon SageMaker AI, the service for training and hosting models, which shows up in Unified Studio as one capability among several.[^s2]

## How it works

The organizing entity is a **domain**, which holds assets, users and projects. An organization can run one domain or one per business unit, and **domain units** divide a domain by team and delegate authority over it. The IAM principal that creates the domain becomes its default administrator.[^s2] Users sign in at the domain URL their administrator gives them, with IAM credentials or single sign-on through IAM Identity Center or SAML.[^s1][^s2] A 2026 What's New post also refers to IAM-based domains as a supported setup.[^s7]

Work happens in **projects**, which are both the place a team collaborates and its permission boundary. Members see the files and tools their project's profile provides.[^s3] Projects are created from **project profiles**, templates made up of **blueprints**. The documented profile types are All capabilities, SQL analytics, Generative AI application development and Custom, and only domain administrators can define them. Default blueprints cover Redshift Serverless, EMR on EC2 and EMR Serverless, a lakehouse catalog and database, ML experiments, workflows, partner apps and the Bedrock generative AI tools.[^s2] Each project has its own Git repository. The concepts page notes that AWS CodeCommit, the original default, can no longer back new projects.[^s2]

Inside a project, users get JupyterLab notebook spaces (each a compute instance plus an EBS volume), a SQL query editor, Visual ETL, workflows, and Bedrock features such as Agents, Guardrails, Prompts, Flows and Evaluation. Amazon Q is available on its Free Tier to everyone in a domain by default.[^s2] At GA, AWS highlighted Amazon Q Developer for SQL generation, ETL building and notebook troubleshooting, plus Bedrock Knowledge Bases that can draw on S3 folders in a project.[^s5][^s6]

Governance runs through the **SageMaker Catalog**, a domain-wide business data catalog with glossaries, metadata forms and a publish/subscribe workflow for access requests and grants. Custom permissions are expressed in Cedar.[^s2] The catalog is built on Amazon DataZone, and domains are managed from the DataZone console.[^s2][^s4] An existing DataZone domain is a separate domain type from a SageMaker unified domain, and each has its own guide.[^s3]

## Pricing and gotchas

Unified Studio has no charge of its own. You pay for SageMaker Catalog usage plus each service you use through it, such as Athena, Redshift, EMR, Glue, MWAA, Bedrock and SageMaker AI, at that service's rates.[^s4] As of 2026-11, with no Region named on the page, SageMaker Catalog charges $10 per 100,000 requests, $0.40 per GB of metadata storage and $1.776 per compute unit. Each account gets 20 MB of storage, 4,000 requests and 0.2 compute units free per month, and core APIs such as CreateDomain, CreateProject and Search are always free. Catalog AI recommendations cost $0.015 per 1,000 input tokens and $0.075 per 1,000 output tokens, with no free allowance. New accounts get 250 hours of sc.t3.medium notebooks per month for the first two months.[^s4]

Gotchas:

- The quick-setup option builds an Identity Center-based domain and adds networking resources that cost extra. AWS points to manual setup for full cost visibility.[^s4]
- The GA announcement named twelve Regions, including US East (N. Virginia), Europe (Frankfurt) and South America (São Paulo), and AWS keeps the current list on a separate Supported Regions page.[^s6] Check that page before you design around a Region.
- Not every new feature lands in every domain type. For example, the May 2026 getting-started tutorials apply to IAM-based domains.[^s7]

## When to use it (and when not)

Unified Studio makes sense when data engineers, analysts, ML scientists and generative AI builders need to share governed data and tools in one place, with project-level permissions and catalog-driven access.[^s1][^s2] If you only need model training and hosting, SageMaker AI on its own is simpler. If you only need a business catalog and access workflow, standalone DataZone is still an option.[^s3] Teams already committed to a single engine, such as Redshift alone, may get little from the extra layer.

## Connections

Unified Studio is part of the `kt:aws-analytics-services` portfolio and gives a `kt:data-lake-on-aws` its working environment. It **depends on** `kt:amazon-datazone`, whose domain and catalog machinery underlies the SageMaker Catalog.[^s2][^s4] Through its blueprints it **integrates with** `kt:amazon-redshift`, `kt:amazon-emr` and `kt:aws-glue`, and it hosts `kt:amazon-bedrock` features, `kt:amazon-sagemaker-ai` ML experiments and `kt:amazon-q-developer` assistance.[^s2][^s5] Sign-in is **secured by** `kt:iam-identity-center` (or IAM), and availability is **scoped to** a list of supported `kt:aws-regions`.[^s2][^s6]

## Sources
[^s1]: Amazon Web Services, "What is Amazon SageMaker Unified Studio?". https://docs.aws.amazon.com/sagemaker-unified-studio/latest/userguide/what-is-sagemaker-unified-studio.html (accessed 2026-11-21)
[^s2]: Amazon Web Services, "Amazon SageMaker Unified Studio terminology and concepts" (Administrator Guide). https://docs.aws.amazon.com/sagemaker-unified-studio/latest/adminguide/concepts.html (accessed 2026-11-21)
[^s3]: Amazon Web Services, "Projects - Amazon SageMaker Unified Studio". https://docs.aws.amazon.com/sagemaker-unified-studio/latest/userguide/projects.html (accessed 2026-11-21)
[^s4]: Amazon Web Services, "SageMaker pricing". https://aws.amazon.com/sagemaker/pricing/ (accessed 2026-11-21)
[^s5]: Amazon Web Services, "Amazon SageMaker Unified Studio is now generally available" (What's New, 2025-03-13). https://aws.amazon.com/about-aws/whats-new/2025/03/amazon-sagemaker-unified-studio-generally-available (accessed 2026-11-21)
[^s6]: Amazon Web Services, "Collaborate and build faster with Amazon SageMaker Unified Studio, now generally available" (AWS News Blog, 2025-03-13). https://aws.amazon.com/blogs/aws/collaborate-and-build-faster-with-amazon-sagemaker-unified-studio-now-generally-available (accessed 2026-11-21)
[^s7]: Amazon Web Services, "Amazon SageMaker Unified Studio adds getting started tutorials and in-product release notes" (What's New, 2026-05-11). https://aws.amazon.com/about-aws/whats-new/2026/05/smus-getting-started (accessed 2026-11-21)
