---
id: kt:kms-quotas
type: Limit
title: AWS KMS quotas
aliases: [KMS quotas, AWS KMS limits, KMS limits, AWS Key Management Service quotas, KMS request quotas, KMS API rate limits]
domain: security
summary: >-
  The per-account, per-Region limits on AWS Key Management Service: resource quotas on keys, aliases,
  grants, custom key stores and on-demand rotations, and request-per-second quotas that throttle
  cryptographic and management API calls, including calls AWS services make on your behalf.
verified: 2026-11-17
status: current
broader: [kt:aws-kms]
related: [kt:amazon-s3-quotas, kt:aws-cloudhsm, kt:iam-quotas]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:amazon-s3-quotas}
sources:
  - id: s1
    title: "Resource quotas - AWS Key Management Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kms/latest/developerguide/resource-limits.html
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Request quotas - AWS Key Management Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kms/latest/developerguide/requests-per-second.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Throttling AWS KMS requests - AWS Key Management Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kms/latest/developerguide/throttling.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "Reducing the cost of SSE-KMS with Amazon S3 Bucket Keys"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonS3/latest/userguide//bucket-key.html
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "Manage your AWS KMS API request rates using Service Quotas and Amazon CloudWatch"
    publisher: Amazon Web Services (AWS Security Blog)
    url: https://aws.amazon.com/blogs/security/manage-your-aws-kms-api-request-rates-using-service-quotas-and-amazon-cloudwatch
    type: official-blog
    accessed: 2026-11-17
created: 2026-11-17
---

# AWS KMS quotas

AWS Key Management Service enforces two families of limits. **Resource quotas** cap how many KMS objects (keys, aliases, grants, custom key stores) an account can hold, and an attempt to create one more returns a `LimitExceededException`.[^s1] **Request quotas** cap how many API calls per second an account can make in a Region; calls over the limit are rejected with a `ThrottlingException` (HTTP 400).[^s2][^s3] Because nearly every encrypted AWS service calls KMS on your behalf, request quotas are the ones that usually bite in production. All figures below are defaults as of 2026-11.

## Resource quotas

- **KMS keys:** 100,000 per account per Region, adjustable. Only customer managed keys count, whatever their key spec or state; AWS managed and AWS owned keys do not.[^s1]
- **Aliases per KMS key:** 50, adjustable. This replaced an older per-Region alias quota. A key with more than 50 aliases also breaks access granted through the `kms:ResourceAliases` condition key.[^s1]
- **Grants per KMS key:** 50,000 per customer managed key, adjustable, and grants created by integrated services count. In effect this caps concurrent grant-authorized operations on one key: if every EBS volume uses the same key, no more than 50,000 of them can be attached at once, and new grants are possible only after old ones are retired or revoked.[^s1]
- **Custom key stores:** 10 per account per Region (CloudHSM and external stores combined, connected or not), adjustable.[^s1]
- **On-demand rotations:** 25 per key, **not adjustable** by Service Quotas or Support; AWS recommends automatic rotation instead.[^s1]

## Request quotas

Request quotas are per account and per Region, cover requests from every principal in the account (including AWS services), and are shared between FIPS and non-FIPS endpoints.[^s2]

- **Symmetric cryptographic operations** (Encrypt, Decrypt, GenerateDataKey, ReEncrypt, GenerateRandom, and the HMAC operations) share one pool: 100,000 RPS in us-east-1, us-west-2 and eu-west-1; 20,000 RPS in us-east-2, ap-southeast-1, ap-southeast-2, ap-northeast-1, eu-central-1, eu-west-2 and eu-south-2; and 10,000 RPS in other Regions. Any mix of these operations draws on the same total.[^s2]
- **Asymmetric operations** have separate 1,000 RPS pools for RSA, ECC/SM2, ML-DSA and ML-KEM keys, independent of each other and of the symmetric pool.[^s2]
- **GenerateDataKeyPair** has its own much lower rates that fall as the key size grows: 100 RPS for ECC and ML-KEM specs, 20 for RSA_2048, 4 for RSA_3072 and 1 for RSA_4096 (lower again in the China Regions).[^s2]
- **Management APIs** each have an independent rate, for example 2,000 RPS for DescribeKey, 50 for CreateGrant, 15 for PutKeyPolicy and ScheduleKeyDeletion, and 5 for CreateKey.[^s2]
- **Custom key stores** add a second, per-store limit of 1,800 RPS for cryptographic operations on top of the account pool. These per-store limits don't appear in Service Quotas, and a busy CloudHSM cluster can throttle below that figure.[^s2][^s3]

Almost every quota can be raised through Service Quotas, or through AWS Support where Service Quotas does not list it. The exceptions are the on-demand rotation quota and the CloudHSM key store request quota. The request-quota page also marks the external key store limit as not adjustable.[^s2][^s3]

## Gotchas

**Your services spend your quota.** Server-side encryption in S3, EBS and other services generates KMS calls that count against your account, and in cross-account use the calling account is throttled, not the key owner. AWS owned keys are the exception and never count.[^s2] The S3 Bucket Key feature caches a short-lived bucket-level key in S3, which AWS says can cut KMS request costs by up to 99 percent. It also reduces quota pressure, but it applies only to objects written after it is enabled. It also changes the encryption context and CloudTrail entries to the bucket ARN, and it does not support DSSE-KMS.[^s4]

**Hot keys throttle early.** Repeated state-changing calls on one key, such as toggling EnableKey and DisableKey, can be throttled even below the per-operation quota.[^s3]

**Design for throttling.** Use backoff and retry (many SDKs retry 400 errors automatically), make only the calls you need, and reuse data keys where the AWS Encryption SDK's data key caching fits.[^s3][^s2] To catch trouble early, watch utilization in the Service Quotas console and create a CloudWatch alarm from there; AWS's security blog suggests 80 percent of the applied quota as a starting threshold.[^s5]

## Connections

These quotas are the limits **of AWS KMS** and are **configured via Service Quotas**, where increases are requested and usage is charted. They are **monitored by Amazon CloudWatch** alarms on that usage.[^s2][^s5] They are **scoped to AWS Regions**, since each Region keeps its own counts and rate pools.[^s1][^s2] They **contrast with Amazon S3 quotas**, because S3's SSE-KMS throughput is bounded by the KMS request rate rather than by S3's own limits.[^s2][^s4] **AWS CloudHSM** is related through custom key stores, which carry their own per-store request limit, and **IAM quotas** are a sibling set of account-level security limits.[^s2]

## Sources
[^s1]: Amazon Web Services, "Resource quotas - AWS Key Management Service". https://docs.aws.amazon.com/kms/latest/developerguide/resource-limits.html (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Request quotas - AWS Key Management Service". https://docs.aws.amazon.com/kms/latest/developerguide/requests-per-second.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Throttling AWS KMS requests - AWS Key Management Service". https://docs.aws.amazon.com/kms/latest/developerguide/throttling.html (accessed 2026-11-17)
[^s4]: Amazon Web Services, "Reducing the cost of SSE-KMS with Amazon S3 Bucket Keys". https://docs.aws.amazon.com/AmazonS3/latest/userguide//bucket-key.html (accessed 2026-11-17)
[^s5]: Amazon Web Services, "Manage your AWS KMS API request rates using Service Quotas and Amazon CloudWatch", AWS Security Blog. https://aws.amazon.com/blogs/security/manage-your-aws-kms-api-request-rates-using-service-quotas-and-amazon-cloudwatch (accessed 2026-11-17)
