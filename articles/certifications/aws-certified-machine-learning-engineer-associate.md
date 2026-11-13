---
id: kt:aws-certified-machine-learning-engineer-associate
type: Certification
title: AWS Certified Machine Learning Engineer - Associate
aliases: [MLA-C01, MLA-C02, AWS ML Engineer Associate, AWS MLA]
domain: certifications
summary: >-
  AWS's associate-level certification for ML engineers, validating the ability to prepare data, build,
  deploy, orchestrate, monitor and secure ML solutions on AWS (centred on SageMaker AI); exam MLA-C01 is
  being replaced by MLA-C02, which adds generative and agentic AI and has been in beta since September 2026.
verified: 2026-11-13
status: current
broader: [kt:aws-certification-program]
related: [kt:aws-certified-ai-practitioner, kt:amazon-sagemaker-ai, kt:amazon-bedrock, kt:aws-glue, kt:ci-cd]
relations:
  - {predicate: contrastsWith, object: kt:aws-certified-developer-associate}
  - {predicate: contrastsWith, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "AWS Certified Machine Learning Engineer - Associate (MLA-C01) Exam Guide (PDF)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/machine-learning-engineer-associate-01/machine-learning-engineer-associate-01.pdf
    type: official-learning
    accessed: 2026-11-13
  - id: s2
    title: "AWS Certified Machine Learning Engineer - Associate"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-machine-learning-engineer-associate
    type: official-learning
    accessed: 2026-11-13
  - id: s3
    title: "Updates to AWS Certified Machine Learning Engineer - Associate (MLA-C02)"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/updates-to-aws-certified-machine-learning-engineer-associate-mla-c02/
    type: official-blog
    accessed: 2026-11-13
created: 2026-11-13
---

# AWS Certified Machine Learning Engineer - Associate

AWS Certified Machine Learning Engineer - Associate is an associate-level AWS certification for people who build and run machine learning systems.[^s2] Its exam guide says it validates the ability to build, operationalize, deploy and maintain ML solutions and pipelines on AWS: ingesting and preparing data, choosing and training models, picking deployment infrastructure, automating ML workflows with CI/CD, monitoring models and infrastructure, and securing ML systems.[^s1] The target candidate has at least a year of experience with Amazon SageMaker and other AWS ML engineering services, and at least a year in a related role such as backend developer, DevOps developer, data engineer or data scientist.[^s1]

## Exam versions and format

As of 2026-11 the certification is moving between exam versions. MLA-C01 has 65 questions in 130 minutes and costs 150 USD, delivered at a Pearson VUE test center or online proctored.[^s2] Of those 65 questions, 50 are scored and 15 are unscored items trialled for future use. Results are a scaled score of 100–1,000 with 720 to pass, under a compensatory model where only the overall score counts.[^s1] Question types are multiple choice, multiple response, ordering and matching. Unanswered questions count as wrong, and there is no penalty for guessing.[^s1]

AWS announced the updated version, MLA-C02, on July 14, 2026. Beta registration opened September 1, 2026, the last day to take MLA-C01 in English was September 28, 2026, and beta delivery began September 29, 2026.[^s3] The beta is English-only, with 85 questions in 170 minutes for 75 USD. AWS expects the standard MLA-C02 in early 2027 in all exam languages, and Japanese, Korean and Simplified Chinese MLA-C01 stay available until then.[^s3][^s2] The certification page lists the beta code as "ME1-C02" in its table but calls it MLA-C02 in the text, so check the code when you register.[^s2] The credential is valid for three years and stays active for its full term whichever version you passed. You recertify by passing the latest version of the exam.[^s2]

## What the exam covers

MLA-C01 has four domains: Data Preparation for Machine Learning (28%), ML Model Development (26%), Deployment and Orchestration of ML Workflows (22%), and ML Solution Monitoring, Maintenance, and Security (24%).[^s1] The data domain covers formats such as Parquet, ORC, Avro and RecordIO, along with SageMaker Data Wrangler, Feature Store, Ground Truth, AWS Glue, Glue DataBrew and Glue Data Quality. It also covers pre-training bias metrics and handling PII and PHI.[^s1] Model development covers choosing built-in algorithms, foundation models or AWS AI services, plus hyperparameter tuning with SageMaker Automatic Model Tuning, fine-tuning in Bedrock or JumpStart, the Model Registry, and evaluation with metrics such as F1, RMSE and AUC, using SageMaker Clarify and Model Debugger.[^s1]

The deployment domain covers real-time, serverless, asynchronous and batch inference; container choices including bring-your-own-container; SageMaker Neo for the edge; and orchestration with SageMaker Pipelines or Amazon MWAA. It also includes CloudFormation or CDK as infrastructure as code, and CodePipeline, CodeBuild and CodeDeploy with blue/green, canary and linear rollouts.[^s1] The monitoring domain covers drift detection with SageMaker Model Monitor, observability with CloudWatch and X-Ray, rightsizing with Inference Recommender and Compute Optimizer, cost tools and purchase options such as SageMaker Savings Plans, and least-privilege IAM and VPC isolation for ML resources.[^s1]

MLA-C02 keeps the same domain structure with no new domains, but rewrites task statements. It adds generative AI (Amazon Bedrock and retrieval-augmented generation), agentic AI workflows, selecting, fine-tuning and operating foundation models, and updated responsible-AI guidance.[^s3][^s2] The certification page's recommended experience now names Amazon SageMaker AI and Amazon Bedrock for the new version.[^s2]

## Gotchas and scope

The guide puts designing full end-to-end ML architectures, setting ML strategy, deep expertise in two or more ML fields (such as NLP and computer vision), and model quantization out of scope. This is an engineer-and-operator exam, not an architect or research one.[^s1] Its in-scope services reach well beyond ML, into analytics (Athena, EMR, Kinesis, Redshift), developer tools, containers, cost management and security. Services such as GuardDuty, Security Hub, WAF and Route 53 are listed as out of scope.[^s1] Because the exam is mid-transition, English-language prep material written for MLA-C01 misses the generative and agentic AI content that MLA-C02 tests.[^s3]

## When to take it (and when not)

It fits practitioners who already train and deploy models on AWS and want to validate the operational side: data pipelines, endpoints, MLOps automation and monitoring.[^s1] People who use AI rather than build it are the audience for the foundational AI Practitioner exam. Engineers whose work is general application development fit Developer - Associate, which shares the CI/CD and IaC topics but has no ML modelling.[^s1][^s2]

## Connections

The certification sits at the associate level of the AWS Certification program.[^s2] It contrasts with AI Practitioner (conceptual, foundational) and Developer - Associate (application rather than ML engineering). Its content relies heavily on Amazon SageMaker AI and, increasingly, Amazon Bedrock. It draws on AWS Glue for data preparation and on CI/CD services for automated ML pipelines.[^s1][^s3]

## Sources
[^s1]: Amazon Web Services, "AWS Certified Machine Learning Engineer - Associate (MLA-C01) Exam Guide (PDF)". https://docs.aws.amazon.com/pdfs/aws-certification/latest/machine-learning-engineer-associate-01/machine-learning-engineer-associate-01.pdf (accessed 2026-11-13)
[^s2]: Amazon Web Services, "AWS Certified Machine Learning Engineer - Associate". https://aws.amazon.com/certification/certified-machine-learning-engineer-associate (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Updates to AWS Certified Machine Learning Engineer - Associate (MLA-C02)", AWS Training and Certification Blog, 2026-07-14. https://aws.amazon.com/blogs/training-and-certification/updates-to-aws-certified-machine-learning-engineer-associate-mla-c02/ (accessed 2026-11-13)
