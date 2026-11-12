---
id: kt:aws-transfer-family
type: Service
title: AWS Transfer Family
aliases: [Transfer Family, AWS Transfer for SFTP]
domain: migration-and-hybrid
summary: >-
  AWS's fully managed file-transfer service that exposes SFTP, FTPS, FTP and AS2 endpoints, browser-based
  web apps and outbound connectors in front of Amazon S3 and Amazon EFS, so partners and applications can
  keep their existing clients while files land directly in AWS storage.
verified: 2026-11-12
status: current
broader: [kt:aws-migration-and-transfer-services]
related: [kt:amazon-efs, kt:amazon-vpc, kt:aws-govcloud-us]
relations:
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: contrastsWith, object: kt:aws-datasync}
  - {predicate: governedBy, object: kt:transfer-family-quotas}
  - {predicate: securedBy, object: kt:aws-secrets-manager}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-solutions-architect-associate}
sources:
  - id: s1
    title: "What is AWS Transfer Family?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/transfer/latest/userguide/what-is-aws-transfer-family.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Configuring an SFTP, FTPS, or FTP server endpoint"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/transfer/latest/userguide/sftp-for-transfer-family.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "AWS Transfer Family endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/transfer-service.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "AWS Transfer Family Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/aws-transfer-family/pricing/
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "AWS Transfer Family SFTP Connectors now support continuing file transfers during credential rotation"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/transfer-family-sftp-credential-rotation/
    type: official-blog
    accessed: 2026-11-12
  - id: s6
    title: "Managing users for server endpoints"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/transfer/latest/userguide/create-user.html
    type: official-docs
    accessed: 2026-11-12
  - id: s7
    title: "In-Scope AWS Services - AWS Certified Solutions Architect - Associate (SAA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html
    type: official-learning
    accessed: 2026-11-12
created: 2026-11-12
---

# AWS Transfer Family

AWS Transfer Family is a fully managed service for moving files into and out of Amazon S3 and Amazon EFS over the protocols business partners already use: SFTP (version 3), FTPS, FTP and AS2, plus browser-based transfers through Transfer Family web apps.[^s1] The point is that existing clients, scripts and firewall rules keep working unchanged, while the files land in AWS storage where analytics, archival and event-driven processing can pick them up. There is no server fleet to run; AWS operates an auto-scaling, redundant fleet spread across up to three Availability Zones, and billing is pay-as-you-go with no upfront cost.[^s1]

## How it works

You create a **server** (an endpoint), choose its protocols and storage domain (S3 or EFS), point your own hostname at it, and add users with the right level of access.[^s1] Each user maps to an IAM role and a home directory; logical directory mappings can hide real bucket paths, S3 access is narrowed with session policies, and EFS access is enforced through POSIX user and group IDs.[^s6] Identity can be **service-managed** (users and SSH public keys stored in the service), **AWS Directory Service** for Microsoft Active Directory or Entra ID Domain Services (100 AD groups per server by default), or a **custom identity provider** backed by AWS Lambda or Amazon API Gateway.[^s2][^s6] Usernames of one or two characters, and `root`, cannot authenticate to SFTP servers.[^s6]

The endpoint type determines who can connect. A **Public** endpoint serves SFTP only, has no static IP and cannot use source-IP allow lists. A **VPC – Internet** endpoint (SFTP, FTPS, AS2) attaches Elastic IP addresses, including bring-your-own IPs, that never change, so partners can allow-list them. A **VPC – Internal** endpoint is reachable only from the VPC and connected networks over VPN or Direct Connect, and is the only type that allows plain FTP. VPC endpoints can be filtered with security groups and network ACLs; the older `VPC_ENDPOINT` type is deprecated for new servers.[^s2]

Beyond inbound servers, the family includes **AS2** for B2B and EDI-style exchanges, **SFTP connectors** that push or pull files to remote partner SFTP servers using credentials stored in AWS Secrets Manager, **web apps** that give business users browser access to S3 (including S3 Access Grants), and **managed workflows** that copy, tag, scan, filter, compress, encrypt or decrypt files after upload.[^s1][^s5]

## Quotas, pricing and gotchas

Default quotas per account per Region (as of 2026-11): 50 servers (adjustable), of which at most 10 may use the legacy VPC_ENDPOINT type (not adjustable); 10,000 concurrent sessions per server (not adjustable); 10,000 service-managed users per server and 50 SSH keys per user (both adjustable); a 5 TB maximum file size; a 1,800-second idle timeout; 100 SFTP connectors and 10 web apps (adjustable); and 10 workflows per account.[^s3] FIPS endpoints are offered only in the four US commercial Regions, two Canada Regions and both AWS GovCloud (US) Regions.[^s3]

Pricing, using the US East (N. Virginia) examples on the pricing page (as of 2026-11): $0.30 per hour for each protocol enabled on an endpoint, $0.04 per GB uploaded or downloaded over SFTP, FTPS or FTP, $0.40 per GB plus $0.001 per call for SFTP connectors, $0.01 per AS2 message for the first 100,000 a month, $0.50 per web app unit-hour, and $0.10 per GB decrypted in a workflow. S3, EFS, Lambda, CloudWatch and other underlying services are billed separately.[^s4] The main gotcha is that the hourly fee accrues per protocol for as long as the server exists, even when idle, so a test server with SFTP and FTPS enabled costs about $0.60 an hour around the clock. Other traps: service-managed identity works only with SFTP, Active Directory users authenticate with passwords only, and putting a Network Load Balancer in front of FTP/FTPS is discouraged.[^s2]

## When to use it (and when not)

Use Transfer Family when external parties or legacy applications must keep using SFTP, FTPS, FTP or AS2 and you want the files in S3 or EFS without running your own file-transfer servers, for example vendor feeds into a data lake, supply-chain and payments exchanges over AS2, or subscription data distribution.[^s1] It is not a bulk migration engine: for one-time or scheduled copies of NAS, HDFS or object data into AWS, AWS DataSync is the better fit, and server or database migrations belong to their own tools.

## Connections

Transfer Family belongs to the Migration and Transfer category and is listed there among the in-scope services for the AWS Certified Solutions Architect – Associate (SAA-C03) exam, alongside DataSync and AWS DMS.[^s7] Its storage targets are Amazon S3 and Amazon EFS, and users can be defined in the service, in a directory, or in a custom identity provider.[^s1][^s6] It contrasts with AWS DataSync (bulk data movement versus protocol endpoints for partners), runs its private endpoints inside an Amazon VPC, stores connector credentials in AWS Secrets Manager, and is scoped to AWS Regions, including the GovCloud (US) Regions with FIPS endpoints.[^s2][^s3][^s5] Its limits form the Transfer Family quotas node.[^s3]

## Sources

[^s1]: Amazon Web Services, "What is AWS Transfer Family?". https://docs.aws.amazon.com/transfer/latest/userguide/what-is-aws-transfer-family.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Configuring an SFTP, FTPS, or FTP server endpoint". https://docs.aws.amazon.com/transfer/latest/userguide/sftp-for-transfer-family.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "AWS Transfer Family endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/transfer-service.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, "AWS Transfer Family Pricing". https://aws.amazon.com/aws-transfer-family/pricing/ (accessed 2026-11-12)
[^s5]: Amazon Web Services, "AWS Transfer Family SFTP Connectors now support continuing file transfers during credential rotation". https://aws.amazon.com/about-aws/whats-new/2026/09/transfer-family-sftp-credential-rotation/ (accessed 2026-11-12)
[^s6]: Amazon Web Services, "Managing users for server endpoints". https://docs.aws.amazon.com/transfer/latest/userguide/create-user.html (accessed 2026-11-12)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Solutions Architect - Associate". https://docs.aws.amazon.com/aws-certification/latest/solutions-architect-associate-03/saa-03-in-scope-services.html (accessed 2026-11-12)
