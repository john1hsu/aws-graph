---
id: kt:amazon-macie
type: Service
title: Amazon Macie
aliases: [Macie, AWS Macie]
domain: security
summary: >-
  AWS's managed data security service for Amazon S3: it inventories and monitors general purpose
  buckets for security and access-control risks, and uses machine learning and pattern matching to
  discover sensitive data such as PII, financial data and credentials in S3 objects.
verified: 2026-11-20
status: current
broader: [kt:aws-security-services]
related: [kt:aws-security-hub, kt:amazon-guardduty, kt:amazon-s3]
relations:
  - {predicate: dependsOn, object: kt:amazon-s3}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: integratesWith, object: kt:aws-security-hub}
  - {predicate: configuredVia, object: kt:aws-organizations}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: governedBy, object: kt:macie-quotas}
sources:
  - id: s1
    title: "What is Amazon Macie? - Amazon Macie"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/macie/latest/user/what-is-macie.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Quotas for Macie - Amazon Macie"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/macie/latest/user/macie-quotas.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "How automated sensitive data discovery works - Amazon Macie"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/macie/latest/user/discovery-asdd-how-it-works.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "Participating in the free trial of Macie - Amazon Macie"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/macie/latest/user/account-mgmt-free-trial.html
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "Amazon Macie Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/macie/pricing/
    type: official-docs
    accessed: 2026-11-20
  - id: s6
    title: "Amazon Macie FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/macie/faq/
    type: official-docs
    accessed: 2026-11-20
created: 2026-11-20
---

# Amazon Macie

Amazon Macie is a data security service that finds sensitive data with machine learning and pattern matching, shows where it is exposed, and lets you automate a response. Its scope is Amazon S3. When you enable it, Macie builds and maintains an inventory of the account's S3 general purpose buckets, checks them for security and access-control problems, and analyzes the objects in them for sensitive data.[^s1] It is a regional service: you enable it in each Region you want covered.[^s6]

## How it works

Macie does two jobs.

- **Bucket posture monitoring (preventative control monitoring).** Macie keeps the bucket inventory up to date and raises *policy findings* when a bucket's settings weaken its security, for example when a bucket becomes publicly accessible. A summary dashboard shows counts such as publicly accessible or externally shared buckets.[^s1]
- **Sensitive data discovery.** Macie inspects object contents with *managed data identifiers* and *custom data identifiers*. The managed identifiers are built in and cover PII, financial data and credentials for many countries and regions. The custom ones are regular expressions you write, optionally with keyword and proximity rules. *Allow lists* tell Macie which text or patterns to ignore, such as a company's public phone number or test data. Each match produces a *sensitive data finding*.[^s1]

Discovery runs in two modes. **Automated sensitive data discovery** runs every day. Macie looks at the bucket inventory, picks a representative sample of eligible objects, and analyzes the latest version of each one. New and changed objects go first, and objects it has already analyzed are skipped until they change. Macie throws away its copy of each object once the analysis is done, and results can take up to about 48 hours to appear.[^s3] Each bucket gets a sensitivity score from 1 to 100. A bucket starts at 50 ("Not yet analyzed"), and an empty bucket scores 1 ("Not sensitive"). AWS notes that the score reflects the sensitive data found, not how important the bucket is to the business.[^s3] **Sensitive data discovery jobs** are targeted scans. You choose the buckets, the sampling depth and the object criteria, and a job runs once or on a schedule.[^s1]

Findings appear in the console and through the API. Macie publishes them to Amazon EventBridge for automated response, and it can also send them to AWS Security Hub CSPM, which can collect findings from several Regions in one place.[^s1] For multiple accounts, you can manage Macie through AWS Organizations or through membership invitations. A delegated Macie administrator account can see member accounts' bucket inventories and policy findings, run discovery against their buckets, and, through Organizations, turn Macie on for members.[^s1] Only the administrator, or a standalone account, can change the automated discovery settings.[^s3]

## Quotas, pricing and gotchas

Selected quotas as of 2026-11, per account per Region unless stated.[^s2]

- Up to 10,000 member accounts through Organizations, or 1,000 through invitations.[^s2]
- 10,000 custom data identifiers per account, and at most 30 per job.
- Jobs can analyze 5 TB per account per month by default. This is the main quota you can raise: up to 1,000 TB through Service Quotas, and beyond that through AWS Support. Some other quotas are fixed.
- A job can cover up to 1,000 buckets.
- Full posture monitoring covers 10,000 buckets per account. Above that, only the 10,000 most recently created or changed buckets are fully monitored.
- Per-file size limits apply, for example 1,024 MB for PDF and 512 MB for Excel and Word files. Archives have limits on nesting depth and extracted size.[^s2]

Pricing has three dimensions. These are US East (N. Virginia) example rates from the pricing page as of 2026-11:[^s5]

- $0.10 per bucket per month for inventory and monitoring.[^s5]
- $0.01 per 100,000 objects monitored for automated discovery.
- $1 per GB of data inspected, for both automated discovery and jobs.[^s5]

S3 GET and LIST requests made during discovery are billed separately under S3 pricing.[^s5] The free tier includes 1 GB of data inspected per month, and it does not expire.[^s6]

Each account gets a 30-day free trial per Region the first time Macie is enabled, and it cannot be paused. The trial covers bucket monitoring and automated discovery but not discovery jobs. For accounts holding more than 150 TB in S3, automated discovery pauses during the trial after 150 GB has been analyzed.[^s4] Decrypting objects with customer managed AWS KMS keys can still incur KMS charges, and Macie can only analyze encrypted objects whose keys it can use.[^s3][^s4] Macie analyzes only S3 general purpose buckets, and only objects in supported storage classes and file formats.[^s3]

## When to use it (and when not)

Use Macie to answer the question "where is our sensitive data in S3, and is any of it exposed?" Common cases are compliance scoping, finding stray PII or credentials, and catching buckets that become public or shared. Turn on automated discovery for broad, low-cost coverage, and run targeted jobs when you need a complete scan of particular buckets, such as before an audit.[^s1][^s3] Macie does not look for active threats. Threat detection from logs and runtime activity is Amazon GuardDuty's job. Macie also doesn't scan databases or file systems, so data outside S3 needs other controls.[^s1][^s6]

## Connections

Macie is one of the **AWS security services**. It **depends on Amazon S3**, the only data store it inventories and inspects.[^s1] It **integrates with Amazon EventBridge** for automated response and with **AWS Security Hub**, which collects its findings next to those from other tools. It is **configured via AWS Organizations** for central administration across accounts.[^s1] It is **scoped to AWS Regions**, so you enable and configure it in each Region.[^s6] Its limits are **governed by Macie quotas**.[^s2] It is a close relative of **Amazon GuardDuty**: GuardDuty detects threatening activity, while Macie classifies data and reports bucket exposure.[^s1]

## Sources
[^s1]: Amazon Web Services, "What is Amazon Macie? - Amazon Macie". https://docs.aws.amazon.com/macie/latest/user/what-is-macie.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "Quotas for Macie - Amazon Macie". https://docs.aws.amazon.com/macie/latest/user/macie-quotas.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "How automated sensitive data discovery works - Amazon Macie". https://docs.aws.amazon.com/macie/latest/user/discovery-asdd-how-it-works.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "Participating in the free trial of Macie - Amazon Macie". https://docs.aws.amazon.com/macie/latest/user/account-mgmt-free-trial.html (accessed 2026-11-20)
[^s5]: Amazon Web Services, "Amazon Macie Pricing". https://aws.amazon.com/macie/pricing/ (accessed 2026-11-20)
[^s6]: Amazon Web Services, "Amazon Macie FAQs". https://aws.amazon.com/macie/faq/ (accessed 2026-11-20)
