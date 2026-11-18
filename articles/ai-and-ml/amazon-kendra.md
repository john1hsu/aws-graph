---
id: kt:amazon-kendra
type: Service
title: Amazon Kendra
aliases: [Kendra, AWS Kendra, Kendra GenAI Index]
domain: ai-and-ml
summary: >-
  AWS's managed intelligent enterprise search and retrieval service, which indexes documents
  from Amazon S3 and third-party repositories and ranks results semantically; it entered
  maintenance mode on 2026-06-30 and closed to new customers on 2026-07-30, with AWS pointing
  new work to Amazon Bedrock Knowledge Bases.
verified: 2026-11-18
status: closed-to-new-customers
broader: [kt:aws-ai-services]
related: [kt:amazon-q-business, kt:amazon-opensearch-service, kt:generative-ai-on-aws]
relations:
  - {predicate: alternativeTo, object: kt:amazon-bedrock-knowledge-bases}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:amazon-q-business}
  - {predicate: governedBy, object: kt:kendra-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is Amazon Kendra?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kendra/latest/dg/what-is-kendra.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Amazon Kendra availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kendra/latest/dg/kendra-availability-change.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Document history for Amazon Kendra"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kendra/latest/dg/doc-history.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Amazon Kendra Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/kendra/pricing/
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "Quotas for Amazon Kendra"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kendra/latest/dg/quotas.html
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "Retrieving passages"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kendra/latest/dg/searching-retrieve.html
    type: official-docs
    accessed: 2026-11-18
  - id: s7
    title: "Introducing Amazon Kendra GenAI Index – Enhanced semantic search and retrieval capabilities"
    publisher: AWS Machine Learning Blog
    url: https://aws.amazon.com/blogs/machine-learning/introducing-amazon-kendra-genai-index-enhanced-semantic-search-and-retrieval-capabilities/
    type: official-blog
    accessed: 2026-11-18
  - id: s8
    title: "CreateIndex - Amazon Kendra API Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kendra/latest/APIReference/API_CreateIndex.html
    type: official-docs
    accessed: 2026-11-18
created: 2026-11-18
---

# Amazon Kendra

Amazon Kendra is a managed information-retrieval and intelligent search service. Instead of matching keywords only, it uses natural language processing and deep learning to judge whether a document or text chunk is semantically relevant to a question, and it can answer factoid questions, return descriptive passages, or rank whole documents. You connect several repositories to one index, use document metadata to tune results, and can filter results by user or group so search respects your organization's access model (you still authenticate and authorize users yourself).[^s1]

**Lifecycle:** Kendra entered *maintenance mode* on June 30, 2026 (no new features, but bug fixes and security updates continue) and stopped accepting new customers on July 30, 2026. AWS has published no end-of-support date and recommends building new search and RAG applications on Amazon Bedrock's managed knowledge bases.[^s2][^s3]

## How it works

Content reaches an index either by batch upload of documents or through data source connectors that crawl and sync repositories such as Amazon S3 and Microsoft SharePoint; the GenAI index launch post counted 43 enterprise connectors, including OneDrive, Google Drive and Salesforce.[^s1][^s7] Indexes come in three editions, chosen at creation with the `Edition` parameter of `CreateIndex` and not changeable afterwards: **GenAI Enterprise** (hybrid vector-plus-keyword index tuned for RAG, launched December 2024), **Basic Enterprise** (semantic search for production) and **Basic Developer** (proof-of-concept only).[^s1][^s3][^s7][^s8]

Applications read from an index through two APIs. **Query** returns ranked documents with excerpts of up to 100 token words by default, plus FAQ and question-answer results, spell correction, facets and query suggestions. **Retrieve** is designed as a RAG retriever: it returns up to 100 passages of up to 200 token words each, with optional attribute and user-access filtering, but omits FAQ answers and Query's analytics. Both APIs draw on the same query capacity.[^s6] A GenAI Enterprise index can also serve as the retriever for Amazon Q Business applications and as a data source for Bedrock knowledge bases, so one index can feed several assistants without re-indexing.[^s7]

## Quotas, pricing and gotchas

Pricing as of 2026-11 (the page's examples assume us-east-1): a GenAI Enterprise index costs $0.32/hour for one storage and one query unit (up to 20,000 documents or 200 MB of text, about 0.1 queries per second); Basic Enterprise costs $1.40/hour (up to 100,000 documents or 30 GB, 0.1 QPS); Basic Developer costs $1.125/hour (up to 10,000 documents or 3 GB, 0.05 QPS) and cannot add units. Connectors are a flat $30 per GenAI index per month (including up to 500 sync hours), or $0.35 per sync hour plus $1 per million documents scanned on Basic editions.[^s4] You pay for every provisioned index from creation to deletion, even when it is empty or idle, which is the most common cost surprise.[^s1][^s4]

Default quotas as of 2026-11: 10 indexes per account (adjustable); 5 connectors per Developer index (fixed) and 50 per Enterprise or GenAI index (adjustable); 1,000 characters per query; 50 MB per document via a connector and 5 MB via `BatchPutDocument` (both adjustable).[^s5] GenAI indexes support only attribute-based user-context filtering, not user tokens.[^s8]

## When to use it (and when not)

Because Kendra is closed to new customers, it is now mainly a platform for existing deployments. AWS's migration guide maps it to Bedrock's managed knowledge bases, which add generated answers with citations, but notes gaps: fewer native connectors, no query suggestions, facets, synonyms or spell checking, and a requirement to choose your own chunking strategy. AWS advises running both services in parallel and comparing relevance before cutting over.[^s2] Teams that need keyword search, aggregations or autocomplete at scale can also look at Amazon OpenSearch Service.[^s2]

## Connections

Kendra sits under the broader family of AWS AI services. It is an **alternative to** Amazon Bedrock Knowledge Bases, AWS's named successor path, and **integrates with** Amazon Q Business, which can use a GenAI index as its retriever.[^s2][^s7] It ingests heavily from Amazon S3, is **governed by** the per-edition Kendra quotas, and is **scoped to** individual AWS Regions, with Amazon OpenSearch Service as the self-tuned search alternative.[^s1][^s5]

## Sources

[^s1]: Amazon Web Services, "What is Amazon Kendra?". https://docs.aws.amazon.com/kendra/latest/dg/what-is-kendra.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Amazon Kendra availability change". https://docs.aws.amazon.com/kendra/latest/dg/kendra-availability-change.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Document history for Amazon Kendra". https://docs.aws.amazon.com/kendra/latest/dg/doc-history.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Amazon Kendra Pricing". https://aws.amazon.com/kendra/pricing/ (accessed 2026-11-18)
[^s5]: Amazon Web Services, "Quotas for Amazon Kendra". https://docs.aws.amazon.com/kendra/latest/dg/quotas.html (accessed 2026-11-18)
[^s6]: Amazon Web Services, "Retrieving passages". https://docs.aws.amazon.com/kendra/latest/dg/searching-retrieve.html (accessed 2026-11-18)
[^s7]: AWS Machine Learning Blog, "Introducing Amazon Kendra GenAI Index – Enhanced semantic search and retrieval capabilities". https://aws.amazon.com/blogs/machine-learning/introducing-amazon-kendra-genai-index-enhanced-semantic-search-and-retrieval-capabilities/ (accessed 2026-11-18)
[^s8]: Amazon Web Services, "CreateIndex - Amazon Kendra API Reference". https://docs.aws.amazon.com/kendra/latest/APIReference/API_CreateIndex.html (accessed 2026-11-18)
