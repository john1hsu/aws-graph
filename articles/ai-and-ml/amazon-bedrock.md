---
id: kt:amazon-bedrock
type: Service
title: Amazon Bedrock
aliases: [Bedrock, AWS Bedrock]
domain: ai-and-ml
summary: >-
  AWS's fully managed, serverless service for building generative AI applications and agents on
  foundation models from Amazon and third-party providers through a single API, with built-in
  retrieval, guardrails, customization and tiered per-token pricing.
verified: 2026-11-08
status: current
broader: [kt:generative-ai-on-aws]
related: [kt:amazon-bedrock-knowledge-bases, kt:amazon-bedrock-guardrails]
relations:
  - {predicate: alternativeTo, object: kt:amazon-sagemaker-ai}
  - {predicate: governedBy, object: kt:service-control-policies}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: coveredBy, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "Amazon Bedrock FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/bedrock/faqs/
    type: official-docs
    accessed: 2026-11-08
  - id: s2
    title: "Amazon Bedrock or Amazon SageMaker AI? - AWS Decision Guides"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/decision-guides/bedrock-or-sagemaker.html
    type: official-docs
    accessed: 2026-11-08
  - id: s3
    title: "Amazon Bedrock Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/bedrock/pricing/
    type: official-docs
    accessed: 2026-11-08
  - id: s4
    title: "Route model inference requests across AWS Regions with cross-Region inference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/cross-region-inference.html
    type: official-docs
    accessed: 2026-11-08
  - id: s5
    title: "Amazon Bedrock - Generative AI inference architecture and best practices on AWS (AWS Prescriptive Guidance)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/gen-ai-inference-architecture-and-best-practices-on-aws/amazon-bedrock.html
    type: official-docs
    accessed: 2026-11-08
  - id: s6
    title: "In-Scope AWS Services - AWS Certified AI Practitioner (AIF-C01)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/aif-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-08
created: 2026-11-08
---

# Amazon Bedrock

Amazon Bedrock is AWS's fully managed, serverless service for building generative AI applications. It exposes foundation models (FMs) from Amazon (the Nova and Titan families) and from third-party providers such as Anthropic, Meta, Mistral AI, Cohere, AI21 Labs and OpenAI behind one inference API, so an application can switch models with small code changes and without provisioning any infrastructure.[^s1] Around the models it adds managed building blocks: Knowledge Bases for retrieval-augmented generation (RAG), Guardrails for safety controls, model customization, and agent tooling.[^s1]

## How it works

Applications call Bedrock through the AWS SDKs, REST APIs or OpenAI-compatible endpoints, authenticated with IAM like any other AWS API.[^s2][^s5] Requests can reach the service privately from a VPC over AWS PrivateLink, are recorded in AWS CloudTrail, and can be restricted with service control policies.[^s1][^s5] AWS states that customer prompts and outputs are not used to train the base models and are not shared with model providers, that content is encrypted in transit and at rest (optionally with your own keys), and that processed content stays in the Region where it is used unless you opt into cross-Region inference.[^s1]

Beyond the serverless catalog, Bedrock Marketplace offers over 100 additional models that are deployed to managed SageMaker endpoints rather than run serverlessly.[^s1] Customization options include fine-tuning on labeled data, continued pre-training (for Amazon Titan models) on unlabeled domain data, and Custom Model Import for supported open-weight model families; customized models are invoked through the same API.[^s1]

Higher-level features build on the inference layer. Knowledge Bases provide managed RAG over sources such as Amazon S3 with source citations; Guardrails apply content filters, denied topics, sensitive-information filters, contextual grounding checks and Automated Reasoning checks, and can also be applied to models hosted outside Bedrock via the standalone ApplyGuardrail API.[^s1][^s5] Bedrock Agents remain available, but AWS now steers new agent projects to Amazon Bedrock AgentCore, which is framework- and model-agnostic.[^s1]

### Cross-Region inference

Instead of a model ID, you can invoke an *inference profile* that lets Bedrock route each request to another commercial Region. Geographic profiles (for example US, EU or APAC) keep processing inside that geography; Global profiles can route to any supported commercial Region and are about 10% cheaper.[^s4] There is no routing surcharge, billing follows the source Region, and traffic stays on the AWS network, encrypted in transit.[^s4] CloudTrail logs the request in the source Region, with the Region that actually processed it in `additionalEventData.inferenceRegion`.[^s4]

## Quotas, pricing and gotchas

Pricing is per input and output token for on-demand use, with features priced separately.[^s2] As of 2026-11, on-demand use has service tiers selected per request: **Standard** (default), **Flex** at a 50% discount to Standard, **Priority** at a 75% premium, and **Reserved** capacity arranged through an AWS account team.[^s3][^s5] Batch inference is offered for select models at 50% below on-demand pricing, and Provisioned Throughput is billed hourly per model unit with no-commitment, 1-month or 6-month terms.[^s3] As a dated example, the pricing page listed Mistral Large 3 Standard on-demand at $0.50 per million input tokens and $1.50 per million output tokens in US East (N. Virginia), US East (Ohio) and US West (Oregon) (as of 2026-11); prices vary by model, Region and profile type.[^s3]

Gotchas worth knowing:

- Priority, Standard and Flex share a single per-model on-demand quota; omitting the tier resolves to Standard; and Reserved traffic that exceeds its reservation overflows to Standard rather than being throttled, so watch the `ResolvedServiceTier` CloudWatch dimension.[^s5] Reserved capacity has minimums of 100,000 input and 10,000 output tokens per minute.[^s5]
- Cross-Region inference does not support Provisioned Throughput.[^s4]
- A Region-restricting SCP must allow every destination Region of a geographic profile, and must allow `aws:RequestedRegion` = `unspecified` for Global profiles, or calls fail.[^s4]
- Global profiles do not meet data-residency requirements; use a geographic profile when residency matters.[^s4]
- Tier support, prompt caching and other features vary by model, so validate before moving production traffic.[^s5]

## When to use it (and when not)

AWS's decision guide positions Bedrock for teams that want to build AI applications and agents on pre-trained models with prompt engineering and RAG, minimal ML expertise and no infrastructure, and for access to proprietary models such as Claude that are not in SageMaker JumpStart.[^s2] Amazon SageMaker AI fits when you need full control over training, frameworks, compute and inference, reinforcement-learning-based customization, large-scale HyperPod clusters, or classical and predictive ML alongside generative AI.[^s2] The two are complementary: a common path starts with Bedrock prompting and RAG, moves to SageMaker AI customization when prompting is not enough, then serves the result on SageMaker endpoints or imports it into Bedrock.[^s2]

## Connections

Bedrock sits under the broader **generative AI on AWS** landscape. **Amazon Bedrock Knowledge Bases** and **Amazon Bedrock Guardrails** are its main built-in capabilities for grounding and safety.[^s1] **Amazon SageMaker AI** is its principal alternative for deeper model control.[^s2] Access is constrained by **service control policies**, especially with cross-Region inference, and calls are audited by **AWS CloudTrail**.[^s4][^s5] Bedrock is listed among the in-scope machine-learning services for the **AWS Certified AI Practitioner** (AIF-C01) exam.[^s6]

## Sources
[^s1]: Amazon Web Services, "Amazon Bedrock FAQs". https://aws.amazon.com/bedrock/faqs/ (accessed 2026-11-08)
[^s2]: Amazon Web Services, "Amazon Bedrock or Amazon SageMaker AI? - AWS Decision Guides". https://docs.aws.amazon.com/decision-guides/latest/decision-guides/bedrock-or-sagemaker.html (accessed 2026-11-08)
[^s3]: Amazon Web Services, "Amazon Bedrock Pricing". https://aws.amazon.com/bedrock/pricing/ (accessed 2026-11-08)
[^s4]: Amazon Web Services, "Route model inference requests across AWS Regions with cross-Region inference". https://docs.aws.amazon.com/bedrock/latest/userguide/cross-region-inference.html (accessed 2026-11-08)
[^s5]: Amazon Web Services, "Amazon Bedrock - Generative AI inference architecture and best practices on AWS". https://docs.aws.amazon.com/prescriptive-guidance/latest/gen-ai-inference-architecture-and-best-practices-on-aws/amazon-bedrock.html (accessed 2026-11-08)
[^s6]: Amazon Web Services, "In-Scope AWS Services - AWS Certified AI Practitioner". https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/aif-01-in-scope-services.html (accessed 2026-11-08)
