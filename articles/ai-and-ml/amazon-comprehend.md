---
id: kt:amazon-comprehend
type: Service
title: Amazon Comprehend
aliases: [Comprehend, AWS Comprehend, Amazon Comprehend Custom]
domain: ai-and-ml
summary: >-
  AWS's pretrained natural-language-processing service that extracts entities, key phrases,
  dominant language, sentiment, syntax and personally identifiable information from text, detects
  toxic content, and lets you train custom classifiers and entity recognizers without ML expertise.
verified: 2026-11-13
status: current
broader: [kt:aws-ai-services]
related: [kt:amazon-textract, kt:amazon-bedrock, kt:amazon-comprehend-medical]
relations:
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: alternativeTo, object: kt:amazon-bedrock}
  - {predicate: alternativeTo, object: kt:amazon-bedrock-guardrails}
  - {predicate: governedBy, object: kt:comprehend-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "What is Amazon Comprehend?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/comprehend/latest/dg/what-is.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Amazon Comprehend feature availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/comprehend/latest/dg/comprehend-availability-change.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "Guidelines and quotas (Amazon Comprehend)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/comprehend/latest/dg/guidelines-and-limits.html
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "Amazon Comprehend Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/comprehend/pricing
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "Trust and safety (Amazon Comprehend)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/comprehend/latest/dg/trust-safety.html
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "AWS Certified AI Practitioner (AIF-C01): In-scope AWS services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/it_it/aws-certification/latest/userguide/aif-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-13
created: 2026-11-13
---

# Amazon Comprehend

Amazon Comprehend is AWS's managed natural-language-processing (NLP) service. It reads UTF-8 text and returns structured insights: named entities, key phrases, the dominant language, document-level sentiment (positive, negative, neutral or mixed), targeted sentiment tied to specific entities, parts of speech, and personally identifiable information (PII).[^s1] The built-in models are pretrained and continuously updated by AWS, so you send text and get results without supplying training data.[^s1] For domain-specific needs, Comprehend Custom uses AutoML to train custom classifiers (your own document categories) and custom entity recognizers (your own terms) from labelled data you already hold; custom models also accept PDF, Word and image files.[^s1]

## How it works

Every built-in API runs in two modes: synchronous, real-time calls for single documents or small batches, and asynchronous jobs that read a document set from Amazon S3 and write results back to S3.[^s1][^s3] Custom models run either as asynchronous jobs or behind a provisioned real-time endpoint sized in inference units, each giving 100 characters per second of throughput.[^s3][^s4] Flywheels manage the retraining and versioning of custom models over time.[^s1]

For trust and safety, `DetectToxicContent` scores English text against categories such as hate speech, harassment or abuse, insult, profanity, sexual content and violence or threat, plus an overall toxicity score. PII can be detected in real time or by batch job, but redaction runs only as an asynchronous job, and PII support covers English and Spanish.[^s5]

## Lifecycle: features closed to new customers

Comprehend itself is generally available, but three features are no longer offered to new customers: topic modeling, event detection and prompt safety classification. Accounts that used them in the previous 12 months keep access, and other Comprehend features are unaffected.[^s2] AWS's documented migration paths are Amazon Bedrock foundation models (prompted to return events or assign topics from a fixed list) for event detection and topic modeling, and Amazon Bedrock Guardrails with its prompt-attack filter in place of prompt safety classification.[^s2][^s5]

## Quotas, pricing and gotchas

Quotas are per Region; adjustable ones are raised through Service Quotas (as of 2026-11).[^s3]

- Synchronous built-in calls: 100 KB per document for entities, key phrases and dominant language, but only 5 KB for sentiment, targeted sentiment and syntax. Batch calls take at most 25 documents of 5 KB each.[^s3]
- Asynchronous built-in jobs: up to 1 MB per document and 5 GB per request for general analysis, with 10 active jobs per operation.[^s3]
- Toxicity detection: at most 10 text segments of 1 KB each per request.[^s5]
- Custom endpoints: 20 active endpoints and 200 inference units per Region per account, 50 inference units per endpoint.[^s3]

Pricing (us-east-1 pricing page, as of 2026-11) is metered in 100-character units with a three-unit (300-character) minimum per request. Standard NLP APIs such as entities, sentiment, key phrases, syntax and PII detection list $0.0001 per unit; custom asynchronous inference is $0.0005 per unit; custom model training is $3 per hour, plus $0.50 per model per month for management. Real-time custom endpoints bill $0.0005 per inference unit per second from creation until deletion, with a 60-second minimum.[^s4] The free tier gives 50,000 units per API per month for 12 months from first use; Comprehend Custom has no free tier.[^s4]

Two common gotchas: the 300-character minimum makes very short texts disproportionately expensive, so batch them; and an idle custom endpoint keeps billing until you delete it.[^s4] Region coverage is narrower than for core services: the quotas page lists 13 Regions, including AWS GovCloud (US-West).[^s3]

## When to use it (and when not)

Use Comprehend for high-volume, predictable NLP: tagging support tickets by sentiment, extracting entities for search indexes, finding or redacting PII before data lands in an analytics store, or routing documents with a custom classifier. Per-unit pricing is easy to forecast, and newer APIs such as toxicity detection return confidence scores per category.[^s5][^s1][^s4] For open-ended extraction, summarisation, or the features now closed to new customers, AWS points to Amazon Bedrock models and Bedrock Guardrails instead.[^s2] Clinical text belongs to Amazon Comprehend Medical, a separate service.

## Connections

Comprehend is one of AWS's pretrained AI services and is listed in the Machine Learning category of in-scope services for the AWS Certified AI Practitioner (AIF-C01) exam, alongside Amazon Textract, Amazon Bedrock and Amazon Lex.[^s6] It integrates with Amazon S3 for asynchronous input and output and pairs naturally with Textract, which extracts text that Comprehend then analyses; custom classifiers can also consume Textract output directly.[^s3] Amazon Bedrock and Bedrock Guardrails are its alternatives and successors for the retired-to-new-customer features.[^s2] Its detailed limits form the `kt:comprehend-quotas` node, and it is a Regional service.[^s3]

## Sources

[^s1]: Amazon Web Services, "What is Amazon Comprehend?". https://docs.aws.amazon.com/comprehend/latest/dg/what-is.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Amazon Comprehend feature availability change". https://docs.aws.amazon.com/comprehend/latest/dg/comprehend-availability-change.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Guidelines and quotas (Amazon Comprehend)". https://docs.aws.amazon.com/comprehend/latest/dg/guidelines-and-limits.html (accessed 2026-11-13)
[^s4]: Amazon Web Services, "Amazon Comprehend Pricing". https://aws.amazon.com/comprehend/pricing (accessed 2026-11-13)
[^s5]: Amazon Web Services, "Trust and safety (Amazon Comprehend)". https://docs.aws.amazon.com/comprehend/latest/dg/trust-safety.html (accessed 2026-11-13)
[^s6]: Amazon Web Services, "AWS Certified AI Practitioner (AIF-C01): In-scope AWS services". https://docs.aws.amazon.com/it_it/aws-certification/latest/userguide/aif-01-in-scope-services.html (accessed 2026-11-13)
