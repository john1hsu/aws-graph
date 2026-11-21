---
id: kt:aws-ground-station
type: Service
title: AWS Ground Station
aliases: [Ground Station, AWS Ground Station Dedicated Antennas]
domain: global-infrastructure
summary: >-
  A fully managed AWS service that rents time on AWS-operated satellite antennas, co-located with or
  attached to AWS Regions, so satellite operators can uplink commands and downlink data per minute and
  process it in EC2 or S3 without building their own ground stations.
verified: 2026-11-21
status: current
broader: [kt:aws-global-infrastructure]
related: [kt:amazon-ec2, kt:amazon-s3, kt:amazon-vpc, kt:aws-global-network]
relations:
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: integratesWith, object: kt:amazon-eventbridge}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: governedBy, object: kt:ground-station-quotas}
sources:
  - id: s1
    title: "What is AWS Ground Station? - AWS Ground Station User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ground-station/latest/ug/what-is.html
    type: official-docs
    accessed: 2026-11-21
  - id: s2
    title: "How AWS Ground Station works - AWS Ground Station User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ground-station/latest/ug/how-it-works.html
    type: official-docs
    accessed: 2026-11-21
  - id: s3
    title: "AWS Ground Station Locations - AWS Ground Station User Guide"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/ground-station/latest/ug/aws-ground-station-antenna-locations.html
    type: official-docs
    accessed: 2026-11-21
  - id: s4
    title: "AWS Ground Station FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ground-station/faqs/
    type: official-docs
    accessed: 2026-11-21
  - id: s5
    title: "AWS Ground Station Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ground-station/pricing/
    type: official-docs
    accessed: 2026-11-21
  - id: s6
    title: "AWS Ground Station endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/gs.html
    type: official-docs
    accessed: 2026-11-21
created: 2026-11-21
---

# AWS Ground Station

AWS Ground Station is a fully managed service for satellite communications: instead of building and staffing their own antenna sites, operators schedule time on AWS-run antennas and receive the data inside AWS, where processing can start within seconds of reception over AWS's fiber network.[^s1] Customers who need capacity of their own can also use Ground Station Dedicated Antennas, custom-built antennas that AWS manages for them.[^s1] Typical uses are downlinking mission data, uplinking commands, telemetry, tracking and command (TT&C), and running a software-defined radio or front-end processor on EC2.[^s1]

## How it works

A satellite must first be **onboarded** to one or more antenna locations, a multistep process of data collection, technical validation, spectrum licensing, and integration and testing.[^s2] Scheduling a **contact** (a reservation of antenna time at a location) then needs a valid **ephemeris** so the antenna can point at the satellite, and a valid **mission profile**.[^s2] A mission profile is a reusable set of **configs**: a tracking config plus one or more **dataflows**, each pairing a source and a destination, such as an antenna downlink delivered to a **dataflow endpoint** on a customer-managed EC2 instance or recorded to an S3 bucket.[^s2]

Data can arrive synchronously on EC2 as VITA-49 digital IF (DigIF) or as demodulated and decoded bits, or asynchronously in S3 as PCAP files; uplink sends DigIF from EC2 to the antenna, and cross-Region delivery lets one Region receive several simultaneous contacts.[^s1] The FAQ lists S-band transmit at 2025–2120 MHz, S-band receive at 2200–2300 MHz and X-band receive at 7750–8400 MHz, for LEO, NGSO and MEO satellites.[^s4] Wideband DigIF centralizes the SDR in a VPC with up to five channels and 400 MHz in total per polarity.[^s4]

Scheduling is asynchronous: a request sits in `SCHEDULING` and usually resolves within a minute (at most five) to `SCHEDULED` or `FAILED_TO_SCHEDULE`.[^s2] During a contact AWS orchestrates its antennas while you orchestrate your EC2 endpoints, typically by reacting to Amazon EventBridge events; telemetry goes to Amazon CloudWatch, and a finished contact ends as `COMPLETED`, `AWS_FAILED` or `FAILED` (a customer-side error).[^s2] A **digital twin** mirrors production stations' capabilities, site masks and coordinates so you can test scheduling and integration at lower cost.[^s2]

## Locations

As of 2026-11, AWS lists eleven ground stations, each mapped to a Region: Alaska 1, Hawaii 1 and Oregon 1 (us-west-2), Ohio 1 (us-east-2), Ireland 1 (eu-west-1), Stockholm 1 (eu-north-1), Bahrain 1 (me-south-1), Cape Town 1 (af-south-1), Singapore 1 (ap-southeast-1), Dubbo 1 (ap-southeast-2) and Punta Arenas 1 (sa-east-1).[^s3] Alaska, Hawaii, Dubbo and Punta Arenas are not physically inside their Region, and support for antennas in Seoul ended on June 16, 2026.[^s3] You can configure contacts and receive data from any supported Region, and digital-twin stations exist in all of them, named with a "Digital Twin" prefix.[^s3] The General Reference lists API endpoints in ten Regions, including us-east-1 and eu-central-1, which have no antenna of their own, plus FIPS endpoints in the three US Regions.[^s6]

## Quotas, pricing and gotchas

- **Billing:** you pay per minute of antenna time, either On-Demand or Reserved; Reserved gives a discounted rate and better scheduling in exchange for reserving monthly usage for 12 months.[^s5] Passes are priced as narrowband below 40 MHz of instantaneous bandwidth and wideband at 40 MHz or more.[^s5] Check the pricing page for current per-minute rates by location.[^s5]
- **Scheduling windows:** On-Demand contacts can be booked 15 minutes to 7 days ahead and cancelled up to 15 minutes before start, with a cancellation fee equal to the contact price; Reserved cancellation terms depend on the reservation.[^s4]
- **Licensing is on you first:** the customer handles ITU and national (for example FCC) filings and the satellite license, after which AWS files for the ground-station license; the FAQ's non-binding estimates range from 45 days in Ireland to 9+ months in Australia.[^s4]
- **Default quotas (per supported Region, as of 2026-11, all adjustable):** 100 configs, 100 mission profiles, 100 dataflow endpoint groups with 20 endpoints each, 100 scheduled contacts, 1,000 scheduled minutes, a 20-minute maximum contact and a 7-day maximum lead time.[^s6]

## When to use it (and when not)

Ground Station suits operators of LEO or MEO constellations who want pay-per-minute access to a worldwide antenna network and to process imagery or telemetry in AWS directly.[^s4] It is less suitable when a mission needs bands outside the listed S- and X-band ranges, or cannot wait for the per-location licensing lead times.[^s4] Operators that need more predictable access can choose Reserved minutes or Dedicated Antennas.[^s1][^s5]

## Connections

Ground Station is part of the **AWS global infrastructure**: every antenna is **scoped to an AWS Region**, and data crosses the **AWS global network** for cross-Region delivery.[^s1][^s3] It delivers into **Amazon EC2** endpoints inside an **Amazon VPC**, or into **Amazon S3**; it **integrates with Amazon EventBridge** for contact automation and is **monitored by Amazon CloudWatch**.[^s1][^s2] Wideband deliveries are **secured by AWS KMS**, with customer-controlled keys and server-side encryption for S3.[^s4] Its limits form the **Ground Station quotas** node.[^s6]

## Sources

[^s1]: Amazon Web Services, "What is AWS Ground Station?". https://docs.aws.amazon.com/ground-station/latest/ug/what-is.html (accessed 2026-11-21)
[^s2]: Amazon Web Services, "How AWS Ground Station works". https://docs.aws.amazon.com/ground-station/latest/ug/how-it-works.html (accessed 2026-11-21)
[^s3]: Amazon Web Services, "AWS Ground Station Locations". https://docs.aws.amazon.com/ground-station/latest/ug/aws-ground-station-antenna-locations.html (accessed 2026-11-21)
[^s4]: Amazon Web Services, "AWS Ground Station FAQs". https://aws.amazon.com/ground-station/faqs/ (accessed 2026-11-21)
[^s5]: Amazon Web Services, "AWS Ground Station Pricing". https://aws.amazon.com/ground-station/pricing/ (accessed 2026-11-21)
[^s6]: Amazon Web Services, "AWS Ground Station endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/gs.html (accessed 2026-11-21)
