---
id: kt:aws-elastic-beanstalk
type: Service
title: AWS Elastic Beanstalk
aliases: [Elastic Beanstalk, Amazon Elastic Beanstalk, EB, Beanstalk]
domain: compute
summary: >-
  AWS's managed application-deployment service: you upload code or a container and Elastic Beanstalk
  provisions and operates the EC2 instances (Standard mode) or Amazon EKS cluster (Cluster mode), load
  balancing, scaling and health monitoring in your own account, at no charge beyond the resources used.
verified: 2026-11-14
status: current
broader: [kt:aws-compute-services]
related: [kt:amazon-ec2-auto-scaling, kt:elastic-load-balancing, kt:amazon-sqs, kt:blue-green-deployments, kt:eks-auto-mode]
relations:
  - {predicate: runsOn, object: kt:amazon-ec2}
  - {predicate: runsOn, object: kt:amazon-eks}
  - {predicate: configuredVia, object: kt:aws-cloudformation}
  - {predicate: governedBy, object: kt:elastic-beanstalk-quotas}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
  - {predicate: coveredBy, object: kt:aws-certified-developer-associate}
sources:
  - id: s1
    title: "What is AWS Elastic Beanstalk?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome
    type: official-docs
    accessed: 2026-11-14
  - id: s2
    title: "AWS Elastic Beanstalk pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/elasticbeanstalk/pricing
    type: official-docs
    accessed: 2026-11-14
  - id: s3
    title: "AWS Elastic Beanstalk introduces Cluster Mode to run multiple applications on shared infrastructure"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/elastic-beanstalk-cluster-mode/
    type: official-blog
    accessed: 2026-11-14
  - id: s4
    title: "Deployment policies and settings"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html
    type: official-docs
    accessed: 2026-11-14
  - id: s5
    title: "Elastic Beanstalk worker environments"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts-worker.html
    type: official-docs
    accessed: 2026-11-14
  - id: s6
    title: "AWS Elastic Beanstalk endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/elasticbeanstalk.html
    type: official-docs
    accessed: 2026-11-14
  - id: s7
    title: "Configuring application version lifecycle settings"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/applications-lifecycle.html
    type: official-docs
    accessed: 2026-11-14
  - id: s8
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-14
  - id: s9
    title: "In-Scope AWS Services - AWS Certified Developer - Associate (DVA-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/dva-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-14
  - id: s10
    title: "In-Scope AWS Services - AWS Certified Cloud Practitioner (CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-14
created: 2026-11-14
---

# AWS Elastic Beanstalk

AWS Elastic Beanstalk deploys web applications into AWS on supported platforms: you supply the code, and the service provisions the compute, configures load balancing, sets up health monitoring and scales the environment.[^s1] Everything it creates lives in your own AWS account, and you manage it from the console, the AWS CLI, the EB CLI, an official GitHub Action, or infrastructure as code with AWS CloudFormation or the Terraform AWS provider.[^s1] Standard mode supports Go, Java, .NET, Node.js, PHP, Python and Ruby, plus Docker containers for anything else.[^s1]

## How it works

You create an **application**, upload a source bundle (an **application version**), and Elastic Beanstalk launches an **environment** with the AWS resources needed to run it; later you deploy new versions to that environment.[^s1] An environment is either a web server environment or a **worker** environment.[^s1] A worker environment has an Auto Scaling group, EC2 instances, an IAM role and an Amazon SQS queue (created for you if you don't supply one); a daemon on each instance reads messages from the shared queue and hands each one to your application for processing.[^s5]

Since September 2026 there are two deployment modes.[^s3] **Standard mode** is the original experience: it runs applications directly on Amazon EC2, supports Windows applications, and is the cheaper choice for a small number of applications.[^s1] **Cluster mode** runs applications on Amazon EKS and pools infrastructure across applications instead of creating separate infrastructure per environment; you supply source, a Dockerfile or an ECR image and Elastic Beanstalk builds and runs the container.[^s3][^s1] Cluster mode adds event-driven autoscaling, OpenTelemetry observability to CloudWatch and third-party tools, Secrets Manager integration, and HTTPS by default via AWS Certificate Manager; at launch it was available in all commercial Regions that offer Elastic Beanstalk.[^s3]

## Deployment policies

For EC2-based environments you choose how a new version rolls out.[^s4] **All at once** takes every instance out of service briefly. **Rolling** updates one batch at a time, reducing capacity by a batch; **rolling with additional batch** first launches an extra batch so capacity stays full.[^s4] If a rolling deployment fails, completed batches keep the new version and the rest keep the old one, so you recover by redeploying a known good version.[^s4] **Immutable** launches a full new set of instances in a separate Auto Scaling group and terminates them if they fail health checks, leaving the originals untouched.[^s4] **Traffic splitting** sends a percentage of traffic to new instances for an evaluation period and can shift traffic back quickly; it requires an Application Load Balancer.[^s4] Immutable and traffic-splitting deployments replace all instances, so burstable instances lose accumulated CPU credits.[^s4] Cluster-mode environments configure rolling behaviour differently, with surge and unavailability settings.[^s4]

## Quotas, pricing and gotchas

There is no additional charge for Elastic Beanstalk; you pay for the underlying resources, mainly compute and load balancing (as of 2026-11).[^s2] In Cluster mode you also pay standard Amazon EKS charges, including EKS Auto Mode for infrastructure it provisions.[^s2][^s3] The free-tier offer for new accounts covers Standard mode only; Cluster mode is not Free Tier eligible.[^s2] AWS's own rough estimate is about $12 per month for a minimal single-instance Standard environment and about $29 with a load balancer, varying by Region and configuration.[^s2]

Default quotas, per account per Region and all adjustable (as of 2026-11): 75 applications, 1,000 application versions, 200 environments, 2,000 configuration templates and 50 custom platform versions.[^s6] The application-version quota is shared across all applications in the Region; because a version lifecycle policy runs only after a new version is created successfully, an account already at the quota must delete versions manually before it can deploy again.[^s7] Lifecycle settings apply to Standard environments only.[^s7]

## When to use it (and when not)

Elastic Beanstalk fits teams who want a conventional web app or worker running on AWS without assembling EC2, Auto Scaling, load balancers and monitoring themselves, while still being able to see and tune those resources.[^s1] Choose Standard mode for one or a few applications or for Windows workloads; consider Cluster mode when many containerized applications could share capacity and need faster scaling.[^s1][^s3] Because Cluster mode is itself built on Amazon EKS, teams that want to manage Kubernetes directly can use EKS without the Elastic Beanstalk layer.[^s3]

## Connections

Elastic Beanstalk sits within the broader **AWS compute services** and **runs on** **Amazon EC2** in Standard mode and **Amazon EKS** (with **EKS Auto Mode**) in Cluster mode.[^s1][^s3] It assembles **Amazon EC2 Auto Scaling** groups, **Elastic Load Balancing**, and **Amazon SQS** queues for worker environments, and its immutable and traffic-splitting policies are forms of **blue/green**-style deployment.[^s4][^s5] It can be **configured via** **AWS CloudFormation**.[^s1] It is **governed by** the Elastic Beanstalk service quotas.[^s6] AWS lists it in scope for FedRAMP Moderate (East/West) and High (GovCloud), so it **complies with** **FedRAMP on AWS**.[^s8] It is an in-scope compute service in the current exam guides for the **AWS Certified Cloud Practitioner** and **AWS Certified Developer – Associate**.[^s10][^s9]

## Sources
[^s1]: Amazon Web Services, "What is AWS Elastic Beanstalk?". https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome (accessed 2026-11-14)
[^s2]: Amazon Web Services, "AWS Elastic Beanstalk pricing". https://aws.amazon.com/elasticbeanstalk/pricing (accessed 2026-11-14)
[^s3]: Amazon Web Services, What's New with AWS, "AWS Elastic Beanstalk introduces Cluster Mode to run multiple applications on shared infrastructure" (2026-09-17). https://aws.amazon.com/about-aws/whats-new/2026/09/elastic-beanstalk-cluster-mode/ (accessed 2026-11-14)
[^s4]: Amazon Web Services, "Deployment policies and settings", AWS Elastic Beanstalk Developer Guide. https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html (accessed 2026-11-14)
[^s5]: Amazon Web Services, "Elastic Beanstalk worker environments", AWS Elastic Beanstalk Developer Guide. https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts-worker.html (accessed 2026-11-14)
[^s6]: Amazon Web Services, "AWS Elastic Beanstalk endpoints and quotas", AWS General Reference. https://docs.aws.amazon.com/general/latest/gr/elasticbeanstalk.html (accessed 2026-11-14)
[^s7]: Amazon Web Services, "Configuring application version lifecycle settings", AWS Elastic Beanstalk Developer Guide. https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/applications-lifecycle.html (accessed 2026-11-14)
[^s8]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-14)
[^s9]: Amazon Web Services, "In-Scope AWS Services – AWS Certified Developer – Associate (DVA-C02)". https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/dva-02-in-scope-services.html (accessed 2026-11-14)
[^s10]: Amazon Web Services, "In-Scope AWS Services – AWS Certified Cloud Practitioner (CLF-C02)". https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-14)
