---
id: kt:aws-sam
type: Service
title: AWS Serverless Application Model (AWS SAM)
aliases: [AWS SAM, SAM, SAM CLI, AWS SAM CLI, Serverless Application Model]
domain: devops-and-iac
summary: >-
  An open-source framework for building serverless applications on AWS: a shorthand template
  specification that extends AWS CloudFormation, plus the SAM CLI for building, testing locally,
  syncing and deploying those applications.
verified: 2026-11-10
status: current
broader: [kt:infrastructure-as-code, kt:aws-serverless-services]
related: [kt:amazon-api-gateway]
relations:
  - {predicate: dependsOn, object: kt:aws-cloudformation}
  - {predicate: alternativeTo, object: kt:aws-cdk}
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: integratesWith, object: kt:amazon-dynamodb}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "How AWS SAM works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam-overview.html
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "AWS SAM FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/serverless/sam/faqs/
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "AWS SAM template anatomy"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Introduction to using sam sync to sync to AWS Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli-sync.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "CapacityProviderConfig"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-property-function-capacityproviderconfig.html
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "AWS SAM now supports WebSocket APIs for Amazon API Gateway"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/05/aws-sam-websocket-apis-api-gateway/
    type: official-blog
    accessed: 2026-11-10
created: 2026-11-10
---

# AWS Serverless Application Model (AWS SAM)

The AWS Serverless Application Model (AWS SAM) is AWS's open-source framework for defining and shipping serverless applications as code. It has two parts: the **AWS SAM template specification**, a shorthand syntax that extends AWS CloudFormation, and the **AWS SAM CLI**, a command-line tool that initializes, builds, tests, deploys and monitors a SAM project.[^s1] Both are released under the Apache 2.0 license, there is no additional charge for using SAM (you pay only for the resources it creates, as if you had created them by hand), and SAM is available in every Region where AWS Lambda is available.[^s2]

## How it works

A SAM template is a CloudFormation template that declares the transform `AWS::Serverless-2016-10-31`; that declaration is what marks the file as SAM. Only the `Transform` and `Resources` sections are required, and every other section except `Globals` maps directly to the CloudFormation section of the same name.[^s3] `Globals` is unique to SAM: it sets shared properties once for serverless resource types such as `AWS::Serverless::Function`, `Api`, `HttpApi`, `SimpleTable`, `StateMachine` and `CapacityProvider`.[^s3] Because SAM extends CloudFormation, a template can freely mix SAM resource types with any resource CloudFormation supports.[^s2] If you also use CloudFormation language extensions, the `AWS::LanguageExtensions` transform must be listed before the serverless transform.[^s3]

At deployment the transform expands the shorthand into plain CloudFormation. In AWS's walkthrough, a 23-line template grows to more than 200 lines: an `AWS::Serverless::Function` becomes a Lambda function plus an IAM execution role, an `HttpApi` event becomes an API Gateway HTTP API, a `$default` auto-deploying stage and a Lambda permission, and an `AWS::Serverless::SimpleTable` becomes an on-demand DynamoDB table.[^s1] **Connectors** express intent such as "this function can read that table"; SAM turns them into a least-privilege IAM managed policy attached to the function's role, covering the table and its indexes.[^s1] In May 2026 AWS added `AWS::Serverless::WebSocketApi`, which wires up `$connect`, `$disconnect`, `$default` and custom routes with their Lambda integrations and permissions.[^s6]

## The SAM CLI

The CLI covers the developer loop: `sam init` scaffolds a project from a starter template, `sam build` packages dependencies into a `.aws-sam` directory, `sam local invoke` runs a function in a local container (a container runtime such as Docker is required), `sam deploy --guided` transforms the template and deploys it as a CloudFormation stack, and `sam pipeline init --bootstrap` generates CI/CD configuration and the AWS resources it needs. `sam logs`, `sam list` and `sam remote invoke` inspect deployed resources.[^s1] Local debugging is supported for Node.js, Java, Python and Go, and the CLI can also start a local API endpoint and generate sample event payloads.[^s2]

## Gotchas

`sam sync` (part of SAM Accelerate) is built for fast iteration: with `--watch` it keeps running and pushes changes as you save, and where a service API allows it, for example Lambda function code, it updates the resource directly instead of running a CloudFormation deployment.[^s4] That speed has a cost. Direct updates can drift from the CloudFormation template, each sync runs your local build scripts with your own credentials, and AWS recommends using it only for development stacks and `sam deploy` or a pipeline for production.[^s4] Changes a service API can't apply, such as an API Gateway route path in AWS's example, still fall back to a full CloudFormation deployment.[^s4] When skipping unchanged-template deployments, the CLI still forces a CloudFormation deployment if 7 or more days have passed since the last one.[^s4]

A function can run on customer-owned EC2 instances managed by Lambda by setting `CapacityProviderConfig`, but that choice fixes the function's compute type: it must be set on the first deployment and can't be added or removed afterwards.[^s5]

## When to use it (and when not)

SAM fits teams whose stack is mostly Lambda, API Gateway, DynamoDB and Step Functions and who want a declarative YAML template, local emulation and a ready-made pipeline generator, while keeping CloudFormation's deployment model.[^s1][^s2] The AWS CDK is the alternative when you prefer general-purpose languages and reusable libraries over YAML; both ultimately deploy through CloudFormation. Plain CloudFormation remains the option when a template has few serverless resources and the shorthand adds little.[^s2][^s3]

## Connections

SAM **depends on AWS CloudFormation**: every template is transformed into CloudFormation and deployed as a stack.[^s1] It is a form of **infrastructure as code** and the framework most closely tied to **AWS serverless services**, generating **AWS Lambda** functions, **Amazon API Gateway** APIs and **Amazon DynamoDB** tables from short definitions.[^s1][^s6] Its connectors and execution roles are expressed as **AWS IAM** policies.[^s1] It is an **alternative to the AWS CDK** for authoring, and its Regional availability follows Lambda across **AWS Regions**.[^s2]

## Sources
[^s1]: Amazon Web Services, "How AWS SAM works". https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam-overview.html (accessed 2026-11-10)
[^s2]: Amazon Web Services, "AWS SAM FAQs". https://aws.amazon.com/serverless/sam/faqs/ (accessed 2026-11-10)
[^s3]: Amazon Web Services, "AWS SAM template anatomy". https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Introduction to using sam sync to sync to AWS Cloud". https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli-sync.html (accessed 2026-11-10)
[^s5]: Amazon Web Services, "CapacityProviderConfig". https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-property-function-capacityproviderconfig.html (accessed 2026-11-10)
[^s6]: Amazon Web Services, "AWS SAM now supports WebSocket APIs for Amazon API Gateway", What's New with AWS. https://aws.amazon.com/about-aws/whats-new/2026/05/aws-sam-websocket-apis-api-gateway/ (accessed 2026-11-10)
