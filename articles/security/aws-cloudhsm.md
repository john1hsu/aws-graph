---
id: kt:aws-cloudhsm
type: Service
title: AWS CloudHSM
aliases: [CloudHSM, Amazon CloudHSM]
domain: security
summary: >-
  AWS's service for running single-tenant hardware security modules (HSMs), grouped
  into highly available clusters, where you alone manage the keys and HSM users and call them through
  standard interfaces such as PKCS #11, JCE and Microsoft CNG/KSP.
verified: 2026-11-15
status: current
broader: [kt:aws-security-services]
related: [kt:aws-kms, kt:aws-payment-cryptography, kt:amazon-vpc]
relations:
  - {predicate: alternativeTo, object: kt:aws-kms}
  - {predicate: integratesWith, object: kt:aws-kms}
  - {predicate: scopedTo, object: kt:availability-zones}
  - {predicate: governedBy, object: kt:cloudhsm-quotas}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
sources:
  - id: s1
    title: "What is AWS CloudHSM?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cloudhsm/latest/userguide/introduction.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "AWS CloudHSM FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/cloudhsm/faqs/
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "AWS CloudHSM quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cloudhsm/latest/userguide/limits.html
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "Deprecation Notifications - AWS CloudHSM"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/cloudhsm/latest/userguide/compliance-dep-notif.html
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "AWS CloudHSM key stores - AWS Key Management Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/kms/latest/developerguide/keystore-cloudhsm.html
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-15
created: 2026-11-15
---

# AWS CloudHSM

AWS CloudHSM gives you dedicated hardware security modules in the AWS Cloud: devices that perform cryptographic operations and store key material securely. AWS provisions the hardware, patches it and takes backups, but the HSMs are single-tenant and general purpose, so you choose the algorithms and key sizes, generate or import your own keys, and manage HSM users yourself rather than through IAM roles. The data plane is end-to-end encrypted and not visible to AWS.[^s1] AWS states that it has no access to the keys or data in your cluster, and that if you lose your HSM credentials it cannot recover the keys for you.[^s2]

## How it works

HSMs are grouped into **clusters**. A cluster runs in **FIPS mode**, where only FIPS-validated keys and algorithms are allowed and the HSMs are FIPS 140-2 or FIPS 140-3 Level 3 validated, or in **non-FIPS mode**, which unlocks every algorithm the service supports for workloads that are not bound by FIPS rules.[^s1] HSMs in a cluster keep keys and users synchronized, and AWS takes an encrypted backup daily and on lifecycle events such as adding or removing an HSM; keys created between backups are the customer's durability responsibility.[^s2]

Applications talk to the cluster through the CloudHSM client SDK, which exposes industry-standard interfaces: PKCS #11, the Java Cryptography Extension (JCE), and Microsoft's Cryptography API: Next Generation (CNG) and Key Storage Provider (KSP). That compatibility is what makes CloudHSM the usual landing spot for lifting an existing public key infrastructure, code-signing service or TLS offload setup to AWS with few code changes.[^s1] AWS monitors HSM health, replaces failed HSMs and publishes HSM audit logs to Amazon CloudWatch Logs.[^s2]

**Instance types.** The current type is `hsm2m.medium`. The older `hsm1.medium` type stopped accepting new clusters in April 2025, existing hsm1 clusters began automatic migration to hsm2m.medium in January 2026, and hsm1.medium reached end of support on March 31, 2026. Migration requires the latest client SDK; you can opt in to a managed migration or restore an hsm2m.medium cluster from an hsm1 backup and cut over blue/green.[^s4]

## Quotas, pricing and gotchas

- **Service quotas** (per account per Region, as of 2026-11): 4 clusters and 6 HSMs by default, both adjustable; 28 HSMs per cluster, not adjustable.[^s3]
- **System quotas** (not adjustable): an hsm2m.medium cluster holds up to 16,666 keys, of which at most 3,333 asymmetric, and 1,024 users; hsm1.medium was limited to 3,300 keys and 250 users. Either type allows 900 concurrent client connections per cluster and 1,024 PKCS #11 sessions per application. Certificate storage (60 per cluster) exists only on hsm2m.medium.[^s3]
- **Pricing**: an hourly fee for every hour or partial hour an HSM is provisioned, varying by Region and independent of usage. There is no free tier and no reserved pricing; a cluster with zero HSMs and the automatic backups are not billed, while data transfer is billed separately.[^s2] Because a production cluster needs at least two HSMs running around the clock, CloudHSM's floor cost is far higher than per-key KMS pricing.
- **High availability is your design job**: AWS strongly recommends at least two HSMs in different Availability Zones for production and at least three across two or more AZs for mission-critical workloads.[^s2]
- **Algorithm deprecations**: since January 1, 2024, FIPS-mode clusters no longer allow Triple DES key generation and encryption or RSA PKCS #1 v1.5 wrap, unwrap, encrypt and decrypt; these remain available in non-FIPS clusters.[^s4]

## When to use it (and when not)

Choose CloudHSM when you need sole control of single-tenant HSMs, a standard API such as PKCS #11 or JCE that existing software already speaks, algorithms or key types that a managed service does not offer, or non-FIPS workloads.[^s1] For ordinary encryption of AWS data at rest, AWS KMS is the managed alternative and needs no HSM operations; for payment-processing HSMs and keys, AWS points to AWS Payment Cryptography instead.[^s1]

A middle path is a **KMS CloudHSM key store**: a KMS custom key store backed by your own cluster, so integrated services encrypt with KMS keys whose material never leaves your HSMs. It requires an active cluster with at least two HSMs in different AZs to create keys, a dedicated `kmsuser` crypto user, and a one-to-one link between cluster and key store. It supports only symmetric AES-256 encryption keys, with no asymmetric, HMAC, imported, auto-rotated or multi-Region keys, and you pay for both KMS and the HSMs.[^s5] AWS's FAQ recommends this route, for example, for Amazon RDS for Oracle TDE, which CloudHSM does not support directly.[^s2]

## Connections

CloudHSM sits under the AWS security services family and is the main **alternative to** AWS KMS, while also **integrating with** KMS through custom key stores.[^s5] Individual HSMs are **scoped to** Availability Zones, which is why the HA guidance is phrased in AZs.[^s2] Its hard limits live in a **CloudHSM quotas** node,[^s3] its audit trail flows to Amazon CloudWatch,[^s2] and it appears in AWS's FedRAMP services-in-scope list for both the East/West (formerly Moderate) and GovCloud (formerly High) baselines, as of 2026-11.[^s6] AWS Payment Cryptography is the related, purpose-built HSM service for card and payment workloads.[^s1]

## Sources

[^s1]: Amazon Web Services, "What is AWS CloudHSM?". https://docs.aws.amazon.com/cloudhsm/latest/userguide/introduction.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "AWS CloudHSM FAQs". https://aws.amazon.com/cloudhsm/faqs/ (accessed 2026-11-15)
[^s3]: Amazon Web Services, "AWS CloudHSM quotas". https://docs.aws.amazon.com/cloudhsm/latest/userguide/limits.html (accessed 2026-11-15)
[^s4]: Amazon Web Services, "Deprecation Notifications - AWS CloudHSM". https://docs.aws.amazon.com/cloudhsm/latest/userguide/compliance-dep-notif.html (accessed 2026-11-15)
[^s5]: Amazon Web Services, "AWS CloudHSM key stores - AWS Key Management Service". https://docs.aws.amazon.com/kms/latest/developerguide/keystore-cloudhsm.html (accessed 2026-11-15)
[^s6]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-15)
