---
id: kt:amazon-q-developer
type: Service
title: Amazon Q Developer
aliases: [Q Developer, Amazon Q Developer Pro, Q Developer IDE plugins]
domain: ai-and-ml
summary: >-
  AWS's generative-AI assistant for building and operating on AWS, delivered as IDE plugins with
  chat, inline completions, code generation, security scanning and code transformation; closed to
  new subscriptions and Free Tier sign-ups since May 15, 2026, with IDE plugin support ending
  April 30, 2027 and AWS directing developers to Kiro.
verified: 2026-11-15
status: closed-to-new-customers
broader: [kt:generative-ai-on-aws]
related: [kt:aws-transform, kt:amazon-q-business]
relations:
  - {predicate: replacedBy, object: kt:kiro}
  - {predicate: runsOn, object: kt:amazon-bedrock}
  - {predicate: dependsOn, object: kt:iam-identity-center}
  - {predicate: integratesWith, object: kt:aws-transform}
sources:
  - id: s1
    title: "What is Amazon Q Developer?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/what-is.html
    type: official-docs
    accessed: 2026-11-15
  - id: s2
    title: "Amazon Q Developer end-of-support announcement"
    publisher: Amazon Web Services (AWS DevOps Blog)
    url: https://aws.amazon.com/blogs/devops/amazon-q-developer-end-of-support-announcement/
    type: official-blog
    accessed: 2026-11-15
  - id: s3
    title: "Amazon Q Developer pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/q/developer/pricing/
    type: official-docs
    accessed: 2026-11-15
  - id: s4
    title: "Amazon Q Developer in console is now the AWS assistant (What's New, November 5, 2026)"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/11/q-developer-renamed-aws-assistant/
    type: official-blog
    accessed: 2026-11-15
  - id: s5
    title: "Using Amazon Q Developer on the command line"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line.html
    type: official-docs
    accessed: 2026-11-15
  - id: s6
    title: "Amazon Q Developer endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/amazonqdev.html
    type: official-docs
    accessed: 2026-11-15
created: 2026-11-15
---

# Amazon Q Developer

Amazon Q Developer is AWS's generative-AI conversational assistant for understanding, building, extending and operating applications on AWS. It answers questions about AWS architecture, your resources, best practices and documentation, and inside an IDE it adds software-development help.[^s1] It is sold as a Free tier and a paid Pro tier.[^s3]

**Lifecycle (as of 2026-11):** On April 30, 2026 AWS announced end of support for the product's developer-facing parts. From May 15, 2026 no new Free Tier accounts (via Builder ID in the IDE plugins) or new Q Developer subscriptions can be created; existing Pro subscriptions keep working and can still add users.[^s2] The IDE plugins, paid subscriptions and in-IDE code transformation reach end of support on **April 30, 2027**, and AWS recommends **Kiro**, its agentic IDE and CLI for spec-driven development, as the successor.[^s2][^s1] The command-line tool has already become the Kiro CLI.[^s5]

## How it works

In the IDE, Q Developer runs as a plugin for Visual Studio Code, JetBrains IDEs, the AWS Toolkit for Visual Studio and Eclipse (preview). It offers chat about code, inline completions, net-new code generation, security vulnerability scanning, and upgrades such as language-version changes, debugging and optimization.[^s1] Developers can sign in with an AWS Builder ID for the Free tier, which does not need an AWS account; Pro is a paid subscription.[^s1][^s3] Pro subscriptions are tied to IAM Identity Center and its supported Regions.[^s3]

The assistant is built on Amazon Bedrock, with the model augmented by AWS content and Bedrock's automated abuse detection applied to requests.[^s1] Model choice has diverged from Kiro: Opus 4.6 was removed from Q Developer Pro on May 29, 2026, and newer models are offered only in Kiro.[^s2]

Outside the IDE, the brand has been splitting up. On November 5, 2026 AWS renamed the conversational experience in the AWS Management Console and Console Mobile App to the **AWS assistant**, currently free, with existing Pro subscribers keeping Pro features automatically.[^s4] The documentation site and marketing site use their own assistants, and the Slack and Microsoft Teams integration continues as Q Developer in chat applications.[^s1][^s2]

## Quotas, pricing and gotchas

Prices and limits from the AWS pricing page (no Region stated; checked 2026-11-15):[^s3]

- **Free:** 50 agentic requests (chat and agentic coding) a month and 1,000 lines of code of Java upgrade transformation per user per month.[^s3]
- **Pro:** $19 per user per month; agentic requests included with limits; 4,000 lines of code of Java transformation per month pooled at the account level, then $0.003 per line submitted.[^s3]

The AWS General Reference lists further per-user Pro quotas, for example 1,000 generative SQL queries a month and 20 network reachability analyses a day in the console, and 1,000,000 lines of .NET transformation a month per supported Region. It does not say whether these are adjustable.[^s6]

Gotchas:
- **No new sign-ups.** A team without an existing subscription cannot start on Q Developer; it has to adopt Kiro.[^s2]
- **Plan transformation work.** In-IDE Java upgrades and .NET porting end with the plugins; AWS points to AWS Transform custom and AWS Transform full-stack Windows modernization instead.[^s2]
- **Docs lag the rename.** Console features appear under the AWS assistant now, while the IDE and CLI documentation still says Q Developer.[^s1][^s4]

## When to use it (and when not)

Existing Pro customers can keep using the plugins through April 30, 2027, but should plan a move to Kiro, which gets the newest models and features.[^s2] New coding-assistant projects should start on Kiro. Large migrations and modernizations belong in AWS Transform rather than per-developer IDE transformations.[^s2] For an employee assistant over company documents rather than a coding assistant, Amazon Q Business is the sibling product under the Amazon Q brand.

## Connections

Q Developer sits under **generative AI on AWS**. It is **replaced by** Kiro, AWS's named successor for the IDE and CLI experience.[^s2][^s5] It **runs on** Amazon Bedrock foundation models,[^s1] and Pro subscriptions **depend on** IAM Identity Center.[^s3] Its transformation features **integrate with** and hand off to AWS Transform.[^s2] It shares the Amazon Q brand with Amazon Q Business, while its console chat now carries the separate AWS assistant name.[^s4]

## Sources
[^s1]: Amazon Web Services, "What is Amazon Q Developer?". https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/what-is.html (accessed 2026-11-15)
[^s2]: Amazon Web Services, "Amazon Q Developer end-of-support announcement" (AWS DevOps Blog, April 30, 2026). https://aws.amazon.com/blogs/devops/amazon-q-developer-end-of-support-announcement/ (accessed 2026-11-15)
[^s3]: Amazon Web Services, "Amazon Q Developer pricing". https://aws.amazon.com/q/developer/pricing/ (accessed 2026-11-15)
[^s4]: Amazon Web Services, "Amazon Q Developer in console is now the AWS assistant" (What's New, November 5, 2026). https://aws.amazon.com/about-aws/whats-new/2026/11/q-developer-renamed-aws-assistant/ (accessed 2026-11-15)
[^s5]: Amazon Web Services, "Using Amazon Q Developer on the command line". https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line.html (accessed 2026-11-15)
[^s6]: Amazon Web Services, "Amazon Q Developer endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/amazonqdev.html (accessed 2026-11-15)
