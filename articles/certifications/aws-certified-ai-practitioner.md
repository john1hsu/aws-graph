---
id: kt:aws-certified-ai-practitioner
type: Certification
title: AWS Certified AI Practitioner
aliases: [AIF-C01, AWS AI Practitioner]
domain: certifications
summary: >-
  AWS's foundational-level certification (exam AIF-C01) validating a conceptual understanding of
  AI, machine learning and generative AI, and of which AWS AI services fit which business use cases,
  for people who use rather than build AI/ML solutions.
verified: 2026-11-08
status: current
broader: [kt:aws-certification-program]
related: [kt:amazon-bedrock, kt:amazon-sagemaker-ai, kt:aws-certified-machine-learning-engineer-associate, kt:generative-ai-on-aws]
relations:
  - {predicate: contrastsWith, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "AWS Certified AI Practitioner (AIF-C01) - Exam Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01.html
    type: official-learning
    accessed: 2026-11-08
  - id: s2
    title: "AWS Certified AI Practitioner"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-ai-practitioner/
    type: official-learning
    accessed: 2026-11-08
  - id: s3
    title: "In-Scope AWS Services - AWS Certified AI Practitioner"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/aif-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-08
  - id: s4
    title: "Content Domain 2: Fundamentals of GenAI - AWS Certified AI Practitioner"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain2.html
    type: official-learning
    accessed: 2026-11-08
  - id: s5
    title: "Content Domain 3: Applications of Foundation Models - AWS Certified AI Practitioner"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain3.html
    type: official-learning
    accessed: 2026-11-08
  - id: s6
    title: "Revisions - AWS Certified AI Practitioner"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/aif-01-revisions.html
    type: official-learning
    accessed: 2026-11-08
created: 2026-11-08
---

# AWS Certified AI Practitioner

AWS Certified AI Practitioner is a foundational-level AWS certification, taken as exam AIF-C01.[^s2] It validates that a candidate can describe AI, ML and generative AI (GenAI) concepts, pick suitable AI technologies for business problems, and use them responsibly, rather than that they can build models.[^s1] The intended candidate has up to about six months of exposure to AI/ML on AWS and uses, but does not necessarily build, AI/ML solutions; AWS also expects familiarity with core services such as Amazon EC2, Amazon S3, AWS Lambda, Amazon Bedrock and Amazon SageMaker AI, the shared responsibility model, IAM and AWS pricing models.[^s1]

## Exam format and scoring

As of 2026-11, the exam has 65 questions in 90 minutes and costs 100 USD (plus possible extra costs such as currency exchange), delivered at a Pearson VUE test center or as an online proctored exam.[^s2] Of the 65 questions, 50 are scored and 15 are unscored items being trialled for future use, which are not marked on the exam.[^s1] Question types are multiple choice, multiple response, ordering and matching; partial answers earn no credit, and blank answers count as wrong with no extra penalty for guessing.[^s1] Results are pass/fail on a scaled score of 100–1,000 with 700 to pass, using a compensatory model, so you need not pass every section.[^s1]

The credential is valid for three years. You recertify by passing the current version of the exam, or automatically by earning AWS Certified Machine Learning Engineer – Associate.[^s2] The exam is offered in twelve languages, but AWS states that the German and Italian versions are retired after October 15, 2026.[^s2]

## What the exam covers

The scored content is split across five domains: Fundamentals of AI and ML (20%), Fundamentals of GenAI (24%), Applications of Foundation Models (28%), Guidelines for Responsible AI (14%), and Security, Compliance, and Governance for AI Solutions (14%).[^s1]

The GenAI domain covers tokens, embeddings, chunking, prompt engineering, the foundation-model lifecycle and token-based pricing, and includes agentic AI basics such as multi-agent patterns, the Model Context Protocol, agent memory and tool use.[^s4] It asks candidates to weigh GenAI strengths against hallucination, nondeterminism and limited interpretability, and to recognise AWS building blocks including Amazon Bedrock, SageMaker AI, SageMaker JumpStart, Amazon Quick, Kiro, Strands Agents and Amazon Bedrock AgentCore.[^s4]

The heaviest domain, Applications of Foundation Models, tests model selection (cost, modality, latency, context length, prompt caching), inference parameters such as temperature, and the cost trade-offs between in-context learning, RAG, fine-tuning, distillation and pre-training.[^s5] It names Bedrock Knowledge Bases with vector stores such as OpenSearch Service, Aurora, Neptune and RDS for PostgreSQL, Bedrock Prompt Management, and Bedrock Model Evaluation, along with metrics such as ROUGE, BLEU, BERTScore and LLM-as-a-judge.[^s5]

The in-scope service list goes beyond ML services (Comprehend, Lex, Polly, Rekognition, Textract, Transcribe, Translate, Personalize, Amazon Nova) to governance and security services such as CloudTrail, CloudWatch, Config, Trusted Advisor, AWS Artifact, IAM, KMS, Macie and Secrets Manager.[^s3] AWS calls the list non-exhaustive and subject to change.[^s3]

## Gotchas and scope

The guide explicitly excludes coding models or algorithms, data and feature engineering, hyperparameter tuning, building ML pipelines, mathematical analysis of models, and implementing security or governance frameworks; the exam tests recognition and judgment, not implementation.[^s1] The exam guide changes during the life of the exam code: its revision history lists versions dated March 26, April 30 and October 29, 2026, and AWS says updates are published about a month before they take effect, so study from the current guide rather than older prep material.[^s6]

## When to take it (and when not)

It suits people in roles that use, but do not build, AI/ML solutions and who need a shared vocabulary for AI and GenAI on AWS; it is not a test of hands-on model development.[^s1] Practitioners who train, deploy and operate models are better served by Machine Learning Engineer – Associate, which also renews this credential.[^s2]

## Connections

The exam sits in the AWS Certification program at the foundational level, alongside but distinct from Cloud Practitioner: Cloud Practitioner covers general AWS cloud concepts, while this exam assumes them and focuses on AI.[^s1][^s2] Its content leans on Amazon Bedrock and Amazon SageMaker AI as the main AWS platforms for generative and traditional ML, and on the broader generative-AI-on-AWS stack it tests.[^s3][^s4] Machine Learning Engineer – Associate is the natural next step and the automatic recertification path.[^s2]

## Sources
[^s1]: Amazon Web Services, "AWS Certified AI Practitioner (AIF-C01) - Exam Guide". https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01.html (accessed 2026-11-08)
[^s2]: Amazon Web Services, "AWS Certified AI Practitioner". https://aws.amazon.com/certification/certified-ai-practitioner/ (accessed 2026-11-08)
[^s3]: Amazon Web Services, "In-Scope AWS Services - AWS Certified AI Practitioner". https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/aif-01-in-scope-services.html (accessed 2026-11-08)
[^s4]: Amazon Web Services, "Content Domain 2: Fundamentals of GenAI - AWS Certified AI Practitioner". https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain2.html (accessed 2026-11-08)
[^s5]: Amazon Web Services, "Content Domain 3: Applications of Foundation Models - AWS Certified AI Practitioner". https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain3.html (accessed 2026-11-08)
[^s6]: Amazon Web Services, "Revisions - AWS Certified AI Practitioner". https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/aif-01-revisions.html (accessed 2026-11-08)
