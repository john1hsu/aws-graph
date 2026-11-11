---
id: kt:amazon-emr
type: Service
title: Amazon EMR
aliases: [EMR, Amazon Elastic MapReduce, Elastic MapReduce, Amazon EMR Serverless, EMR on EKS]
domain: analytics
summary: >-
  AWS's managed big data platform for running open-source frameworks such as Apache Spark, Hive,
  Trino, Flink and HBase at scale, deployable as EC2 clusters, on Amazon EKS, or serverless, with an
  EMR charge added on top of the underlying compute.
verified: 2026-11-11
status: current
broader: [kt:data-lake-on-aws, kt:aws-analytics-services]
related: [kt:aws-glue, kt:amazon-athena, kt:amazon-redshift]
relations:
  - {predicate: runsOn, object: kt:amazon-ec2}
  - {predicate: runsOn, object: kt:amazon-eks}
  - {predicate: dependsOn, object: kt:amazon-s3}
  - {predicate: pricedBy, object: kt:spot-instances}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "Understanding how to create and work with Amazon EMR clusters - Amazon EMR"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-overview.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Amazon EMR architecture and service layers - Amazon EMR"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-overview-arch.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "What is Amazon EMR Serverless? - Amazon EMR"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/emr/latest/EMR-Serverless-UserGuide/emr-serverless.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Amazon EMR Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/emr/pricing/
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Amazon EMR FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/emr/faqs/
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "Amazon EMR - Big Data Analytics Options on AWS (whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/big-data-analytics-options/amazon-emr
    type: official-docs
    accessed: 2026-11-11
created: 2026-11-11
---

# Amazon EMR

Amazon EMR (originally Elastic MapReduce) is AWS's managed platform for running big data frameworks such as Apache Hadoop, Spark, HBase, Presto and Flink on dynamically scalable compute.[^s1] AWS now positions it around performance-optimized runtimes for Spark, Trino, Flink and Hive, and offers three main ways to run it: clusters of EC2 instances that you size and control, job submission onto an existing Amazon EKS cluster, and EMR Serverless, which removes cluster management entirely.[^s5] It is the "bring your own open-source engine" option in the AWS analytics portfolio.

## How it works

On EMR on EC2, a **cluster** is a set of EC2 instances (nodes) with three roles. The **primary** node coordinates task distribution and monitors health; every cluster has one. **Core** nodes run tasks and hold data in HDFS, and multi-node clusters need at least one. **Task** nodes only run tasks and store no HDFS data, so they are optional and safe to add or remove.[^s1] Capacity is defined as instance groups or instance fleets (fleets mix instance types and On-Demand/Spot targets), and that choice cannot be changed after the cluster is created.[^s1] For availability you can run one or three primary nodes; three requires EMR 5.23.0 or later.[^s6]

Work arrives as ordered **steps** defined at launch, steps submitted later through the console, CLI or API, or interactive sessions over SSH. A cluster moves through STARTING, BOOTSTRAPPING (custom bootstrap actions), RUNNING and WAITING, then either auto-terminates or stays up as a long-running cluster.[^s1] Under the hood, YARN is the default resource manager, and EMR pins YARN application masters to core nodes using node labels so jobs survive the loss of Spot task nodes.[^s2]

Storage is layered: HDFS is ephemeral and disappears when the cluster terminates; EMRFS lets Hadoop treat Amazon S3 like a file system, so the common pattern is S3 for input and output with HDFS for intermediate data. From EMR 7.10.0 the S3A connector is the default for all S3 URI schemes.[^s2] Keeping durable data in S3 is what makes transient, job-scoped clusters practical.[^s6]

**EMR Serverless** replaces clusters with *applications* tied to a release version and runtime (for example Spark or Hive). You submit *job runs*, each with its own runtime IAM role, and the service provisions and decommissions workers automatically; pre-initialized capacity keeps a warm pool for jobs that must start in seconds.[^s3] **EMR on EKS** runs jobs on demand on Kubernetes without provisioning EMR clusters.[^s5] EMR Studio provides managed Jupyter notebooks with IAM Identity Center sign-in and Git integration, at no added charge beyond the underlying resources.[^s5]

## Quotas, pricing and gotchas

Pricing (as of 2026-11, examples from the AWS pricing page):[^s4]

- **EMR on EC2:** per-second billing with a one-minute minimum; the EMR fee is added to the EC2 (and EBS) charge. In us-east-1 a c4.2xlarge carries a $0.105/hour EMR charge on top of $0.398/hour for EC2.[^s4]
- **EMR on EKS:** charged on requested vCPU and memory, per second; us-east-1 example rates are $0.007084 per vCPU-hour and $0.000777875 per GB-hour, on top of the EKS cluster fee and EC2 or Fargate capacity.[^s4]
- **EMR Serverless:** per-second billing (one-minute minimum) for worker vCPU, memory and storage; the page's example uses $0.052624 per vCPU-hour and $0.0057785 per GB-hour without naming a Region. The first 20 GB of standard storage per worker is included, but shuffle-optimized storage is billed in full.[^s4]

On EMR Serverless, worker storage defaults to 20 GB and can be raised to 200 GB (up to 2 TB with shuffle-optimized disks), and idle workers are released after 15 minutes by default.[^s5]

Gotchas: anything only in HDFS is lost when a cluster terminates, including a failure that ends in TERMINATED_WITH_ERRORS; enable termination protection on clusters you can't afford to lose.[^s1] EMR replaces failed core nodes but does not recover a cluster that loses all its nodes.[^s6] Spot is best placed on task fleets, and is not supported in AWS GovCloud (US).[^s5] AWS's own anti-pattern guidance is small datasets that fit on one machine, where distributed overhead outweighs the benefit.[^s6]

## When to use it (and when not)

Choose EMR when you need open-source engines with control over versions, configuration and instance types: large ETL, log and clickstream processing, genomics or risk modelling.[^s6] Prefer EMR Serverless when you want Spark or Hive without sizing clusters, and EMR on EC2 for long-running clusters or specific hardware.[^s5] For serverless SQL over S3 Amazon Athena is simpler, for managed Spark ETL with a built-in catalog AWS Glue needs less tuning, and for warehouse-style BI Amazon Redshift fits better; EMR earns its extra operational overhead when the workload is large and needs engine-level control.[^s6]

## Connections

- **Runs on Amazon EC2 and Amazon EKS** (`kt:amazon-ec2`, `kt:amazon-eks`): the two provisioned deployment models, with the EMR fee added to their compute charges.[^s4][^s5]
- **Depends on Amazon S3** (`kt:amazon-s3`) through EMRFS/S3A for durable input and output.[^s2]
- **Priced by Spot Instances** (`kt:spot-instances`): Spot, which the pricing page says can be up to 90% cheaper than On-Demand, is the main cost lever for task capacity.[^s4][^s5]
- **Monitored by Amazon CloudWatch** (`kt:amazon-cloudwatch`) for node health and replacement.[^s6]
- **Related to AWS Glue, Amazon Athena and Amazon Redshift**, the other engines of an AWS data lake (`kt:data-lake-on-aws`), and **scoped to Regions**: EMR Serverless applications are Regional.[^s3]

## Sources
[^s1]: Amazon Web Services, "Understanding how to create and work with Amazon EMR clusters". https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-overview.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Amazon EMR architecture and service layers". https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-overview-arch.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "What is Amazon EMR Serverless?". https://docs.aws.amazon.com/emr/latest/EMR-Serverless-UserGuide/emr-serverless.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Amazon EMR Pricing". https://aws.amazon.com/emr/pricing/ (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Amazon EMR FAQs". https://aws.amazon.com/emr/faqs/ (accessed 2026-11-11)
[^s6]: Amazon Web Services, "Amazon EMR", in Big Data Analytics Options on AWS (whitepaper). https://docs.aws.amazon.com/whitepapers/latest/big-data-analytics-options/amazon-emr (accessed 2026-11-11)
