---
id: kt:aws-free-tier
type: PricingModel
title: AWS Free Tier
aliases: [Free Tier, AWS Free plan, AWS Free account plan, AWS Free Tier credits]
domain: cost-management
summary: >-
  AWS's no-cost offer for new customers: up to USD $200 in credits, a choice between a
  Free account plan (up to six months, no charges, select services) and a Paid account
  plan, plus always-free monthly allowances on more than 30 services.
verified: 2026-11-12
status: current
broader: [kt:aws-pricing-fundamentals]
related: [kt:aws-budgets, kt:aws-lambda, kt:savings-plans, kt:reserved-instances]
relations:
  - {predicate: integratesWith, object: kt:aws-budgets}
  - {predicate: integratesWith, object: kt:aws-organizations}
  - {predicate: integratesWith, object: kt:aws-control-tower}
  - {predicate: monitoredBy, object: kt:aws-billing-and-cost-management}
  - {predicate: contrastsWith, object: kt:aws-govcloud-us}
sources:
  - id: s1
    title: "Choosing an AWS Free Tier plan - AWS Billing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier-plans.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Explore AWS services with AWS Free Tier - AWS Billing"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_us/awsaccountbilling/latest/aboutv2/free-tier.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "AWS Free Tier FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/free/faqs/
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "AWS Free Tier update: New customers can get started and explore AWS with up to $200 in credits"
    publisher: AWS News Blog
    url: https://aws.amazon.com/blogs/aws/aws-free-tier-update-new-customers-can-get-started-and-explore-aws-with-up-to-200-in-credits
    type: official-blog
    accessed: 2026-11-12
  - id: s5
    title: "New AWS experience helps builders get started and ship faster"
    publisher: What's New with AWS
    url: https://aws.amazon.com/about-aws/whats-new/2026/09/New-AWS-Builder-Experience
    type: official-blog
    accessed: 2026-11-12
  - id: s6
    title: "AWS Lambda pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/lambda/pricing/
    type: official-docs
    accessed: 2026-11-12
created: 2026-11-12
---

# AWS Free Tier

The AWS Free Tier is how new customers use AWS without paying up front. Since 15 July 2025 it has been built around credits rather than per-service 12-month allowances: every new customer gets USD $100 in credits at sign-up and can earn up to another USD $100 by completing introductory activities, and chooses either a Free account plan or a Paid account plan.[^s1][^s4] Accounts created before that date stay on the legacy Free Tier, which keeps its short-term trials, 12-month trials and always-free offers.[^s4]

## How it works

**Credits.** The extra USD $100 comes from five activities worth $20 each: launching and terminating an EC2 instance, exploring RDS configuration options, building a web app with a Lambda function URL, submitting a prompt in the Amazon Bedrock playground, and creating a cost budget in AWS Budgets.[^s4] Only customers with no current or previous AWS account qualify, and credits can't be transferred.[^s3]

**Free account plan.** It never bills you. It lasts six months or until the credits are used up, whichever comes first, and it can't be extended.[^s1][^s3] To prevent surprise charges it leaves out anything that could burn through credits quickly or involves buying hardware, such as Savings Plans, Reserved Instances and some AWS Marketplace offers. Free-plan accounts also can't receive other promotional credits.[^s1] Some actions upgrade the account to a Paid plan automatically, including joining AWS Organizations, setting up an AWS Control Tower landing zone, signing an Enterprise Agreement, or designating the account as HIPAA or SEC compliant.[^s1]

**Paid account plan.** You get every service right away, and the same credits apply first to eligible usage. Anything beyond them is billed at standard on-demand rates, and the account stays open when the credits run out.[^s1][^s4] Paid accounts can also use short-term trials of selected services, which start when you activate the service.[^s2]

**Always free.** More than 30 services include a monthly allowance that never expires and applies on both plans.[^s1][^s2] For example, Lambda's free tier covers one million requests and 400,000 GB-seconds a month. It doesn't apply to functions that use Provisioned Concurrency.[^s6]

## What happens at the end, and gotchas

- **The Free plan ends by closing the account.** AWS sends warnings 15, 7 and 2 days before the free period ends.[^s4] After it ends, the account is closed and you lose access to its resources. AWS keeps the content for 90 days, and upgrading to a Paid plan within that window restores the account. After that, the account and everything in it are permanently deleted.[^s1][^s3]
- **Credit expiry isn't stated consistently.** As of 2026-11, the FAQ says Free Tier credits expire 6 months after account creation in some answers and 12 months in others. Credits earned from activities expire 12 months after the account is created. Check your account's Credits page instead of relying on either figure.[^s3]
- **Where it applies.** Free Tier credits and plan choices aren't available in the China (Beijing and Ningxia) Regions. The Free Tier doesn't apply in AWS GovCloud (US) apart from certain services such as Lambda, and you must upgrade to a Paid plan before you can create a GovCloud account.[^s3]
- **Tracking usage.** The Billing console's Free Tier page shows usage by service, usage type and Region. The console home page and the GetAccountPlanState API show your remaining credits and days. AWS also emails you as your balance and end date get close.[^s3]

## What changed (2026-09)

In September 2026 AWS launched a new sign-up experience. You can sign up with Google, GitHub, Apple or Amazon credentials, and most new customers need no credit card to get up to $200 in Free Tier credits. Sign-up creates a starter project, and after upgrading to a Paid plan each project can have a monthly spend limit that pauses it for the rest of the month once reached.[^s5] The FAQ gives $20 as the lowest spend limit, with notifications at 50%, 75% and 90%.[^s3]

## When to use it (and when not)

The Free plan suits learning, certification study and prototypes where a hard stop is better than a bill. Teams that plan to use multiple accounts, commitment discounts or regulated workloads should start on the Paid plan, because Organizations, Control Tower and compliance designations upgrade the account anyway.[^s1] Even on a Paid plan, creating a budget is a cheap safeguard, and it also earns one of the activity credits.[^s4]

## Connections

The Free Tier sits under **AWS pricing fundamentals** as the entry point to pay-as-you-go pricing. It ties into **AWS Budgets** through the budget activity and into **AWS Organizations** and **AWS Control Tower** because using either one upgrades the account.[^s1][^s4] It doesn't overlap with commitment models such as **Savings Plans** and **Reserved Instances**, which the Free plan excludes.[^s1] Credit balances and Free Tier usage show up in **AWS Billing and Cost Management**.[^s3] **AWS GovCloud (US)** is mostly outside the offer, and **AWS Lambda** is the classic example of an always-free allowance.[^s3][^s6]

## Sources
[^s1]: Amazon Web Services, "Choosing an AWS Free Tier plan - AWS Billing". https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier-plans.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Explore AWS services with AWS Free Tier - AWS Billing". https://docs.aws.amazon.com/en_us/awsaccountbilling/latest/aboutv2/free-tier.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "AWS Free Tier FAQs". https://aws.amazon.com/free/faqs/ (accessed 2026-11-12)
[^s4]: AWS News Blog, "AWS Free Tier update: New customers can get started and explore AWS with up to $200 in credits". https://aws.amazon.com/blogs/aws/aws-free-tier-update-new-customers-can-get-started-and-explore-aws-with-up-to-200-in-credits (accessed 2026-11-12)
[^s5]: What's New with AWS, "New AWS experience helps builders get started and ship faster". https://aws.amazon.com/about-aws/whats-new/2026/09/New-AWS-Builder-Experience (accessed 2026-11-12)
[^s6]: Amazon Web Services, "AWS Lambda pricing". https://aws.amazon.com/lambda/pricing/ (accessed 2026-11-12)
