---
id: kt:aws-cdk
type: Service
title: AWS Cloud Development Kit (AWS CDK)
aliases: [AWS CDK, CDK, Cloud Development Kit]
domain: devops-and-iac
summary: >-
  An open-source infrastructure-as-code framework that lets you define AWS infrastructure in
  general-purpose programming languages, synthesizes it into CloudFormation templates and deploys
  it through AWS CloudFormation.
verified: 2026-11-09
status: current
broader: [kt:infrastructure-as-code]
related: [kt:aws-sam, kt:cloudformation-drift-detection]
relations:
  - {predicate: dependsOn, object: kt:aws-cloudformation}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:amazon-ecr}
  - {predicate: governedBy, object: kt:cloudformation-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "AWS Cloud Development Kit FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/cdk/faqs/
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "AWS CDK Constructs"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/cdk/v2/guide/constructs.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "AWS CDK bootstrapping"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cdk/v2/guide/bootstrapping.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Deploy AWS CDK applications"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cdk/v2/guide/deploy.html
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "Introducing AWS CDK Refactor (Preview)"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/09/aws-cdk-refactor-preview
    type: official-blog
    accessed: 2026-11-09
  - id: s6
    title: "AWS CDK is splitting Construct Library and CLI"
    publisher: Amazon Web Services (AWS Open Source Blog)
    url: https://aws.amazon.com/blogs/opensource/aws-cdk-is-splitting-construct-library-and-cli/
    type: official-blog
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS Cloud Development Kit (AWS CDK)

The AWS Cloud Development Kit (AWS CDK) is a development framework for defining cloud infrastructure in familiar programming languages instead of hand-written templates. When a CDK app runs, it generates AWS CloudFormation templates, and CloudFormation then provisions the resources, so you keep CloudFormation's deployment safety, automatic rollback and drift detection.[^s1] As of 2026-11 it is generally available for JavaScript, TypeScript, Python, Java and C#, with Go in Developer Preview; it is open source under the Apache License 2.0 and available in all public AWS Regions.[^s1] The current major version is CDK v2: CDK v1 entered maintenance on 1 June 2022 and reached end of support on 1 June 2023.[^s2]

## How it works

The basic building block is the **construct**, which represents one or more CloudFormation resources and their configuration; an app is a tree of constructs grouped into stacks.[^s2] The AWS Construct Library offers three levels. **L1** constructs (prefixed `Cfn`, such as `CfnBucket`) are generated from the CloudFormation resource specification and map one-to-one to a resource type. **L2** constructs, such as `s3.Bucket`, still map to a single resource but add sensible defaults, security best practices, grant methods and glue code; they are the level most teams use. **L3** constructs, or patterns, combine several resources into an opinionated architecture, for example a load-balanced Fargate service.[^s2] Constructs from third parties and Construct Hub are treated the same as AWS's own, and the guide recommends composing constructs rather than inheriting from them.[^s2]

Deployment runs in stages. `cdk synth` (also run implicitly by `cdk deploy`) executes the app through construction, preparation, validation and synthesis, producing a **cloud assembly** that holds one CloudFormation template per stack plus file and Docker image assets.[^s4] The CLI then publishes file assets to an S3 bucket and images to an ECR repository, and submits each template to CloudFormation as a stack.[^s4] By that point the CDK app has already exited, so code that must run during deployment needs a custom resource or the Triggers module; values unknown at synth time, such as generated bucket names, appear as tokens.[^s4]

## Bootstrapping, quotas and pricing

Every target environment must be **bootstrapped** before its first deployment. `cdk bootstrap` deploys a CloudFormation stack named `CDKToolkit` by default, containing an S3 bucket for assets, an ECR repository for container images and IAM roles for deployment; environments are independent, so each one is bootstrapped separately.[^s3] At deploy time the caller's identity assumes these bootstrap roles: a deployment action role that passes a CloudFormation execution role to CloudFormation, plus file-publishing, image-publishing and lookup roles. The CLI checks the bootstrap version parameter for compatibility.[^s4] A frequent gotcha is a synthesized template that expects a differently customized bootstrap stack, such as another asset bucket.[^s4]

There is no additional charge for AWS CDK: you pay only for the AWS resources your app creates, including the bootstrap bucket and repository.[^s1][^s3] CDK apps inherit CloudFormation's limits, so per-template and per-account CloudFormation quotas still cap how large a stack can grow.[^s1]

## What changed (2025)

From February 2025 the CDK CLI and the Construct Library release independently. The CLI jumped to a new version line starting at 2.1000.0, while the library kept its 2.x numbering. Compatibility is now judged by release date: a CLI released on or after a library version is guaranteed to work with it, so AWS advises upgrading the CLI at least as often as the library.[^s6] In September 2025 AWS previewed `cdk refactor`, which renames constructs and moves resources between stacks without replacing deployed resources. It builds on CloudFormation stack refactoring and is available wherever CDK is supported.[^s5]

## When to use it (and when not)

CDK suits teams that want loops, types, IDE support, unit tests and reusable libraries for infrastructure, and that are happy to stay on AWS's CloudFormation engine.[^s1][^s2] Raw CloudFormation templates remain simpler where a declarative file is enough or where non-developers review changes, and L1 constructs are the escape hatch when an L2 hides a property you need.[^s2] Renaming constructs or moving them between stacks can otherwise cause stateful resources to be replaced, so use `cdk refactor` for such reorganizations.[^s5]

## Connections

CDK **depends on AWS CloudFormation**: every app becomes CloudFormation templates and stacks, so **CloudFormation quotas** and drift detection apply to it too.[^s1][^s4] It is a form of **infrastructure as code** and sits beside **AWS SAM** as AWS's higher-level authoring options. Its bootstrap environment integrates with **Amazon S3** and **Amazon ECR** for assets and is secured by **AWS IAM** roles; each environment is bootstrapped separately, and CDK is available in all public AWS **Regions**.[^s1][^s3][^s4]

## Sources
[^s1]: Amazon Web Services, "AWS Cloud Development Kit FAQs". https://aws.amazon.com/cdk/faqs/ (accessed 2026-11-09)
[^s2]: Amazon Web Services, "AWS CDK Constructs". https://docs.aws.amazon.com/en_us/cdk/v2/guide/constructs.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "AWS CDK bootstrapping". https://docs.aws.amazon.com/cdk/v2/guide/bootstrapping.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Deploy AWS CDK applications". https://docs.aws.amazon.com/cdk/v2/guide/deploy.html (accessed 2026-11-09)
[^s5]: Amazon Web Services, "Introducing AWS CDK Refactor (Preview)", What's New with AWS. https://aws.amazon.com/about-aws/whats-new/2025/09/aws-cdk-refactor-preview (accessed 2026-11-09)
[^s6]: Rico Huijbers, "AWS CDK is splitting Construct Library and CLI", AWS Open Source Blog. https://aws.amazon.com/blogs/opensource/aws-cdk-is-splitting-construct-library-and-cli/ (accessed 2026-11-09)
