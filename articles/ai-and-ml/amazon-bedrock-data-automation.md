---
id: kt:amazon-bedrock-data-automation
type: Feature
title: Amazon Bedrock Data Automation
aliases: [Bedrock Data Automation, BDA]
domain: ai-and-ml
summary: >-
  A generative-AI capability of Amazon Bedrock that turns unstructured documents, images, audio
  and video into structured output, either as default "standard output" or as fields defined in
  reusable blueprints, grouped and invoked through projects.
verified: 2026-11-20
status: current
broader: [kt:amazon-bedrock, kt:generative-ai-on-aws]
related: [kt:amazon-bedrock-knowledge-bases, kt:amazon-textract, kt:amazon-transcribe, kt:amazon-rekognition]
relations:
  - {predicate: partOf, object: kt:amazon-bedrock}
  - {predicate: alternativeTo, object: kt:amazon-textract}
  - {predicate: integratesWith, object: kt:amazon-bedrock-knowledge-bases}
  - {predicate: governedBy, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "How Bedrock Data Automation works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/bda-how-it-works.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Prerequisites for using Bedrock Data Automation"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/bda-limits.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Bedrock Data Automation projects"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/bda-projects.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "Blueprints"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/bda-blueprint-info.html
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "Amazon Bedrock endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/bedrock.html
    type: official-docs
    accessed: 2026-11-20
  - id: s6
    title: "Amazon Bedrock Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/bedrock/pricing/
    type: official-docs
    accessed: 2026-11-20
  - id: s7
    title: "Get insights from multimodal content with Amazon Bedrock Data Automation, now generally available"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/aws/get-insights-from-multimodal-content-with-amazon-bedrock-data-automation-now-generally-available/
    type: official-blog
    accessed: 2026-11-20
created: 2026-11-20
---

# Amazon Bedrock Data Automation

Amazon Bedrock Data Automation (BDA) is a managed capability of Amazon Bedrock that extracts insight from unstructured, multimodal content: documents, images, video and audio. Instead of chaining OCR, speech-to-text and a foundation-model prompt yourself, you send a file to BDA and get structured JSON back.[^s1] AWS announced it as generally available on 3 March 2025, positioning it both as a standalone API and as a parser that Amazon Bedrock Knowledge Bases can use to index multimodal content for retrieval-augmented generation.[^s7]

## How it works

BDA produces two kinds of result. **Standard output** is the default for every modality, for example a document summary, an audio transcript or video scene summaries, and its contents can be tuned. **Custom output** is available for documents, images and audio and is driven by a **blueprint**: a list of fields you expect, each with a natural-language description that BDA uses to find, normalise or validate the value.[^s1][^s4] Blueprints are AWS resources with their own ARN. You can start from a catalog blueprint, describe what you want in a blueprint prompt, add fields by hand or edit the JSON directly; audio blueprints cannot be generated from a prompt.[^s4]

Field values can be extracted explicitly (stated in the content) or implicitly (derived, such as reformatting an identifier), with types including string, number, boolean and arrays, plus tables, groups and custom types. Document results carry per-field confidence scores and page numbers; audio and image blueprints do not return confidence scores.[^s4]

A **project** bundles a standard-output configuration (required) with optional blueprints, and you pass its ARN when you invoke BDA. Projects have separate, mutable LIVE and DEVELOPMENT stages, and DEVELOPMENT projects can only be changed and invoked through the API.[^s3] A document project can hold many blueprints; BDA can split a multi-document file into its component documents and match each one to the best-fitting blueprint.[^s3] Invocation is asynchronous through `InvokeDataAutomationAsync` on the separate runtime endpoint, with a synchronous `InvokeDataAutomation` path for small documents. At GA, async calls began requiring a data automation profile ARN, which ties BDA to cross-Region inference.[^s7][^s2]

## Quotas, pricing and gotchas

As of 2026-11, the Bedrock quotas page lists these defaults for each supported Region:[^s5]

- Non-adjustable: 3,000 pages and 500 MB per document (20 pages and 200 MB in the console); 5 MB per image; audio up to 2,048 MB and 240 minutes; video up to 10,240 MB and 240 minutes; 40 document blueprints per project but one each for audio, image and video; 1,800 open async jobs per Region.[^s5]
- Adjustable: 350 blueprints per account; 60 synchronous document requests per minute; concurrent async jobs that vary by Region, for example 25 document jobs in most Regions but 5 in Regions such as Mumbai, Singapore and London, and 20 video jobs in us-east-1 and us-west-2 versus 3 elsewhere.[^s5]

The synchronous API is far tighter: 10 pages, 50 MB and no splitter, and it does not accept DOC or DOCX.[^s2] Document input languages are limited to English, German, Spanish, French, Italian and Portuguese, vertical text is not supported, and password-protected PDFs are rejected.[^s2] The user-guide limits page and the quotas page disagree on some values (for example blueprints per account and field description length), so check the Service Quotas console for your account.[^s2][^s5]

Pricing is per unit processed. The pricing page's worked examples (as of 2026-11; the page does not name a Region) show custom document output at $0.040 per page for blueprints of up to 30 fields plus $0.0005 per page per extra field, document parsing for Knowledge Bases at $0.010 per page, video standard output at $0.050 per minute, audio standard output at $0.006 per minute and image custom output from $0.005 per image.[^s6]

## When to use it (and when not)

Choose BDA when one pipeline must handle mixed media, when you want schema-driven extraction or classification without writing prompts and post-processing code, or when you are building a multimodal knowledge base.[^s1][^s7] Amazon Textract remains the better fit when you need deterministic OCR geometry such as bounding boxes and table cells, and Amazon Transcribe or Amazon Rekognition when you need their specialised features on a single modality. BDA is Regional: as of 2026-11 its endpoints are listed in US, Canada, Europe, Asia Pacific and AWS GovCloud (US-West) Regions, but not in every commercial Region.[^s5]

## Connections

BDA is part of Amazon Bedrock and sits within AWS's generative-AI stack.[^s1] It integrates with Amazon Bedrock Knowledge Bases as a parser for multimodal RAG.[^s7] It is an alternative to assembling Amazon Textract, Amazon Transcribe and Amazon Rekognition pipelines yourself for document, audio and image or video work.[^s1] Its default limits are managed through Service Quotas, and it is scoped to the specific AWS Regions that publish BDA endpoints.[^s5]

## Sources
[^s1]: Amazon Web Services, "How Bedrock Data Automation works". https://docs.aws.amazon.com/bedrock/latest/userguide/bda-how-it-works.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "Prerequisites for using Bedrock Data Automation". https://docs.aws.amazon.com/bedrock/latest/userguide/bda-limits.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "Bedrock Data Automation projects". https://docs.aws.amazon.com/bedrock/latest/userguide/bda-projects.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "Blueprints". https://docs.aws.amazon.com/bedrock/latest/userguide/bda-blueprint-info.html (accessed 2026-11-20)
[^s5]: Amazon Web Services, "Amazon Bedrock endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/bedrock.html (accessed 2026-11-20)
[^s6]: Amazon Web Services, "Amazon Bedrock Pricing". https://aws.amazon.com/bedrock/pricing/ (accessed 2026-11-20)
[^s7]: Amazon Web Services, "Get insights from multimodal content with Amazon Bedrock Data Automation, now generally available". https://aws.amazon.com/blogs/aws/get-insights-from-multimodal-content-with-amazon-bedrock-data-automation-now-generally-available/ (accessed 2026-11-20)
