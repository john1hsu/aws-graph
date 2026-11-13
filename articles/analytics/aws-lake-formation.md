---
id: kt:aws-lake-formation
type: Service
title: AWS Lake Formation
aliases: [Lake Formation, Amazon Lake Formation]
domain: analytics
summary: >-
  AWS's data lake governance service: a grant/revoke permissions layer over the AWS Glue Data
  Catalog and Amazon S3 that enforces database-, table-, column-, row- and cell-level access for
  engines such as Athena, Redshift Spectrum, EMR and Glue, and shares data across accounts.
verified: 2026-11-13
status: current
broader: [kt:data-lake-on-aws]
related: [kt:aws-iam, kt:amazon-athena, kt:amazon-redshift]
relations:
  - {predicate: dependsOn, object: kt:aws-glue}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: governedBy, object: kt:lake-formation-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is AWS Lake Formation? - AWS Lake Formation"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/lake-formation/latest/dg/what-is-lake-formation.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Changing the default settings for your data lake - AWS Lake Formation"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/lake-formation/latest/dg/change-settings.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "AWS Lake Formation Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/lake-formation/pricing/
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "AWS Lake Formation endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_gb/general/latest/gr/lake-formation.html
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "AWS Lake Formation FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/lake-formation/faqs
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "Deprecation of Lake Formation's Governed Tables Feature"
    publisher: AWS Big Data Blog
    url: https://aws.amazon.com/blogs/big-data/deprecation-of-lake-formations-governed-tables-feature
    type: official-blog
    accessed: 2026-11-13
  - id: s7
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-13
created: 2026-11-13
---

# AWS Lake Formation

AWS Lake Formation is the governance layer for data lakes on AWS. It centrally manages who can see which data in Amazon S3 and the matching metadata in the AWS Glue Data Catalog, and it lets you share that data across accounts. Instead of writing per-bucket IAM and S3 policies, you grant and revoke permissions much as you would in a relational database, and Lake Formation enforces them at the database, table, column, row and cell level for engines such as Amazon Athena, Amazon Redshift Spectrum, Amazon EMR and AWS Glue.[^s1]

## How it works

Lake Formation sits on top of the Glue Data Catalog rather than replacing it: the two services share console controls and the same catalog, with Glue focused on ETL and Lake Formation on access control.[^s5] A request has to pass both its IAM check and its Lake Formation check, so the two are complementary, and turning on Lake Formation doesn't change existing IAM permissions.[^s5] Integrated services act as trusted callers. Lake Formation checks the caller's grants and then gives the engine temporary credentials to read the underlying S3 data, which means analysts don't need direct S3 access.[^s5]

Main capabilities:[^s1]

- **Fine-grained grants** on databases, tables and columns, plus **data filters** that combine row and column restrictions. A common use is hiding personally identifiable information.
- **LF-Tags (tag-based access control).** You attach custom tags to databases, tables or columns and grant permissions on tag expressions, which replaces thousands of resource-by-resource grants with a few logical tags.
- **Cross-account and organization sharing** of catalog resources and S3 locations, including direct grants to IAM principals in other accounts.[^s1][^s5]
- **Audit** through AWS CloudTrail, which records which principal accessed what data, through which service and when.
- **Federation and ingestion:** federated catalogs for Amazon Redshift, Amazon S3 Tables and external sources such as Google BigQuery, reachable by Apache Iceberg REST-compatible engines; and bulk or incremental ingestion from relational databases on Amazon RDS or EC2.

## Quotas, pricing and gotchas

**Pricing (as of 2026-11; the page names no Region and lists no dollar rates).** Creating permissions, enforcing them through integrated services and cross-account sharing cost nothing. Charges apply to the Storage API, billed by bytes scanned (rounded up to the next megabyte, with a 10 MB minimum) and only when no other AWS analytics service processes the data. Underlying services such as S3 and the Glue Data Catalog bill at their normal rates.[^s3]

**Default quotas (per Region, as of 2026-11, all adjustable):** 30 data lake administrators; 1,000 LF-Tags per account and 1,000 values per tag; 50 LF-Tag policies per principal per resource type; 10,000 registered paths; a 700-character registered-path length; and 20 subfolders in an S3 path.[^s4] Lake Formation has endpoints, including FIPS endpoints, in both AWS GovCloud (US) Regions.[^s4]

Gotchas:

- **It is off by default.** To stay compatible with Glue, new accounts grant `Super` to the `IAMAllowedPrincipals` group, and "Use only IAM access control" is on for new resources, so IAM alone decides access until you change both settings.[^s2] Changing the defaults affects only new databases and tables. Existing resources keep the `IAMAllowedPrincipals` grant until you upgrade them separately.[^s2] **Hybrid access mode** lets you opt individual databases and tables into Lake Formation one use case at a time, while IAM-based access keeps working for everything else.[^s1]
- **Governed tables are gone.** AWS ended support on December 31, 2024, and all governed-table APIs stopped working after February 17, 2025. AWS recommends migrating to open table formats, mainly Apache Iceberg, for example with an Athena CTAS statement.[^s6] The pricing page still lists governed-table charges, so don't treat that as evidence the feature works.[^s3][^s6]
- **Enforcement depends on the engine.** Only integrated engines, and partner tools that integrate with Lake Formation, apply its row and column rules. BI tools such as Tableau stay governed only when they query through Athena or Redshift.[^s5]

## When to use it (and when not)

Use Lake Formation when several teams or accounts share one S3 data lake and you need column- or row-level control, tag-based permissions that scale, or central auditing, all without managing bucket policies per dataset.[^s1][^s5] It is also the sharing mechanism behind Amazon DataZone.[^s5] A single team with a few buckets and coarse access needs may be served well enough by IAM and S3 policies with the default IAM-only settings.[^s2] Lake Formation is listed as an in-scope Analytics service for the AWS Certified Solutions Architect - Associate (SAA-C03) exam.[^s7]

## Connections

- **Broader: data lake on AWS** (`kt:data-lake-on-aws`): Lake Formation is the security and sharing layer of an S3-based data lake.[^s1]
- **Depends on AWS Glue** (`kt:aws-glue`) and **integrates with Amazon S3** (`kt:amazon-s3`): permissions attach to Glue Data Catalog objects and to registered S3 locations.[^s1][^s5]
- **Related to AWS IAM** (`kt:aws-iam`): IAM and Lake Formation checks must both pass.[^s5]
- **Related to Amazon Athena and Amazon Redshift** (`kt:amazon-athena`, `kt:amazon-redshift`): these engines enforce Lake Formation grants, and Redshift data can be federated into the catalog.[^s1][^s5]
- **Monitored by AWS CloudTrail** (`kt:aws-cloudtrail`) and **governed by Lake Formation quotas** (`kt:lake-formation-quotas`).[^s1][^s4]
- **Covered by AWS Certified Solutions Architect - Associate** (`kt:aws-certified-solutions-architect-associate`).[^s7]

## Sources
[^s1]: Amazon Web Services, "What is AWS Lake Formation?". https://docs.aws.amazon.com/en_us/lake-formation/latest/dg/what-is-lake-formation.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Changing the default settings for your data lake". https://docs.aws.amazon.com/lake-formation/latest/dg/change-settings.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "AWS Lake Formation Pricing". https://aws.amazon.com/lake-formation/pricing/ (accessed 2026-11-13)
[^s4]: Amazon Web Services, "AWS Lake Formation endpoints and quotas". https://docs.aws.amazon.com/en_gb/general/latest/gr/lake-formation.html (accessed 2026-11-13)
[^s5]: Amazon Web Services, "AWS Lake Formation FAQs". https://aws.amazon.com/lake-formation/faqs (accessed 2026-11-13)
[^s6]: AWS Big Data Blog, "Deprecation of Lake Formation's Governed Tables Feature". https://aws.amazon.com/blogs/big-data/deprecation-of-lake-formations-governed-tables-feature (accessed 2026-11-13)
[^s7]: Amazon Web Services, "In-Scope AWS Services — SAA-C03". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-13)
