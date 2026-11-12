---
id: kt:amazon-guardduty
type: Service
title: Amazon GuardDuty
aliases: [GuardDuty]
domain: security
summary: >-
  AWS's managed threat detection service, which continuously analyzes CloudTrail management
  events, VPC flow logs and DNS logs, plus optional protection plans for S3, EKS, runtime, malware,
  RDS, Lambda and AI workloads, and reports suspicious activity as findings per Region.
verified: 2026-11-12
status: current
broader: [kt:aws-security-services]
related: [kt:aws-security-hub, kt:amazon-detective, kt:amazon-inspector]
relations:
  - {predicate: dependsOn, object: kt:aws-cloudtrail}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: configuredVia, object: kt:aws-organizations}
  - {predicate: governedBy, object: kt:guardduty-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: compliesWith, object: kt:fedramp-on-aws}
  - {predicate: coveredBy, object: kt:aws-certified-cloud-practitioner}
sources:
  - id: s1
    title: "What is Amazon GuardDuty?"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Concepts and key terms in Amazon GuardDuty"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_concepts.html
    type: official-docs
    accessed: 2026-11-12
  - id: s3
    title: "Amazon GuardDuty quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_limits.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "Amazon GuardDuty pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/guardduty/pricing
    type: official-docs
    accessed: 2026-11-12
  - id: s5
    title: "Amazon GuardDuty adds Extended Threat Detection for Amazon EC2 and Amazon ECS"
    publisher: AWS News Blog
    url: https://aws.amazon.com/blogs/aws/amazon-guardduty-adds-extended-threat-detection-for-amazon-ec2-and-amazon-ecs
    type: official-blog
    accessed: 2026-11-12
  - id: s6
    title: "AWS Services in Scope by Compliance Program: FedRAMP"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/compliance/services-in-scope/FedRAMP/
    type: official-docs
    accessed: 2026-11-12
  - id: s7
    title: "In-Scope AWS Services (AWS Certified Cloud Practitioner CLF-C02)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html
    type: official-learning
    accessed: 2026-11-12
created: 2026-11-12
---

# Amazon GuardDuty

Amazon GuardDuty is AWS's managed threat detection service. It continuously processes AWS logs and event streams and combines threat intelligence (lists of known malicious IP addresses and domains, file hashes) with machine learning models to flag suspicious activity.[^s1] Typical detections include compromised or exfiltrated AWS credentials, data exfiltration or destruction that can precede ransomware, unusual logins to Aurora and RDS, cryptomining on EC2 and containers, and malware on instances, containers and newly uploaded S3 objects.[^s1]

## How it works

Turning GuardDuty on in an account and Region starts analysis of three **foundational data sources**: CloudTrail management events, VPC flow logs from EC2 instances and DNS logs. No additional setup is required for these sources.[^s1] Each enabled Region gets its own **detector**, a resource with a 32-character ID that every finding, setting and API call refers to. Enabling another Region creates a separate detector, so coverage is decided per Region.[^s2]

On top of the foundation sit optional **protection plans**. As of 2026-11 the user guide lists nine: S3 Protection, EKS Protection (audit logs), Runtime Monitoring (OS-level events on EKS, EC2 and ECS including Fargate), Malware Protection for EC2, Malware Protection for S3, Malware Protection for AWS Backup, RDS Protection, Lambda Protection and AI Protection for Bedrock, Bedrock AgentCore and SageMaker AI workloads.[^s1] A first-time enablement turns on every plan except Runtime Monitoring. Plans launched later are not switched on automatically for existing customers. Malware Protection for S3 is the only plan that works without enabling GuardDuty itself.[^s1]

Results arrive as **findings** describing the affected resource, with types such as `Recon:EC2/Portscan`. They appear in the console, through the GetFindings and ListFindings APIs, and as Amazon EventBridge events.[^s2] **Suppression rules** auto-archive known false positives while keeping their history, and **trusted** and **threat IP lists** tune what GuardDuty treats as safe or malicious.[^s2] Custom detection rules and an AI-assisted investigation feature (in preview as of 2026-11) are also documented.[^s1]

**Extended Threat Detection** links individually ambiguous signals across data sources, resources and time into a single **attack sequence** finding. It is on by default at no extra cost and needs no protection plan.[^s1] At re:Invent 2025 (2 December 2025) AWS extended it beyond IAM, S3 and EKS scenarios to EC2 instance groups and ECS clusters. The resulting critical-severity sequences carry a timeline, MITRE ATT&CK mappings and remediation guidance; enabling Runtime Monitoring makes them more complete.[^s5]

## Quotas, pricing and gotchas

Quotas are per Region (as of 2026-11, none listed as adjustable): one detector, 100 saved filters, a **90-day finding retention period**, six threat IP lists of up to 250,000 entries each, one trusted IP list of up to 2,000 entries, and 5,000 members by invitation or 50,000 through AWS Organizations.[^s3] Because findings expire after 90 days, plan to keep them elsewhere if you need a longer record.[^s3]

Billing is usage-based. US East (N. Virginia) examples as of 2026-11: $4.00 per million CloudTrail management events; VPC flow and DNS log analysis from $1.00 per GB for the first 500 GB, falling to $0.50 and then $0.25 in higher tiers; Runtime Monitoring at $1.50 per vCPU per month for the first 500 vCPUs; Malware Protection for EC2 at $0.03 per GB scanned; Malware Protection for S3 at $0.09 per GB plus $0.215 per 1,000 objects.[^s4] New accounts get a 30-day free trial per Region, and existing users get a fresh 30-day trial for each plan they have not yet enabled. Malware Protection for AWS Backup and on-demand scans have no trial.[^s4] Use the trial to measure what each plan would cost before committing.

## When to use it (and when not)

GuardDuty is the default detective control for almost any AWS account: it needs no agents for its foundational sources and no log plumbing.[^s1] It complements rather than replaces the services around it. Amazon Inspector looks for vulnerabilities before exploitation, Amazon Detective investigates a finding's context, and AWS Security Hub aggregates and prioritizes findings from GuardDuty and others.[^s1] GuardDuty reports findings rather than blocking activity, so pair it with EventBridge-driven automation, or with preventive controls such as AWS WAF and security groups, to act on what it finds.[^s1][^s2]

## Connections

GuardDuty belongs to the **AWS security services** family (**broader**) and **depends on AWS CloudTrail** management events as a foundational source.[^s1] It is **related to** AWS Security Hub, Amazon Detective and Amazon Inspector through the aggregation, investigation and vulnerability-scanning integrations named in its user guide.[^s1] It **integrates with Amazon EventBridge** for near-real-time finding events[^s2] and is **configured via AWS Organizations**, the recommended way to manage it across many accounts.[^s1] Its fixed per-Region limits are **GuardDuty quotas**, and its detectors are **scoped to AWS Regions**.[^s3][^s2] AWS's FedRAMP services-in-scope list names GuardDuty for both the US East/West and GovCloud (US) authorizations (**compliesWith** FedRAMP on AWS).[^s6] It is listed under Security, Identity and Compliance in the in-scope services for the AWS Certified Cloud Practitioner (CLF-C02) exam (**coveredBy**).[^s7]

## Sources
[^s1]: Amazon Web Services, "What is Amazon GuardDuty?". https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html (accessed 2026-11-12)
[^s2]: Amazon Web Services, "Concepts and key terms in Amazon GuardDuty". https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_concepts.html (accessed 2026-11-12)
[^s3]: Amazon Web Services, "Amazon GuardDuty quotas". https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_limits.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, "Amazon GuardDuty pricing". https://aws.amazon.com/guardduty/pricing (accessed 2026-11-12)
[^s5]: AWS News Blog, "Amazon GuardDuty adds Extended Threat Detection for Amazon EC2 and Amazon ECS" (2 December 2025). https://aws.amazon.com/blogs/aws/amazon-guardduty-adds-extended-threat-detection-for-amazon-ec2-and-amazon-ecs (accessed 2026-11-12)
[^s6]: Amazon Web Services, "AWS Services in Scope by Compliance Program: FedRAMP". https://aws.amazon.com/compliance/services-in-scope/FedRAMP/ (accessed 2026-11-12)
[^s7]: Amazon Web Services, "In-Scope AWS Services" (AWS Certified Cloud Practitioner CLF-C02). https://docs.aws.amazon.com/fr_fr/aws-certification/latest/userguide/clf-02-in-scope-services.html (accessed 2026-11-12)
