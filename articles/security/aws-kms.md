---
id: kt:aws-kms
type: Service
title: AWS Key Management Service
aliases: [AWS KMS, KMS]
domain: security
summary: >-
  AWS's managed service for creating and controlling the cryptographic keys that encrypt and sign
  data, with keys held in FIPS 140-3 Security Level 3 validated HSMs, envelope encryption through
  data keys, and native integration with most AWS storage and database services.
verified: 2026-11-09
status: current
broader: [kt:aws-security-services]
related: [kt:aws-iam, kt:aws-secrets-manager]
relations:
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: alternativeTo, object: kt:aws-cloudhsm}
  - {predicate: governedBy, object: kt:kms-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "AWS Key Management Service (overview)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kms/latest/developerguide/overview
    type: official-docs
    accessed: 2026-11-09
  - id: s2
    title: "AWS Key Management Service FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/kms/faqs/
    type: official-docs
    accessed: 2026-11-09
  - id: s3
    title: "AWS Key Management Service pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/kms/pricing/
    type: official-docs
    accessed: 2026-11-09
  - id: s4
    title: "Request quotas - AWS Key Management Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kms/latest/developerguide/requests-per-second.html
    type: official-docs
    accessed: 2026-11-09
  - id: s5
    title: "How multi-Region keys work - AWS Key Management Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/kms/latest/developerguide/mrk-how-it-works.html
    type: official-docs
    accessed: 2026-11-09
  - id: s6
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-09
created: 2026-11-09
---

# AWS Key Management Service

AWS Key Management Service (AWS KMS) is a managed service for creating and controlling the keys that encrypt and sign your data. KMS keys are created, used and deleted entirely inside the service and never leave it unencrypted; you use them only by calling the KMS API. AWS states that these keys are protected by hardware security modules validated to FIPS 140-3 Security Level 3, and there are no contracts or minimum purchases.[^s1] Its job in a key hierarchy is to protect the top-level (root) keys, while key policies restrict who can use each key.[^s1]

## How it works

**Key ownership.** There are three kinds of KMS key. *Customer managed keys* are created by you; you write their key policies and can grant use to other accounts and services. *AWS managed keys* live in your account but are created and controlled by an AWS service on your behalf; their use is logged in CloudTrail, but you cannot manage them directly. *AWS owned keys* belong to AWS, are shared across accounts, and give you neither policy control nor CloudTrail records.[^s2]

**Envelope encryption.** KMS encrypts at most 4 KB directly. For larger data it issues a data key: the application encrypts the data locally with the plaintext data key, stores the KMS-encrypted copy of that data key alongside the ciphertext, and later asks KMS to decrypt the data key.[^s2] This is how integrated services such as Amazon S3 with SSE-KMS work, and it is why every upload or download of an SSE-KMS object becomes a `GenerateDataKey` or `Decrypt` call against your account.[^s4]

**Rotation and lifecycle.** Automatic rotation is configurable between 90 and 2,560 days, and on-demand rotation is limited to 10 rotations over a key's lifetime. A key you delete first goes through a waiting period of 7 to 30 days (default 30) so the deletion can be cancelled.[^s2]

**Where key material lives.** You can import your own key material, which you may expire or delete on demand. Custom key stores keep keys in an AWS CloudHSM cluster you control (at least two HSMs, no automatic rotation) or in an external key manager outside AWS (the external key store), which supports only a subset of operations and fails requests that take longer than 500 ms.[^s2]

**Multi-Region keys.** KMS keys are Regional by default. A multi-Region primary key (key ID prefix `mrk-`) can be replicated to other Regions in the same AWS partition; replicas share the key ID and key material, so ciphertext produced in one Region decrypts in another. Key policy, grants, aliases, tags and enabled state stay independent per Region, and the single-Region/multi-Region choice is fixed at creation.[^s5]

## Quotas, pricing and gotchas

- **Price (as of 2026-11, no Region-specific KMS rates listed):** each customer managed key costs $1 per month, prorated hourly, whatever its type. The first and second rotations each add $1 per month, and later rotations add nothing. AWS managed and AWS owned keys have no monthly fee, but requests made with AWS managed keys are charged. The free tier covers 20,000 requests per month across all Regions, excluding asymmetric and data-key-pair operations. The pricing page's S3 example uses $0.03 per 10,000 requests. For cross-account use, the calling account pays.[^s3]
- **Request quota (as of 2026-11):** symmetric cryptographic operations share a per-account, per-Region pool of 100,000 requests per second in us-east-1, us-west-2 and eu-west-1; 20,000 in a list of large Regions such as us-east-2 and eu-central-1; and 10,000 elsewhere. The quota is adjustable through Service Quotas.[^s4]
- **Who uses up the quota:** calls that AWS services make on your behalf and calls to AWS managed keys count against it; AWS owned keys do not. Over-quota calls are throttled, and in cross-account use the caller is throttled, not the key owner.[^s4]
- **Deletion is gated:** a key pending deletion cannot be used in cryptographic operations, and KMS will not delete a multi-Region primary key until all of its replicas are deleted.[^s5] Deleting imported key material, by contrast, takes effect immediately with no waiting period.[^s2]

## When to use it (and when not)

Use KMS whenever an AWS service offers encryption at rest with your own key, or when applications need to encrypt, sign or generate MACs without handling root keys. Pick customer managed keys when you need your own key policy, cross-account sharing, audit of each use, or control over rotation. AWS managed keys suit cases that only need encryption on by default.[^s2] Choose a CloudHSM-backed custom key store or AWS CloudHSM directly when you must run your own single-tenant HSMs, and an external key store when regulation requires keys to stay outside AWS, accepting extra latency and fewer features.[^s2] Because KMS keys never leave the service unencrypted, KMS is the place to hold root keys, not the application data or credentials they protect.[^s1]

## Connections

- **Broader:** one of the AWS security services. It is in scope for the AWS Certified Cloud Practitioner (CLF-C02) exam under Security, Identity, and Compliance.[^s6]
- **Monitored by AWS CloudTrail**, which records all KMS API requests, both management and cryptographic.[^s2]
- **Integrates with Amazon S3** and other services through envelope encryption, and their calls count toward your request quota.[^s4]
- **Alternative to AWS CloudHSM** when you need dedicated HSMs; KMS can also use a CloudHSM cluster as a custom key store.[^s2]
- **Governed by KMS request quotas** and **scoped to AWS Regions**, with multi-Region keys limited to a single partition.[^s4][^s5]
- **Related to AWS IAM** and AWS Secrets Manager: access to every key is controlled by its key policy, which decides which trusted principals may use it.[^s1]

## Sources
[^s1]: Amazon Web Services, "AWS Key Management Service (overview)". https://docs.aws.amazon.com/kms/latest/developerguide/overview (accessed 2026-11-09)
[^s2]: Amazon Web Services, "AWS Key Management Service FAQs". https://aws.amazon.com/kms/faqs/ (accessed 2026-11-09)
[^s3]: Amazon Web Services, "AWS Key Management Service pricing". https://aws.amazon.com/kms/pricing/ (accessed 2026-11-09)
[^s4]: Amazon Web Services, "Request quotas - AWS Key Management Service". https://docs.aws.amazon.com/kms/latest/developerguide/requests-per-second.html (accessed 2026-11-09)
[^s5]: Amazon Web Services, "How multi-Region keys work - AWS Key Management Service". https://docs.aws.amazon.com/en_us/kms/latest/developerguide/mrk-how-it-works.html (accessed 2026-11-09)
[^s6]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-09)
