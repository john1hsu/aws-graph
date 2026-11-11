---
id: kt:amazon-textract
type: Service
title: Amazon Textract
aliases: [Textract, AWS Textract]
domain: ai-and-ml
summary: >-
  AWS's pretrained machine-learning service that extracts printed and handwritten text, forms
  (key-value pairs), tables, signatures, layout and answers to natural-language queries from
  scanned documents and images, with specialised APIs for invoices and receipts, identity
  documents and mortgage loan packages.
verified: 2026-11-11
status: current
broader: [kt:aws-ai-services]
related: [kt:amazon-comprehend, kt:amazon-bedrock, kt:aws-lambda]
relations:
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:amazon-sns}
  - {predicate: alternativeTo, object: kt:amazon-bedrock-data-automation}
  - {predicate: compliesWith, object: kt:hipaa-eligible-services}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "Amazon Textract FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/textract/faqs
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Set Quotas in Amazon Textract (hard limits)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/textract/latest/dg/limits-document.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Calling Amazon Textract Asynchronous Operations"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/textract/latest/dg/api-async.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Amazon Textract Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/textract/pricing/
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Accelerate intelligent document processing with generative AI on AWS"
    publisher: AWS Machine Learning Blog
    url: https://aws.amazon.com/blogs/machine-learning/accelerate-intelligent-document-processing-with-generative-ai-on-aws
    type: official-blog
    accessed: 2026-11-11
  - id: s6
    title: "AWS Certified AI Practitioner (AIF-C01): In-scope AWS services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/it_it/aws-certification/latest/userguide/aif-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# Amazon Textract

Amazon Textract is a fully managed, pretrained machine-learning service that reads documents. Its `DetectDocumentText` API does plain optical character recognition (OCR), while `AnalyzeDocument` goes further and returns structure: key-value pairs from forms, tables, signatures, page layout, and answers to natural-language Queries, so the output keeps the relationships a person sees on the page. Specialised APIs handle invoices and receipts (`AnalyzeExpense`), identity documents (`AnalyzeID`) and mortgage loan packages (Analyze Lending, which classifies pages and routes them to the right extraction). No ML expertise or model training is needed; Custom Queries adapters let you tune the pretrained Queries feature with your own labelled samples.[^s1] As of 2026-11 the service is generally available in US East (N. Virginia, Ohio), US West (Oregon, N. California), both AWS GovCloud (US) Regions, Canada (Central), several European Regions and five Asia Pacific Regions.[^s1]

## How it works

There are two calling modes. **Synchronous** operations take a document as bytes or an S3 object and return results in the response; they suit single-page, latency-sensitive work. **Asynchronous** operations (`StartDocumentTextDetection`, `StartDocumentAnalysis`, `StartExpenseAnalysis`, `StartLendingAnalysis`) read a multipage PDF or TIFF from Amazon S3 and return a `JobId`.[^s3] You can pass a `NotificationChannel` naming an Amazon SNS topic in the same Region plus an IAM role Textract may assume to publish to it; when the job finishes Textract posts a JSON message with the job ID, status and optional `JobTag`. AWS recommends consuming that message with AWS Lambda or an SQS queue subscribed to the topic, then calling the matching `Get*` operation, rather than polling `Get*` in a loop, which is throttled.[^s3] Results come back as a paginated list of `Blocks` (PAGE, LINE, WORD and so on), at most 1,000 per page of results.[^s3]

By default asynchronous results sit in a Textract-owned bucket, encrypted, and can be retrieved for only 7 days after the job starts. Setting `OutputConfig` writes them to your own S3 bucket instead, optionally with a customer managed KMS key, which then needs the matching key permissions on the role.[^s3] An optional `ClientRequestToken` makes `Start*` calls idempotent for 7 days.[^s3]

## Quotas, pricing and gotchas

Hard limits that cannot be raised (as of 2026-11): JPEG, PNG, PDF and TIFF input only, with no password-protected or XFA-based PDFs; 10 MB per document for synchronous calls (one page for PDF/TIFF); and 500 MB and 3,000 pages for asynchronous PDF/TIFF. Images may be at most 10,000 pixels on any side, and text should be at least 15 pixels tall (about 8-point type at 150 DPI).[^s2] Language support is uneven: text detection, tables, signatures and layout work in more than 40 languages, forms only in English, Spanish, Italian, Portuguese, French and German, handwriting and the other APIs in English only. Queries are capped at 15 per page synchronously and 30 asynchronously.[^s2] Transactions-per-second and concurrent-job quotas are separate per API and per Region and are adjustable; exceeding concurrent jobs makes `Start*` fail with `LimitExceededException`, so bursty workloads should be buffered in Amazon SQS.[^s3]

Billing is per page processed, with no minimums, and each feature you request is priced separately. AWS's US West (Oregon) examples (as of 2026-11) list $0.0015 per page for `DetectDocumentText` for the first million pages in a month and $0.0006 after that, $0.015 per page for Tables and $0.05 per page for Forms for the first million pages.[^s4] Asking for Forms when you only need raw text therefore costs over 30 times more, so request only the feature types you use.

On data use, Textract may store and use document content to improve AWS AI services unless your organization applies an AWS Organizations AI services opt-out policy. Content is encrypted in transit and at rest. The service is HIPAA eligible under a Business Associate Agreement and reachable privately through AWS PrivateLink VPC endpoints.[^s1]

## When to use it (and when not)

Use Textract when you need deterministic, geometry-aware extraction (bounding boxes, confidence scores, table cells) from scanned forms, invoices, IDs or loan files at scale. For loosely structured documents where you want classification, summarisation or schema-driven extraction, AWS's GenAI Intelligent Document Processing Accelerator points most users to Amazon Bedrock Data Automation. Its alternative pipeline mode pairs Textract OCR with Amazon Bedrock models when each step needs custom control.[^s5] Downstream language tasks such as entity or sentiment detection on the extracted text belong to Amazon Comprehend.

## Connections

Textract is one of the pretrained AWS AI services, alongside Amazon Comprehend, and the current AWS Certified AI Practitioner (AIF-C01) exam guide lists both as in-scope machine-learning services.[^s6] Asynchronous jobs depend on Amazon S3 for input and output and on Amazon SNS for completion notifications, usually consumed by AWS Lambda.[^s3] It is a Regional service and HIPAA eligible.[^s1] Amazon Bedrock Data Automation is the generative-AI alternative for document processing, and Bedrock models can post-process Textract output.[^s5]

## Sources
[^s1]: Amazon Web Services, "Amazon Textract FAQs". https://aws.amazon.com/textract/faqs (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Set Quotas in Amazon Textract". https://docs.aws.amazon.com/textract/latest/dg/limits-document.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Calling Amazon Textract Asynchronous Operations". https://docs.aws.amazon.com/textract/latest/dg/api-async.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Amazon Textract Pricing". https://aws.amazon.com/textract/pricing/ (accessed 2026-11-11)
[^s5]: AWS Machine Learning Blog, "Accelerate intelligent document processing with generative AI on AWS" (2025-08-22, updated 2026-03-05). https://aws.amazon.com/blogs/machine-learning/accelerate-intelligent-document-processing-with-generative-ai-on-aws (accessed 2026-11-11)
[^s6]: Amazon Web Services, "AWS Certified AI Practitioner (AIF-C01): In-scope AWS services". https://docs.aws.amazon.com/it_it/aws-certification/latest/userguide/aif-01-in-scope-services.html (accessed 2026-11-11)
