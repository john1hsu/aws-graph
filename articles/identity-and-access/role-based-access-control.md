---
id: kt:role-based-access-control
type: Pattern
title: Role-based access control (RBAC) on AWS
aliases: [RBAC, role-based access control, role based access control, job-function access control]
domain: identity-and-access
summary: >-
  An authorization model in which permissions are bundled into roles that mirror job functions and
  people receive access only through the roles they are assigned; on AWS it is built from IAM
  roles and groups, job-function managed policies and IAM Identity Center permission sets.
verified: 2026-11-19
status: concept
broader: [kt:aws-iam]
related: [kt:iam-roles, kt:iam-identity-center, kt:amazon-verified-permissions, kt:iam-access-analyzer]
relations:
  - {predicate: contrastsWith, object: kt:attribute-based-access-control}
  - {predicate: governedBy, object: kt:iam-quotas}
sources:
  - id: s1
    title: "Role Based Access Control - FAQs"
    publisher: National Institute of Standards and Technology
    url: https://csrc.nist.gov/projects/role-based-access-control/faqs
    type: standards-body
    accessed: 2026-11-19
  - id: s2
    title: "AWS managed policies for job functions - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html
    type: official-docs
    accessed: 2026-11-19
  - id: s3
    title: "Manage AWS accounts with permission sets - AWS IAM Identity Center"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/singlesignon/latest/userguide/permissionsetsconcept.html
    type: official-docs
    accessed: 2026-11-19
  - id: s4
    title: "Types of access control - AWS Prescriptive Guidance"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/saas-multitenant-api-access-authorization/access-control-types.html
    type: official-docs
    accessed: 2026-11-19
  - id: s5
    title: "Amazon Verified Permissions and Cedar policy language terms and concepts"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/verifiedpermissions/latest/userguide/terminology.html
    type: official-docs
    accessed: 2026-11-19
  - id: s6
    title: "IAM and AWS STS quotas - AWS Identity and Access Management"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.html
    type: official-docs
    accessed: 2026-11-19
created: 2026-11-19
---

# Role-based access control (RBAC) on AWS

Role-based access control routes every grant of access through a role: a role is a named collection of permissions, and a user holds permissions only because a role was assigned to them directly or inherited through a role hierarchy.[^s1] The model was formalized by Ferraiolo and Kuhn at NIST in 1992 as an alternative to discretionary and mandatory access control, and it became the ANSI/INCITS 359 standard in 2004 (revised in 2012).[^s1] Its appeal is administrative: roles stay fairly stable while people and permissions churn, so reviewing who can do what becomes a question of role membership.[^s1]

## The standard model

INCITS 359 defines a mandatory **core** (user-to-role and permission-to-role assignments, plus activating roles within a session) and optional components: **role hierarchies**, where senior roles inherit junior roles' permissions, **static separation of duty**, which forbids assigning conflicting roles to one user, and **dynamic separation of duty**, which forbids activating conflicting roles in the same session.[^s1]

## How RBAC is built on AWS

**Job-function managed policies.** AWS publishes managed policies named after common job functions, such as `AdministratorAccess`, `Billing`, `DatabaseAdministrator`, `NetworkAdministrator`, `PowerUserAccess`, `SecurityAudit`, `SystemAdministrator`, `ReadOnlyAccess` and `ViewOnlyAccess`, each bundling permissions across many services.[^s2] AWS maintains and updates them as services launch; you cannot edit them, and a copy you customize stops receiving those updates.[^s2] Several of them allow `iam:PassRole` only for roles whose names follow a set pattern, so the service roles they expect must be created with matching names.[^s2]

**Permission sets in IAM Identity Center.** For multi-account workforces, a permission set is the AWS equivalent of an RBAC role definition: a template of AWS managed, customer managed, inline or job-function policies (optionally with a permissions boundary) that you assign to users or groups across chosen accounts in an organization.[^s3] On assignment, Identity Center creates and manages a corresponding IAM role in each account and keeps it in sync when the permission set changes; users pick among their assigned roles in the AWS access portal or CLI.[^s3] A permission set's session duration defaults to 1 hour and can be set up to 12 hours.[^s3]

**Applications.** For authorization inside your own software, Amazon Verified Permissions describes RBAC as defining roles with permission sets and assigning them to identities, and says its Cedar policy language supports RBAC through principal groups; one Cedar policy can also combine group membership with attribute conditions.[^s5]

## Quotas and gotchas

- **IAM quotas (as of 2026-11; IAM is global):** 300 groups per account (adjustable to 500), 1,000 roles per account (adjustable to 10,000), 20 managed policies per role (adjustable to 25), 10 managed policies per user (adjustable to 20) and 10 per group (not adjustable); a customer managed policy may hold 6,144 characters, which cannot be raised.[^s6] Increases are requested through Service Quotas in US East (N. Virginia).[^s6]
- **Permission sets inherit IAM limits.** Because each assignment materializes as an IAM role in the target account, the per-role managed-policy quota above applies to every permission set.[^s3][^s6]
- **Job-function policies are not least privilege.** AWS says so explicitly; it suggests using IAM Access Analyzer to find the permissions principals actually use and generating a narrower custom policy.[^s2][^s3]
- **ReadOnly vs ViewOnly.** `ReadOnlyAccess` can read data such as S3 objects and DynamoDB items; `ViewOnlyAccess` sees only resource lists and metadata, so it is the safer choice for broad visibility.[^s2]
- **Role sprawl.** AWS Prescriptive Guidance notes RBAC struggles when users hold several roles, when business rules are too intricate to express as roles, when authorization depends on dynamic parameters, and when growth demands constant remapping of permissions to roles.[^s4]

## When to use it (and when not)

Prescriptive Guidance rates RBAC as relatively simple to implement because roles match recognizable business functions, while ABAC is more flexible but costs more upfront to design.[^s4] RBAC fits stable organizations where a handful of job functions (admin, developer, auditor, billing) cover most needs, especially when accounts already separate teams. Where per-project or per-tenant scoping would multiply roles, AWS suggests a hybrid: keep roles for coarse administration and add attribute conditions for granularity.[^s4][^s5]

## Connections

RBAC on AWS is expressed with **AWS IAM** policies attached to **IAM roles** (and groups), and is limited by the **IAM quotas** on roles and attached policies.[^s6] **IAM Identity Center** permission sets are the standard way to assign job-function roles across many accounts, and **IAM Access Analyzer** helps tighten those roles toward least privilege.[^s3][^s2] **Amazon Verified Permissions** brings the same model to application authorization.[^s5] It contrasts with **attribute-based access control**, which keys decisions on tags and other attributes rather than role membership, and the two are often combined.[^s4]

## Sources
[^s1]: NIST, "Role Based Access Control - FAQs". https://csrc.nist.gov/projects/role-based-access-control/faqs (accessed 2026-11-19)
[^s2]: Amazon Web Services, "AWS managed policies for job functions". https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html (accessed 2026-11-19)
[^s3]: Amazon Web Services, "Manage AWS accounts with permission sets". https://docs.aws.amazon.com/singlesignon/latest/userguide/permissionsetsconcept.html (accessed 2026-11-19)
[^s4]: Amazon Web Services, "Types of access control - AWS Prescriptive Guidance". https://docs.aws.amazon.com/prescriptive-guidance/latest/saas-multitenant-api-access-authorization/access-control-types.html (accessed 2026-11-19)
[^s5]: Amazon Web Services, "Amazon Verified Permissions and Cedar policy language terms and concepts". https://docs.aws.amazon.com/verifiedpermissions/latest/userguide/terminology.html (accessed 2026-11-19)
[^s6]: Amazon Web Services, "IAM and AWS STS quotas". https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.html (accessed 2026-11-19)
