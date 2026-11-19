---
id: kt:amazon-transcribe
type: Service
title: Amazon Transcribe
aliases: [Transcribe, AWS Transcribe, Amazon Transcribe Call Analytics]
domain: ai-and-ml
summary: >-
  AWS's managed automatic speech recognition (ASR) service that converts recorded or live audio to
  text through batch jobs on Amazon S3 files or real-time streams, with speaker partitioning,
  custom vocabularies, PII redaction and a Call Analytics variant for contact-center audio.
verified: 2026-11-19
status: current
broader: [kt:aws-ai-services]
related: [kt:amazon-comprehend, kt:amazon-bedrock-data-automation, kt:amazon-bedrock]
relations:
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: alternativeTo, object: kt:amazon-bedrock-data-automation}
  - {predicate: compliesWith, object: kt:hipaa-eligible-services}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "What is Amazon Transcribe?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/transcribe/latest/dg/what-is.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Amazon Transcribe Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/transcribe/pricing/
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Amazon Transcribe endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/transcribe.html
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "Post-call analytics (Amazon Transcribe Developer Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/transcribe/latest/dg/call-analytics-batch.html
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Document history for Amazon Transcribe"
    publisher: Amazon Web Services
    url: https://docs.amazonaws.cn/en_us/transcribe/latest/dg/doc-history.html
    type: official-docs
    accessed: 2026-11-19
  - id: s6
    title: "Amazon Transcribe announces a new speech foundation model-powered ASR system that expands support to over 100 languages"
    publisher: AWS Machine Learning Blog
    url: https://aws.amazon.com/blogs/machine-learning/amazon-transcribe-announces-a-new-speech-foundation-model-powered-asr-system-that-expands-support-to-over-100-languages
    type: official-blog
    accessed: 2026-11-19
  - id: s7
    title: "Amazon Bedrock Data Automation now supports custom vocabulary"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/04/bedrock-data-automation-vocabulary
    type: official-blog
    accessed: 2026-11-19
  - id: s8
    title: "AWS Certified AI Practitioner (AIF-C01): In-scope AWS services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/it_it/aws-certification/latest/userguide/aif-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-19
created: 2026-11-19
---

# Amazon Transcribe

Amazon Transcribe is AWS's automatic speech recognition (ASR) service: it uses machine learning to turn speech in audio into text, either as a standalone transcription service or as a speech-to-text building block inside an application.[^s1] It is generally available and billed pay-as-you-go, and it is one of the pretrained AI services you call through an API without training or hosting a model yourself.[^s1][^s2] Since late 2023 batch transcription runs on a multi-billion-parameter speech foundation model that extended coverage to more than 100 languages; AWS reported 20–50% accuracy gains across most languages, and existing batch callers received the new model without changing endpoints or parameters.[^s6]

## How it works

Transcribe has two modes. **Batch** jobs read a media file from an Amazon S3 bucket and write a JSON transcript; **streaming** transcribes live audio in real time over HTTP/2 or WebSocket connections.[^s1][^s3] Both share a set of options: speaker partitioning (diarization, up to 30 speakers since 2024), multi-channel analysis, automatic language identification, word-level confidence scores, custom vocabularies and vocabulary filters for masking or removing unwanted words, and custom language models trained on your domain text.[^s1][^s5][^s6] PII redaction replaces personal data in transcripts; in 2026 AWS extended batch redaction to French, German, Italian, Portuguese and more English dialects and added 19 entity types such as IP addresses, passwords and AWS access keys.[^s5] Custom vocabularies, vocabulary filters and custom language models can be encrypted with customer-managed AWS KMS keys (added September 2026).[^s5]

**Call Analytics** is a variant tuned for contact-center calls. Post-call analytics on recorded calls returns talk time, non-talk time, interruptions, loudness, talk speed, sentiment (a −5 to 5 score per quarter and per call, plus a label per turn), detected issues, outcomes and action items, rule-based categories, and redacted transcripts and audio.[^s4] An optional generative call summary describes the reason for the call, the resolution steps and next steps; it is offered only in us-east-1 and us-west-2 for several English dialects.[^s4] **Amazon Transcribe Medical** and HealthScribe cover clinical speech, and region support differs between standard Transcribe, Medical and Call Analytics.[^s1][^s5]

## Quotas, pricing and gotchas

Default quotas per Region (AWS General Reference, as of 2026-11):[^s3]

- Batch file: up to 28,800 seconds (8 hours) and 2 GB; Call Analytics batch: 14,400 seconds and 500 MB. Not adjustable.[^s3]
- 250 concurrent transcription jobs, 25 concurrent streams (HTTP/2 and WebSocket combined) and 100 concurrent analytics jobs, all adjustable.[^s3]
- 100 custom vocabularies of up to 51,200 bytes each (adjustable); phrases at most 256 characters; up to 100 vocabulary filters of 50 KB (not adjustable).[^s3]

Billing is per second of audio with no minimum duration, and two-channel audio is charged on total duration, not per channel.[^s1][^s2] The us-east-1 worked examples on the pricing page (as of 2026-11) use $0.006 per minute for batch and $0.01 per minute for streaming. Post-call analytics is tiered at $0.0300 per minute for the first 250,000 minutes, $0.0186 for the next 750,000 and $0.0138 for the next 4,000,000; PII redaction adds $0.0024 per minute at tier 1, and generative summaries $0.0024 per minute at tier 1 with a 15-second minimum per request.[^s2] The free tier gives 60 minutes per month for 12 months each for standard, Call Analytics and Medical, but excludes PII redaction and custom language models.[^s2]

Gotchas: region coverage is uneven, with some Regions offering only batch (for example eu-west-3 and us-west-1) and others only streaming (for example mx-central-1).[^s1] Both AWS GovCloud (US) Regions expose batch, streaming and Medical endpoints, but neither appears in the Call Analytics endpoint list.[^s3] Transcribe is HIPAA eligible, and customers under a BAA must encrypt PHI at rest and in transit.[^s1]

## When to use it (and when not)

Use Transcribe to make recorded media searchable, to add live speech-to-text to an application, to filter or redact what a transcript exposes, and to monitor contact-center quality and trends with Call Analytics.[^s1][^s4] Pair it with downstream NLP when you need entities or sentiment beyond what Call Analytics returns. If you are already processing mixed documents, images, audio and video, Amazon Bedrock Data Automation can transcribe audio as part of one pipeline and gained custom vocabulary support in April 2026, so evaluate it as an alternative for multimodal workloads.[^s7]

## Connections

Transcribe sits under `kt:aws-ai-services` and appears in the Machine Learning category of in-scope services for the AWS Certified AI Practitioner exam, alongside Comprehend, Polly, Translate and Lex, which is the basis for the `coveredBy` edge.[^s8] It `integratesWith` `kt:amazon-s3` for batch input and output and is `securedBy` `kt:aws-kms` for customer-managed encryption of custom models and vocabularies.[^s1][^s5] It is `alternativeTo` `kt:amazon-bedrock-data-automation` for audio in multimodal pipelines.[^s7] Its HIPAA eligibility supports the `compliesWith` edge to `kt:hipaa-eligible-services`, and its Regional endpoints the `scopedTo` edge to `kt:aws-regions`.[^s1][^s3] `kt:amazon-comprehend` is the natural next step for text analytics on transcripts, and `kt:amazon-bedrock` models can summarize or reason over transcripts beyond the built-in Call Analytics summary.[^s4][^s8]

## Sources

[^s1]: Amazon Web Services, "What is Amazon Transcribe?". https://docs.aws.amazon.com/transcribe/latest/dg/what-is.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Amazon Transcribe Pricing". https://aws.amazon.com/transcribe/pricing/ (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Amazon Transcribe endpoints and quotas (AWS General Reference)". https://docs.aws.amazon.com/general/latest/gr/transcribe.html (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Post-call analytics (Amazon Transcribe Developer Guide)". https://docs.aws.amazon.com/transcribe/latest/dg/call-analytics-batch.html (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Document history for Amazon Transcribe". https://docs.amazonaws.cn/en_us/transcribe/latest/dg/doc-history.html (accessed 2026-11-19)
[^s6]: AWS Machine Learning Blog, "Amazon Transcribe announces a new speech foundation model-powered ASR system that expands support to over 100 languages". https://aws.amazon.com/blogs/machine-learning/amazon-transcribe-announces-a-new-speech-foundation-model-powered-asr-system-that-expands-support-to-over-100-languages (accessed 2026-11-19)
[^s7]: Amazon Web Services, "Amazon Bedrock Data Automation now supports custom vocabulary". https://aws.amazon.com/about-aws/whats-new/2026/04/bedrock-data-automation-vocabulary (accessed 2026-11-19)
[^s8]: Amazon Web Services, "AWS Certified AI Practitioner (AIF-C01): In-scope AWS services". https://docs.aws.amazon.com/it_it/aws-certification/latest/userguide/aif-01-in-scope-services.html (accessed 2026-11-19)
