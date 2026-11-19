---
id: kt:aws-certified-machine-learning-specialty
type: Certification
title: AWS Certified Machine Learning - Specialty
aliases: [MLS-C01, AWS ML Specialty, AWS Machine Learning Specialty]
domain: certifications
summary: >-
  AWS's former specialty-level certification for data scientists and ML practitioners (exam MLS-C01),
  covering data engineering, exploratory data analysis, modeling and ML operations on AWS; it was retired
  after its last exam date of March 31, 2026, and AWS points candidates to the Machine Learning Engineer - Associate.
verified: 2026-11-19
status: retired
broader: [kt:aws-certification-program]
related: [kt:amazon-sagemaker-ai, kt:aws-glue, kt:amazon-emr, kt:amazon-data-firehose, kt:aws-certified-ai-practitioner]
relations:
  - {predicate: replacedBy, object: kt:aws-certified-machine-learning-engineer-associate}
  - {predicate: contrastsWith, object: kt:aws-certified-generative-ai-developer-professional}
  - {predicate: contrastsWith, object: kt:aws-certified-data-engineer-associate}
sources:
  - id: s1
    title: "AWS Certified Machine Learning - Specialty (MLS-C01) exam guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/machine-learning-specialty-01/machine-learning-specialty-01.html
    type: official-learning
    accessed: 2026-11-19
  - id: s2
    title: "Content Domain 1: Data Engineering - AWS Certified Machine Learning - Specialty"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/machine-learning-specialty-01/machine-learning-specialty-01-domain1.html
    type: official-learning
    accessed: 2026-11-19
  - id: s3
    title: "AWS Certified Machine Learning - Specialty"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/certification/certified-machine-learning-specialty/
    type: official-learning
    accessed: 2026-11-19
  - id: s4
    title: "AWS expands AI certification portfolio and updates security certification"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/ko/blogs/training-and-certification/big-news-aws-expands-ai-certification-portfolio-and-updates-security-certification/
    type: official-blog
    accessed: 2026-11-19
  - id: s5
    title: "Strategies for excelling across all four exam domains of the AWS Certified Machine Learning - Specialty certification"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/strategies-for-excelling-across-all-four-exam-domains-of-the-aws-certified-machine-learning-specialty-certification/
    type: official-blog
    accessed: 2026-11-19
  - id: s6
    title: "New courses and certification updates from AWS Training and Certification in October 2025"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/october-2025-new-offerings/
    type: official-blog
    accessed: 2026-11-19
created: 2026-11-19
---

# AWS Certified Machine Learning - Specialty

AWS Certified Machine Learning - Specialty (exam MLS-C01) was AWS's specialty-level credential for people in AI/ML development or data science roles who design, build, train, tune, deploy and maintain ML solutions on AWS.[^s1] The exam guide recommended at least two years of hands-on experience running ML or deep learning workloads in the AWS Cloud, plus working knowledge of hyperparameter optimization and common ML frameworks.[^s1] AWS has retired it: the last day to sit the exam was March 31, 2026.[^s3][^s4] It remains relevant because holders keep an active credential until it expires, and because its scope explains how AWS split ML skills across newer exams.

## What the exam covered

The exam guide weighted four content domains: Data Engineering (20%), Exploratory Data Analysis (24%), Modeling (36%) and Machine Learning Implementation and Operations (20%).[^s1] Modeling, the largest, focused on choosing algorithms, training, hyperparameter tuning and evaluation metrics, while the operations domain covered deployment strategies, MLOps, CI/CD for ML and model monitoring.[^s5] The guide's technology list spanned ingestion and collection, processing and ETL, analysis and visualization, training, deployment and inference, operationalizing ML, AWS ML application services, ML languages such as Python, Scala, R and SQL, and notebooks and IDEs.[^s1]

The data engineering domain was unusually broad for an ML exam. Its tasks asked candidates to pick storage for ML data (databases, Amazon S3, Amazon EFS, Amazon EBS), orchestrate batch and streaming ingestion with Amazon Kinesis, Amazon Data Firehose, Amazon EMR, AWS Glue or Amazon Managed Service for Apache Flink, and transform data with Glue, EMR, AWS Batch and MapReduce-family tools such as Spark and Hive.[^s2] AWS's own study guidance pointed candidates to Amazon SageMaker AI for hands-on modeling practice and to Glue and EMR for the data side.[^s5]

## Format and rules (as last published)

The certification page lists a 180-minute exam of 65 multiple-choice or multiple-response questions, priced at 300 USD, delivered through Pearson VUE at a test center or online, in English, Japanese, Korean and Simplified Chinese.[^s3] Of the 65 questions, 50 were scored and 15 were unscored and unidentified; results were reported on a 100-1,000 scale with 750 needed to pass, under a compensatory model where only the overall score mattered.[^s1] Unanswered questions counted as wrong, with no penalty for guessing.[^s1]

## Retirement and what holders should know

AWS announced the retirement in October 2025 as part of a broader expansion of its AI certification portfolio, setting March 31, 2026 as the final exam date.[^s4][^s6] Holders need do nothing: the credential stays active for three years from the date it was earned, and the Credly badge can be shown during that time.[^s3] Recertifying by retaking the exam was possible only on or before March 31, 2026, so there is no way to extend an MLS-C01 credential now; when it expires, the holder needs a different certification to show current ML skills.[^s3] Unused Professional and Specialty vouchers could be applied to this exam only until the same date.[^s3]

## Alternatives

AWS's retirement post points learners toward AWS Certified AI Practitioner, AWS Certified Machine Learning Engineer - Associate, AWS Certified Data Engineer - Associate and AWS Certified Generative AI Developer - Professional.[^s4] Of these, the certification page singles out Machine Learning Engineer - Associate as the credential that validates implementing and operationalizing ML workloads in production, which is the closest match for MLS-C01's modeling and operations scope.[^s3] Candidates whose interest was mainly the data pipeline work in Domain 1 are better served by Data Engineer - Associate, and those building on foundation models by the Generative AI Developer - Professional.[^s4] Study material written for MLS-C01 still describes useful SageMaker AI and data-preparation skills, but it is no longer tied to an exam anyone can book, so check each newer exam's own guide before reusing it.[^s1][^s3]

## Connections

The node sits under `kt:aws-certification-program`, which governs fees, delivery and the three-year validity rule that determines when remaining MLS-C01 credentials lapse.[^s3] Its `replacedBy` edge points to `kt:aws-certified-machine-learning-engineer-associate`, the successor AWS's certification page recommends for production ML skills.[^s3] It `contrastsWith` the Data Engineer - Associate and Generative AI Developer - Professional exams, which absorbed its data-pipeline and AI-application ground respectively and which AWS lists among the alternatives.[^s4] The `related` services, Amazon SageMaker AI, AWS Glue, Amazon EMR and Amazon Data Firehose, are the ones its exam guide and AWS's study guidance named most directly, and AWS Certified AI Practitioner is the foundational alternative AWS lists alongside them.[^s2][^s4][^s5]

## Sources
[^s1]: Amazon Web Services, "AWS Certified Machine Learning - Specialty (MLS-C01) exam guide". https://docs.aws.amazon.com/aws-certification/latest/machine-learning-specialty-01/machine-learning-specialty-01.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, "Content Domain 1: Data Engineering - AWS Certified Machine Learning - Specialty". https://docs.aws.amazon.com/aws-certification/latest/machine-learning-specialty-01/machine-learning-specialty-01-domain1.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "AWS Certified Machine Learning - Specialty". https://aws.amazon.com/certification/certified-machine-learning-specialty/ (accessed 2026-11-19)
[^s4]: Amazon Web Services (AWS Training and Certification Blog), "AWS expands AI certification portfolio and updates security certification". https://aws.amazon.com/ko/blogs/training-and-certification/big-news-aws-expands-ai-certification-portfolio-and-updates-security-certification/ (accessed 2026-11-19)
[^s5]: Amazon Web Services (AWS Training and Certification Blog), "Strategies for excelling across all four exam domains of the AWS Certified Machine Learning - Specialty certification". https://aws.amazon.com/blogs/training-and-certification/strategies-for-excelling-across-all-four-exam-domains-of-the-aws-certified-machine-learning-specialty-certification/ (accessed 2026-11-19)
[^s6]: Amazon Web Services (AWS Training and Certification Blog), "New courses and certification updates from AWS Training and Certification in October 2025". https://aws.amazon.com/blogs/training-and-certification/october-2025-new-offerings/ (accessed 2026-11-19)
