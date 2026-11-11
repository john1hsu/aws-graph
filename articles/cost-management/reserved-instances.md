---
id: kt:reserved-instances
type: PricingModel
title: Reserved Instances
aliases: [Amazon EC2 Reserved Instances, EC2 Reserved Instances, RIs, Standard Reserved Instances, Convertible Reserved Instances, Reserved DB instances]
domain: cost-management
summary: >-
  A billing discount, not a physical server: you commit to a specific instance configuration
  (instance type, Region, tenancy and platform) for a one- or three-year term, and matching
  On-Demand usage is billed at a reduced rate. Offered for Amazon EC2 and, as reserved DB
  instances, for Amazon RDS and Aurora.
verified: 2026-11-11
status: current
broader: [kt:aws-pricing-fundamentals]
related: [kt:amazon-ec2, kt:spot-instances, kt:aws-cost-explorer, kt:aws-budgets]
relations:
  - {predicate: alternativeTo, object: kt:savings-plans}
  - {predicate: contrastsWith, object: kt:on-demand-capacity-reservations}
  - {predicate: integratesWith, object: kt:amazon-rds}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "Reserved Instances for Amazon EC2 overview - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-reserved-instances.html
    type: official-docs
    accessed: 2026-11-11
  - id: s2
    title: "Amazon EC2 Reserved Instances Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ec2/pricing/reserved-instances/
    type: official-docs
    accessed: 2026-11-11
  - id: s3
    title: "How Reserved Instance discounts are applied - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/apply_ri.html
    type: official-docs
    accessed: 2026-11-11
  - id: s4
    title: "Reserved Instance quotas - Amazon Elastic Compute Cloud"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ri-limits.html
    type: official-docs
    accessed: 2026-11-11
  - id: s5
    title: "Reserved DB instances for Amazon RDS - Amazon Relational Database Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithReservedDBInstances.html
    type: official-docs
    accessed: 2026-11-11
  - id: s6
    title: "AWS Certified Cloud Practitioner (CLF-C02) exam guide - Content Domain 4: Billing, Pricing, and Support"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.md
    type: official-learning
    accessed: 2026-11-11
created: 2026-11-11
---

# Reserved Instances

A Reserved Instance (RI) is a discount on your bill, not a server you own. You commit to one instance configuration for a term, and any running On-Demand usage that matches it is billed at the lower rate. Usage that doesn't match is billed at normal On-Demand prices.[^s1] On its EC2 pricing page, AWS says Standard RIs save up to 72% compared with On-Demand. Convertible RIs save up to 66%.[^s2] RIs are still sold, but the EC2 User Guide now recommends Savings Plans first. Savings Plans give similar discounts in exchange for a dollars-per-hour spend commitment rather than a fixed configuration.[^s1]

## How it works

The RI price depends on four things: the instance attributes, the term, the offering class and the payment option. The instance attributes are type (family and size), Region, tenancy and platform.[^s1]

- **Term.** One year (365 days) or three years (1,095 days). The three-year term gives the bigger discount. RIs don't renew automatically: when one expires, the instance keeps running and is billed at On-Demand rates.[^s1]
- **Offering class.** A Standard RI gives the largest discount. You can modify it but not exchange it. A Convertible RI gives a smaller discount but can be exchanged for another Convertible RI with different attributes.[^s1] As of 2026-11, AWS's pricing page lists average savings of 40% (1-year) and 60% (3-year) for Standard RIs, and 31% and 54% for Convertible RIs.[^s2]
- **Payment.** You can pay All Upfront, Partial Upfront or No Upfront. Paying more upfront generally saves more. With Partial and No Upfront, you are billed every hour of the term whether or not you use the RI. No Upfront also requires a successful billing history.[^s1]
- **Scope.** A *zonal* RI reserves capacity in one Availability Zone. A *regional* RI doesn't reserve capacity, but its discount applies in any AZ in the Region and across instance sizes.[^s2][^s3]

**Size flexibility** uses normalization factors: small = 1, medium = 2, large = 4, xlarge = 8 and 2xlarge = 16 units per hour. So a regional `c4.large` RI covers half of a `c4.xlarge`, and the other half is billed at On-Demand rates. Only regional RIs get size flexibility. It doesn't apply to dedicated tenancy or to platforms such as Windows, RHEL and SUSE.[^s3]

## Quotas, pricing and gotchas

- **Purchase quotas** (default, as of 2026-11): 20 new regional RIs per Region per month, plus 20 new zonal RIs per Availability Zone per month. So a three-AZ Region allows 80 a month. The quota counts each RI's instance count, not the number of purchases, and you can ask for an increase from the EC2 console.[^s4]
- **On-Demand limit interaction.** A regional RI only discounts instances that are already running, so it can't take you past your On-Demand Instance limit. A zonal RI also reserves capacity, so matching instances can run beyond that limit.[^s4]
- **No cancellation.** You can't cancel an RI after you buy it. If your needs change, you can modify it, exchange it (Convertible only) or sell it on the Reserved Instance Marketplace.[^s1] Only Standard RIs can be resold on the Marketplace, where sellers sometimes offer shorter terms.[^s2]
- **Capacity is owner-only.** Zonal RIs reserve capacity only for the account that owns them. To share reserved capacity across accounts, AWS points to On-Demand Capacity Reservations.[^s3]

## Sharing across accounts

In an AWS Organizations organization with consolidated billing, RI discounts are shared among member accounts. An account's own RIs apply to its own usage first. Next, unused zonal RIs from other accounts apply, ahead of the account's regional RIs. Remaining regional RIs then cover qualifying usage in other accounts.[^s3] The Cloud Practitioner exam guide asks candidates to describe RI flexibility and how RIs behave in AWS Organizations.[^s6]

## Reserved DB instances

Amazon RDS and Aurora use the same model, called reserved DB instances: a one- or three-year billing discount tied to the instance class and Region. For RDS, No Upfront is available only on a one-year term.[^s5] Size flexibility works within an instance class for RDS for MySQL, MariaDB, PostgreSQL, Db2 and Oracle BYOL. It doesn't apply to SQL Server or Oracle License Included. Multi-AZ DB instances count as twice the Single-AZ normalized units. The discount covers instance hours only, not storage, backups or I/O.[^s5]

## When to use it (and when not)

Use RIs for a steady baseline you are confident about, especially when you also want zonal capacity reserved. AWS's documentation recommends Savings Plans for most new EC2 commitments because they are more flexible.[^s1] Use Spot or On-Demand for spiky or interruption-tolerant work, since a commitment you don't use is still billed every hour.[^s1]

## Connections

Reserved Instances are one of the **AWS pricing fundamentals**. They discount **Amazon EC2** and, as reserved DB instances, **Amazon RDS**.[^s1][^s5] **Savings Plans** are the alternative AWS now recommends.[^s1] RIs contrast with **On-Demand Capacity Reservations**, which reserve capacity that other accounts can share.[^s3] RIs are **scoped to Regions**, with optional zonal capacity.[^s2] Their discounts are shared across **AWS Organizations**.[^s3] RI purchasing options, alongside **Spot Instances**, are in the **AWS Certified Cloud Practitioner** exam.[^s6] **AWS Cost Explorer** and **AWS Budgets** are where you track how well RIs are used.

## Sources
[^s1]: Amazon Web Services, "Reserved Instances for Amazon EC2 overview - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-reserved-instances.html (accessed 2026-11-11)
[^s2]: Amazon Web Services, "Amazon EC2 Reserved Instances Pricing". https://aws.amazon.com/ec2/pricing/reserved-instances/ (accessed 2026-11-11)
[^s3]: Amazon Web Services, "How Reserved Instance discounts are applied - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/apply_ri.html (accessed 2026-11-11)
[^s4]: Amazon Web Services, "Reserved Instance quotas - Amazon Elastic Compute Cloud". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ri-limits.html (accessed 2026-11-11)
[^s5]: Amazon Web Services, "Reserved DB instances for Amazon RDS - Amazon Relational Database Service". https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithReservedDBInstances.html (accessed 2026-11-11)
[^s6]: Amazon Web Services, "AWS Certified Cloud Practitioner (CLF-C02) exam guide - Content Domain 4: Billing, Pricing, and Support". https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02-domain4.md (accessed 2026-11-11)
