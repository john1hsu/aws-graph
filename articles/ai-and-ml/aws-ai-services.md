---
id: kt:aws-ai-services
type: Concept
title: AWS AI services
aliases: [AWS pre-trained AI services, AWS AI services layer, AWS AI APIs, pre-built AI services on AWS]
domain: ai-and-ml
summary: >-
  The top layer of the AWS machine learning stack: fully managed, pre-trained services such as Amazon
  Rekognition, Textract, Comprehend, Transcribe, Polly, Translate, Lex, Kendra and Personalize that add
  vision, speech, language, document and recommendation capabilities through API calls, without building
  or training a model.
verified: 2026-11-19
status: concept
broader: [kt:amazon-web-services]
related: [kt:generative-ai-on-aws, kt:amazon-q-business, kt:amazon-kendra]
relations:
  - {predicate: hasPart, object: kt:amazon-rekognition}
  - {predicate: hasPart, object: kt:amazon-textract}
  - {predicate: hasPart, object: kt:amazon-comprehend}
  - {predicate: hasPart, object: kt:amazon-transcribe}
  - {predicate: alternativeTo, object: kt:amazon-sagemaker-ai}
  - {predicate: pricedBy, object: kt:aws-free-tier}
  - {predicate: coveredBy, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "Choosing an AWS machine learning service - AWS Decision Guides"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/decision-guides/latest/machine-learning-on-aws-how-to-choose/guide.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Machine Learning (ML) and Artificial Intelligence (AI) - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/machine-learning.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "In-Scope AWS Services - AWS Certified AI Practitioner"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/aif-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-19
  - id: s4
    title: "Amazon Translate Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/translate/pricing/
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Transition your Amazon Forecast usage to Amazon SageMaker Canvas"
    publisher: Amazon Web Services (AWS Machine Learning Blog)
    url: https://aws.amazon.com/blogs/machine-learning/transition-your-amazon-forecast-usage-to-amazon-sagemaker-canvas/
    type: official-blog
    accessed: 2026-11-19
  - id: s6
    title: "Exploring alternatives and seamlessly migrating data from Amazon Lookout for Vision"
    publisher: Amazon Web Services (AWS Machine Learning Blog)
    url: https://aws.amazon.com/blogs/machine-learning/exploring-alternatives-and-seamlessly-migrating-data-from-amazon-lookout-for-vision/
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# AWS AI services

"AWS AI services" is the name AWS uses for its pre-trained, task-specific artificial intelligence services: managed APIs that return labels, text, translations, speech or predictions without the caller choosing an algorithm, training a model or running inference servers. AWS's machine learning decision guide describes three levels of offering: specialized AI services for specific use cases, Amazon SageMaker AI for building and training your own models, and lower-level frameworks and infrastructure such as AWS Trainium and Inferentia.[^s1] The AWS overview whitepaper uses the same split between pretrained AI services, ML services and purpose-built infrastructure.[^s2]

## What is in the layer

The decision guide groups the use-case services as follows (it was last updated in May 2024, so newer launches are missing):[^s1]

- **Vision:** Amazon Rekognition for image and video analysis.[^s1]
- **Documents:** Amazon Textract for text, form and table extraction, plus Amazon Augmented AI for human review of low-confidence predictions.
- **Language:** Amazon Comprehend for entities, sentiment and classification; Amazon Comprehend Medical for clinical text; Amazon Translate for machine translation.
- **Speech and conversation:** Amazon Transcribe for speech-to-text, Amazon Polly for text-to-speech, and Amazon Lex V2 for chatbots and voice bots.
- **Search, recommendations and forecasting:** Amazon Kendra for intelligent enterprise search, Amazon Personalize for recommendations, and Amazon Forecast for time-series forecasts.
- **Developer and operations tooling:** Amazon CodeGuru and Amazon DevOps Guru.[^s1]

The whitepaper's AI category is broader still: it also lists Amazon Bedrock, Amazon Q, Amazon Fraud Detector, the Lookout family, Amazon Monitron, AWS HealthScribe and others alongside the classic APIs.[^s2] In practice the term covers the call-an-API services; generative AI building blocks such as Bedrock are usually treated as their own layer (see `kt:generative-ai-on-aws`).

## How it works

AWS describes these as pretrained services: the models are built and maintained by AWS, and you add intelligence to an application by calling the service rather than by preparing training data and hosting endpoints.[^s1][^s2] You send content (an image, a document, audio or text) and receive structured results. Some services allow customization on top of the pretrained model; Amazon Translate, for instance, offers an Active Custom Translation option, billed at a higher rate than standard translation.[^s4] The trade-off is control: you pick from the features the service exposes rather than the model itself, which is the line the decision guide draws between AI services and SageMaker AI.[^s1]

## Pricing and free tier

Pricing is pay-per-use, metered by the unit each service processes: images, pages, characters, audio seconds or requests. For example, as of 2026-11, Amazon Translate charges $15.00 per million characters for standard real-time text translation and the same for batch document translation, and $60.00 per million characters for Active Custom Translation; the page gives no Region-specific rate.[^s4] Translate's free tier is 2 million characters per month for 12 months from your first translation request.[^s4] Other services have their own units and free-tier offers, so model costs per service rather than for "AI services" as a whole.

## Lifecycle gotchas

The catalog changes, and several older AI services are closing. AWS closed Amazon Forecast to new customers effective July 29, 2024; existing customers can keep using it, but no new features are planned and AWS points them to SageMaker Canvas for time-series forecasting.[^s5] Amazon Lookout for Vision closed to new customers on October 10, 2024, with discontinuation scheduled for October 31, 2025; AWS's suggested alternatives were SageMaker (including JumpStart models), Bedrock foundation models or partner solutions.[^s6] The whitepaper notes that AWS would discontinue support for Amazon Lookout for Metrics on October 10, 2025.[^s2] Check each service's own page for its current status before designing around it.

## When to use it (and when not)

Use an AI service when a common task (OCR, transcription, translation, moderation, sentiment) is good enough out of the box and you want results in days rather than months, with no ML team.[^s1] Move down to SageMaker AI when you need your own model, your own training data at scale, or full control over algorithms and hosting.[^s1] Use Amazon Bedrock when the task is open-ended generation or reasoning rather than a fixed prediction; many pipelines combine both, for example Transcribe followed by a foundation model that summarizes the transcript.[^s2]

## Connections

The layer `hasPart` its member services, including `kt:amazon-rekognition`, `kt:amazon-textract`, `kt:amazon-comprehend` and `kt:amazon-transcribe`; `kt:amazon-kendra` and `kt:amazon-q-business` sit alongside it as search and assistant services.[^s1][^s2] It is the `alternativeTo` building your own models in `kt:amazon-sagemaker-ai`, and `kt:generative-ai-on-aws` covers the generative layer next to it.[^s1] Per-service trial offers make `kt:aws-free-tier` a pricing edge.[^s4] The AWS Certified AI Practitioner in-scope list includes Comprehend, Lex, Personalize, Polly, Rekognition, Textract, Transcribe and Translate under Machine Learning, which supports the `coveredBy` edge to `kt:aws-certified-ai-practitioner`.[^s3]

## Sources
[^s1]: Amazon Web Services, "Choosing an AWS machine learning service - AWS Decision Guides". https://docs.aws.amazon.com/decision-guides/latest/machine-learning-on-aws-how-to-choose/guide.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Machine Learning (ML) and Artificial Intelligence (AI) - Overview of Amazon Web Services". https://docs.aws.amazon.com/whitepapers/latest/aws-overview/machine-learning.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "In-Scope AWS Services - AWS Certified AI Practitioner". https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/aif-01-in-scope-services.html (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Amazon Translate Pricing". https://aws.amazon.com/translate/pricing/ (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Transition your Amazon Forecast usage to Amazon SageMaker Canvas". https://aws.amazon.com/blogs/machine-learning/transition-your-amazon-forecast-usage-to-amazon-sagemaker-canvas/ (accessed 2026-11-19)
[^s6]: Amazon Web Services, "Exploring alternatives and seamlessly migrating data from Amazon Lookout for Vision". https://aws.amazon.com/blogs/machine-learning/exploring-alternatives-and-seamlessly-migrating-data-from-amazon-lookout-for-vision/ (accessed 2026-11-19)
