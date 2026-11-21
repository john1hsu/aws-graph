---
id: kt:amazon-polly
type: Service
title: Amazon Polly
aliases: [Polly, AWS Polly, Amazon Polly text-to-speech]
domain: ai-and-ml
summary: >-
  AWS's managed text-to-speech (TTS) service that turns plain text or SSML into lifelike audio
  through four voice engines (Standard, Neural, Long-form and Generative), with pronunciation
  lexicons, speech marks, asynchronous long-audio tasks to Amazon S3 and a bidirectional streaming API.
verified: 2026-11-21
status: current
broader: [kt:aws-ai-services]
related: [kt:amazon-transcribe, kt:amazon-bedrock, kt:amazon-lex]
relations:
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:amazon-sns}
  - {predicate: governedBy, object: kt:service-quotas}
  - {predicate: compliesWith, object: kt:hipaa-eligible-services}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "Amazon Polly voice engines"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/polly/latest/dg/voice-engines-polly.html
    type: official-docs
    accessed: 2026-11-21
  - id: s2
    title: "Quotas in Amazon Polly"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/polly/latest/dg/limits.html
    type: official-docs
    accessed: 2026-11-21
  - id: s3
    title: "SynthesizeSpeech and StartSpeechSynthesisStream compared"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/polly/latest/dg/bidirectional-streaming-choosing.html
    type: official-docs
    accessed: 2026-11-21
  - id: s4
    title: "Amazon Polly endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/pol.html
    type: official-docs
    accessed: 2026-11-21
  - id: s5
    title: "Amazon Polly Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/polly/pricing/
    type: official-docs
    accessed: 2026-11-21
  - id: s6
    title: "Amazon Polly FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/polly/faqs/
    type: official-docs
    accessed: 2026-11-21
  - id: s7
    title: "Long audio files (Amazon Polly Developer Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/polly/latest/dg/asynchronous.html
    type: official-docs
    accessed: 2026-11-21
  - id: s8
    title: "Amazon Polly expands Generative TTS engine with 10 new voices, 2 new regions, and Bidirectional Streaming API"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/03/amazon-polly-expands-TTS-new-voices-and-bidirectional-streaming/
    type: official-blog
    accessed: 2026-11-21
  - id: s9
    title: "Polly Generative engine upgraded with more natural speech, 14 new voices, and 2 new languages"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/10/polly-generative/
    type: official-blog
    accessed: 2026-11-21
  - id: s10
    title: "Amazon Polly now supports voice cloning"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2026/11/amazon-polly-now-supports-voice-cloning/
    type: official-blog
    accessed: 2026-11-21
created: 2026-11-21
---

# Amazon Polly

Amazon Polly is AWS's text-to-speech (TTS) service. You send it text or SSML and it returns synthesized speech, choosing from four voice engines: Generative, Long-form, Neural and Standard.[^s1] It is a pretrained, pay-per-character API service, so there is no model to train or host, and AWS states that the audio Polly generates belongs to the customer and can be cached and replayed at no extra charge.[^s5][^s6] The FAQ explicitly allows uses such as static prompts and mass notifications, and AWS positions the low-latency engines for contact centers and LLM agents.[^s6][^s9]

## How it works

The engines trade cost for naturalness. Standard is the cheapest and is offered in every Region where Polly runs. Neural voices are available in a subset of Regions, and some support a newscaster speaking style. Long-form and Generative are the premium engines, priced well above Neural.[^s1][^s5][^s6] Input can be plain text or SSML, which lets you control pauses and prosody (rate, pitch and volume). Pronunciation lexicons override how specific words such as brand names and acronyms are spoken. Speech marks return newline-delimited JSON with sentence, word, viseme and SSML timing metadata, which is used for lip-sync and text highlighting. Audio comes back as MP3, Ogg Vorbis or raw PCM.[^s6]

There are three ways to synthesize. **SynthesizeSpeech** is a synchronous request–response call that supports all four engines, SSML, lexicons and speech marks.[^s3] **StartSpeechSynthesisTask** runs asynchronously for long text. Polly queues the task, writes the speech or speech marks to an Amazon S3 bucket you must supply, and can publish a completion message, including errors, to an Amazon SNS topic you specify.[^s7] **StartSpeechSynthesisStream** is a bidirectional HTTP/2 event stream. Text goes in as TextEvent messages and audio comes back as AudioEvent messages while text is still arriving, which suits speaking an LLM's output token by token. It requires the Generative engine, does not return speech marks and is SDK-only (no AWS CLI).[^s3] AWS added Generative-engine support to this API in March 2026.[^s8]

## Quotas, pricing and gotchas

Default per-Region quotas (AWS General Reference and Polly Developer Guide, as of 2026-11):[^s2][^s4]

- SynthesizeSpeech: 3,000 billed characters (6,000 total including SSML tags, which are not billed) per request, adjustable. Output audio is capped at 10 minutes, and anything longer is cut off.[^s2][^s4]
- StartSpeechSynthesisTask: 100,000 billed (200,000 total) characters, adjustable. Up to 5 lexicons per request, not adjustable.[^s4][^s7]
- Request rates (adjustable): SynthesizeSpeech 80 TPS on Standard but 8 TPS on Neural, Long-form and Generative. StartSpeechSynthesisTask 10 TPS on Standard and Neural but 1 TPS on Long-form and Generative. StartSpeechSynthesisStream 8 TPS.[^s4]
- Lexicons: 100 per account of up to 40,000 characters each, neither adjustable. Large lexicons add latency.[^s2][^s4]
- Throttled calls return HTTP 400 with a ThrottlingException, so retry with backoff and jitter.[^s2]

Pricing is per character processed. Requesting both speech and speech marks for the same text bills the characters twice.[^s6] Prices per 1 million characters on the public pricing page (as of 2026-11) are Standard $4.00, Neural $16.00, Long-form $100.00 and Generative $30.00. The AWS GovCloud (US) prices are Standard $4.80, Neural $19.20 and Generative $36.00.[^s5] The free tier gives 5 million Standard characters per month, plus 1 million Neural, 500,000 Long-form and 100,000 Generative characters per month for the first 12 months.[^s5]

Gotchas: SSML elements such as `<audio>`, `<lexicon>`, `<lookup>` and `<voice>` are not supported, and a `<break>` is limited to 10 seconds.[^s2] Engine and voice availability varies by Region. Polly has endpoints in 25 Regions including AWS GovCloud (US-West), but FIPS endpoints exist only in the four US commercial Regions, Canada (Central) and GovCloud (US-West).[^s4][^s6] By default Polly may store text inputs to improve AWS AI services. Opting out uses an AWS Organizations AI-services opt-out policy. Polly is HIPAA eligible under the AWS BAA.[^s6]

## What changed (2026-10/11)

In October 2026 AWS put a new model behind the Generative engine as a drop-in upgrade. It improves how numbers, email addresses and street addresses are read, adds Hindi and Finnish, and extends Generative to GovCloud (US-West).[^s9] On 2026-11-19 AWS announced voice cloning, which builds a custom Generative voice from a few minutes of a consenting speaker's recordings in seven languages. It is a gated release that needs account-team approval.[^s10] Creating a cloned voice is free, and synthesis with it is billed at the Generative rate.[^s5]

## When to use it (and when not)

Use Polly when an application needs to speak: contact-center prompts, mass notifications, and voice responses for chatbots and LLM agents.[^s6][^s9] Use Standard for high-volume, cost-sensitive prompts, and the Neural, Long-form or Generative engines when naturalness justifies a 4x–25x higher character price. Pick the streaming API when text is generated incrementally, and the asynchronous task API when a single input exceeds the synchronous limits.[^s3][^s5][^s7] Because output can be cached, synthesize fixed prompts once and store them rather than paying on every playback.[^s6]

## Connections

Polly sits under `kt:aws-ai-services` and is the reverse of `kt:amazon-transcribe` (text to speech rather than speech to text). `kt:amazon-lex` and `kt:amazon-bedrock` produce the text that Polly voices in conversational apps.[^s3][^s9] It `integratesWith` `kt:amazon-s3` for asynchronous output and with `kt:amazon-sns` for task notifications.[^s7] Its rate and size limits are managed through `kt:service-quotas`, and its Regional endpoints support the `scopedTo` edge to `kt:aws-regions`.[^s4] Its HIPAA eligibility is the basis for the `compliesWith` edge to `kt:hipaa-eligible-services`.[^s6]

## Sources

[^s1]: Amazon Web Services, "Amazon Polly voice engines". https://docs.aws.amazon.com/polly/latest/dg/voice-engines-polly.html (accessed 2026-11-21)
[^s2]: Amazon Web Services, "Quotas in Amazon Polly". https://docs.aws.amazon.com/polly/latest/dg/limits.html (accessed 2026-11-21)
[^s3]: Amazon Web Services, "SynthesizeSpeech and StartSpeechSynthesisStream compared". https://docs.aws.amazon.com/polly/latest/dg/bidirectional-streaming-choosing.html (accessed 2026-11-21)
[^s4]: Amazon Web Services, "Amazon Polly endpoints and quotas (AWS General Reference)". https://docs.aws.amazon.com/general/latest/gr/pol.html (accessed 2026-11-21)
[^s5]: Amazon Web Services, "Amazon Polly Pricing". https://aws.amazon.com/polly/pricing/ (accessed 2026-11-21)
[^s6]: Amazon Web Services, "Amazon Polly FAQs". https://aws.amazon.com/polly/faqs/ (accessed 2026-11-21)
[^s7]: Amazon Web Services, "Long audio files (Amazon Polly Developer Guide)". https://docs.aws.amazon.com/polly/latest/dg/asynchronous.html (accessed 2026-11-21)
[^s8]: Amazon Web Services, "Amazon Polly expands Generative TTS engine with 10 new voices, 2 new regions, and Bidirectional Streaming API". https://aws.amazon.com/about-aws/whats-new/2026/03/amazon-polly-expands-TTS-new-voices-and-bidirectional-streaming/ (accessed 2026-11-21)
[^s9]: Amazon Web Services, "Polly Generative engine upgraded with more natural speech, 14 new voices, and 2 new languages". https://aws.amazon.com/about-aws/whats-new/2026/10/polly-generative/ (accessed 2026-11-21)
[^s10]: Amazon Web Services, "Amazon Polly now supports voice cloning". https://aws.amazon.com/about-aws/whats-new/2026/11/amazon-polly-now-supports-voice-cloning/ (accessed 2026-11-21)
