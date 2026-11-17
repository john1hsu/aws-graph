---
id: kt:generative-ai-on-aws
type: Concept
title: Generative AI on AWS
aliases: [GenAI on AWS, AWS generative AI stack, AWS generative AI services]
domain: ai-and-ml
summary: >-
  The layered set of AWS offerings for generative AI: ready-to-use assistants at the top, Amazon
  Bedrock and Bedrock AgentCore for building applications and agents in the middle, and SageMaker AI,
  HyperPod and accelerated EC2 (Trainium, Inferentia, GPUs) for hosting, customizing and training
  models at the bottom.
verified: 2026-11-17
status: concept
broader: [kt:amazon-web-services]
related: [kt:amazon-quick, kt:aws-well-architected-framework]
relations:
  - {predicate: hasPart, object: kt:amazon-bedrock}
  - {predicate: hasPart, object: kt:amazon-bedrock-agentcore}
  - {predicate: hasPart, object: kt:amazon-sagemaker-ai}
  - {predicate: runsOn, object: kt:aws-trainium-and-inferentia}
  - {predicate: coveredBy, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "Choosing a generative AI service - AWS Decision Guides"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/generative-ai-on-aws-how-to-choose/guide.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Generative AI Lens - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/generative-ai-lens.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Layer 3: Security and governance for generative AI platforms on AWS - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-enterprise-ready-gen-ai-platform/security.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "Securing generative AI: An introduction to the Generative AI Security Scoping Matrix"
    publisher: AWS Security Blog
    url: https://aws.amazon.com/blogs/security/securing-generative-ai-an-introduction-to-the-generative-ai-security-scoping-matrix
    type: official-blog
    accessed: 2026-11-17
  - id: s5
    title: "AWS Certified AI Practitioner (AIF-C01) - Content Domain 2: Fundamentals of GenAI"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain2.md
    type: official-learning
    accessed: 2026-11-17
created: 2026-11-17
---

# Generative AI on AWS

"Generative AI on AWS" is not a single service but the umbrella for how AWS packages foundation-model (FM) capabilities. AWS's own decision guide organizes them in three layers, and notes that many organizations use more than one: ready-to-use AI assistants; services for building generative AI applications and agents (Amazon Bedrock and Amazon Bedrock AgentCore); and options to host, customize and train models yourself (Amazon SageMaker AI, SageMaker HyperPod and self-managed infrastructure), which run from least to most operational ownership.[^s1] Underneath sit a models tier (Amazon Nova, third-party models in Bedrock, open-weight models in SageMaker JumpStart) and an infrastructure tier of accelerated compute.[^s1]

## How the stack fits together

**Assistants.** Amazon Quick Suite is positioned as the AI workspace for enterprise data and as the next evolution of Amazon Q Business; Kiro is the agentic development environment. Other services, such as Amazon Connect and SageMaker Unified Studio, embed generative features directly.[^s1]

**Build applications and agents.** Amazon Bedrock is the fully managed, serverless entry point to FMs from Amazon and third parties behind one API, with managed RAG (Knowledge Bases), Guardrails, fine-tuning and distillation, Custom Model Import, cross-Region inference and model evaluation. Amazon Bedrock AgentCore, generally available since 2025-10-13, adds framework- and model-agnostic runtime, memory, gateway, identity and observability components for running agents, with support for MCP and Agent2Agent.[^s1]

**Host, customize and train.** SageMaker AI provides managed training and inference endpoints; its Serverless Inference is CPU-only, so large FMs usually need GPU real-time endpoints. HyperPod adds resilient clusters that replace unhealthy nodes for large-scale training and inference. Self-managed deployments on EC2, EKS or ECS give full control and full operational responsibility.[^s1]

**Infrastructure.** AWS Trainium (latest: EC2 Trn3 UltraServers scaling to 144 Trainium3 chips) and AWS Inferentia (Inf2 instances with up to 12 Inferentia2 chips) are programmed through the AWS Neuron SDK; NVIDIA-based P6, P5 and G6e instances cover GPU workloads (as stated in the guide as of 2026-11).[^s1]

## Choosing a layer

AWS's suggested inference path is to start with Bedrock and move down the stack only when the model isn't offered there, you need deeper control of the serving stack, the model isn't available in a Region you must use, or you have steady high volume or existing compute commitments.[^s1] Cost shape differs by layer: Bedrock on-demand pricing has no idle cost and suits spiky traffic, while instance-based hosting charges for provisioned capacity and pays off at sustained utilization. Global cross-Region inference is described as roughly 10% cheaper than geographic cross-Region inference but without data-residency limits, and Provisioned Throughput cannot be used through cross-Region inference profiles.[^s1] A common hybrid is Bedrock for frontier models plus SageMaker AI or HyperPod for fine-tuned or open-weight models, behind one gateway with consistent guardrails.[^s1]

## Gotchas and lifecycle (as of 2026-11)

The portfolio changes quickly, and several earlier entry points are winding down: Amazon Q Business closed to new customers on 2026-07-31, Q Developer IDE plugin sign-ups ended on 2026-05-15 with end of support on 2027-04-30 (Kiro is the recommended replacement), and Bedrock Agents (now "Classic") closed to new customers on 2026-07-30 in favour of AgentCore.[^s1] Bedrock models themselves move through Active, Legacy and End-of-Life states, so model choice needs a lifecycle plan.[^s1]

## Security, governance and architecture guidance

The Generative AI Security Scoping Matrix classifies workloads into five scopes, from consumer apps and enterprise SaaS (scopes 1–2) through applications on pre-trained models, fine-tuned models and self-trained models (scopes 3–5), and maps each to governance, legal and privacy, risk management, controls and resilience.[^s4] AWS Prescriptive Guidance recommends separate IAM roles for model evaluation, general use, fine-tuned and specialized models; PrivateLink for private invocation of Bedrock and SageMaker AI; Guardrails to filter inputs and outputs; invocation logging to S3 or CloudWatch Logs; and CloudTrail for API auditing.[^s3] The Well-Architected Generative AI Lens (published 2025-11-19) scopes reviews to Bedrock, customer-managed models on SageMaker AI and Amazon Q applications, follows a six-stage lifecycle (scoping, model selection, customization, development, deployment, continuous improvement) and applies all six pillars.[^s2]

## Connections

Generative AI on AWS sits under **Amazon Web Services** and **has parts** **Amazon Bedrock**, **Amazon Bedrock AgentCore** and **Amazon SageMaker AI**, the build and host layers of the stack.[^s1] It **runs on** **AWS Trainium and Inferentia** (alongside GPU instances) at the infrastructure layer.[^s1] **AWS Quick** is the assistant layer, and the **AWS Well-Architected Framework** supplies the Generative AI Lens for reviews.[^s1][^s2] The topic is **covered by** **AWS Certified AI Practitioner**: Task Statement 2.3 of AIF-C01 asks candidates to describe AWS infrastructure and technologies for building GenAI applications, naming Bedrock, SageMaker AI, JumpStart, Amazon Quick, Kiro, Strands Agents and AgentCore, along with cost trade-offs such as token-based pricing and provisioned throughput.[^s5]

## Sources

[^s1]: Amazon Web Services, "Choosing a generative AI service" (AWS Decision Guides, last updated 2026-11-14). https://docs.aws.amazon.com/decision-guides/latest/generative-ai-on-aws-how-to-choose/guide.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Generative AI Lens - AWS Well-Architected Framework" (2025-11-19). https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/generative-ai-lens.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Layer 3: Security and governance for generative AI platforms on AWS" (AWS Prescriptive Guidance). https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-enterprise-ready-gen-ai-platform/security.html (accessed 2026-11-17)
[^s4]: M. Saner and M. Lapidakis, "Securing generative AI: An introduction to the Generative AI Security Scoping Matrix", AWS Security Blog (2023-10-19). https://aws.amazon.com/blogs/security/securing-generative-ai-an-introduction-to-the-generative-ai-security-scoping-matrix (accessed 2026-11-17)
[^s5]: Amazon Web Services, "Content Domain 2: Fundamentals of GenAI" (AWS Certified AI Practitioner AIF-C01 exam guide). https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain2.md (accessed 2026-11-17)
