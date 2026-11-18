---
id: kt:aws-payment-cryptography
type: Service
title: AWS Payment Cryptography
aliases: [Payment Cryptography, AWS payment HSM service]
domain: security
summary: >-
  AWS's managed, elastic service for payment-specific cryptography and key management, such as
  PIN translation, CVV and ARQC validation, MAC generation and TR-31/TR-34 key exchange, run on
  PCI PTS-validated payment HSMs so card processors no longer need dedicated payment HSM appliances.
verified: 2026-11-18
status: current
broader: [kt:aws-security-services]
related: [kt:aws-cloudhsm, kt:aws-kms, kt:pci-dss-on-aws]
relations:
  - {predicate: alternativeTo, object: kt:aws-cloudhsm}
  - {predicate: compliesWith, object: kt:pci-dss-on-aws}
  - {predicate: governedBy, object: kt:payment-cryptography-quotas}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "What is AWS Payment Cryptography?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/payment-cryptography/latest/userguide/what-is.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "AWS Payment Cryptography FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/payment-cryptography/faqs
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Quotas for AWS Payment Cryptography"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/payment-cryptography/latest/userguide/quotas.html
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "AWS Payment Cryptography Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/payment-cryptography/pricing/
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "AWS Payment Cryptography endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/payment-cryptography.html
    type: official-docs
    accessed: 2026-11-18
  - id: s6
    title: "AWS Payment Cryptography announces support for cross account key sharing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/05/payment-cryptography-resource/
    type: official-blog
    accessed: 2026-11-18
created: 2026-11-18
---

# AWS Payment Cryptography

AWS Payment Cryptography is a managed service that performs the cryptographic functions and key management that card payment processing needs, following payment card industry (PCI) standards, without customers procuring dedicated payment HSM instances. It targets acquirers, payment facilitators, card networks, switches, processors and banks that want to move payment cryptography closer to their cloud applications and away from data centers or colocation cages full of payment HSMs. The service is elastic and takes over the chores of running those appliances: provisioning hardware, safeguarding key material and keeping emergency backups in secure facilities.[^s1]

## How it works

The service has two APIs. The **control plane** creates and manages keys; the **data plane** applies those keys to transaction processing. Data-plane functions include encrypting, decrypting and re-encrypting data; translating cardholder PINs between keys without exposing clear text, as PCI PIN rules require; generating and verifying PINs; generating or validating CVV, CVV2 and ARQC values; and generating or verifying MACs.[^s1]

Keys are symmetric (TDES or AES) or asymmetric (RSA), and each carries a declared purpose, such as CVV generation or DUKPT key derivation, which the service uses to enforce key separation between use cases. Keys can be addressed by aliases and labelled with tags for access control and cost tracking.[^s1] Plaintext payment keys stay in HSM volatile memory and are never written to disk; AWS designed the service so that nobody, AWS staff included, can retrieve them.[^s2]

**Key exchange.** Working keys move between the service and partner HSMs as TR-31 key blocks wrapped under key-encryption keys, and those KEKs themselves are exchanged with asymmetric TR-34.[^s1] AWS calls electronic exchange the preferred route and also supports RSA and ECDH techniques. For partners that can only send paper key components, **Physical Key Exchange** lets AWS key custodians run the key ceremony in AWS facilities that meet PCI PIN and P2PE requirements; you request it through a support case or your account team.[^s2] Since May 2026, a key can also be shared with other AWS accounts through a resource-based policy, so several accounts use one copy of the key material instead of importing and exporting it.[^s6]

## Compliance

AWS designed the service to meet PCI PIN, PCI P2PE and PCI DSS requirements, and its hardware is PCI PTS HSM V3 and FIPS 140-2 Level 3 certified.[^s1] The FAQ adds PCI 3-D Secure to that list. Under shared responsibility, AWS handles HSM security, HSM main keys and key storage, while you handle key material before import and after export and must set key attributes correctly. Because responses can contain cardholder or sensitive authentication data, how you call the service affects your own PCI DSS scope.[^s2]

## Quotas, pricing and gotchas

- **Quotas** (per account per Region, as of 2026-11, all adjustable): 2,000 keys (deleted keys excluded); 2,000 aliases; 5 control-plane requests per second; 500 symmetric and 20 asymmetric data-plane requests per second.[^s3] The asymmetric rate is low, so plan RSA-heavy flows such as TR-34 exchanges and certificate work accordingly.
- **Pricing** (examples on AWS's pricing page, as of 2026-11): in its US East (N. Virginia) example, 100,000 API calls at $2.00 per 10,000 plus 10 active keys at $1.00 each total $30.00 a month. Higher monthly volumes drop into a cheaper per-call tier ($0.75 per 10,000 in AWS's Oregon example), keys are pro-rated in one-hour increments, and Physical Key Exchange costs $3,000 per key exchanged.[^s4] There is no minimum fee, setup fee or commitment, and the AWS Free Tier does not apply.[^s2]
- **Regions**: control-plane and data-plane endpoints exist in 16 Regions across North America, South America, Europe, Africa and Asia Pacific (as of 2026-11), so check the endpoints list before assuming local processing.[^s5]

## When to use it (and when not)

Use Payment Cryptography for card-payment workloads that would otherwise need on-premises payment HSMs: issuing, acquiring, PIN handling, EMV authorization cryptograms and partner key exchange.[^s1] AWS KMS is a similar managed key service but has no payment-specific functions, and AWS CloudHSM gives you single-tenant, general-purpose HSM clusters that you operate yourself. Pick CloudHSM for PKCS #11 or JCE workloads, and KMS for encrypting AWS data at rest.[^s2]

## Connections

The service belongs to the AWS security services family and is the payment-focused **alternative to** AWS CloudHSM, alongside AWS KMS for general key management.[^s2] It **complies with** the PCI standards covered under PCI DSS on AWS.[^s1] Its adjustable limits belong in a **Payment Cryptography quotas** node,[^s3] access, including cross-account sharing, is **secured by** IAM and resource-based policies,[^s6] and each key and endpoint is **scoped to** an AWS Region.[^s5]

## Sources

[^s1]: Amazon Web Services, "What is AWS Payment Cryptography?". https://docs.aws.amazon.com/payment-cryptography/latest/userguide/what-is.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "AWS Payment Cryptography FAQs". https://aws.amazon.com/payment-cryptography/faqs (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Quotas for AWS Payment Cryptography". https://docs.aws.amazon.com/payment-cryptography/latest/userguide/quotas.html (accessed 2026-11-18)
[^s4]: Amazon Web Services, "AWS Payment Cryptography Pricing". https://aws.amazon.com/payment-cryptography/pricing/ (accessed 2026-11-18)
[^s5]: Amazon Web Services, "AWS Payment Cryptography endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/payment-cryptography.html (accessed 2026-11-18)
[^s6]: Amazon Web Services, "AWS Payment Cryptography announces support for cross account key sharing". https://aws.amazon.com/about-aws/whats-new/2026/05/payment-cryptography-resource/ (accessed 2026-11-18)
