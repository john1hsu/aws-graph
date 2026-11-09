---
id: kt:amazon-bedrock-guardrails
type: Feature
title: Amazon Bedrock Guardrails
aliases: [Guardrails for Amazon Bedrock, Bedrock Guardrails]
domain: ai-and-ml
summary: >-
  The configurable safety and privacy layer of Amazon Bedrock that evaluates prompts and model
  responses against content filters, denied topics, word and sensitive-information filters,
  contextual grounding checks and Automated Reasoning checks, inline with inference or through the
  standalone ApplyGuardrail API.
verified: 2026-11-09
status: current
broader: [kt:amazon-bedrock]
related: [kt:amazon-bedrock-knowledge-bases, kt:generative-ai-on-aws]
relations:
  - {predicate: governedBy, object: kt:aws-organizations}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "Detect and filter harmful content by using Amazon Bedrock Guardrails"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "How Amazon Bedrock Guardrails works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-how.html
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "Safeguard tiers for guardrails policies"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-tiers.html
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Amazon Bedrock Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/bedrock/pricing/
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "Amazon Bedrock policies - AWS Organizations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_bedrock.html
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "Amazon Bedrock Guardrails announces general availability of cross-account safeguards"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/04/bedrock-guardrails-cross-account-safeguards
    type: official-blog
    accessed: 2026-11-09
  - id: s7
    title: "Content Domain 4: Guidelines for Responsible AI - AWS Certified AI Practitioner (AIF-C01) exam guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain4.md
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# Amazon Bedrock Guardrails

Amazon Bedrock Guardrails is the safety and privacy layer of Amazon Bedrock. A guardrail is a reusable, Regional configuration of safeguards that checks both what users send to a foundation model and what the model sends back, so the same responsible-AI rules apply consistently whichever model an application uses.[^s1] Typical uses are filtering toxic exchanges in a chatbot, blocking a banking assistant from giving investment advice, and redacting personally identifiable information (PII) from call-center summaries.[^s1]

## Safeguard policies

A guardrail combines any of six policy types:[^s1]

- **Content filters** score text (and images) for Hate, Insults, Sexual, Violence and Misconduct, plus a Prompt Attack category for jailbreak and injection attempts, with a strength setting per category.[^s1]
- **Denied topics** describe subjects the application must not engage with, defined in natural language.[^s1]
- **Word filters** block exact words or phrases (case-insensitive), with an optional managed profanity list.[^s1]
- **Sensitive information filters** block or mask PII types such as dates of birth or addresses using probabilistic detection, and accept custom regular expressions.[^s1]
- **Contextual grounding checks** flag responses that are not supported by the supplied source material or that are irrelevant to the query, which targets hallucination in retrieval-augmented generation.[^s1]
- **Automated Reasoning checks** validate a response against formal logical rules, can suggest corrections and surface unstated assumptions.[^s1]

Each guardrail also defines the message returned to the user when an input or a response is blocked.[^s1]

## How it works

You build a guardrail as a working draft, test it in the console's test window, then publish an immutable numbered **version** for applications to reference.[^s1] At inference time you pass the guardrail ID and version with `InvokeModel`, `InvokeModelWithResponseStream`, `Converse` or `ConverseStream`; embedding, reranking and image-generation models are not supported.[^s2] The input is checked first, with the configured policies running in parallel to keep latency down. A blocked input returns the blocked message and the model is never called; otherwise the model's response is checked against the same policies and is either returned unchanged, replaced by the blocked message, or returned with sensitive data masked.[^s2]

The standalone **ApplyGuardrail** API evaluates arbitrary text without invoking a model, so the same guardrail can protect models hosted outside Bedrock or individual pipeline steps. Through the SDK you can also tag only the user-supplied part of a prompt for evaluation, skipping system instructions, retrieved passages and conversation history; the console and playground do not offer this tagging.[^s1]

### Classic and Standard tiers

Content filters, prompt-attack detection, denied topics and contextual grounding come in two safeguard tiers. **Classic** supports English, French and Spanish and allows 200-character denied-topic definitions. **Standard** adds much broader language coverage, prompt-leakage detection, harmful-content detection inside code (comments, identifiers, string literals), 1,000-character topic definitions and grounding that handles reasoning or conversational responses.[^s3] Standard tier requires the guardrail to use cross-Region inference, which may matter for data-residency reviews, and Standard contextual grounding is limited to a list of US, European and Tokyo Regions; elsewhere the request fails validation.[^s3] AWS advises re-testing grounding thresholds before moving them from Classic to Standard, because scoring behaves differently.[^s3]

## Pricing and gotchas

As of 2026-11 the Bedrock pricing page lists on-demand Guardrails rates (no Region is singled out) of $0.15 per 1,000 text units for text content filters and for denied topics, $0.10 per 1,000 text units for contextual grounding and for sensitive information filters, $0.17 per 1,000 text units per Automated Reasoning policy, and $0.00075 per image for image content filters; word filters and regex-based sensitive-information filters are free, and both tiers are priced the same.[^s4] A text unit holds up to 1,000 characters, so a 5,600-character input bills as six units, and grounding checks count the source, query and response together.[^s4]

When the input is blocked you pay only for the guardrail evaluation and no model inference; when the response is blocked you pay for evaluating both sides plus the inference.[^s2] AWS updates the underlying safeguard models automatically, so regression-test guardrails after updates rather than assuming fixed behavior.[^s2]

## Organization-wide enforcement

Since April 2026, cross-account safeguards are generally available: a central team references a guardrail from the management account in an **Amazon Bedrock policy** type in AWS Organizations, and member accounts and OUs inherit it for Bedrock model invocations, with accounts and applications able to layer their own guardrails on top.[^s5][^s6] The policy must pin a specific guardrail version, and because guardrails are Regional you need one guardrail per Region you want covered.[^s5] When several guardrails apply, Bedrock enforces their union.[^s6]

## When to use it

Use Guardrails whenever a Bedrock (or external) model faces untrusted input or produces content shown to users, and especially in RAG apps where grounding checks catch unsupported answers.[^s1] It complements, rather than replaces, IAM permissions and model-selection controls: Guardrails governs *what is said*, not *who may call* the model.[^s2]

## Connections

Guardrails is a built-in capability of **Amazon Bedrock**, and contextual grounding pairs naturally with **Amazon Bedrock Knowledge Bases** for RAG within the wider **generative AI on AWS** stack.[^s1] It can be governed centrally through **AWS Organizations** Bedrock policies and is configured separately in each of the **AWS Regions** where it is enforced.[^s5] The AIF-C01 exam guide names Amazon Bedrock Guardrails as an example tool for identifying features of responsible AI, so it is covered by the **AWS Certified AI Practitioner** certification.[^s7]

## Sources
[^s1]: Amazon Web Services, "Detect and filter harmful content by using Amazon Bedrock Guardrails". https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html (accessed 2026-11-09)
[^s2]: Amazon Web Services, "How Amazon Bedrock Guardrails works". https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-how.html (accessed 2026-11-09)
[^s3]: Amazon Web Services, "Safeguard tiers for guardrails policies". https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-tiers.html (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Amazon Bedrock Pricing". https://aws.amazon.com/bedrock/pricing/ (accessed 2026-11-09)
[^s5]: Amazon Web Services, "Amazon Bedrock policies - AWS Organizations". https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_bedrock.html (accessed 2026-11-09)
[^s6]: Amazon Web Services, "Amazon Bedrock Guardrails announces general availability of cross-account safeguards". https://aws.amazon.com/about-aws/whats-new/2026/04/bedrock-guardrails-cross-account-safeguards (accessed 2026-11-09)
[^s7]: Amazon Web Services, "Content Domain 4: Guidelines for Responsible AI - AWS Certified AI Practitioner (AIF-C01)". https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain4.md (accessed 2026-11-09)
