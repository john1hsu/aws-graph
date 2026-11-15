---
id: kt:amazon-web-services
type: Organization
title: Amazon Web Services
aliases: [AWS, AWS Cloud, Amazon Web Services (AWS)]
domain: global-infrastructure
summary: >-
  Amazon's cloud computing business and platform, which since 2006 has offered on-demand compute,
  storage, database, networking, analytics, AI and other IT services as pay-as-you-go web services
  running on its own global infrastructure.
verified: 2026-11-15
status: current
broader: [kt:cloud-computing]
related: [kt:aws-pricing-fundamentals, kt:shared-responsibility-model, kt:aws-well-architected-framework]
relations:
  - {predicate: hasPart, object: kt:aws-global-infrastructure}
  - {predicate: hasPart, object: kt:aws-compute-services}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Overview of Amazon Web Services (whitepaper): Introduction"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/introduction
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Six advantages of cloud computing - Overview of Amazon Web Services"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/six-advantages-of-cloud-computing.html
    type: official-docs
    accessed: 2026-11-15
  - id: s3
    title: "What is AWS?"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/what-is-aws/
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "Global Infrastructure - AWS"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/global-infrastructure/
    type: official-docs
    accessed: 2026-11-15
  - id: s5
    title: "AWS Certified Cloud Practitioner (CLF-C02) exam guide: Content Domain 1: Cloud Concepts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain1.html
    type: official-learning
    accessed: 2026-11-15
  - id: s6
    title: "Amazon.com Announces Third Quarter Results (Form 8-K, Exhibit 99.1)"
    publisher: Amazon.com, Inc. via U.S. SEC EDGAR
    url: https://www.sec.gov/Archives/edgar/data/0001018724/000101872426000038/amzn-20260930xex991.htm
    type: news
    accessed: 2026-11-15
created: 2026-11-15
---

# Amazon Web Services

Amazon Web Services (AWS) is Amazon's cloud computing business and the platform it sells: a catalog of IT
services (compute, storage, databases, analytics, networking, developer and management tools, IoT,
security, enterprise applications and more) that customers provision on demand, within seconds, and pay
for as they go.[^s1] AWS started offering IT infrastructure to outside businesses as web services in
2006, and its overview whitepaper (updated June 2026) counts more than 200 services used by customers in
190 countries.[^s1] AWS describes itself as the most comprehensive and broadly adopted cloud, serving
millions of customers from startups to large enterprises and government agencies.[^s3]

## How it works

Every AWS service is exposed through APIs, so the same resources can be created from the console, the
CLI, SDKs or infrastructure-as-code. The services run on AWS's own physical footprint: as of 2026-11 AWS
lists 39 geographic Regions containing 124 Availability Zones, with two more Regions (Saudi Arabia and
Chile) and seven more Availability Zones announced, plus more than 750 CloudFront points of presence and
15 Regional edge caches.[^s4] A customer chooses where each workload runs; most services are Regional,
which is why Region choice drives latency, data residency and price.[^s4]

The economic model is the core of the value proposition. AWS's whitepaper frames it as six advantages:
trading upfront capital expense for variable expense, benefiting from economies of scale that lower
pay-as-you-go prices, no longer guessing capacity, faster speed and agility, not spending money running
data centers, and going global in minutes by deploying to several Regions.[^s2] Replacing fixed
infrastructure with variable cost means servers no longer have to be planned and bought weeks or months
ahead; hundreds or thousands can be started in minutes.[^s1]

## Scale, pricing and gotchas

AWS reports as a separate segment of Amazon.com, Inc. For the third quarter of 2026 the AWS segment had
net sales of $45,984 million (up from $33,006 million a year earlier, about 39%) and operating income of
$17,773 million.[^s6] These figures change every quarter and are given here only to show scale.

Gotchas for newcomers: because you pay for resources as you consume them,[^s2] cost tracks whatever is
left running, so budgets and cleanup matter from day one; service availability and prices differ by Region, so a
service or price seen in one Region cannot be assumed in another;[^s4] and the infrastructure count above
changes as Regions open, so check the global infrastructure page rather than relying on a remembered
number.[^s4]

## When it matters (and alternatives)

Understanding AWS as a whole is foundational rather than a design choice: the AWS Certified Cloud
Practitioner exam's first domain, Cloud Concepts (24% of scored content), starts with defining the
benefits of the AWS Cloud, including its value proposition, the benefits of global infrastructure such as
speed of deployment and global reach, and high availability, elasticity and agility.[^s5] The realistic
alternatives are keeping workloads in your own data centers, running them in a hybrid model, or using
another cloud provider; the six advantages above are the arguments AWS makes for moving.[^s2]

## Connections

AWS is an instance of the broader idea of **cloud computing** (`kt:cloud-computing`), which the whitepaper
defines through on-demand, pay-as-you-go delivery.[^s1] Its physical layer is the **AWS global
infrastructure** (`kt:aws-global-infrastructure`) of Regions, Availability Zones and edge locations,[^s4]
and its service catalog is organized into families such as **AWS compute services**
(`kt:aws-compute-services`), storage, databases, analytics and migration services, each of which points
back to this node.[^s1] **AWS pricing fundamentals** (`kt:aws-pricing-fundamentals`) expands the
variable-expense model,[^s2] the **shared responsibility model** (`kt:shared-responsibility-model`) and
**AWS Well-Architected Framework** (`kt:aws-well-architected-framework`) describe how customers are
expected to operate on the platform, and the topic is tested by the **AWS Certified Cloud Practitioner**
exam (`kt:aws-certified-cloud-practitioner`).[^s5]

## Sources
[^s1]: Amazon Web Services, "Overview of Amazon Web Services (whitepaper): Introduction". https://docs.aws.amazon.com/whitepapers/latest/aws-overview/introduction (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Six advantages of cloud computing - Overview of Amazon Web Services". https://docs.aws.amazon.com/whitepapers/latest/aws-overview/six-advantages-of-cloud-computing.html (accessed 2026-11-15)
[^s3]: Amazon Web Services, "What is AWS?". https://aws.amazon.com/what-is-aws/ (accessed 2026-11-15)
[^s4]: Amazon Web Services, "Global Infrastructure - AWS". https://aws.amazon.com/about-aws/global-infrastructure/ (accessed 2026-11-15)
[^s5]: Amazon Web Services, "AWS Certified Cloud Practitioner (CLF-C02) exam guide: Content Domain 1: Cloud Concepts". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain1.html (accessed 2026-11-15)
[^s6]: Amazon.com, Inc., "Amazon.com Announces Third Quarter Results" (Form 8-K, Exhibit 99.1). https://www.sec.gov/Archives/edgar/data/0001018724/000101872426000038/amzn-20260930xex991.htm (accessed 2026-11-15)
