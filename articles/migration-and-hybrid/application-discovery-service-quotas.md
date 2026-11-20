---
id: kt:application-discovery-service-quotas
type: Limit
title: AWS Application Discovery Service quotas
aliases: [Application Discovery Service quotas, ADS quotas, AWS Application Discovery Service limits, ADS service limits]
domain: migration-and-hybrid
summary: >-
  The per-account, per-Region limits on AWS Application Discovery Service: caps on applications,
  servers per application, imported server records and daily import deletions, import file size,
  active and inactive agents, and tags per server. Only the imported-servers quota is adjustable.
verified: 2026-11-20
status: current
broader: [kt:aws-application-discovery-service]
related: [kt:application-portfolio-assessment, kt:migration-wave-planning, kt:aws-migration-hub]
relations:
  - {predicate: configuredVia, object: kt:service-quotas}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: contrastsWith, object: kt:aws-transform}
sources:
  - id: s1
    title: "AWS Application Discovery Service Quotas - AWS Application Discovery Service"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/application-discovery/latest/userguide/ads_service_limits.html
    type: official-docs
    accessed: 2026-11-20
  - id: s2
    title: "AWS Application Discovery Service endpoints and quotas - AWS General Reference"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/appdiscserv.html
    type: official-docs
    accessed: 2026-11-20
  - id: s3
    title: "AWS Application Discovery Service availability change"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/application-discovery/latest/userguide/application-discovery-service-availability-change.html
    type: official-docs
    accessed: 2026-11-20
created: 2026-11-20
---

# AWS Application Discovery Service quotas

AWS Application Discovery Service (ADS) collects server inventory, utilization and dependency data for migration planning, and it enforces a small, mostly fixed set of quotas on how much of that data an account can hold.[^s1] The General Reference states each default as applying in "each supported Region", so the limits are per account and per Region rather than global.[^s2] Of the nine limits AWS documents, only one, imported servers per account, can be raised; the rest are hard ceilings that shape how you structure a discovery project.[^s1][^s2]

**Lifecycle context (as of 2026-11):** ADS closed to new customers on 7 November 2025. Existing customers can keep using it, and AWS recommends AWS Transform for new discovery and assessment work.[^s3] These quotas therefore matter mainly to accounts that already run ADS projects.

## The quotas (as of 2026-11)

| Quota | Default[^s1][^s2] | Adjustable[^s2] |
|---|---|---|
| Applications per account | 1,000 | No |
| Servers per application | 400 | No |
| Imported server records per account | 25,000 | No |
| Imported servers per account | 10,000 | Yes |
| Deletions of import records per day | 25,000 | No |
| Maximum import file size | 10 MB | Not listed as adjustable |
| Active agents (collecting and sending data) | 1,000 | No |
| Inactive agents (heartbeating but not collecting) | 10,000 | No |
| Tags per server | 30 | No |

Values and adjustability come from the ADS user guide and the General Reference quota table.[^s1][^s2] The 10 MB import file limit appears only in the user guide, not in the General Reference table.[^s1][^s2] The daily deletion counter resets at 00:00 UTC.[^s2]

The user guide says the imported-servers quota is "currently" the only one that can be increased, and points to the Service Quotas console for viewing quotas and requesting that increase.[^s1] The General Reference links the same adjustable quota to Service Quotas.[^s2]

## Where ADS runs

The General Reference lists seven ADS endpoints: US East (N. Virginia), US West (Oregon), Asia Pacific (Sydney), Asia Pacific (Tokyo), Europe (Frankfurt), Europe (Ireland) and Europe (London).[^s2] Because quotas are counted per supported Region, the Region you collect data into is the one whose counters you consume.[^s2]

## Gotchas and design implications

- **Applications are a scarce container.** With 1,000 applications and 400 servers each, large estates need deliberate grouping. When you hit the application cap, the fix is deleting applications, either in the Migration Hub console or with the `DeleteApplications` API action, not requesting more.[^s1][^s2]
- **Imported records and imported servers differ.** The account can hold 25,000 imported server records (fixed) but only 10,000 imported servers by default (adjustable). Large spreadsheet imports should be checked against both before loading.[^s1][^s2]
- **Clean-up is rate-limited.** Deleting a bad import is capped at 25,000 record deletions per day, so a botched bulk import of the full record allowance can take more than one UTC day to undo.[^s2]
- **Agent fleets have a hard ceiling.** Only 1,000 agents can be actively sending data at once, while up to 10,000 can sit idle but responsive. To profile more servers than that, rotate agents between collecting and stopped states or rely on the agentless collector and imports for the rest.[^s1][^s2]
- **Split large import files.** Each import file is limited to 10 MB, so very large CMDB exports need to be broken into several files.[^s1]

## When the limits push you elsewhere

If your portfolio outgrows these fixed caps, or your account never signed up before the November 2025 cutoff, AWS Transform is the documented path; AWS says moving there needs no formal migration process, and in-flight ADS projects can run to completion.[^s3]

## Connections

These quotas sit **under** AWS Application Discovery Service, whose data they bound.[^s1] The one adjustable quota is **configured via** Service Quotas.[^s1][^s2] Defaults are **scoped to** each supported AWS Region.[^s2] They shape application portfolio assessment and migration wave planning, because the application and servers-per-application caps limit how an estate can be grouped.[^s1] Migration Hub is where applications are managed and deleted when the cap is reached.[^s2] They **contrast with** AWS Transform, the successor AWS recommends for new discovery work.[^s3]

## Sources

[^s1]: Amazon Web Services, "AWS Application Discovery Service Quotas". https://docs.aws.amazon.com/application-discovery/latest/userguide/ads_service_limits.html (accessed 2026-11-20)
[^s2]: Amazon Web Services, "AWS Application Discovery Service endpoints and quotas", AWS General Reference. https://docs.aws.amazon.com/general/latest/gr/appdiscserv.html (accessed 2026-11-20)
[^s3]: Amazon Web Services, "AWS Application Discovery Service availability change". https://docs.aws.amazon.com/application-discovery/latest/userguide/application-discovery-service-availability-change.html (accessed 2026-11-20)
