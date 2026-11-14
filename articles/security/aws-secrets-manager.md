---
id: kt:aws-secrets-manager
type: Service
title: AWS Secrets Manager
aliases: [Secrets Manager]
domain: security
summary: >-
  AWS's managed service for storing, retrieving, rotating and auditing secrets such as database
  credentials, API keys and OAuth tokens, encrypted with AWS KMS and optionally replicated to other
  Regions, so applications fetch credentials at runtime instead of hard-coding them.
verified: 2026-11-14
status: current
broader: [kt:aws-security-services]
related: [kt:aws-iam, kt:aws-privatelink, kt:amazon-rds]
relations:
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: dependsOn, object: kt:aws-lambda}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: alternativeTo, object: kt:aws-systems-manager}
  - {predicate: governedBy, object: kt:secrets-manager-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is AWS Secrets Manager?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "AWS Secrets Manager FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/secrets-manager/faqs
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "AWS Secrets Manager pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/secrets-manager/pricing/
    type: official-docs
    accessed: 2026-11-14
  - id: s4
    title: "AWS Secrets Manager quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_limits.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "Replicate AWS Secrets Manager secrets across Regions"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/secretsmanager/latest/userguide/replicate-secrets.html
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "delete-secret - AWS CLI Command Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-secret.html
    type: official-docs
    accessed: 2026-11-14
  - id: s7
    title: "AWS Secrets Manager best practices"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/secretsmanager/latest/userguide/best-practices
    type: official-docs
    accessed: 2026-11-14
  - id: s8
    title: "AWS Secrets Manager announces managed external secrets"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/11/aws-secrets-manager-managed-external-secrets
    type: official-blog
    accessed: 2026-11-14
  - id: s9
    title: "AWS Secrets Manager adds managed external secrets support for Cisco Security Platform and Netskope"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/08/secrets-manager-cisco-netskope/
    type: official-blog
    accessed: 2026-11-14
  - id: s10
    title: "How to choose the right AWS service for managing secrets and configurations"
    publisher: Amazon Web Services (AWS Security Blog)
    url: https://aws.amazon.com/blogs/security/how-to-choose-the-right-aws-service-for-managing-secrets-and-configurations
    type: official-blog
    accessed: 2026-11-14
  - id: s11
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-14
created: 2026-11-14
---

# AWS Secrets Manager

AWS Secrets Manager manages the lifecycle of secrets: you create, retrieve, rotate and finally delete them through one service. Applications call Secrets Manager at runtime for database credentials, application credentials, OAuth tokens or API keys, which takes those values out of source code and configuration files.[^s1] AWS points other secret types elsewhere: AWS credentials to IAM, encryption keys to AWS KMS, SSH keys to EC2 Instance Connect, and private keys and certificates to AWS Certificate Manager.[^s1]

## How it works

**Storage and encryption.** A secret is a JSON document or other text of up to 64 KB.[^s2] Secrets Manager encrypts it at rest with envelope encryption (AES-256) under a KMS key and returns it over TLS; by default it does not write retrieved secrets to persistent storage.[^s2] The AWS managed key `aws/secretsmanager` is free to use, while a customer managed key is billed at KMS rates.[^s1]

**Versions and rotation.** Each update creates a new version labelled with staging labels. Automatic rotation swaps long-lived credentials for short-lived ones without redeploying clients, because clients read the current value at runtime.[^s1] Most rotation runs through an AWS Lambda function billed at Lambda rates; *managed rotation* needs no function.[^s1][^s2] Native rotation covers Amazon RDS, Amazon DocumentDB and Amazon Redshift credentials, and custom functions handle other targets.[^s2] Schedules can run as often as every four hours, using either a single-user strategy or an alternating-users strategy that keeps the previous credentials valid during the switch.[^s7] Rotation does not re-authenticate database connections that are already open.[^s2]

**Replication.** A secret can be replicated to other Regions; data and metadata such as tags and resource policies are copied, and the replica's ARN differs only by Region. You rotate the primary and the new value propagates to the replicas. Each replica can use a KMS key in its own Region, and a replica can be promoted to a standalone secret. Replication stays inside one partition, so secrets cannot cross between standard Regions and AWS GovCloud (US) or China Regions.[^s5]

## Quotas, pricing and gotchas

- **Price (as of 2026-11, no per-Region rates shown on the page):** $0.40 per secret per month and $0.05 per 10,000 API calls; the pricing page's ephemeral-secret example works out to $0.00056 per secret per hour. New versions created by rotation are not charged, and secrets marked for deletion are free.[^s3][^s1] Replica secrets are billed as well.[^s5]
- **Quotas (as of 2026-11, per account per Region, listed as not adjustable):** 500,000 secrets; 65,536 bytes per secret value; 100 versions and 20 staging labels per secret; 10,000 TPS for `GetSecretValue`, 40,000 for `DescribeSecret`, 100 for `BatchGetSecretValue`, and 50 for write operations such as `PutSecretValue` and `UpdateSecret` combined.[^s4]
- **Throttling falls on the caller:** in cross-account reads, the requesting account's quota is consumed, not the owner's. AWS recommends exponential backoff with jitter, and warns that updating a secret more than once every 10 minutes can exhaust the version quota.[^s4]
- **Deletion has a recovery window** of 7 to 30 days (default 30), during which `RestoreSecret` works; forced deletion is permanent. A primary with replicas cannot be deleted until the replicas are removed.[^s6]
- **Cache client-side.** AWS publishes caching libraries for Java, Python, .NET, Go and Rust, plus the Parameters and Secrets Lambda Extension, so you call the API only when a secret changes.[^s7]
- **Network restrictions can break rotation.** Policies using `aws:SourceVpc`, `aws:SourceVpce` or `aws:SourceIp` can block rotation functions and other service callers that run outside your VPC or IP range.[^s7]

## Managed external secrets

In November 2025 AWS launched *managed external secrets* for third-party SaaS credentials. They use a provider-defined format and have rotation on by default without a Lambda function. Salesforce, BigID and Snowflake were the first partners, and the feature is available in every Region that has Secrets Manager.[^s8] By August 2026 the partner list had grown to include Datadog, GitLab, MongoDB Atlas, Okta and Netskope, among others.[^s9]

## When to use it (and when not)

AWS's guidance is to use Secrets Manager for sensitive credentials that need rotation, cross-account access or multi-Region resilience. Systems Manager Parameter Store suits basic configuration and non-sensitive values: standard parameters are free, but it has no native rotation and is single-Region. AWS AppConfig fits validated, staged configuration rollouts.[^s10] For a handful of static values the $0.40 per-secret fee can outweigh the benefits; for anything that should rotate or be shared across accounts and Regions, Secrets Manager is the purpose-built choice.[^s10][^s3]

## Connections

- **Broader:** one of the AWS security services. It is listed in scope for the AWS Certified Cloud Practitioner (CLF-C02) exam under Security, Identity, and Compliance.[^s11]
- **Secured by AWS KMS**, which provides the envelope-encryption keys, and **depends on AWS Lambda** for non-managed rotation.[^s1][^s2]
- **Monitored by AWS CloudTrail**, which records Secrets Manager API calls as management events; CloudWatch, AWS Config and GuardDuty add metrics, compliance checks and threat detection.[^s1][^s7]
- **Related to AWS IAM** (identity and resource policies, ABAC) and **AWS PrivateLink** (interface VPC endpoints for private access).[^s7]
- **Related to Amazon RDS**, whose credentials it rotates natively.[^s2]
- **Alternative to AWS Systems Manager** Parameter Store for lower-risk configuration data.[^s10]
- **Governed by Secrets Manager quotas** and **scoped to AWS Regions**, with replication limited to a partition.[^s4][^s5]

## Sources
[^s1]: Amazon Web Services, "What is AWS Secrets Manager?". https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html (accessed 2026-11-14)
[^s2]: Amazon Web Services, "AWS Secrets Manager FAQs". https://aws.amazon.com/secrets-manager/faqs (accessed 2026-11-14)
[^s3]: Amazon Web Services, "AWS Secrets Manager pricing". https://aws.amazon.com/secrets-manager/pricing/ (accessed 2026-11-14)
[^s4]: Amazon Web Services, "AWS Secrets Manager quotas". https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_limits.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, "Replicate AWS Secrets Manager secrets across Regions". https://docs.aws.amazon.com/secretsmanager/latest/userguide/replicate-secrets.html (accessed 2026-11-14)
[^s6]: Amazon Web Services, "delete-secret - AWS CLI Command Reference". https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-secret.html (accessed 2026-11-14)
[^s7]: Amazon Web Services, "AWS Secrets Manager best practices". https://docs.aws.amazon.com/secretsmanager/latest/userguide/best-practices (accessed 2026-11-14)
[^s8]: Amazon Web Services, "AWS Secrets Manager announces managed external secrets" (What's New, 2025-11-19). https://aws.amazon.com/about-aws/whats-new/2025/11/aws-secrets-manager-managed-external-secrets (accessed 2026-11-14)
[^s9]: Amazon Web Services, "AWS Secrets Manager adds managed external secrets support for Cisco Security Platform and Netskope" (What's New, 2026-08-25). https://aws.amazon.com/about-aws/whats-new/2026/08/secrets-manager-cisco-netskope/ (accessed 2026-11-14)
[^s10]: Zachary Miller and Rochak Karki, "How to choose the right AWS service for managing secrets and configurations", AWS Security Blog (2025-10-22). https://aws.amazon.com/blogs/security/how-to-choose-the-right-aws-service-for-managing-secrets-and-configurations (accessed 2026-11-14)
[^s11]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-14)
