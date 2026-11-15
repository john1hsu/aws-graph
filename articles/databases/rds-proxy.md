---
id: kt:rds-proxy
type: Feature
title: Amazon RDS Proxy
aliases: [RDS Proxy]
domain: databases
summary: >-
  A fully managed, highly available database proxy for Amazon RDS and Aurora that pools and multiplexes
  client connections, keeps them open through failovers, and can enforce IAM authentication in front of
  the database.
verified: 2026-11-15
status: current
broader: [kt:amazon-rds]
related: [kt:amazon-aurora, kt:aurora-serverless-v2, kt:aurora-global-database]
relations:
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: dependsOn, object: kt:aws-secrets-manager}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: dependsOn, object: kt:amazon-vpc}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
sources:
  - id: s1
    title: "Amazon RDS Proxy"
    publisher: Amazon Web Services (Amazon RDS User Guide)
    url: https://docs.aws.amazon.com/us_us/AmazonRDS/latest/UserGuide/rds-proxy.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "RDS Proxy concepts and terminology"
    publisher: Amazon Web Services (Amazon RDS User Guide)
    url: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.howitworks.md
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "Amazon RDS Proxy for Aurora"
    publisher: Amazon Web Services (Amazon Aurora User Guide)
    url: https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/rds-proxy
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "RDS Proxy connection considerations"
    publisher: Amazon Web Services (Amazon Aurora User Guide)
    url: https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/rds-proxy-connections.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "Amazon RDS Proxy pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/rds/proxy/pricing
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "Amazon RDS Proxy FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/rds/proxy/faqs/
    type: official-docs
    accessed: 2026-11-15
  - id: s7
    title: "Amazon RDS Proxy announces support for end-to-end IAM authentication"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/09/amazon-rds-proxy-end-to-end-iam-authentication
    type: official-blog
    accessed: 2026-11-15
  - id: s8
    title: "Amazon RDS Proxy now available in the AWS GovCloud (US) Regions (Spanish-language edition)"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/es/about-aws/whats-new/2025/04/amazon-rds-proxy-aws-govcloud-us-regions/?nc1=h_ls
    type: official-blog
    accessed: 2026-11-15
  - id: s9
    title: "Using RDS Proxy with Aurora global databases"
    publisher: Amazon Web Services (Amazon Aurora User Guide)
    url: https://docs.aws.amazon.com/hi_in/AmazonRDS/latest/AuroraUserGuide/rds-proxy-gdb.html
    type: official-docs
    accessed: 2026-11-15
created: 2026-11-15
---

# Amazon RDS Proxy

Amazon RDS Proxy is a fully managed, highly available proxy that sits between applications and an RDS or Aurora database. Applications connect to the proxy endpoint instead of the database, and the proxy keeps a shared pool of database connections, queues or throttles bursts it can't serve at once, and sheds load when requests exceed your limits so the database stays stable. Most applications need no code change beyond the new endpoint.[^s1] It supports Aurora MySQL, Aurora PostgreSQL, RDS for MySQL, RDS for PostgreSQL, RDS for MariaDB and RDS for SQL Server.[^s6] Its compute is serverless, scales with the workload, runs across multiple Availability Zones and is separate from the DB instance's own CPU and memory.[^s2]

## How it works

Each proxy has a **target group** holding one RDS DB instance or Aurora cluster (several proxies may target the same database), and you pick an **engine family** when you create it.[^s1][^s2] The proxy pools connections separately for writer and readers and **multiplexes** them: all statements in one transaction run on one database connection, and the next transaction can use a different one, so many client connections share fewer server connections and "too many connections" errors become less likely.[^s2] When the proxy can't be sure a session's state is safe to move, it **pins** the session to one connection until it ends; any statement larger than 16 KB causes pinning, and the `DatabaseConnectionsCurrentlySessionPinned` CloudWatch metric shows how often it happens.[^s1][^s2]

The default endpoint sends traffic to the writer. For Aurora you can add up to 20 extra endpoints, including read-only ones that pass connections to the cluster's reader endpoint, and an endpoint in a different VPC gives cross-VPC access.[^s3] During a Multi-AZ failover the proxy keeps the same IP address, holds idle client connections open, queues new requests, and routes them to the new writer; only connections in the middle of a transaction or statement are cancelled.[^s2] AWS says this cuts failover times for RDS and Aurora Multi-AZ databases by up to 66%.[^s6]

## Security

Clients can authenticate with database credentials, with **standard IAM authentication** (IAM to the proxy, Secrets Manager credentials from proxy to database), or with **end-to-end IAM authentication**, where the proxy's role holds `rds-db:connect` and no per-user secrets are registered.[^s2] End-to-end IAM launched on 2025-09-12 for MySQL and PostgreSQL engines in all Regions where RDS Proxy runs; it isn't available for SQL Server.[^s7][^s1] The proxy uses ACM certificates, supports TLS 1.0–1.3 (clients may use a newer TLS version than the database), and can require TLS between proxy and database. It doesn't negotiate hybrid post-quantum (ML-KEM) key exchange, so clients offering only post-quantum groups fail to connect.[^s2]

## Quotas, pricing and gotchas

- **Quotas (as of 2026-11):** 20 proxies per account (adjustable via Service Quotas); up to 200 Secrets Manager secrets, and so 200 database users, per proxy; the default endpoint uses only two AZs; one target database per proxy.[^s1][^s3]
- **Pool settings:** the proxy's ceiling is `MaxConnectionsPercent` of the target's `max_connections`. `MaxIdleConnectionsPercent` defaults to half of that, `IdleClientTimeout` to 1,800 seconds, and `ConnectionBorrowTimeout` to 120 seconds. Some connections are reserved for monitoring and failover, so watch `MaxDatabaseConnectionsAllowed`.[^s4]
- **Pricing (as of 2026-11):** billed per vCPU-hour of the underlying provisioned instances, or per ACU-hour for Aurora Serverless, in 1-second increments with a 10-minute minimum after a billable status change. Rates vary by Region and the proxy isn't offered everywhere. The default endpoint is free, but each extra endpoint provisions an AWS PrivateLink interface endpoint billed separately.[^s5]
- **Networking:** the proxy must be in the database's VPC, can't be publicly accessible, and isn't supported in dedicated-tenancy VPCs or VPCs with encryption controls in Enforce Mode. In us-east-1 it isn't supported in AZ `use1-az3`.[^s1]
- **Engine limits:** no PostgreSQL query cancellation (`CancelRequest`) or streaming replication, no MySQL compressed mode or dual passwords, and no Active Directory logins for SQL Server. Parameter-group changes reach the proxy only after a reboot, and the `rdsproxyadmin` user it creates must not be changed.[^s1][^s3]
- **Aurora specifics:** it works with Aurora Serverless (but not Serverless v1) clusters, and with any cluster in a global database, where read/write endpoints on a secondary cluster fail because it has no writer.[^s3][^s9]

## When to use it (and when not)

Use RDS Proxy when many short-lived clients open connections, as AWS Lambda functions do: it absorbs connection bursts, and the function's execution role can authenticate without storing database credentials.[^s6] It also fits applications that need faster, less disruptive failovers, or teams that want to enforce IAM and modern TLS without changing how the database authenticates.[^s2] It adds less when an application already has a well-tuned connection pool and few connections, or when sessions pin heavily, since a pinned session gets no multiplexing benefit.[^s2] RDS Proxy has been available in both AWS GovCloud (US) Regions since 2025-04-02.[^s8]

## Connections

RDS Proxy is a capability of **Amazon RDS** (its broader node) and also fronts **Amazon Aurora** clusters, including **Aurora Serverless** and members of an **Aurora global database**.[^s3][^s9] It **integratesWith** **AWS Lambda**, the workload AWS most often pairs it with.[^s6] It **dependsOn** **AWS Secrets Manager** for database credentials unless end-to-end IAM is used, and is **securedBy** **AWS IAM** authentication.[^s2][^s7] It **dependsOn** an **Amazon VPC** because it lives in the database's VPC, and it is **monitoredBy** **Amazon CloudWatch** metrics such as borrow latency and pinned connections.[^s1][^s4]

## Sources
[^s1]: Amazon Web Services, "Amazon RDS Proxy" (Amazon RDS User Guide). https://docs.aws.amazon.com/us_us/AmazonRDS/latest/UserGuide/rds-proxy.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "RDS Proxy concepts and terminology" (Amazon RDS User Guide). https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.howitworks.md (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Amazon RDS Proxy for Aurora" (Amazon Aurora User Guide). https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/rds-proxy (accessed 2026-11-15)
[^s4]: Amazon Web Services, "RDS Proxy connection considerations" (Amazon Aurora User Guide). https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/rds-proxy-connections.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "Amazon RDS Proxy pricing". https://aws.amazon.com/rds/proxy/pricing (accessed 2026-11-15)
[^s6]: Amazon Web Services, "Amazon RDS Proxy FAQs". https://aws.amazon.com/rds/proxy/faqs/ (accessed 2026-11-15)
[^s7]: Amazon Web Services, "Amazon RDS Proxy announces support for end-to-end IAM authentication" (What's New, 2025-09-12). https://aws.amazon.com/about-aws/whats-new/2025/09/amazon-rds-proxy-end-to-end-iam-authentication (accessed 2026-11-15)
[^s8]: Amazon Web Services, "Amazon RDS Proxy now available in the AWS GovCloud (US) Regions" (What's New, 2025-04-02, Spanish-language edition). https://aws.amazon.com/es/about-aws/whats-new/2025/04/amazon-rds-proxy-aws-govcloud-us-regions/?nc1=h_ls (accessed 2026-11-15)
[^s9]: Amazon Web Services, "Using RDS Proxy with Aurora global databases" (Amazon Aurora User Guide). https://docs.aws.amazon.com/hi_in/AmazonRDS/latest/AuroraUserGuide/rds-proxy-gdb.html (accessed 2026-11-15)
