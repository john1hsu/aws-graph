---
id: kt:aws-snowball-edge
type: Service
title: AWS Snowball Edge
aliases: [Snowball Edge, AWS Snowball, Snowball, AWS Snow Family, Snowball Edge Storage Optimized, Snowball Edge Compute Optimized]
domain: migration-and-hybrid
summary: >-
  AWS's rugged, shippable storage-and-compute appliance for offline data transfer into and out of
  Amazon S3 and for edge computing in disconnected sites; it closed to new customers on 2025-11-07,
  and AWS has announced that support in commercial Regions ends on 2026-12-31.
verified: 2026-11-28
status: closed-to-new-customers
broader: [kt:aws-migration-and-transfer-services]
related: [kt:amazon-s3, kt:amazon-eks-anywhere, kt:aws-schema-conversion-tool, kt:aws-govcloud-us]
relations:
  - {predicate: alternativeTo, object: kt:aws-datasync}
  - {predicate: alternativeTo, object: kt:aws-outposts}
  - {predicate: alternativeTo, object: kt:aws-data-transfer-terminal}
  - {predicate: integratesWith, object: kt:amazon-s3}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "AWS Snowball Edge availability change - AWS Snowball Edge Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/snowball/latest/developer-guide/snowball-edge-availability-change.html
    type: official-docs
    accessed: 2026-11-28
  - id: s2
    title: "How AWS Snowball Edge works - AWS Snowball Edge Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/snowball/latest/developer-guide/how-it-works.html
    type: official-docs
    accessed: 2026-11-28
  - id: s3
    title: "AWS Snowball Edge device hardware information - AWS Snowball Edge Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/snowball/latest/developer-guide/device-differences.html
    type: official-docs
    accessed: 2026-11-28
  - id: s4
    title: "AWS Snowball Edge quotas - AWS Snowball Edge Developer Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/snowball/latest/developer-guide/limits.html
    type: official-docs
    accessed: 2026-11-28
  - id: s5
    title: "AWS Snowball | Secure Edge Computing and Offline Data Transfer"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/snowball-edge/
    type: official-docs
    accessed: 2026-11-28
  - id: s6
    title: "AWS Service Availability Updates"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/10/aws-service-availability
    type: official-blog
    accessed: 2026-11-28
created: 2026-11-28
---

# AWS Snowball Edge

AWS Snowball Edge is a physical appliance that AWS ships to your site. You load data onto it (or run compute on it) without depending on your internet link, then ship it back so AWS moves the data into Amazon S3, or you use it as a small, rugged edge server. It was the last member of the AWS Snow Family. As of 2026-11 it is in maintenance: AWS stopped taking new customers on 7 November 2025, and the product page announces that support for Snowball devices in all commercial Regions ends on 31 December 2026.[^s1][^s5][^s6]

## Lifecycle status

AWS's October 2025 availability update moved both the Storage Optimized and Compute Optimized Snowball Edge devices into maintenance. Services in that category became inaccessible to new customers from 7 November 2025, while existing customers could keep using them.[^s6] The developer guide states that AWS no longer offers any Snow Family device for new customers to order.[^s1] The product page adds a harder date. After 31 December 2026, the Snow console and Snowball resources will no longer be accessible in commercial Regions; it says customers in AWS GovCloud (US) and ADC Regions with active Snow jobs are not affected by that change.[^s5] Anyone still holding a device should plan the final return and the move to a successor before the end of 2026.

## How it works

You create a job in the console against an S3 bucket. AWS prepares and ships a device, which can take up to four weeks from job creation. There are three job types: import into S3, export from S3, and local compute and storage, which includes clusters of 3 to 16 devices.[^s2] You unlock the device with a 29-character unlock code, copy your data on, and ship it back. After each job AWS erases the device to the NIST 800-88 standard.[^s2]

Two configurations remain in the guide. **Storage Optimized 210 TB** has 210 TB of usable NVMe storage and 104 vCPUs with 416 GB of RAM. **Compute Optimized** offers up to 104 vCPUs, up to 416 GB of customer-usable memory and 28 TB of NVMe SSD.[^s3] Both carry 10 Gbit RJ45, 25 Gbit SFP28 and 100 Gbit QSFP28 ports, though only one network interface can be used at a time. Each weighs 49.7 lb (22.54 kg), and the Storage Optimized model is rated for 0–30 °C operation versus 0–45 °C for Compute Optimized.[^s3]

## Quotas, pricing and gotchas

- **One device by default.** The default quota is one Snowball Edge per account per Region at a time. More devices, or cluster jobs, need AWS Support (as of 2026-11).[^s4]
- **360-day job window.** A job must finish within 360 days of device preparation. After that the device locks and must go back.[^s4]
- **Object and connection limits.** Objects transferred to S3 can be up to 5 TB. The S3 adapter accepts at most 1,000 connections, multipart parts are capped at 2 GiB, and jumbo frames are not supported.[^s4]
- **Import and export caveats.** S3 Object Lock with default retention must be disabled on the bucket before an import. Objects in S3 Glacier storage classes must be restored before they can be exported.[^s2]
- **Shipping rules.** Devices cannot go to post office boxes, and AWS will not ship between non-US Regions, for example from Ireland to Frankfurt.[^s4]
- **Regions.** The quotas page lists 22 Regions, including both AWS GovCloud (US) Regions. The product page links to a separate pricing page; prices are not repeated here.[^s4][^s5]

## When to use it (and when not)

For existing customers, Snowball Edge still fits bulk offline transfer where the network is the bottleneck, and compute in disconnected or harsh sites.[^s5] New projects cannot choose it. For online transfer, AWS points to AWS DataSync, noting that one DataSync task can fully use 10 Gbps, with Direct Connect hosted connections if bandwidth is short. For physical transfer it points to AWS Data Transfer Terminal, a facility you reserve to upload data in person, or to AWS Marketplace partners such as Seagate and Tsecond. For edge compute it points to AWS Outposts servers and racks, which can run without AWS connectivity for up to 7 days.[^s1]

## Connections

Snowball Edge sits under `kt:aws-migration-and-transfer-services` as the offline-transfer and edge member of that category.[^s1] It `integratesWith` `kt:amazon-s3`, the bucket each import or export job targets.[^s2] It is `securedBy` `kt:aws-kms`: data written to S3 can use SSE-S3 or SSE-KMS.[^s4] It is `scopedTo` `kt:aws-regions` because devices, quotas and shipping are per Region, and it relates to `kt:aws-govcloud-us`, whose Regions are listed and exempt from the 2026 change.[^s4][^s5] Its recommended successors are `alternativeTo` edges: `kt:aws-datasync` for online transfer, `kt:aws-data-transfer-terminal` for physical transfer and `kt:aws-outposts` for edge compute.[^s1] It also relates to `kt:amazon-eks-anywhere` and `kt:aws-schema-conversion-tool`, two hybrid and migration tools in this graph that link to it.

## Sources
[^s1]: Amazon Web Services, "AWS Snowball Edge availability change - AWS Snowball Edge Developer Guide". https://docs.aws.amazon.com/snowball/latest/developer-guide/snowball-edge-availability-change.html (accessed 2026-11-28)
[^s2]: Amazon Web Services, "How AWS Snowball Edge works - AWS Snowball Edge Developer Guide". https://docs.aws.amazon.com/snowball/latest/developer-guide/how-it-works.html (accessed 2026-11-28)
[^s3]: Amazon Web Services, "AWS Snowball Edge device hardware information - AWS Snowball Edge Developer Guide". https://docs.aws.amazon.com/snowball/latest/developer-guide/device-differences.html (accessed 2026-11-28)
[^s4]: Amazon Web Services, "AWS Snowball Edge quotas - AWS Snowball Edge Developer Guide". https://docs.aws.amazon.com/snowball/latest/developer-guide/limits.html (accessed 2026-11-28)
[^s5]: Amazon Web Services, "AWS Snowball | Secure Edge Computing and Offline Data Transfer". https://aws.amazon.com/snowball-edge/ (accessed 2026-11-28)
[^s6]: Amazon Web Services, "AWS Service Availability Updates" (What's New, 2025-10-13). https://aws.amazon.com/about-aws/whats-new/2025/10/aws-service-availability (accessed 2026-11-28)
