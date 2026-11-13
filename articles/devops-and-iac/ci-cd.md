---
id: kt:ci-cd
type: Concept
title: Continuous integration and continuous delivery
aliases: [CI/CD, CICD, Continuous integration, Continuous delivery, Continuous deployment, CI/CD pipeline]
domain: devops-and-iac
summary: >-
  The DevOps practice of merging small code changes frequently into a shared main branch with
  automated builds and tests (CI), then automatically promoting every validated change through test
  environments so it is always ready for production (continuous delivery) or released to production
  with no manual gate (continuous deployment).
verified: 2026-11-13
status: concept
broader: [kt:devops, kt:operational-excellence-pillar]
related: [kt:aws-codepipeline, kt:aws-codebuild, kt:aws-codedeploy]
relations:
  - {predicate: enables, object: kt:blue-green-deployments}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: coveredBy, object: kt:aws-certified-developer-associate}
sources:
  - id: s1
    title: "What is continuous integration and continuous delivery/deployment? - Practicing Continuous Integration and Continuous Delivery on AWS"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/practicing-continuous-integration-continuous-delivery/what-is-continuous-integration-and-continuous-deliverydeployment.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Understanding CI/CD - AWS Prescriptive Guidance (The CI/CD litmus test)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-cicd-litmus/understanding-cicd.html
    type: official-docs
    accessed: 2026-11-13
  - id: s3
    title: "Indicators for continuous integration - DevOps Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/indicators-for-ci.html
    type: official-docs
    accessed: 2026-11-13
  - id: s4
    title: "[DL.CI.2] Trigger builds automatically upon source code modifications - DevOps Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/dl.ci.2-trigger-builds-automatically-upon-source-code-modifications.html
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "[DL.CD.7] Remove manual approvals to practice continuous deployment - DevOps Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/dl.cd.7-remove-manual-approvals-to-practice-continuous-deployment.html
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "Metrics for continuous delivery - DevOps Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/metrics-for-continuous-delivery.html
    type: official-docs
    accessed: 2026-11-13
  - id: s7
    title: "OPS05-BP04 Use build and deployment management systems - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_en/wellarchitected/latest/framework/ops_dev_integ_build_mgmt_sys.html
    type: official-docs
    accessed: 2026-11-13
  - id: s8
    title: "Continuous integration and continuous delivery - AWS CAF Platform perspective (AWS Prescriptive Guidance)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/aws-caf-platform-perspective/ci-cd.html
    type: official-docs
    accessed: 2026-11-13
  - id: s9
    title: "Automating safe, hands-off deployments (Amazon Builders' Library, Clare Liguori)"
    publisher: Amazon Web Services
    url: https://builder.aws.com/content/3ErTKQOTKc5NIw031UePBPxTQ6I/automating-safe-hands-off-deployments
    type: official-blog
    accessed: 2026-11-13
  - id: s10
    title: "Content Domain 3: Deployment - AWS Certified Developer - Associate (DVA-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/developer-associate-02-domain3.html
    type: official-learning
    accessed: 2026-11-13
  - id: s11
    title: "Content Domain 3: Testing and Deployment - AWS Certified Developer - Associate (DVA-C03)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/developer-associate-03/developer-associate-03-domain3.html
    type: official-learning
    accessed: 2026-11-13
created: 2026-11-13
---

# Continuous integration and continuous delivery

CI/CD automates the path from a code commit to production with little or no manual work.[^s2] AWS's CI/CD whitepaper defines the three parts. **Continuous integration** means developers merge changes into a central repository regularly, and each merge triggers an automated build and test run.[^s1] **Continuous delivery** extends that: every change is built, tested and deployed to test or production environments, so a tested, deployment-ready artifact always exists.[^s1] **Continuous deployment** goes one step further and pushes every validated revision to production with no explicit human approval.[^s1] AWS now labels that whitepaper as historical reference, but its definitions match the current Prescriptive Guidance, which separates delivery (a manual approval before production) from deployment (no approvals at all).[^s2]

## How it works

AWS Prescriptive Guidance describes a pipeline with five stages: source, build, test, staging and production. Each stage provisions the infrastructure it needs to deploy or test the code.[^s2] On the CI side, a single main branch is the source of truth. Developers work in short-lived feature branches, open a pull request, get it reviewed and merge it. Feature branches are never deployed straight to upper environments.[^s2] On the CD side, the pipeline updates isolated environments in order, ending with production. The guidance recommends at least two development or test environments and strongly recommends defining each stage with infrastructure as code. If a step fails, that stage rolls back and the pipeline stops, so any fix has to start again in the repository.[^s2]

The Well-Architected DevOps Guidance lists seven CI indicators. They include integrating changes frequently, triggering builds automatically, running automated quality checks on every build, ordering build actions to give fast feedback, and checking that builds are reproducible.[^s3] Automatic triggering is rated foundational. A CI tool can poll the repository or receive an event on each commit, so a broken build is visible to the team at once, which is the "fail fast" principle.[^s4] The Well-Architected operational excellence pillar (OPS05-BP04, medium risk if not established) recommends automating everything from check-in through build, test, deployment and validation. It names AWS CodeBuild for building and unit testing and AWS CodeDeploy for deploying to EC2, on-premises servers, Lambda or ECS.[^s7] Its listed anti-patterns include builds that compile on a developer's machine but fail in production, and manual builds that can't be reproduced a few days later.[^s7]

## Practices and gotchas

- **Delivery vs. deployment is a choice.** DevOps Guidance rates continuous deployment (removing all manual approvals) as optional. It notes that heavily regulated organizations may need to keep approval steps, and that automated governance guardrails and observability are what make removing them safe.[^s5] The whitepaper adds that continuous delivery doesn't mean every commit ships at once. It means every commit could ship, which turns the release decision into a business call.[^s1]
- **Deploy in stages and roll back on alarms.** Amazon's own pipelines first deploy to a single "one-box" unit, then to production in waves of growing size across Regions and Availability Zones. They wait a bake time between stages and roll back automatically when high-severity aggregate alarms fire.[^s9] Amazon also avoids deploying outside business hours so engineers are available if automatic recovery fails.[^s9]
- **Measure the pipeline.** DevOps Guidance tracks pipeline stability (the share of deployments that fail or need a rollback), mean time to production, operator interventions, changes per release and deployment frequency. A high number of changes per release is a sign of batched work rather than true CI.[^s6]
- **Grow in stages.** The CAF Platform perspective suggests starting with a minimum viable CI pipeline and then adding delivery. Treat pipeline configuration as code, test early ("shift left"), and pick a deployment method: in-place, rolling, immutable or blue/green.[^s8] Its advanced practices are GitOps, signed artifacts with a software bill of materials, and continuous deployment once delivery is fully automated.[^s8]

## Exam relevance

The current Developer – Associate exam (DVA-C02) includes the task "Deploy code by using AWS CI/CD services." Its skills cover invoking pipelines by committing code, orchestrating deployments to multiple environments, rolling back, and configuring blue/green, canary and rolling strategies.[^s10] The DVA-C03 guide keeps the task (3.3) and names AWS CodePipeline explicitly for orchestrating CI/CD.[^s11]

## Connections

CI/CD is a core DevOps practice, and the Well-Architected operational excellence pillar expects it of every workload.[^s7] AWS CodePipeline orchestrates the stages, AWS CodeBuild builds and tests, and AWS CodeDeploy deploys.[^s7] Infrastructure as code lets the pipeline provision environments as well as ship application code.[^s2] Blue/green, canary and rolling releases are the deployment strategies a CD pipeline uses to limit the impact of a bad change.[^s8] Alarm-driven rollback relies on monitoring such as Amazon CloudWatch.[^s9] CI/CD skills are tested in the Developer – Associate exam.[^s10]

## Sources
[^s1]: Amazon Web Services, "What is continuous integration and continuous delivery/deployment? - Practicing Continuous Integration and Continuous Delivery on AWS". https://docs.aws.amazon.com/whitepapers/latest/practicing-continuous-integration-continuous-delivery/what-is-continuous-integration-and-continuous-deliverydeployment.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Understanding CI/CD - AWS Prescriptive Guidance (The CI/CD litmus test)". https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-cicd-litmus/understanding-cicd.html (accessed 2026-11-13)
[^s3]: Amazon Web Services, "Indicators for continuous integration - DevOps Guidance". https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/indicators-for-ci.html (accessed 2026-11-13)
[^s4]: Amazon Web Services, "[DL.CI.2] Trigger builds automatically upon source code modifications - DevOps Guidance". https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/dl.ci.2-trigger-builds-automatically-upon-source-code-modifications.html (accessed 2026-11-13)
[^s5]: Amazon Web Services, "[DL.CD.7] Remove manual approvals to practice continuous deployment - DevOps Guidance". https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/dl.cd.7-remove-manual-approvals-to-practice-continuous-deployment.html (accessed 2026-11-13)
[^s6]: Amazon Web Services, "Metrics for continuous delivery - DevOps Guidance". https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/metrics-for-continuous-delivery.html (accessed 2026-11-13)
[^s7]: Amazon Web Services, "OPS05-BP04 Use build and deployment management systems - AWS Well-Architected Framework". https://docs.aws.amazon.com/en_en/wellarchitected/latest/framework/ops_dev_integ_build_mgmt_sys.html (accessed 2026-11-13)
[^s8]: Amazon Web Services, "Continuous integration and continuous delivery - AWS CAF Platform perspective (AWS Prescriptive Guidance)". https://docs.aws.amazon.com/prescriptive-guidance/latest/aws-caf-platform-perspective/ci-cd.html (accessed 2026-11-13)
[^s9]: Amazon Web Services, "Automating safe, hands-off deployments (Amazon Builders' Library, Clare Liguori)". https://builder.aws.com/content/3ErTKQOTKc5NIw031UePBPxTQ6I/automating-safe-hands-off-deployments (accessed 2026-11-13)
[^s10]: Amazon Web Services, "Content Domain 3: Deployment - AWS Certified Developer - Associate (DVA-C02)". https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/developer-associate-02-domain3.html (accessed 2026-11-13)
[^s11]: Amazon Web Services, "Content Domain 3: Testing and Deployment - AWS Certified Developer - Associate (DVA-C03)". https://docs.aws.amazon.com/aws-certification/latest/developer-associate-03/developer-associate-03-domain3.html (accessed 2026-11-13)
