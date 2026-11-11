---
id: kt:amazon-ecr
type: Service
title: Amazon Elastic Container Registry
aliases: [Amazon ECR, ECR, AWS ECR, Amazon ECR Public]
domain: containers
summary: >-
  AWS's managed container image registry, which stores, scans, replicates and serves Docker
  images, OCI images and OCI-compatible artifacts from IAM-controlled private repositories
  (plus public repositories via ECR Public) to ECS, EKS, Fargate, Lambda and other clients.
verified: 2026-11-11
status: current
broader: [kt:aws-container-services]
related: [kt:amazon-ecs, kt:amazon-eks, kt:aws-fargate]
relations:
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: integratesWith, object: kt:amazon-inspector}
  - {predicate: governedBy, object: kt:ecr-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
  - {predicate: coveredBy, object: kt:aws-certified-developer-associate}
sources:
  - id: s1
    title: "What is Amazon Elastic Container Registry?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Amazon ECR service quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECR/latest/userguide/service-quotas.html
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "Amazon Elastic Container Registry Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ecr/pricing
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Scanning Amazon Elastic Container Registry container images with Amazon Inspector"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/inspector/latest/user/scanning-ecr.html
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Archiving an image in Amazon ECR"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECR/latest/userguide/archive_restore_image.html
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "Amazon ECR now supports cross-repository layer sharing to optimize storage and improve push performance"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/01/amazon-ecr-cross-repository-layer-sharing
    type: official-blog
    accessed: 2026-11-11
  - id: s7
    title: "Amazon ECR now supports image layers up to 200 GB"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/08/amazon-ecr-image-layers/
    type: official-blog
    accessed: 2026-11-11
  - id: s8
    title: "Amazon ECR pull through cache now supports additional upstream registries"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/05/amazon-ecr-pull-through-cache-additional-registries/
    type: official-blog
    accessed: 2026-11-11
  - id: s9
    title: "In-Scope AWS Services - AWS Certified Developer - Associate"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/dva-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-11
  - id: s10
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# Amazon Elastic Container Registry

Amazon Elastic Container Registry (Amazon ECR) is AWS's managed registry for container images. It holds Docker images, Open Container Initiative (OCI) images and OCI-compatible artifacts in private repositories whose access is controlled with AWS IAM resource-based permissions, and you push and pull with the container CLI you already use. Public repositories are offered separately as Amazon ECR Public.[^s1] Each AWS account gets a private registry per Region, and settings such as replication are configured at that registry level, Region by Region.[^s1][^s2]

## How it works

A build pipeline pushes an image into a repository; orchestrators such as Amazon ECS and Amazon EKS, or AWS Fargate and AWS Lambda, pull it at deploy time.[^s1][^s3] Around that basic flow ECR adds a set of registry features:[^s1]

- **Lifecycle policies** — rules that expire unused images automatically; rules can be previewed before they are applied.
- **Image scanning** — scan on push, or continuous scanning through Amazon Inspector (below).
- **Replication** — cross-Region and cross-account copies, configured as a per-Region registry setting.
- **Pull through cache** — mirrors images from an upstream registry into your private registry and periodically re-checks the upstream for updates. Upstreams include ECR Public, Docker Hub, Quay, the Kubernetes registry, GitHub, GitLab, Azure Container Registry, Chainguard and other private ECR registries in the same partition.[^s8]
- **Repository creation templates** — default tag immutability, encryption, policies and tags for repositories that ECR creates itself during pull through cache, create-on-push or replication.
- **Managed signing** — signs images cryptographically as they are pushed.

Since January 2026 a registry-level setting enables cross-repository layer sharing ("blob mounting"): a base-image layer already in one repository is reused by other repositories in the same registry, which speeds up pushes and stores the common layer only once. It is in all commercial and GovCloud (US) Regions.[^s6]

ECR has two storage classes. Standard is the default; the Archival class is a cheaper tier for images kept for compliance or reference. Images can be archived manually or by lifecycle rules based on push age, last-pull age or image count, and lifecycle rules can later delete archived images. A restore moves the image back to Standard, which ECR expects to finish within 20 minutes.[^s5]

## Scanning

Basic scanning is provided and billed by ECR. Enhanced scanning is provided and billed by Amazon Inspector, and it covers vulnerabilities in operating-system packages and programming-language packages. With continuous scanning, Inspector rescans an image when it is pushed, when a relevant new CVE enters its database, or when the image is restored from the archive. Archived images are not scanned. For registries that turn on ECR scanning from 15 October 2026, the default push-date and rescan windows are 7 days, measured from last use; earlier activations have 14-day or 90-day defaults.[^s4]
## Quotas, pricing and gotchas

Default quotas apply per Region (as of 2026-11). You get 100,000 repositories per registry and 100,000 images per repository, and both are adjustable. These are fixed: 50 lifecycle rules per policy, 50 pull through cache rules per registry, 25 replication rules, 1,000 tags per image, and one scan per image per 24 hours. The API rate quotas are adjustable, among them GetAuthorizationToken at 500/s, BatchGetImage at 2,000/s and PutImage at 10/s. If you raise BatchGetImage, check GetDownloadUrlForLayer as well.[^s2] Layer size depends on how you push. Since August 2026, Docker pushes accept layers up to 200 GB in most Regions (the Middle East Bahrain and UAE Regions are excluded), but layers sent through the UploadLayerPart API stay at 50 GB. The quotas page still lists 52,000 MiB.[^s7][^s2]

Pricing (as of 2026-11) has three parts: stored data, data transferred by pushes and pulls, and opt-in actions such as signing and replication.[^s1] AWS's us-east-1 worked examples price private storage at $0.10 per GB-month. Inbound transfer is free, and so are same-Region pulls to EC2, ECS, Lambda, App Runner and Fargate. Cross-Region pulls and replication are charged at internet data transfer rates; the example gives $0.09/GB from us-east-1 to us-west-1. New customers get 500 MB/month of private storage free for a year. Public repositories get 50 GB/month of storage free, with 500 GB/month of anonymous internet transfer or 5 TB/month when the puller is authenticated.[^s3] Pulling across Regions from one registry incurs transfer fees, and repositories without lifecycle rules keep growing.[^s3][^s5]

## When to use it (and when not)

ECR is the default registry for workloads on ECS, EKS, Fargate or Lambda container images. Same-Region pulls are free, access uses IAM, and scanning and replication are built in.[^s1][^s3] For third-party base images, a pull through cache rule keeps a copy in your own registry that ECR refreshes from the upstream periodically, so workloads pull from ECR rather than from the public registry.[^s1][^s8] Teams that already standardise on another registry such as GitHub, GitLab or Azure Container Registry can keep it as the source and mirror into ECR for AWS workloads.[^s8]

## Connections

ECR sits under **AWS container services** (`kt:aws-container-services`) and is **related to Amazon ECS** (`kt:amazon-ecs`), **Amazon EKS** (`kt:amazon-eks`) and **AWS Fargate** (`kt:aws-fargate`), which pull its images free within a Region.[^s3] It is **secured by AWS IAM** (`kt:aws-iam`) through resource-based permissions[^s1] and **integrates with Amazon Inspector** (`kt:amazon-inspector`) for enhanced scanning.[^s4] It is **governed by ECR quotas** (`kt:ecr-quotas`) and **scoped to Regions** (`kt:aws-regions`), because registries, quotas and replication settings are per Region.[^s2][^s1] The current in-scope service lists for **AWS Certified Cloud Practitioner** and **AWS Certified Developer – Associate** both name Amazon ECR under Containers.[^s10][^s9]

## Sources
[^s1]: Amazon Web Services, "What is Amazon Elastic Container Registry?". https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Amazon ECR service quotas". https://docs.aws.amazon.com/AmazonECR/latest/userguide/service-quotas.html (accessed 2026-11-11)
[^s3]: Amazon Web Services, "Amazon Elastic Container Registry Pricing". https://aws.amazon.com/ecr/pricing (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Scanning Amazon Elastic Container Registry container images with Amazon Inspector". https://docs.aws.amazon.com/inspector/latest/user/scanning-ecr.html (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Archiving an image in Amazon ECR". https://docs.aws.amazon.com/AmazonECR/latest/userguide/archive_restore_image.html (accessed 2026-11-11)
[^s6]: Amazon Web Services, "Amazon ECR now supports cross-repository layer sharing to optimize storage and improve push performance" (2026-01-20). https://aws.amazon.com/about-aws/whats-new/2026/01/amazon-ecr-cross-repository-layer-sharing (accessed 2026-11-11)
[^s7]: Amazon Web Services, "Amazon ECR now supports image layers up to 200 GB" (2026-08-03). https://aws.amazon.com/about-aws/whats-new/2026/08/amazon-ecr-image-layers/ (accessed 2026-11-11)
[^s8]: Amazon Web Services, "Amazon ECR pull through cache now supports additional upstream registries" (2026-05-15). https://aws.amazon.com/about-aws/whats-new/2026/05/amazon-ecr-pull-through-cache-additional-registries/ (accessed 2026-11-11)
[^s9]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Developer - Associate". https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/dva-02-in-scope-services.html (accessed 2026-11-11)
[^s10]: Amazon Web Services, "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-11)
