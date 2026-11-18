---
id: kt:aws-skill-builder
type: Service
title: AWS Skill Builder
aliases: [Skill Builder, AWS Skill Builder subscription]
domain: certifications
summary: >-
  AWS Training and Certification's digital learning platform: hundreds of free self-paced courses plus paid
  Individual and Team subscriptions that add hands-on labs, game-based learning, Jam challenges and official
  certification exam preparation.
verified: 2026-11-18
status: current
broader: [kt:aws-certification-program]
related: [kt:aws-certified-cloud-practitioner, kt:aws-certified-security-specialty, kt:aws-certified-generative-ai-developer-professional, kt:aws-certified-cloudops-engineer-associate]
relations:
  - {predicate: dependsOn, object: kt:aws-builder-id}
  - {predicate: enables, object: kt:aws-certification-program}
sources:
  - id: s1
    title: "AWS Training and Certification FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/training/faqs/
    type: official-learning
    accessed: 2026-11-18
  - id: s2
    title: "New – AWS Skill Builder Subscriptions"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/new-aws-skill-builder-subscriptions
    type: official-blog
    accessed: 2026-11-18
  - id: s3
    title: "AWS Training and Certification announces AWS Skill Builder subscriptions"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2022/08/aws-training-certification-aws-skill-builder-subscriptions
    type: official-blog
    accessed: 2026-11-18
  - id: s4
    title: "New courses and certification updates from AWS Training and Certification in December 2025"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/december-2025-new-offerings/
    type: official-blog
    accessed: 2026-11-18
created: 2026-11-18
---

# AWS Skill Builder

AWS Skill Builder is the digital training platform run by AWS Training and Certification. It combines a free tier of self-paced courses and learning plans with paid subscriptions that unlock hands-on and exam-preparation content. AWS's training FAQ describes the free catalogue as more than 500 on-demand courses in one place and 600+ self-paced courses and learning plans in another, offered in 17 languages including English, Japanese, Korean, Arabic and Brazilian Portuguese.[^s1] For most people preparing for an AWS certification, Skill Builder is where AWS's own official preparation material lives.

## How it works

Learners sign in with an AWS Builder ID, which AWS recommends for individuals; AWS Partners can use their Partner Central credentials, and Team learners sign in with the email their administrator registered or through company single sign-on.[^s1] Free content is open to anyone with an account. Paid tiers were introduced on 2 August 2022, when AWS added Individual and Team subscriptions on top of a catalogue that already had 500+ free courses; at launch Individual plans were sold in more than 200 countries and territories and Team plans in 17 countries.[^s3]

Subscriptions add hands-on experiences that the free tier lacks:[^s1][^s2]

- **AWS Builder Labs** — guided exercises in a real AWS console, run in a sandbox account so learners don't pay for resources.[^s2]
- **AWS Cloud Quest** — a role-based 3D game; at launch the roles were Cloud Practitioner, Solutions Architect, Serverless Developer and Machine Learning Specialist.[^s2]
- **AWS Jam** — open-ended, scenario-based challenges that give hints rather than step-by-step instructions.[^s1][^s2]
- **AWS SimuLearn** (Individual) and **AWS Industry Quest** (Team) — problem-solving with a simulated customer, and industry use-case training.[^s1]
- **Exam preparation** — exam-prep courses and Official Practice Exams for selected certifications, with per-question feedback.[^s1][^s2]
- **AWS Digital Classroom** — classroom-depth courses, included with the Individual annual and Team plans only.[^s1]

Team administrators assign seats and training, and track progress through enrollment reports; those reports do not show certification achievements. Team plans support SSO with ADFS, Azure AD, Ping and Okta, and include a dedicated technical account manager.[^s1]

## Pricing and gotchas

As of 2026-11, AWS lists these prices in USD (taxes and duties excluded for Team):[^s1]

| Plan | Price | Notes |
|---|---|---|
| Free | $0 | Self-paced digital courses |
| Individual monthly | $29 per month | Auto-renews monthly |
| Individual annual | $449 per year | Adds AWS Digital Classroom |
| Team | $449 per seat per year | Minimum 5 seats; annual; auto-renews[^s1] |

Gotchas worth knowing:

- **The annual price rose.** At the 2022 launch the Individual annual plan cost $299 per year, and Team pricing targeted organisations of 50+ people; today's annual Individual price is $449 and the Team minimum is 5 seats, so older blog posts and course listings understate the cost.[^s2][^s1]
- **Refunds and seat changes.** Individual subscribers can get a full refund only within 3 calendar days of purchase. Team subscriptions are non-refundable and seat counts can't be reduced before the end date, though up to 20% of seats may be reassigned during the year and seats can be added mid-term.[^s1]
- **Availability.** Individual plans are sold in 243 countries and territories; Team plans only in a listed set of countries.[^s1]
- **Content gets retired.** The News Blog launch post now notes that AWS Jam Journeys were retired from Skill Builder on 30 June 2026, removing all 17 journeys; transcripts and completion history stay available, and Jam Events were not affected. AWS points learners to Builder Labs, SimuLearn, Cloud Quest and Jam Events instead.[^s2]

## Keeping up with the certification cycle

AWS ships new Skill Builder content monthly and announces it on the Training and Certification Blog. The December 2025 update, for example, added 10 new AWS Builder Labs, made a free "Introduction to AWS Cloud: Builder Labs" plan with 10 fundamental labs available in 11 languages, and published exam-prep plans for the new AWS Certified Generative AI Developer – Professional exam and the updated Security – Specialty (SCS-C03) exam, plus official practice question sets for SCS-C03 in several languages.[^s4] When an exam version changes, check that the prep plan you follow targets the current exam code.

## When to use it (and when not)

Use the free tier to build foundations and to check whether a certification path suits you; it's the cheapest route to the Cloud Practitioner level. A monthly Individual subscription makes sense for a short, focused exam push, because Official Practice Exams and Builder Labs give realistic practice without spending money in your own AWS account.[^s1][^s2] Teams that need assignment, reporting and SSO should look at the Team plan, bearing in mind its 5-seat minimum and no-refund terms.[^s1] Instructor-led classroom training and third-party courses are alternatives; Skill Builder's advantage is that the exam-prep material comes from AWS itself.

## Connections

Skill Builder sits under the `kt:aws-certification-program`: it is AWS's official preparation channel for the program's exams, so it *enables* the program rather than being part of it.[^s1] It publishes prep plans for specific credentials, among them the `kt:aws-certified-cloud-practitioner`, the `kt:aws-certified-security-specialty` and the `kt:aws-certified-generative-ai-developer-professional`, and its exam-prep catalogue follows those exams as their versions change.[^s4] Sign-in *depends on* an AWS Builder ID for individual learners.[^s1]

## Sources

[^s1]: Amazon Web Services, "AWS Training and Certification FAQs". https://aws.amazon.com/training/faqs/ (accessed 2026-11-18)
[^s2]: Amazon Web Services (AWS News Blog), "New – AWS Skill Builder Subscriptions" (2022-08-02, updated 2026-06-16). https://aws.amazon.com/blogs/aws/new-aws-skill-builder-subscriptions (accessed 2026-11-18)
[^s3]: Amazon Web Services (What's New with AWS), "AWS Training and Certification announces AWS Skill Builder subscriptions" (2022-08-02). https://aws.amazon.com/about-aws/whats-new/2022/08/aws-training-certification-aws-skill-builder-subscriptions (accessed 2026-11-18)
[^s4]: Amazon Web Services (AWS Training and Certification Blog), "New courses and certification updates from AWS Training and Certification in December 2025" (2025-12-16). https://aws.amazon.com/blogs/training-and-certification/december-2025-new-offerings/ (accessed 2026-11-18)
