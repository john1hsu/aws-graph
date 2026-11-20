---
id: kt:aws-certified-data-analytics-specialty
type: Certification
title: AWS Certified Data Analytics - Specialty
aliases: [DAS-C01, AWS Data Analytics Specialty, AWS DAS]
domain: certifications
summary: >-
  AWS's former specialty-level certification for designing, building, securing and maintaining analytics
  solutions on AWS (exam DAS-C01); AWS retired it in April 2024 and named AWS Certified Data Engineer -
  Associate as its successor.
verified: 2026-11-20
status: retired
broader: [kt:aws-certification-program]
related: [kt:aws-analytics-services, kt:amazon-kinesis-data-streams, kt:amazon-redshift, kt:amazon-emr, kt:aws-glue, kt:amazon-opensearch-service]
relations:
  - {predicate: replacedBy, object: kt:aws-certified-data-engineer-associate}
  - {predicate: contrastsWith, object: kt:aws-certified-machine-learning-specialty}
sources:
  - id: s1
    title: "AWS Certified Data Analytics - Specialty (DAS-C01) Exam Guide, version 1.1 (PDF)"
    publisher: Amazon Web Services
    url: https://d1.awsstatic.com/training-and-certification/docs-data-analytics-specialty/AWS-Certified-Data-Analytics-Specialty_Exam-Guide.pdf
    type: official-learning
    accessed: 2026-11-20
  - id: s2
    title: "AWS Certification retirements and launches"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/aws-certification-retirements-and-launches
    type: official-blog
    accessed: 2026-11-20
  - id: s3
    title: "Registration open for AWS Certified Data Engineer - Associate beta exam"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/registration-open-for-aws-certified-data-engineer-associate-beta-exam
    type: official-blog
    accessed: 2026-11-20
  - id: s4
    title: "10 study areas for the AWS Certified Data Analytics - Specialty exam"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/10-study-areas-for-the-aws-certified-data-analytics-specialty-exam
    type: official-blog
    accessed: 2026-11-20
  - id: s5
    title: "AWS Recertification | AWS Certification Information and Policies"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/policies/recertification/
    type: official-learning
    accessed: 2026-11-20
  - id: s6
    title: "AWS Certified Data Engineer - Associate (DEA-C01) exam guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/data-engineer-associate-01/data-engineer-associate-01.html
    type: official-docs
    accessed: 2026-11-20
created: 2026-11-20
---

# AWS Certified Data Analytics - Specialty

AWS Certified Data Analytics - Specialty was a specialty-level AWS certification, exam code DAS-C01. Its exam guide said it validated using AWS services to design, build, secure and maintain analytics solutions, and explaining how those services fit together across the data lifecycle of collection, storage, processing and visualization.[^s1] AWS retired it in April 2024 as part of a shift toward job-role certifications. The exam is no longer offered, and AWS points candidates to AWS Certified Data Engineer - Associate.[^s2][^s3]

## What the exam covered

The DAS-C01 guide (version 1.1) set a high bar: about five years with common data analytics technologies plus at least two years of hands-on AWS work.[^s1] The exam had 50 scored and 15 unscored questions. Questions were multiple choice or multiple response, and a scaled score of 750 out of 1,000 was needed to pass under a compensatory model. Its five content domains were weighted Collection 18%, Storage and Data Management 22%, Processing 24%, Analysis and Visualization 18%, and Security 18%.[^s1]

The guide's in-scope list was broad. Analytics services such as Amazon Athena, Amazon EMR, AWS Glue, Amazon Kinesis, AWS Lake Formation, Amazon MSK, Amazon OpenSearch Service and Amazon QuickSight sat alongside Amazon Redshift, Amazon DynamoDB, migration tools such as AWS DMS and Snowball, and security services such as AWS KMS and AWS CloudHSM. AWS IoT Core and Kinesis Video Streams were explicitly out of scope.[^s1] AWS's own study guidance stressed data movement and access integrations between services, partitioning and distribution strategies, query scenarios, monitoring, and Amazon S3 as the centre of most designs.[^s4]

## Retirement timeline

In January 2024 AWS announced that Data Analytics - Specialty, Database - Specialty and SAP on AWS - Specialty would all retire in April 2024.[^s2] AWS's posts give the cut-off slightly differently: the retirements post lists April 8 as the last day to take Data Analytics - Specialty, while the Data Engineer beta post says the exam retires on April 9, 2024 and must be taken on or before April 8.[^s2][^s3] Its exam-prep material on AWS Skill Builder was retired on the same schedule.[^s2] The study-areas post now carries a note dated April 9, 2024 saying the certification is retired.[^s4]

## What it means for holders

A certification earned before retirement stays active for three years from the date it was earned, and the Credly digital badge can still be displayed.[^s2] AWS also said holders cannot recertify once the exam has retired.[^s2] Normally, AWS recertification means passing the current version of the same exam within the three-year window. Since no current version of DAS-C01 exists, these credentials simply lapse three years after they were earned, so the last ones expire in April 2027.[^s5][^s2]

## Successor: Data Engineer - Associate

AWS presented Data Engineer - Associate (DEA-C01) as the successor. It was released as a beta from November 27, 2023 to January 12, 2024, and generally from April 2024.[^s3] The two exams differ in level and focus. Data Analytics - Specialty covered a wider set of domains aimed at both data engineers and data analysts. Data Engineer - Associate maps to the data-engineer job role, adds programming concepts, and puts more weight on operations, support and security.[^s3] DEA-C01 asks for 2–3 years of data engineering and 1–2 years of hands-on AWS experience. It has four domains: Data Ingestion and Transformation (34%), Data Store Management (26%), Data Operations and Support (22%), and Data Security and Governance (18%). It also has 50 scored questions, but a lower passing score of 720.[^s6] Visualization-heavy analyst work, which DAS-C01 tested through QuickSight and OpenSearch dashboards, has no direct equivalent among the current exams.[^s1][^s6]

## Gotchas

Third-party courses and practice tests for DAS-C01 still circulate, but they prepare for an exam that no longer exists. Study from the DEA-C01 guide instead.[^s2][^s6] Holding Data Analytics - Specialty does not carry over into Data Engineer - Associate. That is a separate exam, and passing it starts its own three-year validity.[^s3][^s5]

## Connections

This certification sits **broader** under the AWS Certification program and is **replaced by** AWS Certified Data Engineer - Associate, the migration path AWS named.[^s2][^s3] It **contrasts with** the Machine Learning - Specialty, another retired specialty exam that overlapped on data collection and processing services.[^s1] It is **related** to the AWS analytics services it tested, especially Kinesis Data Streams, Amazon Redshift, Amazon EMR, AWS Glue and Amazon OpenSearch Service.[^s1][^s4]

## Sources
[^s1]: Amazon Web Services, "AWS Certified Data Analytics - Specialty (DAS-C01) Exam Guide, version 1.1 (PDF)". https://d1.awsstatic.com/training-and-certification/docs-data-analytics-specialty/AWS-Certified-Data-Analytics-Specialty_Exam-Guide.pdf (accessed 2026-11-20)
[^s2]: Amazon Web Services, "AWS Certification retirements and launches". https://aws.amazon.com/blogs/training-and-certification/aws-certification-retirements-and-launches (accessed 2026-11-20)
[^s3]: Amazon Web Services, "Registration open for AWS Certified Data Engineer - Associate beta exam". https://aws.amazon.com/blogs/training-and-certification/registration-open-for-aws-certified-data-engineer-associate-beta-exam (accessed 2026-11-20)
[^s4]: Amazon Web Services, "10 study areas for the AWS Certified Data Analytics - Specialty exam". https://aws.amazon.com/blogs/training-and-certification/10-study-areas-for-the-aws-certified-data-analytics-specialty-exam (accessed 2026-11-20)
[^s5]: Amazon Web Services, "AWS Recertification | AWS Certification Information and Policies". https://aws.amazon.com/certification/policies/recertification/ (accessed 2026-11-20)
[^s6]: Amazon Web Services, "AWS Certified Data Engineer - Associate (DEA-C01) exam guide". https://docs.aws.amazon.com/aws-certification/latest/data-engineer-associate-01/data-engineer-associate-01.html (accessed 2026-11-20)
