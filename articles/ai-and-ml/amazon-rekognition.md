---
id: kt:amazon-rekognition
type: Service
title: Amazon Rekognition
aliases: [Rekognition, AWS Rekognition, Rekognition Image, Rekognition Video]
domain: ai-and-ml
summary: >-
  AWS's pretrained deep-learning computer-vision service that detects labels, text, faces,
  celebrities, unsafe content and personal protective equipment in images and stored video,
  searches faces against managed face collections, and runs Face Liveness checks, with
  Custom Labels for training use-case-specific detectors.
verified: 2026-11-16
status: current
broader: [kt:aws-ai-services]
related: [kt:amazon-textract, kt:amazon-bedrock, kt:amazon-sagemaker-ai]
relations:
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: integratesWith, object: kt:amazon-kinesis-video-streams}
  - {predicate: compliesWith, object: kt:hipaa-eligible-services}
  - {predicate: governedBy, object: kt:rekognition-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "What is Amazon Rekognition?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/rekognition/latest/dg/what-is.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Guidelines and quotas in Amazon Rekognition"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/rekognition/latest/dg/limits.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Amazon Rekognition Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/rekognition/pricing/
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Amazon Rekognition feature availability changes"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/rekognition/latest/dg/rekognition-availability-changes.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "People pathing (end of support notice)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/rekognition/latest/dg/persons.html
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "Transitioning from Amazon Rekognition people pathing: Exploring other alternatives"
    publisher: AWS Machine Learning Blog
    url: https://aws.amazon.com/blogs/machine-learning/transitioning-from-amazon-rekognition-people-pathing-exploring-other-alternatives/
    type: official-blog
    accessed: 2026-11-16
  - id: s7
    title: "Amazon Rekognition Face Liveness launches accuracy improvements and new challenge setting for improved UX"
    publisher: What's New with AWS
    url: https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-rekognition-face-liveness-accuracy-improvements-challenge-setting
    type: official-blog
    accessed: 2026-11-16
  - id: s8
    title: "AWS Certified AI Practitioner (AIF-C01): In-scope AWS services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/it_it/aws-certification/latest/userguide/aif-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-16
created: 2026-11-16
---

# Amazon Rekognition

Amazon Rekognition is AWS's managed computer-vision service. It is built on deep learning, needs no machine-learning expertise, and is called through a simple API against images and videos, typically ones already stored in Amazon S3.[^s1] Image analysis covers object, scene and concept labels, printed and handwritten text, unsafe-content moderation, celebrity recognition, facial analysis and comparison, image properties (quality, colour, sharpness, contrast) and personal protective equipment (PPE) detection. Video analysis applies labels, text, faces, celebrities and moderation to stored video and adds segment detection such as black frames and end credits.[^s1] Two customisation paths exist: **Custom Labels** trains a project-specific detector for things like logos or products, and **adapters** tune the pretrained moderation and label models with your own sample images.[^s1]

## How it works

The **Image** APIs are synchronous: send JPEG or PNG bytes or an S3 object reference and get labels, bounding boxes and confidence scores back.[^s2] The **Video** APIs for stored video are asynchronous jobs that read an H.264 MPEG-4 or MOV file from S3; results are paged with a `NextToken` that lives 24 hours.[^s2]

Face search is a two-step pattern. You first *index* faces into a **face collection**, a server-side index that stores face vectors (not the images), and can group them into user vectors; you then *search* new images, stored video or streams against that collection.[^s1] A collection holds up to 20 million face vectors and, by default, 10 million user vectors, and a search returns at most 4,096 matches.[^s2]

**Face Liveness** is a managed check, used during face-based identity verification, that a real person is in front of the camera. It targets spoofs such as printed or digital photos, replayed videos, 3D masks and deepfakes injected into the capture path.[^s1] In July 2025 AWS improved its accuracy and added a `FaceMovementChallenge` setting, chosen in `CreateFaceLivenessSession`, that drops the flashing-light sequence. That makes each check about 3 seconds shorter, and it works with front or rear cameras. `FaceMovementAndLightChallenge` remains the most accurate option.[^s7]

## Quotas, pricing and gotchas

Fixed quotas (as of 2026-11, not adjustable): 15 MB for an image in S3 and 5 MB for raw image bytes (4 MB for PPE); minimum 80×80 pixels; faces should be at least 40×40 pixels in a 1920×1080 frame; `DetectText` returns up to 100 words per image. Stored video is capped at 10 GB and 6 hours per file, with 20 concurrent jobs per account.[^s2] Transactions-per-second quotas are adjustable per API and per Region. Throttled calls return retryable `ThrottlingException` or `ProvisionedThroughputExceededException` errors, so AWS advises smoothing spikes and retrying with exponential backoff and jitter before requesting an increase.[^s2]

Pricing is pay-as-you-go with no minimums, and the per-unit rate drops as monthly volume grows.[^s1] The pricing page's US East examples (as of 2026-11) list $0.0010 per image for the first million images a month in both Image API groups, falling in tiers to $0.0004 (Group 1, such as `IndexFaces`) or $0.00025 (Group 2, such as `DetectLabels`). Stored face and user vectors cost $0.00001 each per month. Stored-video label detection and content moderation cost $0.10 per minute. Custom Labels costs $1 per training hour and $4 per inference hour, so an idle model left running is billed.[^s3] The free tier gives 1,000 images a month per Image group and 60 video minutes a month for 12 months.[^s3]

Lifecycle gotchas: **People Pathing** was discontinued on 31 October 2025, after closing to new customers in October 2024, and AWS pointed customers to open-source trackers such as YOLOv9 with ByteTrack running on Lambda or SageMaker AI.[^s5][^s6] **Streaming video analysis and Bulk image analysis** closed to new customers on 30 April 2026. Accounts that used them in the previous 12 months keep access. AWS recommends sending sampled stream frames or S3 batches to the Image API instead.[^s4] The service as a whole remains generally available.

## When to use it (and when not)

Use Rekognition for high-volume, deterministic vision tasks that return structured output: content moderation queues, face matching for identity verification with Face Liveness, PPE compliance checks, and searchable media libraries.[^s1] For text-heavy documents such as forms, tables and invoices, Amazon Textract preserves document structure better than `DetectText`, which is limited to 100 words.[^s2] Multimodal foundation models on Amazon Bedrock suit open-ended questions about an image, and Amazon SageMaker AI suits a fully custom vision model that Custom Labels can't express, at the cost of running your own pipeline.

## Connections

Rekognition belongs to the pretrained AWS AI services alongside Amazon Textract. The current AWS Certified AI Practitioner (AIF-C01) exam guide lists it as an in-scope Machine Learning service.[^s8] It reads input from Amazon S3, is commonly driven by AWS Lambda, and is secured with IAM.[^s1] Its legacy streaming face search consumes Amazon Kinesis Video Streams through stream processors, one per input stream.[^s2] It is HIPAA eligible.[^s1] Hard limits are summarised under its quota node.[^s2]

## Sources
[^s1]: Amazon Web Services, "What is Amazon Rekognition?". https://docs.aws.amazon.com/rekognition/latest/dg/what-is.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Guidelines and quotas in Amazon Rekognition". https://docs.aws.amazon.com/rekognition/latest/dg/limits.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Amazon Rekognition Pricing". https://aws.amazon.com/rekognition/pricing/ (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Amazon Rekognition feature availability changes". https://docs.aws.amazon.com/rekognition/latest/dg/rekognition-availability-changes.html (accessed 2026-11-16)
[^s5]: Amazon Web Services, "People pathing". https://docs.aws.amazon.com/rekognition/latest/dg/persons.html (accessed 2026-11-16)
[^s6]: AWS Machine Learning Blog, "Transitioning from Amazon Rekognition people pathing: Exploring other alternatives" (2024-10-24). https://aws.amazon.com/blogs/machine-learning/transitioning-from-amazon-rekognition-people-pathing-exploring-other-alternatives/ (accessed 2026-11-16)
[^s7]: What's New with AWS, "Amazon Rekognition Face Liveness launches accuracy improvements and new challenge setting for improved UX" (2025-07-03). https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-rekognition-face-liveness-accuracy-improvements-challenge-setting (accessed 2026-11-16)
[^s8]: Amazon Web Services, "AWS Certified AI Practitioner (AIF-C01): In-scope AWS services". https://docs.aws.amazon.com/it_it/aws-certification/latest/userguide/aif-01-in-scope-services.html (accessed 2026-11-16)
