---
id: kt:ecr-quotas
type: Limit
title: Amazon ECR quotas
aliases: [ECR quotas, ECR service quotas, ECR limits, Amazon ECR service limits, Amazon Elastic Container Registry quotas]
domain: containers
summary: >-
  The per-account, per-Region limits on Amazon ECR private registries: repository and image counts,
  layer and multipart-upload sizes, lifecycle, replication and pull through cache rule limits, image
  scanning limits, and per-second rate quotas on the push, pull and authentication APIs. ECR Public
  has its own, lower set.
verified: 2026-11-19
status: current
broader: [kt:amazon-ecr]
related: [kt:amazon-inspector, kt:amazon-ecs, kt:amazon-eks]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: contrastsWith, object: kt:ecs-quotas}
  - {predicate: contrastsWith, object: kt:eks-quotas}
sources:
  - id: s1
    title: "Amazon ECR service quotas (Amazon ECR User Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECR/latest/userguide/service-quotas.html
    type: official-docs
    accessed: 2026-11-19
  - id: s2
    title: "Amazon ECR endpoints and quotas (AWS General Reference)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/ecr.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Amazon ECR now supports 100,000 images per repository"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/08/amazon-ecr-supports-100000-images
    type: official-blog
    accessed: 2026-11-19
  - id: s4
    title: "Amazon ECR now supports additional metrics for monitoring repositories"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/02/amazon-ecr-additional-repository-metrics
    type: official-blog
    accessed: 2026-11-19
  - id: s5
    title: "Amazon ECR Public service quotas (Amazon ECR Public User Guide)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonECR/latest/public/public-service-quotas.md
    type: official-docs
    accessed: 2026-11-19
created: 2026-11-19
---

# Amazon ECR quotas

Amazon ECR quotas are the limits AWS applies to private Amazon Elastic Container Registry resources and APIs. Every default is set per account and per Region ("each supported Region"), and ECR is integrated with Service Quotas, where you can see the applied value of each quota and request increases for the adjustable ones under "Amazon Elastic Container Registry (Amazon ECR)".[^s1][^s2] The values below are the published defaults as of 2026-11; the same numbers apply in every supported Region.[^s1]

## Resource and size quotas

A registry can hold 100,000 repositories per Region, and each repository can hold 100,000 images; both are adjustable.[^s1][^s2] The per-repository figure was raised from 20,000 to 100,000 in August 2025, automatically for existing registries, in all commercial and AWS GovCloud (US) Regions.[^s3] An image can carry up to 1,000 tags (fixed).[^s1]

Layer size is capped at 52,000 MiB per layer, a hard limit.[^s1][^s2] If you call the ECR multipart-upload APIs yourself rather than using a Docker or OCI client, each layer part must be between 5 MiB and 10 MiB and a layer can have at most 4,200 parts; none of these are adjustable.[^s1]

## Policy, replication, cache and scanning limits

Lifecycle policies are limited to 50 rules and 30,720 characters. A replication configuration can have 25 rules, 100 filters per rule and 25 unique destinations across all rules. A registry can have 50 pull through cache rules. All of these are fixed.[^s1][^s2]

Basic scanning is capped at 100,000 image scans per 24 hours per account and Region, counting both scan-on-push and manual scans, and each image can be scanned once per 24 hours; neither is adjustable.[^s1] Enhanced scanning is provided by Amazon Inspector, which has its own limits.

## API rate quotas

The push and pull paths are throttled per second per Region, and all of these rates are adjustable:[^s1][^s2]

| API | Default rate | Called |
|---|---|---|
| GetAuthorizationToken | 500/s | to obtain registry credentials[^s1] |
| BatchGetImage | 2,000/s | once per pull, for the manifest |
| GetDownloadUrlForLayer | 3,000/s | once per uncached layer on pull |
| BatchCheckLayerAvailability | 1,000/s | per layer on push, to skip existing layers |
| InitiateLayerUpload | 100/s | once per new layer on push |
| UploadLayerPart | 500/s | once per part of each new layer |
| CompleteLayerUpload | 100/s | once per new layer |
| PutImage | 10/s | once per push, to write manifest and tags |

The table shows where bottlenecks appear. A large scale-out, such as hundreds of ECS tasks or Kubernetes pods starting at once, mostly consumes BatchGetImage and GetDownloadUrlForLayer, and AWS advises reviewing both together when you raise either.[^s1] A CI system that pushes many images in parallel hits the low PutImage rate of 10 per second first.[^s1]

## Gotchas and practice

- ECR publishes CloudWatch usage metrics for the authentication, push and pull APIs, and the Service Quotas console can create an alarm at a percentage of the applied quota.[^s1] Since February 2026 ECR also publishes `RepositoryCount` and `ImagesPerRepositoryCount` metrics, so you can alarm before repository or image counts reach their quotas.[^s4]
- Lifecycle policies are the usual way to stay under the per-repository image quota, but the 50-rule and 30,720-character limits mean complex retention logic may need fewer, broader rules.[^s1]
- Quotas are regional. If you replicate images to other Regions for disaster recovery, raise the adjustable quotas in the destination Regions too.[^s1][^s2]
- ECR Public has a separate, lower set of quotas: 10,000 repositories, a 10,000 MiB maximum layer size, 1 unauthenticated pull per second (not adjustable), and a fixed 500 GB per month of data for unauthenticated users.[^s5]

## Connections

- **Broader: Amazon ECR** (`kt:amazon-ecr`), whose repositories, images and APIs these quotas bound.[^s1]
- **Configured via Service Quotas** (`kt:service-quotas`), where adjustable ECR values are viewed and raised.[^s1]
- **Scoped to AWS Regions** (`kt:aws-regions`): every default applies per account per Region.[^s2]
- **Monitored by Amazon CloudWatch** (`kt:amazon-cloudwatch`) through API usage metrics and repository count metrics.[^s1][^s4]
- **Contrasts with ECS quotas** (`kt:ecs-quotas`) and **EKS quotas** (`kt:eks-quotas`): those limit the orchestrators, while image pull throughput during scale-out is set by the ECR rate quotas.[^s1]
- **Related to Amazon Inspector** (`kt:amazon-inspector`), which provides enhanced scanning, and to **Amazon ECS** (`kt:amazon-ecs`) and **Amazon EKS** (`kt:amazon-eks`), the main consumers of ECR pulls.

## Sources
[^s1]: Amazon Web Services, Amazon ECR User Guide, "Amazon ECR service quotas". https://docs.aws.amazon.com/AmazonECR/latest/userguide/service-quotas.html (accessed 2026-11-19)
[^s2]: Amazon Web Services, AWS General Reference, "Amazon ECR endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/ecr.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, What's New with AWS, "Amazon ECR now supports 100,000 images per repository" (2025-08-04). https://aws.amazon.com/about-aws/whats-new/2025/08/amazon-ecr-supports-100000-images (accessed 2026-11-19)
[^s4]: Amazon Web Services, What's New with AWS, "Amazon ECR now supports additional metrics for monitoring repositories" (2026-02-06). https://aws.amazon.com/about-aws/whats-new/2026/02/amazon-ecr-additional-repository-metrics (accessed 2026-11-19)
[^s5]: Amazon Web Services, Amazon ECR Public User Guide, "Amazon ECR Public service quotas". https://docs.aws.amazon.com/AmazonECR/latest/public/public-service-quotas.md (accessed 2026-11-19)
