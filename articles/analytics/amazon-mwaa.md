---
id: kt:amazon-mwaa
type: Service
title: Amazon Managed Workflows for Apache Airflow
aliases: [Amazon MWAA, MWAA, Managed Airflow, Amazon MWAA Serverless, MWAA Serverless]
domain: analytics
summary: >-
  AWS's managed service for running Apache Airflow workflow orchestration, either as provisioned
  environments (Fargate-based schedulers, workers and web server billed by the hour) or, with MWAA
  Serverless, as pay-per-task-time workflows with no environment to size.
verified: 2026-11-20
status: current
broader: [kt:aws-analytics-services, kt:aws-application-integration-services]
related: [kt:aws-glue, kt:amazon-emr]
relations:
  - {predicate: implements, object: kt:apache-airflow}
  - {predicate: runsOn, object: kt:aws-fargate}
  - {predicate: dependsOn, object: kt:amazon-s3}
  - {predicate: scopedTo, object: kt:amazon-vpc}
  - {predicate: alternativeTo, object: kt:aws-step-functions}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
sources:
  - id: s1
    title: "What Is Amazon Managed Workflows for Apache Airflow?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/mwaa/latest/userguide/what-is-mwaa.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Configuring the Amazon MWAA environment class"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/mwaa/latest/userguide/environment-class.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Apache Airflow versions on Amazon Managed Workflows for Apache Airflow"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/mwaa/latest/userguide/airflow-versions.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "Amazon Managed Workflows for Apache Airflow pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/managed-workflows-for-apache-airflow/pricing/
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "Amazon MWAA Serverless now supports PythonOperator and BashOperator"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/08/mwaa-serverless-pythonoperator-bashoperator/
    type: official-blog
    accessed: 2026-11-20
  - id: s6
    title: "Amazon MWAA Serverless is now available in AWS GovCloud (US)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-mwaa-serverless-aws-govcloud/
    type: official-blog
    accessed: 2026-11-20
  - id: s7
    title: "Apache Airflow UI available in Amazon MWAA Serverless"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/11/airflow-ui-for-mwaa-serverless/
    type: official-blog
    accessed: 2026-11-20
created: 2026-11-20
---

# Amazon Managed Workflows for Apache Airflow

Amazon Managed Workflows for Apache Airflow (Amazon MWAA) runs open-source Apache Airflow for you, so data teams can author pipelines as Python DAGs while AWS handles setup, scaling, availability, patching and security of the Airflow infrastructure.[^s1] You pick an Airflow version, point the service at an Amazon S3 bucket that holds your DAGs and supporting files, and MWAA creates the scheduler, workers, web server and metadata database.[^s1] Workflows can call Athena, AWS Batch, DynamoDB, DataSync, EMR, EKS, Data Firehose, Glue, Lambda, Redshift, SQS, SNS, SageMaker AI and S3, plus the large catalogue of built-in and community Airflow operators and sensors.[^s1]

MWAA now comes in two shapes. The original **provisioned environments** are long-running Airflow clusters you size by environment class. **MWAA Serverless** provisions and scales compute on demand and charges only for the time workflow tasks actually run.[^s6][^s4]

## How it works

In a provisioned environment, the schedulers and workers run as AWS Fargate containers in private subnets of your Amazon VPC, using the Celery executor.[^s1][^s2] Each environment has its own AWS-managed metadata database (Aurora PostgreSQL), reached through a private VPC endpoint.[^s1][^s2] The schedulers and workers must be able to reach CloudWatch, S3, SQS and KMS, which sit outside the environment, so private networks need endpoints or a NAT path.[^s1] The Airflow web server can be exposed in Public mode over the internet or in Private mode inside the VPC, and access to it is authorised through IAM roles, with optional IAM Identity Center integration.[^s1] Data is encrypted with AWS KMS, and metrics and logs go to Amazon CloudWatch.[^s1] Workers scale automatically between minimum and maximum counts you set, and container-based fleets support the ECS and Kubernetes operators.[^s1]

The **environment class** fixes the size of every component. As documented in 2026-11, mw1.micro targets up to 25 DAGs with 3 concurrent tasks and does not autoscale; mw1.small targets 50 DAGs and 5 tasks; mw1.medium 250 and 10; mw1.large 1,000 and 20; mw1.xlarge 2,000 and 40; and mw1.2xlarge 4,000 DAGs and 80 concurrent tasks by default. Those DAG figures count DAG definitions, not runs.[^s2] Classes above mw1.micro run 2 to 5 schedulers (default 2), each paired with a triggerer; mw1.micro runs one.[^s2]

**MWAA Serverless** takes the environment away. You package code to Amazon S3 and reference it when you create or update a workflow. The service snapshots that code at creation and uses the snapshot for every later run.[^s5] Since August 2026 it supports PythonOperator and BashOperator for custom Python and shell steps.[^s5] In November 2026 it added the native Airflow UI, which acts with the permissions of the IAM identity that opens it. Its calls are logged in CloudTrail under that identity.[^s7]

## Versions, pricing and gotchas

- **Airflow versions (as of 2026-11).** Supported versions run from v2.7.2 to v3.3.1, which became available on MWAA on 1 September 2026. If you don't specify a version, a new environment gets the latest supported one.[^s3] AWS commits to supporting each version for at least 12 months after it becomes available and to announcing end of support at least 180 days in advance. Versions 2.4.3, 2.5.1 and 2.6.3 reached end of support on 30 December 2025.[^s3] In-place upgrades are limited to minor versions, so plan a major-version move (Airflow 2 to 3) separately.[^s3]
- **Provisioned pricing (us-east-1 example, as of 2026-11).** You pay by the hour, metered per second, for the environment class. Additional workers, schedulers and web servers are billed separately, and metadata database storage is billed per GB-month.[^s4] AWS's worked examples use $0.49/hour for a small environment with $0.055/hour per extra small worker, and $0.99/hour for a large environment with $0.22/hour per extra worker or scheduler. Its storage figure is $0.10 per GB-month.[^s4] The environment bills while it exists, even when no DAG is running, so an idle large environment costs about $737 a month before extras.[^s4]
- **Serverless pricing (us-east-1, as of 2026-11).** You are billed per task duration, per second, with a one-minute minimum. AWS-managed tasks and customer-managed tasks have separate hourly rates. AWS's example uses $0.080 per hour of task time.[^s4]
- **GovCloud.** MWAA Serverless has been available in AWS GovCloud (US-East) and (US-West) since September 2026.[^s6]

## When to use it (and when not)

Choose MWAA when your team already writes Airflow DAGs, needs Airflow's operator ecosystem to coordinate many systems, or wants to move a self-managed Airflow deployment without rewriting it.[^s1] Use MWAA Serverless for spiky or infrequent schedules, where an always-on environment would sit idle, and provisioned environments for steady, heavy workloads or where you need full control of the Airflow configuration.[^s4][^s6] If you don't need Airflow's DAG model or operators, AWS Step Functions (its own node in this graph) is the AWS-native orchestration alternative to compare against.

## Connections

MWAA sits in AWS's analytics and application-integration categories (**broader**) and **implements** Apache Airflow. Provisioned environments **run on** AWS Fargate inside your Amazon VPC (**scopedTo**) and **depend on** Amazon S3 for DAGs and code.[^s1][^s5] Access is **secured by** IAM, and operations are **monitored by** Amazon CloudWatch.[^s1][^s7] It is an **alternative to** AWS Step Functions for orchestration, and its operators drive AWS Glue and Amazon EMR jobs (**related**).[^s1]

## Sources
[^s1]: Amazon Web Services, "What Is Amazon Managed Workflows for Apache Airflow?". https://docs.aws.amazon.com/mwaa/latest/userguide/what-is-mwaa.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "Configuring the Amazon MWAA environment class". https://docs.aws.amazon.com/mwaa/latest/userguide/environment-class.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "Apache Airflow versions on Amazon Managed Workflows for Apache Airflow". https://docs.aws.amazon.com/mwaa/latest/userguide/airflow-versions.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "Amazon Managed Workflows for Apache Airflow pricing". https://aws.amazon.com/managed-workflows-for-apache-airflow/pricing/ (accessed 2026-11-20)
[^s5]: Amazon Web Services, "Amazon MWAA Serverless now supports PythonOperator and BashOperator" (What's New, 2026-08-18). https://aws.amazon.com/about-aws/whats-new/2026/08/mwaa-serverless-pythonoperator-bashoperator/ (accessed 2026-11-20)
[^s6]: Amazon Web Services, "Amazon MWAA Serverless is now available in AWS GovCloud (US)" (What's New, 2026-09-04). https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-mwaa-serverless-aws-govcloud/ (accessed 2026-11-20)
[^s7]: Amazon Web Services, "Apache Airflow UI available in Amazon MWAA Serverless" (What's New, 2026-11-12). https://aws.amazon.com/about-aws/whats-new/2026/11/airflow-ui-for-mwaa-serverless/ (accessed 2026-11-20)
