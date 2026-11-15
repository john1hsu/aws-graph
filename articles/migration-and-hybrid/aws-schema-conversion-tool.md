---
id: kt:aws-schema-conversion-tool
type: Tool
title: AWS Schema Conversion Tool
aliases: [AWS SCT, SCT, Schema Conversion Tool]
domain: migration-and-hybrid
summary: >-
  AWS's free, downloadable desktop application that assesses and converts database, data
  warehouse, ETL and application SQL code from one engine to another (for example Oracle to
  Aurora PostgreSQL or Teradata to Amazon Redshift), and whose data extraction agents move
  data warehouse data into Amazon Redshift.
verified: 2026-11-15
status: current
broader: [kt:aws-migration-and-transfer-services]
related: [kt:seven-rs-migration-strategies, kt:amazon-aurora, kt:amazon-rds]
relations:
  - {predicate: integratesWith, object: kt:aws-database-migration-service}
  - {predicate: contrastsWith, object: kt:dms-schema-conversion}
  - {predicate: integratesWith, object: kt:amazon-redshift}
  - {predicate: integratesWith, object: kt:aws-snowball-edge}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
sources:
  - id: s1
    title: "What is the AWS Schema Conversion Tool?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Welcome.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Installing AWS Schema Conversion Tool"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.Procedure.html
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Migrating data from on-premises data warehouse to Amazon Redshift (data extraction agents)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/agents.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "Converting database schemas using DMS Schema Conversion"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/dms/latest/userguide/CHAP_SchemaConversion.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "AWS Database Migration Service FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/dms/faqs
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "Convert the schema (Database migration strategy)"
    publisher: AWS Prescriptive Guidance
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-database-migration/convert-schema.html
    type: official-docs
    accessed: 2026-11-15
created: 2026-11-15
---

# AWS Schema Conversion Tool

The AWS Schema Conversion Tool (AWS SCT) is a project-based desktop application that converts an existing database schema from one engine to another. Its targets include Amazon RDS (MySQL, MariaDB, Oracle, SQL Server, PostgreSQL), Amazon Aurora, Amazon Redshift, databases running on Amazon EC2, and data in Amazon S3. Objects it cannot convert automatically are flagged with guidance for building an equivalent on the target.[^s1] AWS Prescriptive Guidance describes it as a free tool for heterogeneous migrations, where engines differ and some packages or features have no direct counterpart; for homogeneous moves the source engine's native tools are usually enough.[^s6]

## How it works

You install SCT on a workstation (Windows 10, Fedora Linux 36 or later, or Ubuntu Linux 18 or later) and download the JDBC drivers for your source and target engines.[^s2] The workflow is: connect to source and target, generate a database migration assessment report that summarizes the conversion effort and action items, convert, then either apply the converted schema to the target or save it as SQL files.[^s6]

Its scope is wider than OLTP schemas:

- **OLTP**: sources such as Oracle 10.1+, SQL Server 2008 R2 through 2022, IBM Db2 LUW and Db2 for z/OS, SAP ASE, MySQL, PostgreSQL and Azure SQL Database, converting mainly to Aurora or RDS MySQL and PostgreSQL. Babelfish for Aurora PostgreSQL is supported for assessment reports only.[^s1]
- **Data warehouses**: Teradata, Netezza, Greenplum, Vertica, Oracle, SQL Server, Snowflake, BigQuery and Azure Synapse Analytics, all to Amazon Redshift; SCT can also recommend sort and distribution keys for an existing Redshift database.[^s1]
- **NoSQL, ETL and big data**: Apache Cassandra to Amazon DynamoDB; SSIS packages to AWS Glue; Teradata BTEQ and FastLoad-style scripts to Amazon Redshift RSQL; Hive to Amazon EMR, HDFS to Amazon S3, and Oozie to AWS Step Functions.[^s1]
- **Application code**: SQL embedded in Java, C#, C++ and other code can be converted and reviewed.[^s1]
- **Extension packs** install AWS Lambda functions and Python libraries that emulate source features the target lacks.[^s1]

## Data extraction agents

For data warehouse moves, SCT manages separate data extraction agents that you install on your own hosts (the docs recommend one agent per host with at least 4 vCPUs and 32 GB of memory). Each task can extract to a local folder, upload compressed files to Amazon S3, or upload and then run the Redshift `COPY` automatically. Large tables can be split with virtual or native partitioning so subtasks run in parallel.[^s3] For very large transfers an agent can write to an AWS Snowball Edge device (up to 100 TB per device) instead of uploading over the network.[^s3] Agents do not capture ongoing changes; AWS points to AWS DMS for replication.[^s3]

## Pricing, gotchas and lifecycle

- **Cost** (as of 2026-11): Prescriptive Guidance calls SCT free; you pay for the AWS resources you migrate into, the S3 staging storage and any DMS replication.[^s6][^s5]
- **Desktop footprint**: conversion runs on your machine and needs network reach to both databases, unlike the managed DMS Schema Conversion.[^s2][^s4]
- **Manual work remains**: complex procedural code often needs hand fixes; AWS migration playbooks document engine differences.[^s6]
- **Compliance**: the user guide states SCT is FedRAMP compliant and supports FIPS endpoints for S3 and other AWS resources.[^s1]
- **Status**: the user guide carries no deprecation or end-of-support notice as of 2026-11.[^s1]

## When to use it (and when not)

AWS now offers two ways to convert schemas. **DMS Schema Conversion** is a fully managed, web-based feature of AWS DMS that builds on the SCT conversion engine, adds generative-AI-assisted conversion for paths that target PostgreSQL, and is driven from the console, CLI or API; its paths cover Oracle, SQL Server, Db2, SAP ASE, MySQL and PostgreSQL sources, with Redshift as a target only for Oracle.[^s4] The DMS FAQ positions SCT as the downloadable option used mainly for large data warehouse migrations, while DMS Schema Conversion is free as part of DMS apart from storage.[^s5] Choose SCT when the source is a data warehouse, Cassandra, ETL scripts or embedded application SQL, or when you need extraction agents or Snowball Edge for bulk loads; choose DMS Schema Conversion for supported OLTP paths when you want a managed service with no local install.[^s4][^s5]

## Connections

- **Broader**: one of the AWS migration and transfer services (`kt:aws-migration-and-transfer-services`).[^s6]
- **Integrates with AWS Database Migration Service** (`kt:aws-database-migration-service`): SCT can create DMS endpoints and tasks and monitor them, so SCT handles code and DMS moves and replicates data.[^s1]
- **Contrasts with DMS Schema Conversion** (`kt:dms-schema-conversion`), the managed feature built on the same engine.[^s4]
- **Integrates with Amazon Redshift** (`kt:amazon-redshift`) as the warehouse target its agents load via `COPY`, and with **AWS Snowball Edge** (`kt:aws-snowball-edge`) for offline bulk transfer.[^s3]
- **Related to Amazon Aurora** (`kt:amazon-aurora`) and **Amazon RDS** (`kt:amazon-rds`), the usual OLTP targets, and to the **7 Rs** (`kt:seven-rs-migration-strategies`), where schema conversion supports replatform and refactor moves.[^s1][^s6]
- **Complies with FedRAMP** (`kt:fedramp-on-aws`) per the user guide.[^s1]

## Sources
[^s1]: Amazon Web Services, "What is the AWS Schema Conversion Tool?". https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Welcome.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Installing AWS Schema Conversion Tool". https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.Procedure.html (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Migrating data from on-premises data warehouse to Amazon Redshift (data extraction agents)". https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/agents.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "Converting database schemas using DMS Schema Conversion". https://docs.aws.amazon.com/dms/latest/userguide/CHAP_SchemaConversion.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "AWS Database Migration Service FAQs". https://aws.amazon.com/dms/faqs (accessed 2026-11-15)
[^s6]: AWS Prescriptive Guidance, "Convert the schema (Database migration strategy)". https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-database-migration/convert-schema.html (accessed 2026-11-15)
