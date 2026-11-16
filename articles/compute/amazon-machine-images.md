---
id: kt:amazon-machine-images
type: Resource
title: Amazon Machine Image (AMI)
aliases: [AMI, AMIs, Amazon Machine Images, EC2 AMI]
domain: compute
summary: >-
  The Regional, versionable template that Amazon EC2 launches every instance from: an image of the root
  volume (normally backed by EBS snapshots) plus launch permissions and metadata such as architecture and
  virtualization type, with its own lifecycle of create, copy, share, deprecate, disable and deregister.
verified: 2026-11-16
status: current
broader: [kt:amazon-ec2]
related: [kt:ec2-instance-types, kt:amazon-ec2-auto-scaling, kt:ec2-image-builder]
relations:
  - {predicate: dependsOn, object: kt:amazon-ebs}
  - {predicate: securedBy, object: kt:aws-kms}
  - {predicate: scopedTo, object: kt:aws-regions}
  - {predicate: configuredVia, object: kt:ec2-image-builder}
  - {predicate: governedBy, object: kt:ami-quotas}
sources:
  - id: s1
    title: "AMI types and characteristics in Amazon EC2"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide//ComponentsAMIs.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Amazon EC2 AMI lifecycle"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/en_eu/AWSEC2/latest/UserGuide/ami-lifecycle.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "Deprecate an Amazon EC2 AMI"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ami-deprecate.html
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Make your AMI publicly available for use in Amazon EC2"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/sharingamis-intro.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "AMI quotas in Amazon EC2"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ami-quotas.html
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "How Amazon EC2 AMI copy works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/how-ami-copy-works.html
    type: official-docs
    accessed: 2026-11-16
  - id: s7
    title: "What is Image Builder? - EC2 Image Builder"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html
    type: official-docs
    accessed: 2026-11-16
  - id: s8
    title: "AMI Block Public Access now enabled for all new accounts and existing accounts with no public AMIs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2023/10/ami-block-public-enabled-aws-accounts-no-public-amis
    type: official-blog
    accessed: 2026-11-16
  - id: s9
    title: "Amazon EC2 now supports setting AMIs to a disabled state"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2023/10/amazon-ec2-amis-disabled-state
    type: official-blog
    accessed: 2026-11-16
created: 2026-11-16
---

# Amazon Machine Image (AMI)

An Amazon Machine Image (AMI) is the template every Amazon EC2 instance is launched from. Choosing one fixes the operating system, processor architecture, root volume type and virtualization type of the instance, and the AMI must be compatible with the instance type you pick.[^s1] The usual workflow is to launch from an existing AMI, customize the instance (install software, harden the OS), and capture the result as a new AMI so that every later instance starts with those changes baked in.[^s2]

## How it works

**Root volume type.** An AMI is either backed by Amazon EBS, where the root volume is created from an EBS snapshot, or backed by Amazon S3, where the root volume is an instance store volume built from a template in S3.[^s1] EBS-backed AMIs usually boot in under a minute, can be stopped and restarted, let you change instance type while stopped, and are created with a single API call; S3-backed instances usually take under five minutes to boot, cannot be stopped, and lose instance-store data when the instance ends.[^s1] AWS describes S3-backed AMIs as end of life, supported only on a short list of older families (C1, C3, D2, I2, M1, M2, M3, R3, X1), and from 2 November 2026 an account that does not already own an S3-backed AMI in a Region can no longer create one there.[^s1] By default an EBS root volume has `DeleteOnTermination` set to true, so the root disk goes away with the instance unless you change it.[^s1]

**Virtualization.** Current-generation instance types all use hardware virtual machine (HVM) AMIs, which support enhanced networking and GPUs; paravirtual (PV) AMIs run only on a handful of previous-generation types.[^s1]

**Launch permissions.** Only the owner sets who may launch an AMI: the owner implicitly, specific accounts, organizations or organizational units explicitly, or every AWS account if the AMI is made public.[^s1] Recipients of a shared AMI can launch it but cannot modify, delete or reshare it, although they can capture a new AMI from an instance they launched.[^s4]

**Regional scope.** An AMI can only launch instances in the Region where it lives; to use it elsewhere you copy it.[^s2] A copy is a distinct AMI with its own ID, unaffected by later changes to the source, so updates must be recopied.[^s6] Cross-Region copies and copies that change encryption create full rather than incremental snapshot copies, and a destination Region allows up to 300 concurrent AMI copy operations.[^s6] Copying can encrypt an unencrypted AMI with an AWS KMS key, but an encrypted AMI can never be copied to an unencrypted one; copying a shared encrypted AMI across accounts requires the owner to share the snapshots and the KMS key.[^s6]

## Lifecycle

AMIs move through create, copy, deprecate, disable and deregister stages.[^s2] **Deprecation** hides an AMI from listings and the console catalog but still lets anyone who knows its ID launch it, including launch templates and Auto Scaling groups.[^s3] Public AMIs are deprecated two years after creation by default; private AMIs can be given a deprecation date up to 10 years ahead.[^s3] **Disabling**, added in October 2023, blocks all new launches, makes a previously shared AMI private, and can be reversed with an API call, which suits teams whose compliance rules forbid deleting images.[^s9] **Deregistering** deletes the AMI unless a Recycle Bin retention rule matches, in which case it can be restored until the retention period expires; instances already running are never affected.[^s2]

## Quotas, costs and gotchas

As of 2026-11 the default per-Region quotas are 50,000 AMIs (including disabled ones and those in the Recycle Bin), 5 public AMIs, and 1,000 sharing entities per AMI, where an organization or OU counts as one entity; all can be raised through Service Quotas.[^s5] AMIs themselves have no separate fee: an EBS-backed AMI is billed as the EBS snapshot storage behind it, and deprecating an AMI does not stop those charges, only deregistration and snapshot deletion do.[^s1][^s3] A cross-Region copy doubles the stored snapshots, and you pay for both.[^s6]

Public sharing is guarded. Block public access for AMIs has been on by default since October 2023 for new accounts and for existing accounts that had not owned a public AMI since July 2023, in all Regions including GovCloud (US) and China.[^s8] It must be turned off per Region before an AMI can be made public, and AMIs with encrypted volumes or product codes cannot be made public at all.[^s4] Review an image for sensitive data before publishing it.[^s4]

## When to use it (and when not)

Use custom "golden" AMIs when many instances must start with the same pre-installed software and settings, since every instance launched from the image inherits its customizations.[^s2] Hand-built images drift, so AWS offers EC2 Image Builder to build, test and distribute AMIs on a schedule across Regions and accounts, at no charge beyond the underlying resources, and Amazon Data Lifecycle Manager to automate creation, retention, deprecation and deregistration of EBS-backed AMIs.[^s7][^s2]

## Connections

An AMI is a resource **within** Amazon EC2 and **depends on** Amazon EBS snapshots for its root volume.[^s1] It is **scoped to** a single AWS Region and must be copied to reach others.[^s2] Encrypted AMIs are **secured by** AWS KMS keys.[^s6] Images are commonly **configured via** EC2 Image Builder pipelines.[^s7] Usage is **governed by** the AMI quotas above.[^s5] AMIs must match the chosen EC2 instance type, and Auto Scaling groups reference them through launch templates.[^s1][^s3]

## Sources
[^s1]: Amazon Web Services, "AMI types and characteristics in Amazon EC2". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide//ComponentsAMIs.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Amazon EC2 AMI lifecycle". https://docs.aws.amazon.com/en_eu/AWSEC2/latest/UserGuide/ami-lifecycle.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "Deprecate an Amazon EC2 AMI". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ami-deprecate.html (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Make your AMI publicly available for use in Amazon EC2". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/sharingamis-intro.html (accessed 2026-11-16)
[^s5]: Amazon Web Services, "AMI quotas in Amazon EC2". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ami-quotas.html (accessed 2026-11-16)
[^s6]: Amazon Web Services, "How Amazon EC2 AMI copy works". https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/how-ami-copy-works.html (accessed 2026-11-16)
[^s7]: Amazon Web Services, "What is Image Builder?", EC2 Image Builder User Guide. https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html (accessed 2026-11-16)
[^s8]: Amazon Web Services, "AMI Block Public Access now enabled for all new accounts and existing accounts with no public AMIs" (What's New, 2023-10). https://aws.amazon.com/about-aws/whats-new/2023/10/ami-block-public-enabled-aws-accounts-no-public-amis (accessed 2026-11-16)
[^s9]: Amazon Web Services, "Amazon EC2 now supports setting AMIs to a disabled state" (What's New, 2023-10). https://aws.amazon.com/about-aws/whats-new/2023/10/amazon-ec2-amis-disabled-state (accessed 2026-11-16)
