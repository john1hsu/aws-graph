---
id: kt:iam-roles-for-service-accounts
type: Feature
title: IAM roles for service accounts
aliases: [IRSA, EKS IRSA, IAM Roles for Service Accounts (EKS)]
domain: containers
summary: >-
  The Amazon EKS mechanism that lets pods using a Kubernetes service account assume an IAM role by
  exchanging a projected OIDC service-account token through AWS STS AssumeRoleWithWebIdentity, using
  a per-cluster IAM OIDC identity provider.
verified: 2026-11-21
status: current
broader: [kt:amazon-eks]
related: [kt:aws-iam, kt:iam-quotas, kt:aws-outposts]
relations:
  - {predicate: alternativeTo, object: kt:eks-pod-identity}
  - {predicate: dependsOn, object: kt:iam-roles}
  - {predicate: dependsOn, object: kt:aws-sts}
  - {predicate: implements, object: kt:openid-connect}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: integratesWith, object: kt:aws-privatelink}
sources:
  - id: s1
    title: "IAM roles for service accounts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html
    type: official-docs
    accessed: 2026-11-21
  - id: s2
    title: "Assign IAM roles to Kubernetes service accounts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/associate-service-account-role.html
    type: official-docs
    accessed: 2026-11-21
  - id: s3
    title: "Create an IAM OIDC provider for your cluster"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/enable-iam-roles-for-service-accounts.html
    type: official-docs
    accessed: 2026-11-21
  - id: s4
    title: "Use IRSA with the AWS SDK"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts-minimum-sdk.html
    type: official-docs
    accessed: 2026-11-21
  - id: s5
    title: "Authenticate to another account with IRSA"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/cross-account-access.html
    type: official-docs
    accessed: 2026-11-21
  - id: s6
    title: "Diving into IAM Roles for Service Accounts"
    publisher: AWS Containers Blog
    url: https://aws.amazon.com/blogs/containers/diving-into-iam-roles-for-service-accounts
    type: official-blog
    accessed: 2026-11-21
created: 2026-11-21
---

# IAM roles for service accounts

IAM roles for service accounts (IRSA) binds an IAM role to a Kubernetes service account in an Amazon EKS cluster, so that only the pods running under that service account receive the role's temporary AWS credentials. AWS compares it to how an EC2 instance profile supplies credentials to an instance, but at pod rather than node granularity.[^s1] AWS introduced IRSA in 2019, building on IAM's OpenID Connect (OIDC) federation and the Kubernetes projected service-account token feature.[^s6][^s1] The benefits AWS lists are least privilege per service account (replacing third-party tools such as kiam or kube2iam), credential isolation between pods, and auditability through AWS CloudTrail.[^s1]

## How it works

Every EKS cluster has its own OIDC issuer URL, and EKS hosts a public discovery endpoint and signing keys for it; the signing key pair rotates every 7 days.[^s1][^s6] Setup has three parts. First, an administrator registers that issuer as an IAM OIDC identity provider in the account, once per cluster, with the audience `sts.amazonaws.com` (via `eksctl utils associate-iam-oidc-provider` or the IAM console).[^s3] Second, they create an IAM role whose trust policy names that provider as a federated principal, allows `sts:AssumeRoleWithWebIdentity`, and pins the token's `aud` claim to `sts.amazonaws.com` and its `sub` claim to `system:serviceaccount:<namespace>:<name>` (a `StringLike` wildcard can admit a whole namespace).[^s2] Third, they annotate the service account with `eks.amazonaws.com/role-arn` set to the role.[^s2]

At pod creation, the EKS pod identity mutating webhook sees the annotation and mounts a second projected token whose audience is `sts.amazonaws.com` and which expires after 86,400 seconds (24 hours) by default; the `eks.amazonaws.com/token-expiration` annotation changes that.[^s6] The webhook also injects `AWS_ROLE_ARN` and `AWS_WEB_IDENTITY_TOKEN_FILE`, which the AWS SDKs read to call `AssumeRoleWithWebIdentity` and to keep the resulting credentials rotated.[^s6][^s4] The ordinary default service-account token can't be used for this: only the Kubernetes API server validates it, and it never expires.[^s6]

## Requirements and gotchas

- **SDK versions.** Applications need an SDK new enough to support web-identity credentials, for example Boto3 1.9.220, AWS SDK for Java 2.10.11, or JavaScript v3 3.27.0 or later (all Go v2 versions qualify); Java also needs the STS module on the classpath.[^s4]
- **Credential chain order.** IRSA is one step in the SDK's default chain, so credentials found earlier still win.[^s4] If the chain finds nothing else, it can fall back to the node's instance profile through IMDS and give the pod node-level permissions, so restrict IMDS access to keep credentials isolated.[^s6][^s1]
- **STS endpoint.** By default the webhook leaves pods on the global STS endpoint; the annotation `eks.amazonaws.com/sts-regional-endpoints: "true"` switches them to the Regional endpoint.[^s6]
- **Private clusters.** In a VPC with no outbound internet, creating the OIDC provider can fail with an NXDOMAIN error for the `oidc.eks` hostname. The fix is an interface VPC endpoint for `com.amazonaws.<region>.oidc-eks` with private DNS, or running the command from outside the VPC.[^s3]
- **Unsupported.** IRSA is not supported on local clusters for Amazon EKS on AWS Outposts.[^s1]
- **Trust conditions.** Leaving out the `sub` condition lets any service account in the cluster assume the role.[^s5]

## Cross-account access

A pod can reach a role in another account in two ways. In the first, account B registers account A's cluster issuer as its own OIDC provider, and the service account is annotated with account B's role directly. This is simpler, but account B has to manage a provider for someone else's cluster. In the second, the IRSA role in account A is allowed to `sts:AssumeRole` into account B's role, and the pod chains profiles. OIDC management stays in account A, at the cost of two role assumptions. AWS advises trusting the specific role ARN in account B instead of account A's root principal.[^s5]

## When to use it (and when not)

IRSA works in any environment that can run the pod identity webhook. Besides EKS, self-managed Kubernetes clusters can use the open-source webhook.[^s6] Many EKS add-ons, including the Amazon VPC CNI, Cluster Autoscaler and AWS Load Balancer Controller, support it.[^s4] Its overhead is per-cluster: each new cluster needs its own OIDC provider, and every trust policy that should admit it must reference its issuer.[^s3][^s2] EKS Pod Identity, the newer alternative described in its own article, is worth comparing before choosing IRSA for a new cluster.

## Connections

IRSA is a feature of Amazon EKS and depends on IAM roles and on AWS STS web-identity federation, which implements OpenID Connect.[^s2][^s1] EKS Pod Identity is its newer alternative. Role assumptions can be audited in AWS CloudTrail. In isolated VPCs, the OIDC endpoint is reached through an AWS PrivateLink interface endpoint.[^s1][^s3] IRSA is unavailable on EKS local clusters on AWS Outposts.[^s1] IAM's own account limits, covered under IAM quotas, apply to the OIDC providers and roles it creates.[^s3]

## Sources

[^s1]: Amazon Web Services, Amazon EKS User Guide, "IAM roles for service accounts". https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html (accessed 2026-11-21)
[^s2]: Amazon Web Services, Amazon EKS User Guide, "Assign IAM roles to Kubernetes service accounts". https://docs.aws.amazon.com/eks/latest/userguide/associate-service-account-role.html (accessed 2026-11-21)
[^s3]: Amazon Web Services, Amazon EKS User Guide, "Create an IAM OIDC provider for your cluster". https://docs.aws.amazon.com/eks/latest/userguide/enable-iam-roles-for-service-accounts.html (accessed 2026-11-21)
[^s4]: Amazon Web Services, Amazon EKS User Guide, "Use IRSA with the AWS SDK". https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts-minimum-sdk.html (accessed 2026-11-21)
[^s5]: Amazon Web Services, Amazon EKS User Guide, "Authenticate to another account with IRSA". https://docs.aws.amazon.com/eks/latest/userguide/cross-account-access.html (accessed 2026-11-21)
[^s6]: AWS Containers Blog, "Diving into IAM Roles for Service Accounts" (2022-02-28). https://aws.amazon.com/blogs/containers/diving-into-iam-roles-for-service-accounts (accessed 2026-11-21)
