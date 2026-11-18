---
id: kt:fips-140-3-on-aws
type: ComplianceProgram
title: FIPS 140-3 on AWS
aliases: [FIPS 140 on AWS, FIPS 140-2 on AWS, AWS FIPS endpoints, FIPS endpoints, AWS FIPS 140-3 compliance, FIPS-validated cryptography on AWS]
domain: public-sector-and-compliance
summary: >-
  How AWS meets the U.S./Canadian FIPS 140-3 standard for cryptographic modules: services rely on
  CMVP-validated modules (AWS-LC, the AWS KMS and CloudHSM HSMs, Nitro engines), and customers who
  need FIPS-validated TLS in transit must opt in to FIPS endpoints, which exist only in U.S., GovCloud
  (US) and Canadian Regions.
verified: 2026-11-18
status: current
broader: [kt:aws-compliance-programs]
related: [kt:fedramp-on-aws, kt:aws-govcloud-us, kt:nist-sp-800-53, kt:aws-nitro-system]
relations:
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: securedBy, object: kt:aws-cloudhsm}
  - {predicate: configuredVia, object: kt:aws-sdks}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: integratesWith, object: kt:aws-privatelink}
  - {predicate: governedBy, object: kt:shared-responsibility-model}
sources:
  - id: s1
    title: "FIPS 140-3 Compliance - Amazon Web Services"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/fips
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "Dual-stack and FIPS endpoints - AWS SDKs and Tools Reference Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com//sdkref/latest/guide/feature-endpoints.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Enable FIPS Mode on AL2023 - Amazon Linux 2023"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/linux/al2023/ug/fips-mode.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Amazon S3 now supports PrivateLink for FIPS endpoints"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-s3-privatelink-fips-endpoints
    type: official-blog
    accessed: 2026-11-18
  - id: s5
    title: "FIPS 140-3 Transition Effort - Cryptographic Module Validation Program"
    publisher: National Institute of Standards and Technology (NIST CSRC)
    url: https://csrc.nist.gov/Projects/fips-140-3-transition-effort
    type: standards-body
    accessed: 2026-11-18
created: 2026-11-18
---

# FIPS 140-3 on AWS

FIPS 140 is a U.S. and Canadian government standard that sets security requirements for the cryptographic modules protecting sensitive information; 140-3 is the current version.[^s1] Modules are tested and certified through NIST's Cryptographic Module Validation Program (CMVP), where a module can be fully validated, in process, or on the Implementation Under Test list.[^s1] FIPS 140-3 was approved in March 2019 and took effect on September 22, 2019, replacing FIPS 140-2.[^s5] For U.S. federal workloads, FedRAMP programs and many regulated industries, "use FIPS-validated cryptography" is a recurring requirement, and AWS addresses it in two different ways: cryptography AWS runs for you, and FIPS endpoints you must choose.[^s1]

## How it works

**Cryptography AWS runs.** AWS maps each data path to a validated module. Amazon S3 and DynamoDB encryption at rest (every S3 mode, including SSE-S3, SSE-KMS, SSE-C and DSSE-KMS) uses AWS-LC in its static build. EBS volumes and EC2 instance store use Nitro hardware encryption engines, and VPC traffic between Nitro hosts uses the AWS Nitro Networking Cryptographic Module.[^s1] Links between data centers and Regions are encrypted at the physical layer with AES-256 MACsec.[^s1] AWS KMS keys are protected by HSMs that are FIPS 140-3 Level 3 validated or in validation and run only in FIPS-approved mode; AWS CloudHSM offers dedicated single-tenant HSMs validated at FIPS 140-3 Level 3.[^s1] AWS says these at-rest and internal solutions apply globally.[^s1]

AWS keeps modules current with an "update stream" approach: it patches validated modules running in approved mode and submits major versions for formal revalidation, citing FedRAMP's cryptographic module policy as the basis.[^s1]

**FIPS endpoints (encryption in transit).** Standard public service endpoints are not FIPS endpoints. To have the TLS session between your client and an AWS API terminate on a FIPS-validated library (the dynamic build of AWS-LC), you call a dedicated endpoint, usually a hostname containing `-fips`, for example `s3-fips.us-east-1.amazonaws.com`.[^s1][^s2] Rather than hard-coding hostnames, set `use_fips_endpoint = true` in the shared config file or `AWS_USE_FIPS_ENDPOINT=true` in the environment (Java and Kotlin also accept the `aws.useFipsEndpoint` system property). The default is false, and an explicit service-specific endpoint or the CLI's `--endpoint-url` overrides it.[^s2]

## Scope, quotas and gotchas

- **Regional scope (as of 2026-11).** FIPS endpoints exist only in us-east-1, us-east-2, us-west-1, us-west-2, the two GovCloud (US) Regions and ca-central-1 / ca-west-1, and not every service has one in every one of those Regions; check the per-service list on AWS's FIPS page.[^s1] Some GovCloud services serve FIPS-validated TLS on hostnames without `-fips`.[^s1]
- **Calls can fail.** With the setting on, a call to a service that has no FIPS endpoint in your Region may simply fail; the SDK guide does not promise a fallback.[^s2] AWS SDK for Java 1.x does not support the setting.[^s2]
- **Private connectivity.** Amazon S3 added AWS PrivateLink support for FIPS endpoints on 2026-09-03, at no additional cost, in the U.S., Canadian and GovCloud (US) Regions above, so FIPS traffic can stay inside a VPC.[^s4]
- **Your own instances.** Operating-system cryptography is your side of the boundary. Amazon Linux 2023 (AL2023.2 or later) enables FIPS mode with `fips-mode-setup --enable` and a reboot; ED25519 SSH keys are not supported in FIPS mode, so switch key types first or risk losing access.[^s3]
- **Retired HSM type.** The CloudHSM hsm1.medium certificate moved to the CMVP historical list on January 4, 2026; AWS tells customers to migrate to hsm2m.medium.[^s1]
- **140-2 sunset.** CMVP stopped taking new FIPS 140-2 submissions on April 1, 2022, and remaining 140-2 certificates moved to the historical list in September 2026. NIST says historical modules may still be bought and used in existing systems, with agencies deciding their own move to 140-3-only modules.[^s5]

## When to use it (and when not)

Turn on FIPS endpoints when a contract, FedRAMP package or agency policy requires FIPS-validated cryptography for data in transit to AWS APIs and your workload runs in a supported Region.[^s1][^s2] Outside those Regions, or for services without FIPS endpoints, validated at-rest encryption still applies, but an in-transit FIPS requirement needs a different approach, since AWS-side FIPS TLS is not offered there.[^s1] Remember that FIPS validation covers modules, not your system: you remain responsible for configuring clients, operating systems and applications to use them.[^s1][^s3]

## Connections

FIPS 140-3 on AWS sits under **AWS compliance programs** and relates to **FedRAMP on AWS**, whose module policy shapes AWS's update-stream approach.[^s1] It also relates to **NIST SP 800-53**, a companion NIST publication for federal security programs, while FIPS 140-3 itself is maintained through NIST's CMVP.[^s5] It is `scopedTo` **AWS Regions** because FIPS endpoints exist only in U.S., Canadian and **AWS GovCloud (US)** Regions.[^s1] It is `securedBy` **AWS KMS** and **AWS CloudHSM**, whose HSMs carry Level 3 validations, and relates to the **AWS Nitro System**, whose engines encrypt EBS, instance store and VPC traffic.[^s1] It is `configuredVia` the **AWS SDKs** endpoint setting, `integratesWith` **AWS PrivateLink** for S3 FIPS endpoints, and `governedBy` the **shared responsibility model** for everything on the customer side.[^s2][^s3][^s4]

## Sources
[^s1]: Amazon Web Services, "FIPS 140-3 Compliance". https://aws.amazon.com/compliance/fips (accessed 2026-11-18)
[^s2]: Amazon Web Services, "Dual-stack and FIPS endpoints - AWS SDKs and Tools Reference Guide". https://docs.aws.amazon.com//sdkref/latest/guide/feature-endpoints.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Enable FIPS Mode on AL2023 - Amazon Linux 2023". https://docs.aws.amazon.com/linux/al2023/ug/fips-mode.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Amazon S3 now supports PrivateLink for FIPS endpoints". https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-s3-privatelink-fips-endpoints (accessed 2026-11-18)
[^s5]: NIST CSRC, "FIPS 140-3 Transition Effort". https://csrc.nist.gov/Projects/fips-140-3-transition-effort (accessed 2026-11-18)
