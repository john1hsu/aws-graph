---
id: kt:aws-certified-generative-ai-developer-professional
type: Certification
title: AWS Certified Generative AI Developer - Professional
aliases: [AIP-C01, AWS GenAI Developer Professional, AWS Generative AI Developer Pro]
domain: certifications
summary: >-
  AWS's professional-level certification for developers who put foundation models into production,
  validating RAG and vector-store design, agentic AI, prompt management, safety and governance,
  and cost and performance tuning of generative AI applications on AWS (exam AIP-C01).
verified: 2026-11-17
status: current
broader: [kt:aws-certification-program]
related: [kt:amazon-bedrock, kt:amazon-bedrock-knowledge-bases, kt:amazon-bedrock-guardrails, kt:amazon-bedrock-agentcore, kt:generative-ai-on-aws, kt:aws-skill-builder]
relations:
  - {predicate: contrastsWith, object: kt:aws-certified-machine-learning-engineer-associate}
  - {predicate: contrastsWith, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "AWS Certified Generative AI Developer - Professional (AIP-C01) exam guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-professional-01/ai-professional-01.html
    type: official-learning
    accessed: 2026-11-17
  - id: s2
    title: "AWS Certified Generative AI Developer - Professional"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-generative-ai-developer-professional
    type: official-learning
    accessed: 2026-11-17
  - id: s3
    title: "AIP-C01 Content Domain 1: Foundation Model Integration, Data Management, and Compliance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-professional-01/ai-professional-01-domain1.html
    type: official-learning
    accessed: 2026-11-17
  - id: s4
    title: "AIP-C01 Content Domain 2: Implementation and Integration"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-professional-01/ai-professional-01-domain2.html
    type: official-learning
    accessed: 2026-11-17
  - id: s5
    title: "AIP-C01 In-Scope AWS Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-professional-01/aip-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-17
  - id: s6
    title: "AWS expands AI certification portfolio and updates security certification"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/big-news-aws-expands-ai-certification-portfolio-and-updates-security-certification
    type: official-blog
    accessed: 2026-11-17
  - id: s7
    title: "AWS Certification recertification"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/recertification/?nc1=h_ls
    type: official-learning
    accessed: 2026-11-17
created: 2026-11-17
---

# AWS Certified Generative AI Developer - Professional

AWS Certified Generative AI Developer - Professional is AWS's professional-tier credential for engineers who build generative AI (GenAI) applications. Its exam, AIP-C01, checks whether a candidate can wire foundation models (FMs) into applications and business workflows and run those solutions in production on AWS.[^s1] The guide lists the jobs it tests: designing with vector stores, retrieval-augmented generation (RAG) and knowledge bases; prompt engineering and management; agentic AI; optimizing for cost, performance and business value; security, governance and responsible AI; troubleshooting and monitoring; and evaluating FMs for quality.[^s1]

AWS announced it on October 14, 2025 as part of a wider AI certification expansion. The beta ran until March 31, 2026, and a March 2026 update to the announcement says the standard exam is open for registration, refreshed to include Amazon Bedrock AgentCore.[^s6]

## Exam format

As of 2026-11 the certification page lists a 180-minute exam of 75 multiple-choice or multiple-response questions, costing 300 USD (plus any foreign-exchange charges), offered in English, Japanese, Korean and Simplified Chinese at a Pearson VUE test center or online proctored.[^s2] The exam guide explains the 75: 65 scored questions plus 10 unidentified unscored ones being trialled.[^s1] Results are a scaled score from 100 to 1,000, and 750 passes. Scoring is compensatory, so only the overall score matters, and blank answers count as wrong with no penalty for guessing.[^s1] No earlier certification is required. The page suggests AI Practitioner, Solutions Architect - Associate, Machine Learning Engineer - Associate or Data Engineer - Associate as useful groundwork, and links an exam prep plan on AWS Skill Builder with practice questions, labs, AWS SimuLearn and an official practice exam.[^s2]

The target candidate has two or more years building production applications on AWS or open-source stacks, general AI/ML or data-engineering background, and a year of hands-on GenAI work.[^s1]

## What the exam covers

The five domains, by share of scored content, are: Foundation Model Integration, Data Management, and Compliance (31%); Implementation and Integration (26%); AI Safety, Security, and Governance (20%); Operational Efficiency and Optimization for GenAI Applications (12%); and Testing, Validation, and Troubleshooting (11%).[^s1]

Domain 1 is the architecture core. It covers selecting FMs and making them swappable behind Lambda, API Gateway and AppConfig, adding resilience with Step Functions and Bedrock cross-Region inference, building vector stores on Bedrock Knowledge Bases, OpenSearch Service, Aurora pgvector or DynamoDB, and tuning retrieval with chunking, embeddings, hybrid search, reranking and query decomposition. It also asks about exposing retrieval through function calling and Model Context Protocol (MCP) clients, and governing prompts with Bedrock Prompt Management, Prompt Flows and Guardrails.[^s3]

Domain 2 is about building. Its task statements cover agents with memory, ReAct-style reasoning, human review and tool access (Strands Agents, AWS Agent Squad, MCP servers); deployment choices between on-demand calls, Bedrock provisioned throughput and SageMaker AI endpoints; event-driven enterprise integration; streaming and asynchronous FM APIs with backoff, fallbacks and model routing; and developer tooling such as Amplify, Bedrock Data Automation and Amazon Q Developer.[^s4] Domain 3 covers guardrails against prompt injection and jailbreaks, PII detection and masking, data lineage and model cards, and responsible-AI checks.[^s1]

## Gotchas and scope

This is a developer exam, not a data-science one: model development and training, advanced ML techniques, and data and feature engineering are out of scope for the candidate.[^s1] The in-scope service list is still broad. Besides Bedrock, AgentCore, Knowledge Bases and SageMaker AI features such as Clarify and Model Monitor, it includes Amazon Kendra, Lex, Q Business, Comprehend and Textract, plus analytics, containers, databases, networking, Kiro and CloudWatch-based observability. AWS says the list is non-exhaustive and may change.[^s5] Because the standard version was refreshed after beta, prep material written for the beta may not cover AgentCore.[^s6]

Like other AWS credentials it is valid for three years. As of 2026-11 the only recertification path is to pass the latest version of the exam; the one-year Skill Builder maintenance option is not listed for it. Passing it also renews Cloud Practitioner, AI Practitioner, Data Engineer - Associate and Machine Learning Engineer - Associate.[^s7]

## When to take it

It suits application engineers who already ship RAG or agent features and want a credential for production GenAI design, safety and operations.[^s1] Practitioners who need conceptual AI fluency rather than build skills fit AI Practitioner; those who train, tune and deploy custom models fit Machine Learning Engineer - Associate.[^s2]

## Connections

It is a Professional-level member of the AWS Certification program, and contrasts with the foundational AI Practitioner and the associate ML Engineer credential, both of which it renews.[^s7] Its content is anchored in Amazon Bedrock, with Knowledge Bases for RAG, Guardrails for safety and AgentCore for agents, and it applies the patterns described under generative AI on AWS.[^s3][^s5][^s6] AWS Skill Builder hosts its official prep plan.[^s2]

## Sources
[^s1]: Amazon Web Services, "AWS Certified Generative AI Developer - Professional (AIP-C01) exam guide". https://docs.aws.amazon.com/aws-certification/latest/ai-professional-01/ai-professional-01.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "AWS Certified Generative AI Developer - Professional". https://aws.amazon.com/certification/certified-generative-ai-developer-professional (accessed 2026-11-17)
[^s3]: Amazon Web Services, "AIP-C01 Content Domain 1: Foundation Model Integration, Data Management, and Compliance". https://docs.aws.amazon.com/aws-certification/latest/ai-professional-01/ai-professional-01-domain1.html (accessed 2026-11-17)
[^s4]: Amazon Web Services, "AIP-C01 Content Domain 2: Implementation and Integration". https://docs.aws.amazon.com/aws-certification/latest/ai-professional-01/ai-professional-01-domain2.html (accessed 2026-11-17)
[^s5]: Amazon Web Services, "AIP-C01 In-Scope AWS Services". https://docs.aws.amazon.com/aws-certification/latest/ai-professional-01/aip-01-in-scope-services.html (accessed 2026-11-17)
[^s6]: Amazon Web Services, "AWS expands AI certification portfolio and updates security certification", AWS Training and Certification Blog, 2025-10-14 (edited 2026-03-17). https://aws.amazon.com/blogs/training-and-certification/big-news-aws-expands-ai-certification-portfolio-and-updates-security-certification (accessed 2026-11-17)
[^s7]: Amazon Web Services, "AWS Certification recertification". https://aws.amazon.com/certification/recertification/?nc1=h_ls (accessed 2026-11-17)
