---
id: kt:aws-certified-developer-associate
type: Certification
title: AWS Certified Developer - Associate
aliases: [DVA-C02, DVA-C03, AWS Developer Associate, AWS DVA]
domain: certifications
summary: >-
  AWS's associate-level certification for people in a developer role, validating the ability to
  develop, test, deploy and debug cloud applications with AWS services, SDKs and CI/CD; exam DVA-C02
  is replaced by DVA-C03 from December 1, 2026.
verified: 2026-11-10
status: current
broader: [kt:aws-certification-program]
related: [kt:aws-certified-solutions-architect-associate, kt:aws-certified-cloud-practitioner, kt:aws-lambda, kt:amazon-dynamodb, kt:aws-sam, kt:amazon-api-gateway]
relations:
  - {predicate: contrastsWith, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "AWS Certified Developer - Associate (DVA-C02) Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/developer-associate-02/developer-associate-02.pdf
    type: official-learning
    accessed: 2026-11-10
  - id: s2
    title: "AWS Certified Developer - Associate"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-developer-associate
    type: official-learning
    accessed: 2026-11-10
  - id: s3
    title: "AWS Certification Recertification"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/recertification/?nc1=h_ls
    type: official-learning
    accessed: 2026-11-10
  - id: s4
    title: "Certification updates from AWS Training and Certification: September 2026"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/september-2026-new-offerings/
    type: official-blog
    accessed: 2026-11-10
created: 2026-11-10
---

# AWS Certified Developer - Associate

AWS Certified Developer - Associate is AWS's associate-level certification for people who work in a developer role.[^s1][^s2] It validates that a candidate can develop, test, deploy and debug cloud applications on AWS: writing and optimizing application code, packaging and shipping it through CI/CD workflows, securing code and data, and finding and fixing application problems.[^s1] The target candidate has at least one year of hands-on experience building and maintaining applications with AWS services, is proficient in at least one high-level programming language, and can call AWS through service APIs, the AWS CLI and SDKs.[^s1] There are no formal prerequisites; AWS suggests people with no IT background start with AWS Certified Cloud Practitioner.[^s2]

## Exam format and scoring

As of 2026-11, the exam has 65 multiple-choice or multiple-response questions in 130 minutes, costs 150 USD (foreign-exchange and other costs may apply), and is delivered at a Pearson VUE test center or as an online proctored exam.[^s2] Fifty questions are scored and 15 are unidentified, unscored items AWS is trialling; unanswered questions count as wrong and there is no guessing penalty.[^s1] Results are pass/fail on a scaled score of 100–1,000 with **720** to pass, under a compensatory model in which only the overall result matters, so per-section feedback is a rough guide at best.[^s1] The product page lists six exam languages, and the English exam uses short names for some services, with a lookup list behind the in-exam Help button.[^s2]

## What DVA-C02 covers

The DVA-C02 guide (version 2.1, published December 12, 2024) splits scored content into four domains:[^s1]

- **Development with AWS Services (32%)** — architectural patterns such as event-driven, fanout, choreography and orchestration; SDK and API code, messaging and streaming; Amazon EventBridge; resilient third-party integrations with retries and circuit breakers; Lambda configuration (memory, concurrency, layers, destinations, dead-letter queues, VPC access) and tuning; and data stores, including DynamoDB keys, indexes, query vs. scan, consistency models and caching.[^s1]
- **Security (26%)** — federated access with Amazon Cognito or IAM, bearer tokens, assuming IAM roles, encryption at rest and in transit with KMS keys and key rotation, client- vs. server-side encryption, secrets management, and data masking for PII and PHI.[^s1]
- **Deployment (24%)** — packaging artifacts, environment-specific configuration with AWS AppConfig, API Gateway stages, Lambda aliases, AWS SAM and CloudFormation templates, and blue/green, canary and rolling deployment strategies with rollbacks.[^s1]
- **Troubleshooting and Optimization (18%)** — reading metrics, logs and traces, CloudWatch embedded metric format, structured logging, tracing annotations, health checks, concurrency, memory sizing and caching.[^s1]

The guide also lists "emerging topics", such as AI-assisted coding, testing and troubleshooting, that may appear only as unscored pretest items.[^s1] Designing architectures, designing CI/CD pipelines, administering IAM users and groups, and designing networks are explicitly outside the target role.[^s1]

## DVA-C03 transition (gotcha)

AWS announced on September 1, 2026 that DVA-C02 can be taken until **November 30, 2026** and DVA-C03 general availability begins **December 1, 2026**, with registration open from October 27, 2026.[^s4] AWS lists the DVA-C03 domains as Development with AWS Services (30%), Security (26%), Testing and Deployment (22%) and Troubleshooting and Optimization (22%).[^s4] The update adds a task on AI security, threads AI-assisted development through every domain, adds container work with ECR, ECS, EKS and Fargate, brings Amazon Bedrock, Amazon Q and Kiro into scope, and keeps prompt engineering, RAG design and SageMaker AI out of scope.[^s4] Credentials earned on DVA-C02 stay active until their original expiry.[^s4] Study material written for C02 will therefore miss containers and AI tooling.

## Validity and recertification

The certification is valid for three years.[^s2] It renews for three more years by passing the latest Developer - Associate exam or AWS Certified DevOps Engineer - Professional, or it can be maintained for one year through AWS Skill Builder within 90 days of expiry with an active subscription.[^s3] Passing it also renews AWS Certified Cloud Practitioner, as any associate exam does.[^s3] Holders of an active AWS certification get 50% off their next exam.[^s2]

## When to take it (and when not)

It suits engineers who write code against AWS, particularly serverless code with Lambda, DynamoDB and API Gateway, and deploy it with SAM and CI/CD.[^s1] Someone whose job is choosing and combining services for whole workloads is a better fit for Solutions Architect - Associate, since architecture design is out of scope here.[^s1]

## Connections

It sits at the associate level of the AWS Certification program, above Cloud Practitioner (which it renews) and below DevOps Engineer - Professional (which renews it).[^s2][^s3] It contrasts with Solutions Architect - Associate: the same 65-question, 720-to-pass format, but a builder's rather than a designer's view.[^s1][^s2] Its heaviest service links are AWS Lambda, Amazon DynamoDB, AWS SAM and Amazon API Gateway, which dominate the development and deployment domains.[^s1]

## Sources
[^s1]: Amazon Web Services, "AWS Certified Developer - Associate (DVA-C02) Exam Guide". https://docs.aws.amazon.com/pdfs/aws-certification/latest/developer-associate-02/developer-associate-02.pdf (accessed 2026-11-10)
[^s2]: Amazon Web Services, "AWS Certified Developer - Associate". https://aws.amazon.com/certification/certified-developer-associate (accessed 2026-11-10)
[^s3]: Amazon Web Services, "AWS Certification Recertification". https://aws.amazon.com/certification/recertification/?nc1=h_ls (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Certification updates from AWS Training and Certification: September 2026". https://aws.amazon.com/blogs/training-and-certification/september-2026-new-offerings/ (accessed 2026-11-10)
