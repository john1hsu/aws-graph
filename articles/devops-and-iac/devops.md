---
id: kt:devops
type: Concept
title: DevOps
aliases: [Dev Ops, DevOps culture, DevOps on AWS]
domain: devops-and-iac
summary: >-
  The combination of cultural philosophies, engineering practices and tools that lets an
  organization deliver applications and services at high velocity by removing the wall between
  development and operations; AWS codifies its view of it in the Well-Architected DevOps Guidance
  and its five DevOps Sagas.
verified: 2026-11-17
status: concept
broader: [kt:operational-excellence-pillar]
related: [kt:aws-developer-tools, kt:aws-well-architected-framework, kt:aws-certified-devops-engineer-professional]
relations:
  - {predicate: hasPart, object: kt:ci-cd}
  - {predicate: hasPart, object: kt:infrastructure-as-code}
  - {predicate: hasPart, object: kt:microservices}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
sources:
  - id: s1
    title: "What is DevOps?"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/devops/what-is-devops/
    type: official-docs
    accessed: 2026-11-17
  - id: s2
    title: "Introduction to DevOps - Introduction to DevOps on AWS (AWS Whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/introduction-devops-aws/introduction-to-devops.html
    type: official-docs
    accessed: 2026-11-17
  - id: s3
    title: "Communication and Collaboration - Introduction to DevOps on AWS (AWS Whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/introduction-devops-aws/communication-collaboration.html
    type: official-docs
    accessed: 2026-11-17
  - id: s4
    title: "The DevOps Sagas - DevOps Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/the-devops-sagas.html
    type: official-docs
    accessed: 2026-11-17
  - id: s5
    title: "Announcing the AWS Well-Architected Framework DevOps Guidance (AWS DevOps & Developer Productivity Blog)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/blogs/devops/announcing-the-aws-well-architected-framework-devops-guidance
    type: official-blog
    accessed: 2026-11-17
  - id: s6
    title: "[OA.LS.2] Align DevOps adoption with business objectives - DevOps Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/oa.ls.2-align-devops-adoption-with-business-objectives.html
    type: official-docs
    accessed: 2026-11-17
  - id: s7
    title: "Two pizza teams, from Ops to DevOps - Public Sector Cloud Transformation (AWS Whitepaper)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/public-sector-cloud-transformation/two-pizza-teams-from-ops-to-devops.html
    type: official-docs
    accessed: 2026-11-17
created: 2026-11-17
---

# DevOps

AWS defines DevOps as a mix of cultural philosophies, practices and tools that raises an organization's ability to deliver applications and services at high velocity, improving products faster than teams using traditional development and infrastructure processes.[^s1] The *Introduction to DevOps on AWS* whitepaper (April 2023) uses nearly the same wording and adds "better quality" to the goal.[^s2] DevOps is not a product or a job title in this framing: it is a way of organizing people and work, which AWS services then make cheaper to adopt by removing undifferentiated heavy lifting such as running build servers.[^s2]

## How it works

The core move is ending the hand-off between a development team that writes code and an operations team that runs it. In a DevOps model the two groups are no longer siloed; sometimes they merge into one team whose engineers own the full application lifecycle, from development and test through deployment and operations.[^s1] When quality assurance and security are pulled into the same loop, AWS notes that the model is sometimes called DevSecOps.[^s1] Automation replaces slow manual steps, and self-service tooling lets engineers deploy code or provision infrastructure without waiting on another team.[^s1]

AWS lists six practices that make the culture work: continuous integration, continuous delivery, microservices, infrastructure as code (including configuration management and policy as code), monitoring and logging, and communication and collaboration.[^s1] The whitepaper names much the same set and treats security as a cross-cutting concern, which includes protecting the CI/CD pipelines themselves with proper access controls.[^s2]

Team shape matters as much as tooling. Amazon's "two-pizza team" rule caps a team at the size two pizzas can feed, on the theory that smaller teams collaborate better and ship faster.[^s3] AWS's public-sector transformation whitepaper (now marked historical) argues that adding people adds coordination overhead, and describes two routes to shared responsibility: form a common DevOps team, or embed a DevOps-focused member in each existing team.[^s7] Either way, teams should see production as something they own end to end rather than a black box.[^s3]

## AWS DevOps Guidance and the five Sagas

In October 2023 AWS published the DevOps Guidance as part of the Well-Architected Framework, drawing on Amazon's own DevOps transformation in the early 2000s and on customer modernization work.[^s5] It became the DevOps Lens in the AWS Well-Architected Tool on 26 March 2024, so teams can self-assess workloads and get improvement plans.[^s5] The guidance groups DevOps into five "Sagas":[^s4]

- **Organizational adoption** – customer-centric culture, people-driven processes and developer experience.[^s4]
- **Development lifecycle** – feedback loops, consistent deployment methods and "everything as code".[^s4]
- **Quality assurance** – a test-first approach built into development.[^s4]
- **Automated governance** – directive, detective, preventive and responsive controls applied as automated guardrails.[^s4]
- **Observability** – instrumenting workloads to detect issues, tune performance and cost, and track business outcomes.[^s4]

Each Saga lists capabilities, indicators of success, metrics and anti-patterns.[^s4] The guidance covers only how to operate in a DevOps way; AWS says to pair it with the Well-Architected Framework for the workloads themselves.[^s4]

## Gotchas and adoption advice

- **It is a business change, not a tooling project.** The foundational practice OA.LS.2 says to treat DevOps adoption as part of business strategy with leadership backing, assess current practice against the Sagas' indicators, and turn the gaps into a prioritized list that teams plan into normal work.[^s6]
- **Revisit yearly.** AWS cites Amazon's annual operating-plan cycle (OP1, then OP2) as the model and recommends reviewing the capability list at least once a year.[^s6]
- **Speed without reliability is not the goal.** AWS pairs rapid delivery with testing every change and real-time monitoring, and pairs speed with automated compliance and policy as code.[^s1]

## Connections

DevOps is an umbrella: CI/CD and infrastructure as code are two of its named practices, and microservices are the architecture AWS associates with it.[^s1] It sits under the Well-Architected Framework through the DevOps Guidance and Lens, and is closely tied to the operational excellence pillar's focus on running workloads well.[^s5] The AWS developer tools (CodePipeline, CodeBuild, CodeDeploy and others) are the managed services that implement its delivery practices, and monitoring with a service such as Amazon CloudWatch supplies the feedback loop the observability Saga calls for.[^s2][^s4] The AWS Certified DevOps Engineer – Professional credential is AWS's certification for practitioners in this area.

## Sources
[^s1]: Amazon Web Services, "What is DevOps?". https://aws.amazon.com/devops/what-is-devops/ (accessed 2026-11-17)
[^s2]: Amazon Web Services, "Introduction to DevOps - Introduction to DevOps on AWS (AWS Whitepaper)". https://docs.aws.amazon.com/whitepapers/latest/introduction-devops-aws/introduction-to-devops.html (accessed 2026-11-17)
[^s3]: Amazon Web Services, "Communication and Collaboration - Introduction to DevOps on AWS (AWS Whitepaper)". https://docs.aws.amazon.com/whitepapers/latest/introduction-devops-aws/communication-collaboration.html (accessed 2026-11-17)
[^s4]: Amazon Web Services, "The DevOps Sagas - DevOps Guidance". https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/the-devops-sagas.html (accessed 2026-11-17)
[^s5]: Amazon Web Services, "Announcing the AWS Well-Architected Framework DevOps Guidance (AWS DevOps & Developer Productivity Blog)". https://aws.amazon.com/blogs/devops/announcing-the-aws-well-architected-framework-devops-guidance (accessed 2026-11-17)
[^s6]: Amazon Web Services, "[OA.LS.2] Align DevOps adoption with business objectives - DevOps Guidance". https://docs.aws.amazon.com/wellarchitected/latest/devops-guidance/oa.ls.2-align-devops-adoption-with-business-objectives.html (accessed 2026-11-17)
[^s7]: Amazon Web Services, "Two pizza teams, from Ops to DevOps - Public Sector Cloud Transformation (AWS Whitepaper)". https://docs.aws.amazon.com/whitepapers/latest/public-sector-cloud-transformation/two-pizza-teams-from-ops-to-devops.html (accessed 2026-11-17)
