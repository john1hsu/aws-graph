---
id: kt:amazon-bedrock-knowledge-bases
type: Feature
title: Amazon Bedrock Knowledge Bases
aliases: [Knowledge Bases for Amazon Bedrock, Bedrock Knowledge Bases, Amazon Bedrock Managed Knowledge Base]
domain: ai-and-ml
summary: >-
  The managed retrieval-augmented generation (RAG) capability of Amazon Bedrock that ingests,
  chunks, embeds and indexes an organization's data, or queries structured stores with generated
  SQL, and serves relevant passages or cited, model-generated answers through the Retrieve and
  RetrieveAndGenerate APIs.
verified: 2026-11-10
status: current
broader: [kt:amazon-bedrock, kt:generative-ai-on-aws]
related: [kt:amazon-bedrock-guardrails, kt:amazon-opensearch-service]
relations:
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:amazon-redshift}
  - {predicate: alternativeTo, object: kt:amazon-q-business}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "Retrieving information from data sources using Amazon Bedrock Knowledge Bases"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/kb-how-retrieval.html
    type: official-docs
    accessed: 2026-11-10
  - id: s2
    title: "Turning data into a knowledge base"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/kb-how-data.html
    type: official-docs
    accessed: 2026-11-10
  - id: s3
    title: "Knowledge bases for Amazon Bedrock - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/retrieval-augmented-generation-options/rag-fully-managed-bedrock.html
    type: official-docs
    accessed: 2026-11-10
  - id: s4
    title: "Using S3 Vectors with Amazon Bedrock Knowledge Bases"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-vectors-bedrock-kb.html
    type: official-docs
    accessed: 2026-11-10
  - id: s5
    title: "Amazon Bedrock Managed Knowledge Base is now generally available"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-bedrock-managed-knowledge-base/
    type: official-blog
    accessed: 2026-11-10
  - id: s6
    title: "Service quotas for managed knowledge bases"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/kb-managed-quotas.html
    type: official-docs
    accessed: 2026-11-10
  - id: s7
    title: "Amazon Bedrock Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/bedrock/pricing/
    type: official-docs
    accessed: 2026-11-10
  - id: s8
    title: "Content Domain 3: Applications of Foundation Models - AWS Certified AI Practitioner (AIF-C01)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain3.html
    type: official-learning
    accessed: 2026-11-10
created: 2026-11-10
---

# Amazon Bedrock Knowledge Bases

Amazon Bedrock Knowledge Bases is Bedrock's built-in way to do retrieval-augmented generation (RAG): grounding a foundation model's answer in an organization's own documents or tables instead of only its training data. It covers the whole RAG workflow (ingestion, retrieval and prompt augmentation), so teams don't have to write connectors or manage the data flow themselves, and it keeps session context for multi-turn conversations.[^s3] Answers can be traced back to their source passages, which AWS presents as a way to improve transparency and reduce hallucination.[^s3]

## How ingestion works

For unstructured content, you point a knowledge base at a data source and Bedrock runs a four-step pipeline: a parser extracts the content, the documents are split into token-based chunks, an embedding model turns each chunk into a vector, and the vectors are written to an index in a vector store.[^s2] Multimodal knowledge bases embed images as visual vectors alongside text vectors, so both can be searched together.[^s2] Managed data sources include Amazon S3, Confluence, Google Drive, Microsoft OneDrive, Microsoft SharePoint, a Web Crawler and a Custom source. Connector credentials (OAuth or tokens) are stored in AWS Secrets Manager.[^s2][^s3] Source changes reach the index only when you sync, but some sources also accept direct ingestion or deletion of individual documents, so you don't need a full re-sync.[^s2]

For a self-managed knowledge base you choose the vector store. Options are Amazon OpenSearch Serverless (which Bedrock can create for you), OpenSearch Service managed clusters, Amazon Neptune, Amazon Aurora, Pinecone, Redis Enterprise Cloud, MongoDB Atlas and Amazon S3 Vectors.[^s2][^s3] S3 Vectors suits large, cost-sensitive RAG datasets that don't need ultra-low latency. AWS cites sub-second cold and roughly 100 ms warm query latency, but it supports semantic search only (no hybrid search) and caps custom metadata at 1 KB and 35 keys per vector. That cap can bite with hierarchical chunking.[^s4]

Structured data works differently. Knowledge bases connected to Amazon Redshift or to the AWS Glue Data Catalog (with Lake Formation) are not vectorized. Instead, Bedrock translates the natural-language question into SQL using schema metadata, query patterns and query history.[^s2]

## How retrieval works

Applications query a knowledge base through four runtime operations:[^s1]

- **Retrieve** returns the most relevant chunks or images and leaves generation to your code. Use it when you want to control the generation step yourself, for example through LangChain's Bedrock retriever.[^s1][^s3]
- **RetrieveAndGenerate** runs retrieval and model inference in one managed call and returns an answer with citations to specific source chunks.[^s1]
- **GenerateQuery** exposes the natural-language-to-SQL step for structured stores, so you can embed it in your own workflow.[^s1]
- **AgenticRetrieveStream** uses a model to break a complex question into sub-queries, retrieve iteratively, judge whether the results are sufficient and return deduplicated chunks with trace events.[^s1]

A reranking model can reorder retrieved results by relevance with either Retrieve or RetrieveAndGenerate.[^s1]

## Managed Knowledge Base

In June 2026 AWS made **Amazon Bedrock Managed Knowledge Base** generally available. In this variant AWS also runs the vector storage, ingestion and retrieval infrastructure, so you don't operate a vector database at all. It adds hybrid search, document ranking, agentic retrieval and multimodal (text, video, audio and image) knowledge bases, and connecting it to an Amazon Bedrock AgentCore agent generates the needed permissions automatically.[^s5] At launch it was offered in US East (N. Virginia), US West (Oregon), Asia Pacific (Sydney, Tokyo), Europe (Dublin, Frankfurt, London) and AWS GovCloud (US-West).[^s5] AWS's retrieval guide now recommends the managed option for retrieval accuracy and a managed experience.[^s1]

## Quotas, pricing and gotchas

The managed-knowledge-base quotas (as of 2026-11) are as follows. Non-adjustable: 10,000 knowledge bases per account per Region, 50 concurrent ingestion jobs per knowledge base, and 10,000 English query characters per Retrieve or AgenticRetrieveStream request. Adjustable: 200 data sources and 10 TB of raw data per knowledge base, 600 Retrieve requests per minute per knowledge base (burst of 25 per second), and 300 AgenticRetrieveStream requests per minute per account.[^s6]

As of 2026-11, the Bedrock pricing page lists Managed Knowledge Base rates without naming a Region. Index storage is $5.00 per GB of raw data per month and standard retrieval is $1.00 per 1,000 Retrieve calls. Managed-LLM agentic retrieval costs $4.00 per 1,000 calls plus the underlying Retrieve calls. The managed reranker and managed parser cost nothing.[^s7] Self-managed knowledge bases bill differently: you pay for the embedding and generation models, your own vector store, per-request structured-data SQL generation and per-query rerank models.[^s7]

Gotchas:

- Use a separate S3 vector index for each knowledge base.[^s4]
- S3 Vectors' default deletion policy, `Delete`, removes vectors when the knowledge base or data source is deleted. Set `Retain` if you need to keep them.[^s4]
- Unsynced source changes are invisible to queries.[^s2]

## When to use it (and when not)

Choose Knowledge Bases when a Bedrock application needs answers grounded in company content with citations, without building and operating a RAG pipeline.[^s3] Use **Retrieve** with your own prompt orchestration when you need control over generation, and **RetrieveAndGenerate** for the fastest path.[^s1] AWS's RAG guidance also lists Amazon Q Business as a separate fully managed RAG option, so compare the two before building.[^s3] RAG is also one of several customization approaches, alongside prompting, fine-tuning and pre-training, with different cost trade-offs.[^s8]

## Connections

Knowledge Bases is a capability of **Amazon Bedrock** within the broader **generative AI on AWS** landscape. It sits alongside **Amazon Bedrock Guardrails**, Bedrock's safety layer, in RAG applications, and can be attached to Bedrock agents that pick the right knowledge base for each request.[^s3] It ingests from **Amazon S3** and other connectors, can store vectors in **Amazon OpenSearch Service** (Serverless or managed clusters) and other stores, and queries **Amazon Redshift** directly for structured data.[^s2] **Amazon Q Business** is the alternative fully managed RAG product.[^s3] Knowledge bases and quotas are per **AWS Region**.[^s6] The AIF-C01 exam guide names Amazon Bedrock Knowledge Bases as its example of RAG, so it is covered by the **AWS Certified AI Practitioner** certification.[^s8]

## Sources
[^s1]: Amazon Web Services, "Retrieving information from data sources using Amazon Bedrock Knowledge Bases". https://docs.aws.amazon.com/bedrock/latest/userguide/kb-how-retrieval.html (accessed 2026-11-10)
[^s2]: Amazon Web Services, "Turning data into a knowledge base". https://docs.aws.amazon.com/bedrock/latest/userguide/kb-how-data.html (accessed 2026-11-10)
[^s3]: Amazon Web Services, "Knowledge bases for Amazon Bedrock - AWS Prescriptive Guidance". https://docs.aws.amazon.com/prescriptive-guidance/latest/retrieval-augmented-generation-options/rag-fully-managed-bedrock.html (accessed 2026-11-10)
[^s4]: Amazon Web Services, "Using S3 Vectors with Amazon Bedrock Knowledge Bases". https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-vectors-bedrock-kb.html (accessed 2026-11-10)
[^s5]: Amazon Web Services, "Amazon Bedrock Managed Knowledge Base is now generally available". https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-bedrock-managed-knowledge-base/ (accessed 2026-11-10)
[^s6]: Amazon Web Services, "Service quotas for managed knowledge bases". https://docs.aws.amazon.com/bedrock/latest/userguide/kb-managed-quotas.html (accessed 2026-11-10)
[^s7]: Amazon Web Services, "Amazon Bedrock Pricing". https://aws.amazon.com/bedrock/pricing/ (accessed 2026-11-10)
[^s8]: Amazon Web Services, "Content Domain 3: Applications of Foundation Models - AWS Certified AI Practitioner (AIF-C01)". https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain3.html (accessed 2026-11-10)
