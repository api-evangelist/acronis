# Acronis (acronis)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Acronis is a leading provider of cyber protection solutions delivering innovative technology to protect data, applications, and systems. They offer backup and disaster recovery, file sync and share, anti-malware, and endpoint detection and response through the Acronis Cyber Protect Cloud platform. The Acronis developer API enables MSPs, IT administrators, and security teams to automate tenant management, agent monitoring, backup task tracking, and usage reporting.

**URL:** [Visit APIs.json](https://raw.githubusercontent.com/api-evangelist/acronis/refs/heads/main/apis.yml)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags:

 - Cybersecurity, Data Protection, Endpoint Management

## Timestamps

- **Created:** 2025-02-17
- **Modified:** 2026-04-19

## APIs

### Acronis Account Management API
Manages tenant hierarchy, users, OAuth clients, applications, licensing (offering items), usage reporting, and infrastructure within Acronis Cyber Protect Cloud.

**Human URL:** [https://developer.acronis.com/doc/account-management/v2/reference/index.html](https://developer.acronis.com/doc/account-management/v2/reference/index.html)

#### Tags:

 - Account Management, Acronis, Tenants, Users

#### Properties

- [Documentation](https://developer.acronis.com/doc/outbound/apis/api-library/account/index.html)
- [OpenAPI](openapi/acronis-account-management-openapi.yaml)
- [NaftikoCapability](capabilities/shared/acronis-account-management.yaml)

---

### Acronis Agent Management REST API
Provides endpoints for managing Acronis backup agents registered across the platform, update settings, and hardware node management.

**Human URL:** [https://developer.acronis.com/doc/agents/v2/reference/index.html](https://developer.acronis.com/doc/agents/v2/reference/index.html)

#### Tags:

 - Acronis, Agent Management, Backup, Endpoints

#### Properties

- [Documentation](https://developer.acronis.com/doc/agents/v2/reference/index.html)
- [OpenAPI](openapi/acronis-agent-management-openapi.yaml)

---

### Acronis Resource and Policy Management API
Enables organizations to manage protection policies and resources within their IT infrastructure.

**Human URL:** [https://developer.acronis.com/doc/resource-policy-management/v4/reference/index.html](https://developer.acronis.com/doc/resource-policy-management/v4/reference/index.html)

#### Tags:

 - Acronis, Policy Management, Resources

#### Properties

- [Documentation](https://developer.acronis.com/doc/resource-policy-management/v4/guide/index.html)

---

### Acronis Task Manager API
Monitors backup and protection tasks and activities with filtering, state tracking, and pagination.

**Human URL:** [https://developer.acronis.com/doc/tasks/v2/reference/index.html](https://developer.acronis.com/doc/tasks/v2/reference/index.html)

#### Tags:

 - Acronis, Backup, Monitoring, Tasks

#### Properties

- [Documentation](https://developer.acronis.com/doc/tasks/v2/guide/index.html)
- [OpenAPI](openapi/acronis-task-manager-openapi.yaml)

---

### Acronis Advanced Automation API
Automates and streamlines backup and recovery processes with custom scripts and workflows for MSPs.

**Human URL:** [https://developer.acronis.com/doc/advanced-automation/v1/reference/index.html](https://developer.acronis.com/doc/advanced-automation/v1/reference/index.html)

#### Tags:

 - Acronis, Advanced Automation, PSA

#### Properties

- [Documentation](https://developer.acronis.com/doc/outbound/apis/api-library/advanced-automation/index.html)

---

### Acronis Event Manager API
Monitors and manages events across the Acronis ecosystem with real-time event data access.

**Human URL:** [https://developer.acronis.com/doc/events/v1/reference/index.html](https://developer.acronis.com/doc/events/v1/reference/index.html)

#### Tags:

 - Acronis, Events, Monitoring

#### Properties

- [Documentation](https://developer.acronis.com/doc/vendor-side/apis/api-library/events/index.html)

---

### Acronis Disaster Recovery Service API
Automates and streamlines disaster recovery processes for business continuity.

**Human URL:** [https://developer.acronis.com/doc/disaster-recovery/v2/reference/index.html](https://developer.acronis.com/doc/disaster-recovery/v2/reference/index.html)

#### Tags:

 - Acronis, Disaster Recovery

#### Properties

- [Documentation](https://developer.acronis.com/doc/outbound/apis/api-library/dr/index.html)

---

### Acronis Endpoint Detection and Response API
Provides EDR capabilities for detecting and responding to cybersecurity threats in real-time.

**Human URL:** [https://developer.acronis.com/doc/mdr/v1/reference/index.html](https://developer.acronis.com/doc/mdr/v1/reference/index.html)

#### Tags:

 - Acronis, EDR, Endpoint Security

#### Properties

- [Documentation](https://developer.acronis.com/doc/outbound/apis/api-library/mdr/index.html)

---

### Acronis Vault Manager REST API
Manages Acronis Vault storage solutions programmatically for backup storage configuration.

**Human URL:** [https://developer.acronis.com/doc/vaultman/v1/reference/index.html](https://developer.acronis.com/doc/vaultman/v1/reference/index.html)

#### Tags:

 - Acronis, Storage, Vault Management

#### Properties

- [Documentation](https://developer.acronis.com/doc/vaultman/v1/reference/index.html)

## Common Properties

- [Portal](https://developer.acronis.com/)
- [GettingStarted](https://developer.acronis.com/doc/outbound/apis/getting-started/index.html)
- [Authentication](https://developer.acronis.com/doc/outbound/apis/authentication/index.html)
- [Blog](https://www.acronis.com/en-us/blog/)
- [Support](https://www.acronis.com/en-us/support/)
- [Pricing](https://www.acronis.com/en-us/products/cloud/cyber-protect/pricing/)
- [Partners](https://www.acronis.com/en-us/partners/)
- [GitHubOrganization](https://github.com/acronis)

## Features

| Name | Description |
|------|-------------|
| Tenant Hierarchy Management | Multi-tier tenant management for MSPs, partners, and customers with offering item quotas. |
| Agent Management | Remote management of Acronis backup agents across Windows, Linux, macOS, and cloud workloads. |
| Backup Task Monitoring | Real-time monitoring of backup and protection tasks with state, result, and activity tracking. |
| Usage Reporting | Automated usage metrics collection and report generation for billing and capacity planning. |
| Policy Management | Programmatic creation and application of protection policies to resources. |
| Disaster Recovery API | Automated failover and recovery orchestration for business continuity. |
| Endpoint Detection and Response | EDR capabilities for threat detection, investigation, and response via API. |

## Use Cases

| Name | Description |
|------|-------------|
| MSP Platform Automation | Automate tenant provisioning, licensing management, and usage reporting for managed service providers. |
| Backup Monitoring Dashboard | Build custom dashboards tracking backup task status, failures, and completion rates. |
| Agent Health Monitoring | Monitor agent online status, version compliance, and update management across endpoints. |
| Compliance Reporting | Generate automated reports on data protection status for compliance and audit requirements. |
| Disaster Recovery Automation | Trigger and monitor DR failover workflows programmatically for RTO/RPO compliance. |

## Integrations

| Name | Description |
|------|-------------|
| PSA Platforms | Integration with ConnectWise, Autotask, and other PSA platforms for MSP billing and ticketing. |
| SIEM Systems | Event streaming to SIEM platforms via Event Manager API for security monitoring. |
| RMM Tools | Integration with RMM platforms for agent deployment and backup policy management. |
| Billing Systems | Usage data export for automated billing via usage and offering item APIs. |

## Artifacts

Machine-readable API specifications organized by format.

### OpenAPI

- [Acronis Account Management API](openapi/acronis-account-management-openapi.yaml)
- [Acronis Agent Management REST API](openapi/acronis-agent-management-openapi.yaml)
- [Acronis Task Manager API](openapi/acronis-task-manager-openapi.yaml)

### JSON Schema

- [Tenant Schema](json-schema/account-management-tenant-schema.json)
- [User Schema](json-schema/account-management-user-schema.json)
- [Agent Schema](json-schema/agent-management-agent-schema.json)
- [Task Schema](json-schema/task-manager-task-schema.json)
- [Offering Item Schema](json-schema/account-management-offering-item-schema.json)
- [Client Schema](json-schema/account-management-client-schema.json)
- [Usage Item Schema](json-schema/account-management-usage-item-schema.json)

### JSON Structure

- [Tenant Structure](json-structure/account-management-tenant-structure.json)
- [Agent Structure](json-structure/agent-management-agent-structure.json)
- [Task Structure](json-structure/task-manager-task-structure.json)

### JSON-LD

- [Acronis Context](json-ld/acronis-context.jsonld)

### Examples

- [Tenant Example](examples/account-management-tenant-example.json)
- [Agent Example](examples/agent-management-agent-example.json)
- [Task Example](examples/task-manager-task-example.json)

## Capabilities

Naftiko capabilities organized as shared per-API definitions composed into customer-facing workflows.

### Shared Per-API Definitions

- [Acronis Account Management](capabilities/shared/acronis-account-management.yaml) — 7 operations for tenant, user, and licensing management
- [Acronis Agent Management](capabilities/shared/acronis-agent-management.yaml) — 3 operations for agent monitoring
- [Acronis Task Manager](capabilities/shared/acronis-task-manager.yaml) — 3 operations for task monitoring

### Workflow Capabilities

| Workflow | APIs Combined | Tools | Persona |
|----------|--------------|-------|---------|
| [Cyber Protection Operations](capabilities/cyber-protection-operations.yaml) | Account, Agents, Tasks | 10 | MSP Administrator, IT Administrator, Security Analyst |

## Vocabulary

- [Acronis Vocabulary](vocabulary/acronis-vocabulary.yaml) — Unified taxonomy mapping 12 resources, 7 actions, 1 workflow, and 3 personas across operational (OpenAPI) and capability (Naftiko) dimensions

## Rules

- [Acronis Spectral Rules](rules/acronis-spectral-rules.yml) — 35+ rules across 13 categories enforcing Acronis API conventions

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
