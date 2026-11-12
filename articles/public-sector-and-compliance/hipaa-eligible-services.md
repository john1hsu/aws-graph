---
id: kt:hipaa-eligible-services
type: ComplianceProgram
title: HIPAA eligible services
aliases: [HIPAA Eligible Services, AWS HIPAA Eligible Services, HIPAA on AWS, AWS HIPAA compliance, HIPAA BAA on AWS, AWS Business Associate Addendum]
domain: public-sector-and-compliance
summary: >-
  How AWS supports workloads regulated by the U.S. HIPAA rules: customers accept AWS's Business
  Associate Addendum (BAA) in AWS Artifact, and may then process protected health information only in
  the AWS services AWS lists as HIPAA eligible, configured under the shared responsibility model.
verified: 2026-11-12
status: current
broader: [kt:aws-compliance-programs]
related: [kt:aws-kms, kt:aws-cloudtrail, kt:amazon-textract, kt:fedramp-on-aws]
relations:
  - {predicate: configuredVia, object: kt:aws-artifact}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: governedBy, object: kt:shared-responsibility-model}
  - {predicate: implements, object: kt:nist-sp-800-53}
  - {predicate: contrastsWith, object: kt:hitrust-csf}
sources:
  - id: s1
    title: "HIPAA Eligible Services (AWS Services in Scope by Compliance Program: HIPAA BAA)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/HIPAA_BAA/
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "HIPAA Compliance - Amazon Web Services (AWS)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/de/compliance/hipaa-compliance/
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Managing agreements in AWS Artifact"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/artifact/latest/ug/managing-agreements.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "Guidance on HIPAA & Cloud Computing"
    publisher: U.S. Department of Health and Human Services
    url: https://www.hhs.gov/hipaa/for-professionals/special-topics/cloud-computing
    type: government
    accessed: 2026-11-12
created: 2026-11-12
---

# HIPAA eligible services

The U.S. Health Insurance Portability and Accountability Act (HIPAA) and its later rules (Privacy, Security, Enforcement and Breach Notification, extended by HITECH in 2009) govern how covered entities (health plans, clearinghouses and certain providers) and their business associates protect protected health information (PHI).[^s2] On AWS, HIPAA support has two parts: a standard **Business Associate Addendum (BAA)** between the customer and AWS, and a published list of **HIPAA eligible services** in which PHI may be created, received, stored, processed or transmitted.[^s1][^s2] There is no HIPAA certification for a cloud provider; AWS instead aligns its HIPAA risk-management program with FedRAMP and NIST SP 800-53, which map onto the HIPAA Security Rule.[^s2]

## How it works

Under HHS guidance, a cloud provider that stores or processes electronic PHI is a business associate, even if it only holds encrypted data and has no decryption key (a "no-view" service), and using such a provider for ePHI without a BAA violates the HIPAA rules.[^s4] AWS therefore offers a standard BAA that defines and limits how AWS may use and disclose PHI and accounts for the shared responsibility model.[^s2]

Customers review and accept the BAA in **AWS Artifact**. An account agreement designates a single AWS account as able to process PHI; with AWS Organizations, an organization agreement covers every existing and future member account automatically. Agreements can later be terminated in Artifact and reactivated if needed.[^s3] AWS's HIPAA page directs users without Artifact access to ask an administrator for IAM permissions to it.[^s2]

Once an account is under the BAA, any AWS service may run in it, but PHI must stay inside services on the eligible list.[^s2] AWS says it has demonstrated HIPAA compliance for each listed service and that, unless a note says otherwise, all features of a listed service are eligible too.[^s1] AWS adds services to the list based on customer demand.[^s2]

## Scope, exclusions and gotchas

The HIPAA BAA page of AWS Services in Scope is the authoritative list; as of 2026-11 it was marked last updated July 14, 2026.[^s1] It spans core services such as Amazon EC2, Amazon S3, AWS Lambda, Amazon DynamoDB, AWS KMS and AWS CloudTrail, generative-AI and ML services such as Amazon Bedrock and Amazon Textract, and health-specific services such as AWS HealthLake and Amazon Comprehend Medical.[^s1] Several entries carry exclusions that are easy to miss (as of 2026-11):[^s1]

- **Amazon RDS** is eligible only for the SQL Server, MySQL, Oracle, PostgreSQL, Db2 and MariaDB engines.[^s1]
- **AWS Fargate** is eligible only with the ECS and EKS engines.[^s1]
- **Amazon CloudFront** excludes delivery through embedded Points of Presence; **AWS Directory Service** excludes Simple AD.[^s1]
- **Amazon SageMaker AI** excludes Studio Lab, Ground Truth Plus and the public and vendor workforces.[^s1]
- Some services sit under other agreements, for example healthcare Alexa skills under an Alexa Skills BAA.[^s1]

Eligibility is not compliance. Each listed service still has to be configured to meet HIPAA requirements, and customers remain responsible for their own compliance.[^s1] HHS stresses that encryption alone does not satisfy the Security Rule, because integrity, availability and administrative and physical safeguards are also required, and that the parties should document in writing who handles which Security Rule controls.[^s4] Customers must also run their own risk analysis, which HHS says should consider the cloud deployment model and the location of the data; storing ePHI outside the U.S. is allowed with a BAA in place but changes the risk picture.[^s4]

## When it applies (and when not)

The BAA matters when your own AWS accounts handle PHI. If you only use a SaaS product built on AWS, the partner normally signs the BAA with AWS and you sign one with the partner; a covered entity that also runs its own HIPAA systems on AWS may need its own BAA.[^s2] Data de-identified under the Privacy Rule is outside business-associate obligations entirely.[^s4] Healthcare organizations that follow HITRUST CSF, which combines federal, state and industry requirements, can use AWS for workloads aligned with HIPAA, HITECH and HITRUST.[^s2]

## Connections

HIPAA eligible services are one of the **AWS compliance programs**. The BAA is **configured via AWS Artifact** and **integrates with AWS Organizations** for organization-wide coverage.[^s3] The program is **governed by the shared responsibility model**, since AWS's BAA covers only AWS's side.[^s1][^s2] AWS's HIPAA program **implements NIST SP 800-53** controls, the same baseline behind **FedRAMP on AWS**.[^s2] It **contrasts with HITRUST CSF**, a broader healthcare framework.[^s2] Eligible services commonly used to build compliant stacks include **AWS KMS** for encryption, **AWS CloudTrail** for audit trails and **Amazon Textract** for document processing.[^s1]

## Sources
[^s1]: Amazon Web Services, "HIPAA Eligible Services (AWS Services in Scope by Compliance Program: HIPAA BAA)". https://aws.amazon.com/compliance/services-in-scope/HIPAA_BAA/ (accessed 2026-11-12)
[^s2]: Amazon Web Services, "HIPAA Compliance - Amazon Web Services (AWS)". https://aws.amazon.com/de/compliance/hipaa-compliance/ (accessed 2026-11-12)
[^s3]: Amazon Web Services, "Managing agreements in AWS Artifact". https://docs.aws.amazon.com/artifact/latest/ug/managing-agreements.html (accessed 2026-11-12)
[^s4]: U.S. Department of Health and Human Services, "Guidance on HIPAA & Cloud Computing". https://www.hhs.gov/hipaa/for-professionals/special-topics/cloud-computing (accessed 2026-11-12)
