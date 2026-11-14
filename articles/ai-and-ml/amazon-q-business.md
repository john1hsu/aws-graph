---
id: kt:amazon-q-business
type: Service
title: Amazon Q Business
aliases: [Q Business, Amazon Q for business]
domain: ai-and-ml
summary: >-
  AWS's managed generative-AI assistant for employees that answers questions with citations from
  connected enterprise data, respects source permissions and can take actions through plugins;
  closed to new customers since July 30, 2026, with AWS directing customers to Amazon Quick.
verified: 2026-11-14
status: closed-to-new-customers
broader: [kt:generative-ai-on-aws, kt:aws-ai-services]
related: [kt:amazon-bedrock-knowledge-bases, kt:amazon-bedrock]
relations:
  - {predicate: replacedBy, object: kt:amazon-quick}
  - {predicate: dependsOn, object: kt:iam-identity-center}
  - {predicate: integratesWith, object: kt:amazon-kendra}
  - {predicate: alternativeTo, object: kt:amazon-bedrock-knowledge-bases}
  - {predicate: scopedTo, object: kt:availability-zones}
sources:
  - id: s1
    title: "Amazon Q Business availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazonq/latest/qbusiness-ug/qbusiness-availability-change.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "AWS Service Availability Updates (What's New, June 30, 2026)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/aws-service-availability/
    type: official-blog
    accessed: 2026-11-14
  - id: s3
    title: "How Amazon Q Business works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazonq/latest/qbusiness-ug/how-it-works.md
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "Key concepts of Amazon Q Business"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazonq/latest/qbusiness-ug/concepts-terms.md
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "Amazon Q Business subscription tiers and index types"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazonq/latest/qbusiness-ug/tiers.md
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "Amazon Q Business pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/q/business/pricing/
    type: official-docs
    accessed: 2026-11-14
created: 2026-11-14
---

# Amazon Q Business

Amazon Q Business is a managed assistant that lets an organization stand up a chat application over its own content: employees ask questions and get generated answers grounded in enterprise data, with each answer citing the documents it drew on.[^s3] It can also be allowed to answer from the underlying model's general knowledge, and with plugins it can take actions such as opening a ticket in a third-party system.[^s3][^s4]

**Lifecycle (as of 2026-11):** AWS announced on June 30, 2026 that Q Business was moving to maintenance and would stop accepting new customers from July 30, 2026; existing customers can keep using it and AWS continues to operate and support it.[^s2] The service's documentation adds that existing customers get bug fixes and security updates but no new features, and recommends moving Q Business applications, and building any new generative or agentic AI assistants, on **Amazon Quick**.[^s1] The same announcement moved Amazon Kendra and Amazon Bedrock Agents (now "Bedrock Agents Classic") to maintenance.[^s2]

## How it works

The central resource is an **application environment**, created through the console or API with one of four identity models: IAM Identity Center (the standard path), IAM federation via OIDC or SAML, anonymous access for public-facing use, or an integration with Quick.[^s4] AWS recommends an organization instance of IAM Identity Center; an account instance limits the deployment to one account and Region, and federation without Identity Center limits integration with other AWS applications.[^s3]

Content reaches the assistant through an **index**, filled by **data source connectors** that crawl repositories on a schedule, or by adding documents directly. A **retriever** queries the index during each chat; it can be the native Q Business index or an existing Amazon Kendra index.[^s4] At query time the service retrieves relevant documents subject to authorization and access control, then generates an answer either from retrieved data plus model knowledge or from enterprise data only, depending on admin settings.[^s3] Answers are permissions-aware, so users see only content their source ACLs allow.[^s5]

Around that core sit admin **guardrails** (global and topic-level controls on sources and topics), document-attribute filtering and relevance boosting, hallucination mitigation for RAG answers, **chat orchestration** that routes requests across plugins and data sources, **Amazon Q Apps** for user-built task apps, browser extensions, and integrations with Slack, Microsoft Teams, Outlook and Word.[^s4] Independent software vendors can read a customer's index through data accessors and the `SearchRelevantContent` API.[^s4] Conversation history is kept for 30 days.[^s3]

## Quotas, pricing and gotchas

Prices as listed on the AWS pricing page (no Region stated; checked 2026-11-14): **Lite** $3 per user per month and **Pro** $20 per user per month; **Starter index** $0.140 per unit-hour, limited to five units per application; **Enterprise index** $0.264 per unit-hour.[^s6] Each index unit covers 20,000 documents or 200 MB of extracted text, whichever comes first, plus 100 hours of connector usage a month.[^s6][^s5] A Starter index runs in one Availability Zone and suits proofs of concept; an Enterprise index runs in three AZs, adds customer managed key encryption and is meant for production.[^s5]

Gotchas:
- **Lite lacks plugins and Q Apps.** Built-in and custom plugins, Q Apps, chat orchestration and model-knowledge Q&A need Pro.[^s5]
- **Subscription billing is per identity boundary.** With Identity Center, a user is charged once (at the highest tier) across applications sharing that instance; with IAM federation, a user is charged once per identity provider.[^s5] Charges start at first use, new or upgraded subscriptions are prorated, and downgrades or cancellations take effect next month.[^s5]
- **Pro is reduced in some Regions.** Pro subscriptions in Europe (Ireland) and Asia Pacific (Sydney) carry a limited feature set.[^s5]

## Migrating to Amazon Quick

AWS describes a phased move. **Bring Your Own Index (BYOI)** attaches an existing Q Business index to Quick as a knowledge base while Q Business keeps running; the index and Quick instance must be in the same account and Region.[^s1] BYOI allows at most two Q Business indexes per Region, a quota that cannot be raised, and excludes Q Apps, actions and chat guardrails.[^s1] Q Apps must be rebuilt as Quick Flows and guardrails recreated with Quick's own controls.[^s1] Without Identity Center, every Quick user gets access to a connected index, so per-user and per-group distinctions are lost.[^s1] Quick also skips documents that lack an ACL entry, and document-level ACLs must be chosen when a knowledge base is created.[^s1]

## When to use it (and when not)

For an existing deployment, keep running Q Business while planning the Quick migration; no new capabilities are coming.[^s1] New employee-assistant projects should start on Amazon Quick, which AWS names as the destination.[^s1] Q Business was a packaged assistant with its own web experience and workplace integrations;[^s4] teams building their own application rather than adopting an assistant would instead look at **Amazon Bedrock Knowledge Bases** for managed RAG inside their code.

## Connections

Q Business sits under **generative AI on AWS** and the **AWS AI services** family. It is **replaced by** Amazon Quick, AWS's recommended migration target.[^s1] It **depends on** IAM Identity Center for its standard identity model and **integrates with** Amazon Kendra, whose index can serve as a retriever.[^s3][^s4] It is an **alternative to** Bedrock Knowledge Bases for grounded answers over company data, and its index types are **scoped to** Availability Zones (one for Starter, three for Enterprise).[^s5]

## Sources
[^s1]: Amazon Web Services, "Amazon Q Business availability change". https://docs.aws.amazon.com/amazonq/latest/qbusiness-ug/qbusiness-availability-change.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "AWS Service Availability Updates" (What's New, June 30, 2026). https://aws.amazon.com/about-aws/whats-new/2026/06/aws-service-availability/ (accessed 2026-11-14)
[^s3]: Amazon Web Services, "How Amazon Q Business works". https://docs.aws.amazon.com/amazonq/latest/qbusiness-ug/how-it-works.md (accessed 2026-11-14)
[^s4]: Amazon Web Services, "Key concepts of Amazon Q Business". https://docs.aws.amazon.com/amazonq/latest/qbusiness-ug/concepts-terms.md (accessed 2026-11-14)
[^s5]: Amazon Web Services, "Amazon Q Business subscription tiers and index types". https://docs.aws.amazon.com/amazonq/latest/qbusiness-ug/tiers.md (accessed 2026-11-14)
[^s6]: Amazon Web Services, "Amazon Q Business pricing". https://aws.amazon.com/q/business/pricing/ (accessed 2026-11-14)
