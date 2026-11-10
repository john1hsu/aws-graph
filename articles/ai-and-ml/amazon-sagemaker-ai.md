---
id: kt:amazon-sagemaker-ai
type: Service
title: Amazon SageMaker AI
aliases: [SageMaker AI, SageMaker]
domain: ai-and-ml
summary: >-
  AWS's fully managed machine learning service for building, training and deploying ML and
  foundation models, with managed notebooks, training jobs, HyperPod clusters and several hosted
  inference options; called Amazon SageMaker until December 2024, when that name passed to the
  broader data-and-AI platform.
verified: 2026-11-10
status: current
broader: [kt:generative-ai-on-aws]
related: [kt:amazon-bedrock, kt:aws-certified-machine-learning-engineer-associate]
relations:
  - {predicate: alternativeTo, object: kt:amazon-bedrock}
  - {predicate: pricedBy, object: kt:savings-plans}
  - {predicate: governedBy, object: kt:sagemaker-ai-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "What is Amazon SageMaker AI?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/us_en/sagemaker/latest/dg/whatis.html
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Introducing the next generation of Amazon SageMaker: The center for all your data, analytics, and AI"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/aws/introducing-the-next-generation-of-amazon-sagemaker-the-center-for-all-your-data-analytics-and-ai
    type: official-blog
    accessed: 2026-11-10
  - id: s3
    title: "Inference options in Amazon SageMaker AI"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/sagemaker/latest/dg/deploy-model-options.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Amazon SageMaker AI endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/sagemaker
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Amazon SageMaker AI pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/sagemaker-ai/pricing/
    type: official-docs
    accessed: 2026-11-10
  - id: s6
    title: "Amazon Bedrock or Amazon SageMaker AI? - AWS Decision Guides"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/bedrock-or-sagemaker/bedrock-or-sagemaker.html
    type: official-docs
    accessed: 2026-11-10
  - id: s7
    title: "In-Scope AWS Services - AWS Certified AI Practitioner (AIF-C01)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/id_id/aws-certification/latest/userguide/aif-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# Amazon SageMaker AI

Amazon SageMaker AI is AWS's fully managed machine learning (ML) service. Data scientists and developers use it to prepare data, build and train models, and deploy them to a production-ready hosted environment, either through its own UI or from the IDE of their choice. It offers managed built-in algorithms that scale to very large datasets, accepts your own algorithms and frameworks with distributed training, and deploys models to secure, scalable endpoints.[^s1]

## Naming: SageMaker vs. SageMaker AI

On December 3, 2024, the ML service that had been called Amazon SageMaker was renamed **Amazon SageMaker AI**, and the name "Amazon SageMaker" was reused for a new, broader platform for data, analytics and AI.[^s1][^s2] That next-generation SageMaker bundles SageMaker AI with components such as SageMaker Lakehouse, data and AI governance, SQL analytics, data processing, Unified Studio and Amazon Bedrock; SageMaker AI is still sold on its own for teams that only need to build, train and deploy models.[^s1][^s2] The rename was cosmetic at the API level: the `sagemaker` API namespaces and CLI commands, `AmazonSageMaker*` managed policies, `AWS::SageMaker` CloudFormation resources, the `AWSServiceRoleForSageMaker` service-linked role and `sagemaker` endpoints and URLs are all unchanged.[^s1] When older material (including exam guides) says "Amazon SageMaker" for model training or hosting, it means this service.[^s7]

## How it works

The workflow is built around managed jobs and endpoints that run on ML instance types (`ml.*`) you choose. **Training jobs** run on managed instances with full control over frameworks and compute, and write a model artifact; **HyperPod** provides resilient clusters for large-scale training and inference with Amazon EKS or Slurm orchestration.[^s6] **JumpStart** is a model hub of nearly 1,000 open, proprietary and custom models with templates and example notebooks, and serverless model customization supports techniques such as SFT and DPO.[^s6] Deployed models can sit behind endpoints that support scale-to-zero, multiple models per endpoint (inference components) and A/B, canary, blue/green and shadow deployments, with Model Monitor for post-deployment oversight.[^s6]

SageMaker AI offers four inference options:[^s3]

- **Real-time inference**: a persistent, fully managed endpoint on instances you choose, for low-latency or high-throughput traffic; payloads up to 25 MB and processing up to 60 seconds (8 minutes for streaming responses).[^s3]
- **Serverless inference**: no instances or scaling policies to manage and no charge for idle time, for intermittent traffic; payloads up to 4 MB and up to 60 seconds of processing.[^s3]
- **Asynchronous inference**: requests are queued, payloads up to 1 GB and processing up to one hour, and the endpoint can scale to zero when idle.[^s3]
- **Batch transform**: offline inference over datasets available up front, with no persistent endpoint, for jobs that can run for days.[^s3]

## Quotas, pricing and gotchas

Quotas are per account per Region and visible in the Service Quotas console. As of 2026-11 the General Reference lists defaults of 5 serverless endpoints and a total concurrency of 10 across all serverless endpoints (both adjustable); a hyperparameter tuning job can run at most 750 training jobs, 10 in parallel (not adjustable); and `InvokeEndpoint` is limited to 10,000 requests per second (not adjustable).[^s4] Instance-count quotas for training and hosting are low by default for new accounts but adjustable, so request increases before a launch rather than discovering them at deploy time.[^s4] FIPS endpoints exist for the control plane in US and GovCloud (US) Regions and for the runtime in four US Regions.[^s4]

Pricing (as of 2026-11, per the SageMaker AI pricing page) is pay-as-you-go with no minimums: most compute is billed per instance type for the time it runs, serverless inference per millisecond plus a per-GB data charge, and storage per GB-month. SageMaker Savings Plans cut eligible instance usage (notebooks, processing, training, real-time inference, batch transform) by up to 64% regardless of family, size or Region.[^s5] The Free Tier covers the first two months after your first SageMaker AI resource, including 250 hours per month of ml.t3.medium notebooks, 50 hours of m4.xlarge or m5.xlarge training and 125 hours of m4.xlarge or m5.xlarge real-time inference.[^s5] The classic cost trap is a real-time endpoint left running after an experiment: it bills for every hour it is up, which is why serverless and asynchronous options, which can scale down when idle, suit spiky or occasional workloads.[^s3][^s5]

## When to use it (and when not)

AWS's decision guide frames the choice against **Amazon Bedrock**. Bedrock is fully serverless and API-driven, suits developers with limited ML resources, and fits prompting, RAG and chatbots on pre-trained foundation models. SageMaker AI is for predictive and classical ML, custom training at scale and large-scale generative AI inference that needs fine-grained control over containers, orchestration, cost, latency and throughput.[^s6] The guide suggests a progression: start in Bedrock, move to SageMaker AI serverless customization to change model behavior, then to training jobs or HyperPod for full infrastructure control.[^s6]

## Connections

SageMaker AI sits within **generative AI on AWS** and is the main **alternative to Amazon Bedrock** when you need to train or host models yourself rather than call managed foundation models.[^s6] Its instance usage can be discounted with **Savings Plans** (the SageMaker plan type), and it is **governed by** per-Region **SageMaker AI quotas** within the **AWS Regions** where you deploy.[^s4][^s5] The AWS Certified AI Practitioner (AIF-C01) in-scope service list includes Amazon SageMaker under Machine Learning, so it is **covered by** that certification; it is also central to the **AWS Certified Machine Learning Engineer – Associate** track.[^s7]

## Sources
[^s1]: Amazon Web Services, "What is Amazon SageMaker AI?". https://docs.aws.amazon.com/us_en/sagemaker/latest/dg/whatis.html (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Introducing the next generation of Amazon SageMaker: The center for all your data, analytics, and AI" (AWS News Blog, 2024-12-03). https://aws.amazon.com/blogs/aws/introducing-the-next-generation-of-amazon-sagemaker-the-center-for-all-your-data-analytics-and-ai (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Inference options in Amazon SageMaker AI". https://docs.aws.amazon.com/sagemaker/latest/dg/deploy-model-options.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Amazon SageMaker AI endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/sagemaker (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Amazon SageMaker AI pricing". https://aws.amazon.com/sagemaker-ai/pricing/ (accessed 2026-11-10)
[^s6]: Amazon Web Services, "Amazon Bedrock or Amazon SageMaker AI? - AWS Decision Guides". https://docs.aws.amazon.com/decision-guides/latest/bedrock-or-sagemaker/bedrock-or-sagemaker.html (accessed 2026-11-10)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified AI Practitioner (AIF-C01)". https://docs.aws.amazon.com/id_id/aws-certification/latest/userguide/aif-01-in-scope-services.html (accessed 2026-11-10)
