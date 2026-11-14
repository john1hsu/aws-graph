---
id: kt:aws-certified-data-engineer-associate
type: Certification
title: AWS Certified Data Engineer - Associate
aliases: [DEA-C01, AWS Data Engineer Associate, AWS DEA]
domain: certifications
summary: >-
  AWS's associate-level certification for data engineers, validating the ability to build, orchestrate,
  operate and secure data pipelines and data stores on AWS (exam DEA-C01); it launched in 2024 as the
  successor to the retired AWS Certified Data Analytics - Specialty.
verified: 2026-11-14
status: current
broader: [kt:aws-certification-program]
related: [kt:aws-glue, kt:amazon-redshift, kt:aws-lake-formation, kt:amazon-athena, kt:data-lake-on-aws, kt:aws-analytics-services]
relations:
  - {predicate: contrastsWith, object: kt:aws-certified-machine-learning-engineer-associate}
  - {predicate: contrastsWith, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "Exam Guide (DEA-C01): AWS Certified Data Engineer - Associate (PDF)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/pdfs/aws-certification/latest/data-engineer-associate-01/data-engineer-associate-01.pdf
    type: official-learning
    accessed: 2026-11-14
  - id: s2
    title: "AWS Certified Data Engineer - Associate"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-data-engineer-associate/
    type: official-learning
    accessed: 2026-11-14
  - id: s3
    title: "Registration open for AWS Certified Data Engineer - Associate beta exam"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/registration-open-for-aws-certified-data-engineer-associate-beta-exam/
    type: official-blog
    accessed: 2026-11-14
created: 2026-11-14
---

# AWS Certified Data Engineer - Associate

AWS Certified Data Engineer - Associate is an associate-level AWS certification for people who design and run data pipelines.[^s2] Its exam guide says it validates implementing data pipelines and monitoring, troubleshooting and optimizing them for cost and performance. That breaks down into ingesting and transforming data, choosing data stores and modelling data, operating and monitoring pipelines with data-quality checks, and applying authentication, authorization, encryption, privacy and governance.[^s1] AWS recommends about 2–3 years of data engineering or data architecture experience plus at least 1–2 years of hands-on AWS work.[^s2][^s1]

## History and exam format

AWS announced the exam in November 2023. A discounted beta ran from November 27, 2023 to January 12, 2024, and the standard exam became available in April 2024.[^s3] It arrived alongside the retirement of AWS Certified Data Analytics - Specialty in April 2024, so it is that exam's practical successor for analytics and data-pipeline work.[^s3]

As of 2026-11 the exam code is DEA-C01: 65 multiple-choice or multiple-response questions in 130 minutes, costing 150 USD (foreign-exchange charges may apply), at a Pearson VUE test center or online proctored. It is offered in English, Japanese, Korean and Simplified Chinese.[^s2] Of the 65 questions, 50 are scored and 15 are unmarked, unscored trial items. Scores run 100–1,000 with 720 to pass under a compensatory model, so only the overall score matters. Unanswered questions count as wrong and guessing is not penalized.[^s1] The credential is valid for three years, and you recertify by passing the latest version of the exam.[^s2] AWS also says the exam will start using short names for some services, with a list of full names behind the in-exam Help button, rolling out to English first.[^s2]

## What the exam covers

The four domains are Data Ingestion and Transformation (34%), Data Store Management (26%), Data Operations and Support (22%), and Data Security and Governance (18%).[^s1] Ingestion covers streaming sources such as Kinesis, Amazon MSK and DynamoDB Streams, and batch loading from S3, Glue, EMR, DMS and AppFlow. It also covers scheduling with EventBridge or Airflow, transforming with EMR, Glue, Lambda and Redshift (for example CSV to Parquet), and orchestrating with Step Functions, Amazon MWAA and Glue workflows.[^s1] Programming tasks include SQL and Python, Lambda concurrency, CI/CD, and infrastructure as code with CloudFormation, CDK or SAM.[^s1]

Data store management covers picking between Redshift, RDS, DynamoDB, Lake Formation and streaming stores by cost and access pattern. It also covers Apache Iceberg tables, vector indexes, Glue Data Catalog and crawlers, S3 Lifecycle and versioning, DynamoDB TTL, and schema evolution.[^s1] Operations covers Athena and Redshift SQL, data preparation with Glue DataBrew, visualization, CloudWatch and CloudTrail monitoring, and data-quality rules. Security covers IAM and least privilege, Secrets Manager, KMS encryption and masking, Lake Formation fine-grained access, PII discovery with Macie, Redshift data sharing, and keeping data inside allowed Regions.[^s1]

## Recent changes and gotchas

The current guide is version 1.1, dated December 12, 2025, and the exam code did not change. It added skills on integrating large language models into data processing, Apache Iceberg, vector indexes such as HNSW and IVF, vectorization, SageMaker Catalog and SageMaker Unified Studio, and data governance frameworks.[^s1] It brought Aurora, Bedrock, Kendra, Amazon Q, AWS Data Exchange and S3 Tables into scope, and removed Cloud9, CodeCommit and AWS SCT.[^s1] Study material written for version 1.0 therefore misses the generative-AI and lakehouse content. AWS says it publishes guide revisions about a month before they reach the exam.[^s1]

The in-scope list is broad, but some services are explicitly out: App Runner, Elastic Beanstalk, Lightsail, Outposts, X-Ray, Amplify, AppSync, FinSpace, Migration Hub and all IoT services.[^s1]

## When to take it (and when not)

It suits engineers who build ETL and streaming pipelines, data lakes and warehouses on AWS and want to validate operations and governance as well as design.[^s1][^s2] People who mainly train and deploy models fit Machine Learning Engineer - Associate, which overlaps on Glue-based data preparation but centres on SageMaker AI. Architects designing whole workloads fit Solutions Architect - Associate, which covers analytics only as one part of broader architecture.[^s1]

## Connections

The certification is an associate-level member of the AWS Certification program.[^s2] Its content leans on the AWS analytics stack: AWS Glue for ETL and the Data Catalog, Amazon Redshift and Amazon Athena for querying, and AWS Lake Formation for governed data lakes.[^s1] It is commonly confused with the ML Engineer and Solutions Architect associates, which share some services but test different jobs.[^s1]

## Sources
[^s1]: Amazon Web Services, "Exam Guide (DEA-C01): AWS Certified Data Engineer - Associate (PDF)", version 1.1. https://docs.aws.amazon.com/pdfs/aws-certification/latest/data-engineer-associate-01/data-engineer-associate-01.pdf (accessed 2026-11-14)
[^s2]: Amazon Web Services, "AWS Certified Data Engineer - Associate". https://aws.amazon.com/certification/certified-data-engineer-associate/ (accessed 2026-11-14)
[^s3]: Amazon Web Services, "Registration open for AWS Certified Data Engineer - Associate beta exam", AWS Training and Certification Blog, 2023-11-02. https://aws.amazon.com/blogs/training-and-certification/registration-open-for-aws-certified-data-engineer-associate-beta-exam/ (accessed 2026-11-14)
