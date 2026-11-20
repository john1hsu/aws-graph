---
id: kt:iam-users
type: Resource
title: IAM users
aliases: [IAM user, AWS IAM user]
domain: identity-and-access
summary: >-
  An identity created inside one AWS account, made of a name plus long-term credentials such as a
  console password or access keys; AWS now reserves IAM users for the few cases that cannot use
  federation or temporary credentials from IAM roles.
verified: 2026-11-20
status: current
broader: [kt:aws-iam]
related: [kt:iam-identity-center, kt:iam-policy-evaluation-logic, kt:security-pillar, kt:aws-secrets-manager]
relations:
  - {predicate: contrastsWith, object: kt:iam-roles}
  - {predicate: governedBy, object: kt:iam-quotas}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "IAM users - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Manage access keys for IAM users - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Security best practices in IAM - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html
    type: official-docs
    accessed: 2026-11-20
  - id: s4
    title: "Remove or deactivate an IAM user - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_remove.html
    type: official-docs
    accessed: 2026-11-20
  - id: s5
    title: "SEC02-BP02 Use temporary credentials - Security Pillar"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/sec_identities_unique.html
    type: official-docs
    accessed: 2026-11-20
  - id: s6
    title: "IAM and AWS STS quotas - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.html
    type: official-docs
    accessed: 2026-11-20
  - id: s7
    title: "Content Domain 2: Security and Compliance - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain2.html
    type: official-learning
    accessed: 2026-11-20
created: 2026-11-20
---

# IAM users

An IAM user is an identity you create inside an AWS account. It consists of a name and the credentials it signs in or calls APIs with, and it can stand for either a person or a workload.[^s1] Each user belongs to exactly one account and has no payment method of its own: whatever it does is billed to that account.[^s1] An IAM user that has administrator permissions is still not the account root user, which is a separate identity with complete access.[^s1] AWS's current guidance is to use IAM users only for specific use cases that federated users cannot cover, and to give people access through an identity provider with temporary credentials instead.[^s1][^s3]

## How it works

A user is identified three ways: a friendly name (for example Richard) that appears in policies and the console; an ARN of the form `arn:aws:iam::<account>:user/<name>`; and a unique ID that only the API, CLI and Tools for Windows PowerShell return. Because the name is embedded in the ARN, AWS advises keeping personal information out of it.[^s1] A user created through the CLI or API starts with no credentials of any kind; you add only the ones it needs.[^s1]

The credential types are a **console password** (which can be disabled without changing the user's permissions), **access keys** for programmatic calls, **SSH public keys** for AWS CodeCommit, **service-specific credentials**, server certificates (AWS recommends ACM instead wherever ACM is supported), and **MFA** devices, which AWS recommends for every IAM user.[^s1][^s3] An access key is a pair of an access key ID and a secret access key; the secret can be retrieved only when the key is created, so a lost secret means deleting the key and creating a new one.[^s2]

A user's permissions come from managed and inline policies attached to it and from the groups it belongs to.[^s4][^s6] Some accounts created under AWS's newer sign-up experience cannot create IAM users with console access at all; human users are added to a team instead.[^s1]

## Quotas and gotchas

As of 2026-11 (per account; increases are requested in Service Quotas with US East (N. Virginia) selected): a user can have 10 managed policies attached by default, adjustable and auto-approved up to 20. Fixed limits include 2,048 characters of aggregate inline policy per user (whitespace not counted), a 64-character user name and a 512-character path.[^s6] Names are unique per account and case-insensitive.[^s6] A user can have at most two access keys, which is what allows rotating one key while the other is still in use.[^s2]

Access keys are long-term, so they are the main risk. The Well-Architected security pillar rates not using temporary credentials as high risk and lists anti-patterns such as developers using IAM user keys instead of federating, keys committed to public Git repositories or embedded in mobile apps, and keys shared or kept by departing staff.[^s5] Keys kept in the shared AWS credentials file sit there in plaintext, and AWS advises against creating keys for the root user.[^s2] Monitor key use with CloudTrail and alarms, and regularly review, update and delete keys; IAM last-used information shows which keys and users can be removed safely.[^s2][^s3]

Deletion is permanent: a deleted user and its keys cannot be restored. In the console IAM removes the password, group memberships, inline policies and MFA device for you (managed policies are detached, and active keys must first be deactivated); through the CLI or API you must remove each credential, policy and group membership yourself before `delete-user` succeeds. To suspend a user instead, attach a policy that denies all actions.[^s4]

## When to use it (and when not)

Use IAM roles for workloads (EC2 instance profiles and Lambda execution roles supply temporary credentials automatically, and IAM Roles Anywhere covers on-premises systems) and IAM Identity Center for people.[^s3] IAM users remain the fallback when something cannot use temporary credentials: a third-party tool or client that does not support Identity Center or roles, CodeCommit access with SSH keys or service-specific credentials, and similar cases.[^s1][^s3] When long-term keys are unavoidable, the security pillar suggests granting the user little more than permission to assume one role, restricting source IPs, alerting on use, and storing the keys in a vault.[^s5]

## Connections

IAM users are an identity type of **AWS IAM** and the commonly confused counterpart of **IAM roles**, which issue temporary rather than long-term credentials.[^s1][^s3] Their effective permissions are decided by **IAM policy evaluation logic**, and their policy counts and sizes fall under **IAM quotas**.[^s6] The **security pillar** of the Well-Architected Framework and **IAM Identity Center** describe the federated model that replaces them for humans, while **AWS Secrets Manager** is a vault option for credentials that must persist.[^s5][^s3] Key and user activity is audited with **AWS CloudTrail**.[^s2] The Cloud Practitioner exam (CLF-C02) covers defining users and groups under least privilege, access keys and MFA in Task 2.3.[^s7]

## Sources
[^s1]: Amazon Web Services, "IAM users - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "Manage access keys for IAM users - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "Security best practices in IAM - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html (accessed 2026-11-20)
[^s4]: Amazon Web Services, "Remove or deactivate an IAM user - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_remove.html (accessed 2026-11-20)
[^s5]: Amazon Web Services, "SEC02-BP02 Use temporary credentials", AWS Well-Architected Framework Security Pillar. https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/sec_identities_unique.html (accessed 2026-11-20)
[^s6]: Amazon Web Services, "IAM and AWS STS quotas - AWS Identity and Access Management". https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.html (accessed 2026-11-20)
[^s7]: Amazon Web Services, "Content Domain 2: Security and Compliance", AWS Certified Cloud Practitioner (CLF-C02) exam guide. https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain2.html (accessed 2026-11-20)
