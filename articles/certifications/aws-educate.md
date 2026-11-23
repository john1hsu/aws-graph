---
id: kt:aws-educate
type: Concept
title: AWS Educate
aliases: [Amazon Web Services Educate]
domain: certifications
summary: >-
  AWS's no-cost, self-paced cloud learning program for pre-professional learners aged 13 and up, offering
  courses, hands-on labs in the AWS Management Console, digital badges and (for adults) a job board, with no
  credit card or AWS account required.
verified: 2026-11-23
status: current
broader: [kt:amazon-web-services]
related: [kt:aws-skill-builder, kt:aws-certification-program, kt:aws-certified-cloud-practitioner, kt:aws-builder-id]
relations:
  - {predicate: alternativeTo, object: kt:aws-academy}
  - {predicate: contrastsWith, object: kt:aws-skill-builder}
sources:
  - id: s1
    title: "AWS Educate - Cloud Skills for Education"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/educate
    type: official-learning
    accessed: 2026-11-23
  - id: s2
    title: "AWS Academy FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/training/awsacademy/faq
    type: official-learning
    accessed: 2026-11-23
  - id: s3
    title: "AWS Educate expands access to free cloud education"
    publisher: Amazon Web Services (AWS Training and Certification Blog)
    url: https://aws.amazon.com/blogs/training-and-certification/aws-educate-expands-access-to-free-cloud-education
    type: official-blog
    accessed: 2026-11-23
  - id: s4
    title: "Get ahead this summer with no-cost cloud training from AWS Educate"
    publisher: Amazon Web Services (AWS Public Sector Blog)
    url: https://aws.amazon.com/blogs/publicsector/get-ahead-summer-no-cost-cloud-training-aws-educate
    type: official-blog
    accessed: 2026-11-23
  - id: s5
    title: "AWS Educate – Credits, Training, Content, and Collaboration for Students & Educators"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/aws-educate-credits-training-content-and-collaboration-for-students-educators
    type: official-blog
    accessed: 2026-11-23
created: 2026-11-23
---

# AWS Educate

AWS Educate is AWS's free, self-paced entry point to cloud skills. AWS describes it as open to any individual regardless of education level, technical experience or career stage, and lets learners as young as 13 register with just an email address and no credit card.[^s1] Its audience is the pre-professional learner — students, career changers and the merely curious — rather than working practitioners preparing for a specific exam.[^s5]

## How it works

Learners sign up on a dedicated AWS Educate portal and work through self-paced courses and hands-on labs at their own pace.[^s1] The labs run in the real AWS Management Console but in a sandbox provisioned for the learner, so practising does not require opening a billable AWS account; before the 2022 relaunch, self-guided console practice required a credit card.[^s3][^s4] The current landing page features getting-started labs for storage (Amazon S3), compute (Amazon EC2), networking (Amazon VPC), databases (Amazon RDS) and cloud operations, including a cost-estimation lab.[^s1] Labs are time-boxed, but at relaunch AWS said learners could attempt them an unlimited number of times.[^s3]

Completing a course earns a shareable digital badge.[^s1] Learners aged 18 and over can also use the AWS Educate Job Board to find jobs and internships, which employers populate through recruiter accounts.[^s1][^s4] Learners who finish qualifying courses are invited to the Emerging Talent Community.[^s4]

## History and scope changes

AWS Educate launched in May 2015 as a grant-based program for institutions, educators and students, bundling AWS credits, training content and collaboration tools.[^s5] In March 2022 AWS relaunched it: previously limited to educators, students with a .edu address and US military veterans, it opened to learners aged 13 and up in more than 200 countries and territories, without needing an Amazon account.[^s3] At that relaunch it offered 59 courses (46 foundational, five intermediate, eight advanced; 30 designed for young learners), ten English-language labs, and content in eight languages — figures as of March 2022 that have likely changed since.[^s3] A February 2023 update shifted the focus to pre-professional learners: AWS Educate no longer offers grants to institutions or educators, and educator tooling moved to AWS Academy.[^s5]

## Gotchas

- Minimum age is 13, but the AWS Learner Terms can set a higher minimum depending on the learner's country of residence.[^s4]
- The job board is restricted to learners 18 and older.[^s1]
- The 2015-era credit amounts and certification discounts for educators and students no longer apply; treat older third-party write-ups that cite them as historical.[^s5]
- AWS Educate badges are not AWS Certifications; the certification exam discount vouchers described in AWS's education FAQs come through AWS Academy and its Emerging Talent Community.[^s2]

## When to use it (and when not)

Choose AWS Educate when someone is new to cloud and wants a free, credit-card-free sandbox to try core services. If a learner's college is an AWS Academy member, AWS Academy is the better fit: it is an educator-facilitated curriculum for accredited institutions that aligns most courses with AWS Certifications and can provide exam discount vouchers.[^s2] AWS's own AWS Academy FAQ draws the line: Educate is self-paced for new-to-cloud learners, while Academy is taught by an educator, and learners whose institution is not in Academy are pointed to Educate.[^s2]

## Connections

AWS Educate sits under **Amazon Web Services** as one of its education programs, alongside AWS Academy, AWS re/Start and others listed at the 2022 relaunch.[^s3] **AWS Academy** is its institutional `alternativeTo`, since the two split the learner market between self-paced and classroom delivery.[^s2] It `contrastsWith` **AWS Skill Builder**, AWS's general training platform, which Educate's own landing page does not route learners through, and relates to the **AWS Certification program** and **AWS Certified Cloud Practitioner** as the natural next step after foundational study.[^s2] The landing page's account-creation link leads to an **AWS Builder ID** sign-up, hence the `related` edge.[^s1]

## Sources

[^s1]: Amazon Web Services, "AWS Educate - Cloud Skills for Education". https://aws.amazon.com/educate (accessed 2026-11-23)
[^s2]: Amazon Web Services, "AWS Academy FAQs". https://aws.amazon.com/training/awsacademy/faq (accessed 2026-11-23)
[^s3]: AWS Training and Certification Blog, "AWS Educate expands access to free cloud education" (2022-03-15). https://aws.amazon.com/blogs/training-and-certification/aws-educate-expands-access-to-free-cloud-education (accessed 2026-11-23)
[^s4]: AWS Public Sector Blog, "Get ahead this summer with no-cost cloud training from AWS Educate" (2022-07-05). https://aws.amazon.com/blogs/publicsector/get-ahead-summer-no-cost-cloud-training-aws-educate (accessed 2026-11-23)
[^s5]: Jeff Barr, AWS News Blog, "AWS Educate – Credits, Training, Content, and Collaboration for Students & Educators" (2015-05-13, updated 2023-02-21). https://aws.amazon.com/blogs/aws/aws-educate-credits-training-content-and-collaboration-for-students-educators (accessed 2026-11-23)
