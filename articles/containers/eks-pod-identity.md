---
id: kt:eks-pod-identity
type: Feature
title: Amazon EKS Pod Identity
aliases: [EKS Pod Identity, Pod Identity, EKS Pod Identity Agent]
domain: containers
summary: >-
  An Amazon EKS feature that gives the pods of a Kubernetes service account temporary IAM
  credentials through an association managed by the EKS API and a per-node agent, without an
  OIDC identity provider per cluster; a simpler alternative to IAM roles for service accounts (IRSA).
verified: 2026-11-20
status: current
broader: [kt:amazon-eks]
related: [kt:eks-auto-mode, kt:eks-quotas, kt:attribute-based-access-control]
relations:
  - {predicate: alternativeTo, object: kt:iam-roles-for-service-accounts}
  - {predicate: dependsOn, object: kt:iam-roles}
  - {predicate: integratesWith, object: kt:aws-sts}
  - {predicate: monitoredBy, object: kt:aws-cloudtrail}
  - {predicate: governedBy, object: kt:eks-quotas}
sources:
  - id: s1
    title: "Learn how EKS Pod Identity grants pods access to AWS services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/pod-identities.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "Understand how EKS Pod Identity works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/pod-id-how-it-works.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "Amazon EKS Pod Identity: a new way for applications on EKS to obtain IAM credentials"
    publisher: AWS Containers Blog
    url: https://aws.amazon.com/blogs/containers/amazon-eks-pod-identity-a-new-way-for-applications-on-eks-to-obtain-iam-credentials
    type: official-blog
    accessed: 2026-11-20
  - id: s4
    title: "Amazon EKS Pod Identity simplifies the experience for cross-account access"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2025/06/amazon-eks-pod-identity-cross-account-access
    type: official-blog
    accessed: 2026-11-20
  - id: s5
    title: "Session policies for Amazon EKS Pod Identity"
    publisher: AWS Containers Blog
    url: https://aws.amazon.com/blogs/containers/session-policies-for-amazon-eks-pod-identity
    type: official-blog
    accessed: 2026-11-20
  - id: s6
    title: "Amazon EKS introduces EKS Pod Identity in the AWS GovCloud (US) Regions"
    publisher: Amazon Web Services (What's New)
    url: https://aws.amazon.com/about-aws/whats-new/2024/08/amazon-eks-pod-identity-aws-govcloud-us-regions
    type: official-blog
    accessed: 2026-11-20
created: 2026-11-20
---

# Amazon EKS Pod Identity

Amazon EKS Pod Identity is how applications running in EKS pods get AWS credentials scoped to their own Kubernetes service account rather than to the node they happen to run on. A cluster administrator creates a *Pod Identity association* that maps one service account in one namespace to an IAM role, and pods using that service account receive temporary credentials for that role.[^s1] It launched around re:Invent 2023 as a simpler alternative to IAM roles for service accounts (IRSA), and AWS lists least privilege, credential isolation between pods and CloudTrail auditability as its main benefits.[^s3][^s1]

## How it works

Each worker node runs the EKS Pod Identity Agent, installed once per cluster as the `eks-pod-identity-agent` add-on (EKS Auto Mode clusters already include it).[^s1][^s3] When a pod with an associated service account starts, EKS injects two environment variables: one points the AWS SDK's container credential provider at the agent's link-local endpoint (169.254.170.23), the other at a projected service-account token with the audience `pods.eks.amazonaws.com` that expires after 24 hours.[^s2] The agent exchanges that token by calling `AssumeRoleForPodIdentity` on the EKS Auth API and hands the resulting temporary credentials to the SDK; because the container credential provider sits in the default credential chain, credentials configured earlier in the chain still win, which eases migration.[^s2] The agent listens on IPv4 and IPv6 link-local addresses with host networking, so proxied workloads must add those addresses to `no_proxy`.[^s1]

On the IAM side, the role's trust policy trusts the service principal `pods.eks.amazonaws.com` and allows `sts:AssumeRole` and `sts:TagSession`; optional `aws:SourceAccount` or `aws:SourceArn` conditions can pin the role to particular clusters.[^s3] Credentials carry session tags such as the cluster name, namespace and service account name, so one role can serve many workloads and policies can use attribute-based conditions on those tags.[^s3][^s6]

## Cross-account access and session policies

Since June 2025 an association can name a *target role* in another account: Pod Identity assumes the role in the cluster's account and then chains into the target role, with no application code change, in all Regions where EKS is available.[^s4] In 2026 AWS added session policies: an inline policy attached to the association that can only narrow the role's permissions. As of 2026-11 the plaintext policy is limited to 2,048 characters and must also pass STS's packed-size check, and session tags must be disabled on any association that uses one.[^s5]

## Quotas, restrictions and gotchas

- A cluster supports up to 5,000 Pod Identity associations (as of 2026-11), and each service account maps to exactly one IAM role.[^s1]
- Associations are eventually consistent and can take several seconds to apply, so don't create them in a critical code path.[^s1] Session-policy changes reach new credential requests within roughly 10 seconds.[^s5]
- It works only on Linux EC2 worker nodes: Fargate pods, Windows nodes, EKS on AWS Outposts, EKS Anywhere and self-managed Kubernetes on EC2 are not supported.[^s1]
- At launch it was unavailable in AWS GovCloud (US) and the China Regions; GovCloud (US) support followed in August 2024.[^s6]

## When to use it (and when not)

For new workloads on EKS in AWS Regions, Pod Identity is the simpler choice: no per-cluster OIDC provider to create, trust policies that don't need editing for every new cluster, and a separation of duties where IAM administrators own roles while cluster administrators own associations.[^s1][^s3] AWS also says it scales better because the EKS Auth service and node agent, not every SDK in every pod, perform the role assumption.[^s1] IRSA remains the option where Pod Identity can't run, such as Fargate pods, EKS Anywhere or self-managed clusters, and IRSA does not offer session tags.[^s1][^s3]

## Connections

Pod Identity is a feature of Amazon EKS and an alternative to IAM roles for service accounts.[^s1] It depends on IAM roles and on AWS STS role assumption and session tagging, which in turn enables attribute-based access control.[^s3] Its association ceiling is tracked in the EKS quotas, role assumptions are auditable in AWS CloudTrail, and EKS Auto Mode clusters ship the agent built in.[^s1]

## Sources

[^s1]: Amazon Web Services, Amazon EKS User Guide, "Learn how EKS Pod Identity grants pods access to AWS services". https://docs.aws.amazon.com/eks/latest/userguide/pod-identities.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, Amazon EKS User Guide, "Understand how EKS Pod Identity works". https://docs.aws.amazon.com/eks/latest/userguide/pod-id-how-it-works.html (accessed 2026-11-20)
[^s3]: AWS Containers Blog, "Amazon EKS Pod Identity: a new way for applications on EKS to obtain IAM credentials" (2023-12-28). https://aws.amazon.com/blogs/containers/amazon-eks-pod-identity-a-new-way-for-applications-on-eks-to-obtain-iam-credentials (accessed 2026-11-20)
[^s4]: Amazon Web Services, What's New, "Amazon EKS Pod Identity simplifies the experience for cross-account access" (2025-06-12). https://aws.amazon.com/about-aws/whats-new/2025/06/amazon-eks-pod-identity-cross-account-access (accessed 2026-11-20)
[^s5]: AWS Containers Blog, "Session policies for Amazon EKS Pod Identity" (2026-03-24). https://aws.amazon.com/blogs/containers/session-policies-for-amazon-eks-pod-identity (accessed 2026-11-20)
[^s6]: Amazon Web Services, What's New, "Amazon EKS introduces EKS Pod Identity in the AWS GovCloud (US) Regions" (2024-08-14). https://aws.amazon.com/about-aws/whats-new/2024/08/amazon-eks-pod-identity-aws-govcloud-us-regions (accessed 2026-11-20)
