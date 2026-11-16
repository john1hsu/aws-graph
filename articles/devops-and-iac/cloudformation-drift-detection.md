---
id: kt:cloudformation-drift-detection
type: Feature
title: CloudFormation drift detection
aliases: [CloudFormation stack drift, Stack drift detection, CFN drift detection]
domain: devops-and-iac
summary: >-
  The AWS CloudFormation capability that compares a stack's live resource configuration with the
  configuration its template and parameters expect, flagging resources changed or deleted outside
  CloudFormation; drift-aware change sets and resource import are the main ways to resolve it.
verified: 2026-11-16
status: current
broader: [kt:aws-cloudformation, kt:infrastructure-as-code]
related: [kt:aws-cdk, kt:immutable-infrastructure]
relations:
  - {predicate: partOf, object: kt:aws-cloudformation}
  - {predicate: monitoredBy, object: kt:aws-config}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: integratesWith, object: kt:cloudformation-stacksets}
sources:
  - id: s1
    title: "Detect unmanaged configuration changes to stacks and resources with drift detection"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-drift.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Performing drift detection on CloudFormation StackSets"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide//stacksets-drift.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "cloudformation-stack-drift-detection-check (AWS Config managed rule)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/config/latest/developerguide/cloudformation-stack-drift-detection-check.md
    type: official-docs
    accessed: 2026-11-16
  - id: s4
    title: "Resolve drift with an import operation"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resource-import-resolve-drift.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "Using drift-aware change sets"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/us_en/AWSCloudFormation/latest/UserGuide/drift-aware-change-sets.html
    type: official-docs
    accessed: 2026-11-16
  - id: s6
    title: "Safely handle configuration drift with CloudFormation drift-aware change sets"
    publisher: AWS DevOps & Developer Productivity Blog
    url: https://aws.amazon.com/blogs/devops/safely-handle-configuration-drift-with-cloudformation-drift-aware-change-sets
    type: official-blog
    accessed: 2026-11-16
created: 2026-11-16
---

# CloudFormation drift detection

**Drift** is the gap between what a CloudFormation stack's template says a resource should look like and what the resource actually looks like after someone edits it directly, for example in the EC2 console. Drift detection finds those unmanaged changes, which otherwise make later stack updates and deletions unpredictable. CloudFormation works out the expected values from the template plus any parameter values, reads the current properties of each resource, and compares them; it also checks stack-level tags. A resource counts as drifted if any property differs or if the property or the whole resource was deleted, and a stack counts as drifted if any of its resources has.[^s1]

## How it works

You run detection on demand against a whole stack or a single resource, from the console, CLI or API. The stack must be in `CREATE_COMPLETE`, `UPDATE_COMPLETE`, `UPDATE_ROLLBACK_COMPLETE` or `UPDATE_ROLLBACK_FAILED`.[^s1] Results come at three levels:[^s1]

- **Operation:** `DETECTION_IN_PROGRESS`, `DETECTION_COMPLETE`, or `DETECTION_FAILED` (partial results remain available).[^s1]
- **Stack:** `DRIFTED`, `IN_SYNC` or `NOT_CHECKED`; a stack with no resources that support detection reports `IN_SYNC`.[^s1]
- **Resource:** `MODIFIED`, `DELETED`, `IN_SYNC` or `NOT_CHECKED`, with each property difference typed as `ADD`, `REMOVE` or `NOT_EQUAL`.[^s1]

Resource types that don't support drift detection come back as `NOT_CHECKED`; private registry types are covered only when they are provisionable.[^s1] For **StackSets**, detection runs across every stack instance, takes overridden parameters into account, and rolls up so that a stack set with any drifted instance is itself drifted. Only one drift operation can run on a given stack set at a time, and large sets can take a long while to finish.[^s2]

## Gotchas

- **Only explicit properties are compared.** Defaults are ignored, so to watch a property you must set it in the template, even to its default value.[^s1]
- **Nested stacks are not included** in stack-level detection; run it on each nested stack separately.[^s1]
- **Blind spots and false positives.** `KMSKeyId` is never checked, values the service does not return (such as an IAM login profile password) or cannot map back to the template (such as Lambda source code) are skipped, and equivalent values written differently, like `1024 MB` versus `1GB`, show up as drift. Attachment resources split across stacks, such as security-group ingress rules or IAM policies defined in another stack, can also give inaccurate results.[^s1]
- **Permissions.** The caller needs `cloudformation:DetectStackDrift`, `cloudformation:DetectStackResourceDrift` and `cloudformation:BatchDescribeTypeConfigurations`, plus read access to every checked resource (for example `ec2:DescribeInstances`).[^s1]

## Resolving drift

Detection only reports; fixing is a separate step. The simplest fix is to change the resource back by hand or change the template to match and update the stack.[^s1] When the out-of-band change should be kept but updating the template would force a replacement, the **import** route avoids downtime: set `DeletionPolicy: Retain`, remove the resource from the stack, import it back with a template describing its live state, then update the stack.[^s4]

Since November 2025, **drift-aware change sets** (console option, or `--deployment-mode REVERT_DRIFT` on CreateChangeSet) compare three states (the live configuration, the last deployed template and the new template) so you can preview which out-of-band changes a deployment would overwrite and then revert them.[^s5][^s6] On failure they roll back to the actual pre-deployment state. Write-only values such as passwords use previous-deployment values, tag keys absent from the template are left alone, immutable-property drift is not fixed by replacement, and some types (for example `AWS::MSK::Cluster`) fall back to plain template comparison.[^s5] AWS's launch post shows them catching a Lambda memory increase made during an incident, removing a debug SSH rule, and recreating a manually deleted S3 bucket.[^s6]

## When to use it

Run drift detection before any significant update, after incident response where console changes are likely, and continuously for compliance. For continuous checking, the AWS Config managed rule `cloudformation-stack-drift-detection-check` calls DetectStackDrift on each stack and marks drifted stacks `NON_COMPLIANT`. Its run is capped at 15 minutes, so AWS suggests scoping several rule instances by tag in accounts with many stacks; as of 2026-11 the rule is not offered in a few Regions, including Europe (Spain) and Israel (Tel Aviv).[^s3] Because drift usually starts with console edits made under pressure, such as during an outage, it pays to route even urgent fixes back through the template.[^s6]

## Connections

Drift detection is **part of AWS CloudFormation**, a core control for **infrastructure as code**, and so also applies to **AWS CDK** apps, which deploy through CloudFormation stacks.[^s1] It runs across **CloudFormation StackSets** for multi-account fleets,[^s2] can be **monitored by AWS Config** through a managed rule,[^s3] and is **secured by AWS IAM** permissions on both CloudFormation and each resource's read APIs.[^s1] Drift-aware change sets and resource import are the remediation paths, and **immutable infrastructure** is the practice that keeps drift from happening at all.[^s4][^s5][^s6]

## Sources
[^s1]: Amazon Web Services, "Detect unmanaged configuration changes to stacks and resources with drift detection", AWS CloudFormation User Guide. https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-drift.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Performing drift detection on CloudFormation StackSets", AWS CloudFormation User Guide. https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide//stacksets-drift.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "cloudformation-stack-drift-detection-check", AWS Config Developer Guide. https://docs.aws.amazon.com/config/latest/developerguide/cloudformation-stack-drift-detection-check.md (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Resolve drift with an import operation", AWS CloudFormation User Guide. https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resource-import-resolve-drift.html (accessed 2026-11-16)
[^s5]: Amazon Web Services, "Using drift-aware change sets", AWS CloudFormation User Guide. https://docs.aws.amazon.com/us_en/AWSCloudFormation/latest/UserGuide/drift-aware-change-sets.html (accessed 2026-11-16)
[^s6]: JJ Lei and Aakash Deshpande, "Safely handle configuration drift with CloudFormation drift-aware change sets", AWS DevOps & Developer Productivity Blog, 2025-11-19. https://aws.amazon.com/blogs/devops/safely-handle-configuration-drift-with-cloudformation-drift-aware-change-sets (accessed 2026-11-16)
