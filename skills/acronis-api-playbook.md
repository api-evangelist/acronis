---
name: acronis-api-playbook
description: >-
  Acronis' own operating instructions for the Cyber Protect Cloud API — terminology mapping between
  console labels and API fields, tenant/user resolution, and step-by-step workflows for creating a
  customer tenant, registering an agent, building a protection plan, checking protection status,
  investigating EDR incidents, and generating a usage report.
api: Acronis Cyber Platform API
source: https://www.npmjs.com/package/@acronis-platform/mcp
method: searched
generated: '2026-08-30'
provenance: >-
  Verbatim, provider-authored. Acronis ships this document inside its own MCP server
  (@acronis-platform/mcp v1.0.1) as the get_acronis_skill tool, and the server's default instructions
  tell every client to call it before taking any action. Only this frontmatter block was added by
  API Evangelist; the body below is unmodified.
operations_note: >-
  The workflows below name MCP tools from mcp/acronis-mcp.yml; each tool's backing REST operation is
  bound in mcp/acronis-tool-crosswalk.yml.
---

# Acronis API Playbook

Apply the terminology table and workflows below to any Acronis request. Resolve ambiguous user phrasing with the terminology table first — API terms often differ from console UI labels. The listed workflows are precise step-by-step references; compose individual MCP tool calls for requests that fall between them.

CRITICAL CONTEXT: before you proceed with any user instructions, you must identify the tenant where you are working. Ask the user to explicitly specify for details when they are required to complete the scenario. Do not confuse "personal tenant ID" with the "tenant ID" of a customer or partner; they are distinct and serve different purposes.

## Terminology Reference

| User / doc term | API field / value | Notes |
|---|---|---|
| partner | tenant with `kind: "partner"` | top-level reseller |
| customer | tenant with `kind: "customer"`, a child of `partner` | |
| client (API context) | OAuth 2.0 API client application | not a tenant; used for machine-to-machine authentication |
| folder / group | tenant with `kind: "folder"` | grouping tenant; not a customer |
| managed mode | `ancestral_access: true` | parent admin has access |
| self-service mode | `ancestral_access: false` | tenant manages itself |
| tenant billing mode | `pricing_mode` | `"trial"` (excluded from reports) or `"production"` |
| user role | `role_id` in an access policy | e.g. `company_admin`, `partner_admin` |
| role assignment | access policy object | `trustee_id` + `role_id` scoped to `tenant_id` |
| account active / suspended | `enabled` boolean on user | `false` = suspended; distinct from `activated` |
| account activated | `activated` boolean on user | `true` after password setup; new users start `false` |
| service / product | application | identified by `application_id` UUID |
| feature / quota unit | offering item | `count` (devices/storage) or `feature` flag |
| soft quota | `quota.value` | usage ceiling; `null` = unlimited |
| hard quota / overage | `quota.overage` | excess allowed above soft quota; `null` = unlimited |
| storage backend / vault | `infra` | storage node within a location; called "Storages" in the console |
| protection plan | composite policy with `type: "policy.protection.total"` | a named container (plan) that holds a set of module sub-policies (backup, antimalware, VA, EDR, patches, etc.); each sub-policy carries `parent_ids` pointing to this composite |
| device / machine / workload | resource | registered when an agent is installed |
| resource display name | `user_defined_name` | user-visible; distinct from system `name` |
| agent connected / online | `online: true` on agent object | |
| plan applied to resource | policy application | `policy_id` bound to a resource context |
| protection / backup run status | `aggregate.status` on resource | `ok`, `not_protected`, `idle`, `running`, `failed` |
| incident investigation status | `state` on incident | NOT_STARTED, INVESTIGATING, CLOSED, REOPENED |
| threat verdict | `verdict` on incident | MALICIOUS, SUSPICIOUS, LEGITIMATE — engine-set; separate from `state` |
| incident mitigated | `mitigation_state` | NOT_MITIGATED / MITIGATED; distinct from `state` and `verdict` |
| incident type / threat category | `incident_categories` array | e.g. MALWARE_DETECTED, EXPLOIT_DETECTED |
| personal tenant / personal tenant ID | `personal_tenant_id` field on a user object | each user has a dedicated personal tenant; used to scope agent registration to a specific user |
| incident | MDR incident | EDR-detected threat event |

---

## Offering Item Editions

The `edition` field on an offering item is `null` for all services except **Cyber Protection** and **Sync & Share**. It controls which offering item names are available (active editions add a prefix to base names like `workstations`, `servers`, `storage`).

**Cyber Protection service** (use with `fetch_offering_items_available_for_tenant_child` / `set_tenant_offering_items`):

| Name | `edition` value | Offering item prefix | Example |
|---|---|---|---|
| Service-based (per gigabyte) | `pck_per_gigabyte` | `pg_base_` | `pg_base_workstations` |
| Service-based (per workload) | `pck_per_workload` | `pw_base_` | `pw_base_workstations` |
| Solution-based (per workload) | `bundled` | `bndl_` | `bndl_workstations` |

**Legacy editions** (Cyber Backup / Cyber Protect) may appear on older tenants; their `edition` values include `standard`, `advanced`, `disaster_recovery`, `cyber_protect_std`, `cyber_protect_adv`, `cyber_protect_dre`, `per_workload`, `per_gigabyte`.

---

## Finding a Tenant or User by Name

If the user refers to a tenant or user by name rather than by UUID, try to resolve the name to an ID before proceeding:

1. **`search`** — call with `tenant` set to the UUID of the root/current tenant and `text` set to the name of the user or tenant.
2. From the returned `items` array, find the object where `obj_type` is `"tenant"` or `"user"` and `name` exactly matches the user-supplied name.
3. Use the `id` field of that object as the `tenant_id` or `user_id` for all subsequent steps.
4. If no exact match is found, inform the user and ask them to clarify the name or provide the UUID directly.

## Tenant ID Formats

A tenant has two interchangeable identifiers; **`resolve_tenant_ids`** converts either way. Request items carry the form you have; the response returns both:
- UUID -> numeric: `{ "items": [{ "uuid": "<uuid>" }] }` -> `items[0].id`
- numeric -> UUID: `{ "items": [{ "id": <numeric> }] }` -> `items[0].uuid`

UUID is the default. The **resource, policy & agent APIs** use the tenant's **numeric string ID** (e.g. `"10584816"`) instead. When a call rejects the ID you passed, convert with `resolve_tenant_ids` and retry with the other form.

## Create a Customer Tenant and Assign Offering Items

Ask user for:
- tenant `name`; `parent_id` of the tenant where to create, optional `contact` (email, phone, address), `language`, `internal_tag`.
- whether the tenant should be in **managed mode** (`ancestral_access: true`) or **self-service mode** (`ancestral_access: false`).
- whether the tenant should be in **trial** or **production** billing mode (`pricing_mode`).
- whether to enable Cyber Protection and which editions to enable (from **Offering Item Editions**).
- tenant administrator user: `login`, `email`, `firstname`, `lastname`.

1. **`create_tenant`** — `{ name, kind: "customer", parent_id, language?, contact? }`
2. **`fetch_offering_items_available_for_tenant_child`** — pass `parent_id`, `kind: "customer"` and `edition` to get the list of offering items the parent can offer to the new customer tenant.
3. **`set_tenant_offering_items`** — `status: 1` = enabled; set the exact catalog `name`. Common names: `workstations`, `servers`, `mailboxes`, `storage`, `local_storage`.
4. **`check_login_name_availability`** — 204 = available; 409 = taken.
5. **`create_user`** — `{ tenant_id, login, contact: { email (required), firstname, lastname } }`.
6. **`update_user_access_policies`** — assign a role with `{ items: [{ trustee_id, trustee_type: "user", tenant_id, role_id: "company_admin", version: 0, id: "00000000-0000-0000-0000-000000000000", issuer_id: "00000000-0000-0000-0000-000000000000" }] }`. Note that this **overwrites** existing roles.
7. **`send_activation_email`**

| Role | Scope |
|---|---|
| `partner_admin` | Partner/Folder — management portal access |
| `company_admin` | Customer — full admin incl. DR |
| `unit_admin` | Unit — full admin |
| `protection_admin` | Partner/Customer/Unit — full protection management |
| `cyber_admin` | Partner/Customer/Unit — configure protection, approve scripts |
| `security_analyst` | Customer/Unit — EDR investigation |
| `accounts_admin` | Partner/Customer/Unit — user management only |
| `protection_ro_admin` | Partner/Customer/Unit — read-only protection |

---

## Register an Agent and Protect a Device

> Agent installation on the device is a **manual step**.

**Resolve the protection plan first.** Call **`fetch_a_list_of_policies`** and filter for `type == "policy.protection.total"` and `include_templates == false`. If the user has not specified a protection plan, present the available plans including their protection policies, suggest the most suitable enabled one for the workload type, and confirm before proceeding. If no suitable plan exists, offer to create one (see **Create a Protection Plan**). The `{policy_id}` used in the token scope must be a concrete, applicable plan with at least one **enabled** protective module — a bare-root plan (all modules disabled) or a `template` policy will not protect the workload on registration.

**Path A — Auto-apply on registration** (plan confirmed before agent install):

1. **`create_registration_token`** — `tenant_id` + `scopes`:
   - `urn:acronis.com:tenant-id:{personal_tenant_id}:backup_agent_admin`
   - `urn:acronis.com::policy_management:{tenant_id}|{policy_id}:apply_revoke`
   Where:
   - `{personal_tenant_id}` = the personal tenant ID of the user who will install the agent (from `fetch_a_list_of_users`).
   - `{tenant_id}` = the tenant where the protection plan was created.
   - `{policy_id}` = the plan to apply (from `fetch_a_list_of_policies`).
2. Provide token to user; the plan is applied automatically when the agent registers.
3. **`fetch_agents`** filtered by `tenant_id` — confirm `online: true`.
4. Optionally: **`start_policy_execution`** — trigger an immediate backup run.

**Path B — Manual apply after install** (apply plan once agent is online):

1. **`create_registration_token`** — `tenant_id` only; provide to user.
2. **`fetch_agents`** filtered by `tenant_id` — confirm `online: true`.
3. **`fetch_a_list_of_all_resources`** filtered by `tenant_id` — get the new resource `id`.
4. **`apply_a_policy`** — `{ policy_id, context: { items: [resource_id] } }`.
5. Optionally: **`start_policy_execution`** — trigger an immediate backup run.

---

## Create a Protection Plan

A protection plan is a composite policy (`type: "policy.protection.total"`) — a container whose module sub-policies (backup, antimalware, vulnerability assessment, EDR, patch management, etc.) each reference the plan through `parent_ids`.

You don't build every module by hand, and you don't need a follow-up PATCH. Create the `policy.protection.total` root **together with** the module sub-policies you want enabled in a **single `create_a_policy` call**; the server expands the composite, filling in the remaining default modules **disabled**. The minimal functional plan is the root plus one **machine backup** sub-policy. Do **not** author backup settings by hand — fetch an existing backup template and reuse its `settings` verbatim (a machine backup is rejected unless it has a valid `settings_schema` and `scheduling` block). Plans must be created in a **customer** tenant.

1. **`resolve_tenant_ids`** — `{ "items": [{ "uuid": "<customer-tenant-uuid>" }] }` -> `items[0].id`. Add this **numeric** tenant ID as `tenant_id` on every policy object in the body; the UUID is rejected.
2. **`fetch_a_list_of_policies`** — with `templates_only=true`, `include_settings=true`, `types=policy.backup.machine`. `templates_only=true` is **required**: it returns only template policies which carry complete, valid settings. From the returned `items[]`, take a `policy.backup.machine` sub-policy and keep its `settings` object and `settings_schema`.
3. **`create_a_policy`** — send the composite as `subject`. Use fresh UUIDs for both `id` values, set `tenant_id` on each object, point the backup's `parent_ids` at the root's id, and paste the fetched `settings`/`settings_schema` into the backup sub-policy:

   ```json
   {
     "subject": {
       "policy": [
         {
           "id": "<root-uuid>",
           "type": "policy.protection.total",
           "name": "<plan name>",
           "origin": "upstream",
           "enabled": true,
           "tenant_id": "<numeric id>",
           "settings_schema": "1.0"
         },
         {
           "id": "<backup-uuid>",
           "parent_ids": ["<root-uuid>"],
           "type": "policy.backup.machine",
           "name": "Backup",
           "origin": "upstream",
           "enabled": true,
           "tenant_id": "<numeric id>",
           "settings_schema": "<from template, e.g. 2.0>",
           "settings": { "<paste the template's settings object here>" }
         }
       ]
     }
   }
   ```

   > NOTE: **`splitting.size` must be a valid int32.** The template's `settings` may overflow.

   Take the `...@policy.protection.total` id from `result[]` as the plan's `policy_id`. If it fails with `PolicySettingsNotSet` / `PolicyHasUnsupportedSourceType`, the backup is missing `settings_schema` or a valid `scheduling` block — re-fetch the template and copy its `settings` exactly. Stop on other errors and notify the user; do not invent backup settings.

4. To enable additional modules, add them to the `policy` array in the same call with `enabled: true` and `parent_ids: ["<root-uuid>"]` — e.g. `policy.security.antimalware_protection`, `policy.security.active_protection`, `policy.security.vulnerability_assessment`, `policy.security.patch_management`, `policy.security.edr`, `policy.security.url_filtering`. Do **not** enable `policy.security.backups_scanning` unless machine backup is enabled (it depends on backup storage and otherwise fails the request).

Report the plan `name`, composite `policy_id`, and enabled modules. Apply it with **`apply_a_policy`**, or embed the `policy_id` in an agent registration token (see **Register an Agent, Path A**).

---

## Check Protection Status and Protect Unprotected Workloads

1. **`fetch_a_list_of_all_resources`** (filter `tenant_id`) — collect resource IDs and types.
2. **`fetch_the_protection_status_of_resources`** (filter `tenant_id`) — `aggregate.status` per resource: `ok` / `not_protected` / `error`.
3. **`fetch_tenant_offering_items`** — show quota usage alongside counts.
4. Summarize by workload type, count per status.
5. **`fetch_a_list_of_policies`** — list policies; find the plan by name where `type == "policy.protection.total"`; extract the plan's `id` as `policy_id`.
6. Confirm count of resources to be affected with user before proceeding.
7. **`apply_a_policy`** — for each unprotected resource: `{ policy_id, context: { items: [resource_id] } }`.
8. Verify: **`fetch_the_protection_status_of_resources`** on affected resources; confirm `aggregate: "ok"`.

---

## List and Investigate EDR Incidents

1. **`fetch_incidents`** — params: `customer_id` (tenant UUID), optional `severity` filter (`CRITICAL`, `HIGH`).
   - Key response fields: `incident_id`, `host_name`, `incident_categories`, `severity`, `verdict`, `state`, `incident_time`, `incident_link`.
2. **`fetch_incident`** — params: `incident_id`, `customer_id`, `with_response_actions: true`, `with_detections: true`. Returns timeline, `response_actions[]` (action name + uri), and detection details (MITRE ATT&CK, process, file hash).
3. **Isolate endpoint** (requires explicit user confirmation before executing):
   - **`initiate_response_action`** — params: `incident_id`, `customer_id`, `action: "WORKLOAD_ISOLATE"`, optional `comment`.
   - Returns `activity_id`.
4. **Release endpoint** after remediation:
   - Same tool with `action: "WORKLOAD_RELEASE"` (verify exact action name from `response_actions[]`).
5. Track action status: **`fetch_response_action_status`** with `activity_id`.

---

## Generate a Usage Report

**Two delivery modes — choose based on the user's intent, ask explicitly if unclear:**

| `result_action` | What happens | When to use |
|---|---|---|
| `"save"` | Report is generated and stored server-side; retrieved via `fetch_stored_reports` + `download_stored_report`. | User wants to read/display report data in the conversation. |
| `"send"` | Report is emailed to the specified `recipients` (array of user UUIDs); **no stored report is created** and the report cannot be downloaded via API. | User wants the report delivered by email to one or more users. |

1. **`fetch_tenant_reports`** — check if a matching report config already exists. If found and `result_action` matches, skip to step 3.
2. **`create_report`** — body varies by delivery mode:
   - **Save (API download):** `{ "parameters": { "kind": "usage_current", "tenant_id": "<uuid>", "level": "direct_partners", "period": { "start": "YYYY-MM-DD", "end": "YYYY-MM-DD" }, "formats": ["csv_v2_0"] }, "result_action": "save", "schedule": { "type": "once" } }`
   - **Send by email:** `{ "recipients": ["<user-uuid>", ...], "parameters": { "kind": "usage_summary", "tenant_id": "<uuid>", "level": "direct_partners", "period": { "start": "YYYY-MM-DD", "end": "YYYY-MM-DD" }, "formats": ["csv_v2_0"] }, "result_action": "send", "schedule": { "type": "once" } }`
   - `kind`: `usage_current` | `usage_daily` | `usage_summary`; `level`: `direct_partners` | `all_partners` | `all_customers` | `accounts`; `formats`: `csv` | `html` | `json_v1` | `csv_v2_0` | `json_v2_0`

3. *(Save mode only)* **`fetch_stored_reports`** (with report UUID) — poll until a stored report appears (`status: "saved"`).
4. *(Save mode only)* **`download_stored_report`** — retrieve and present CSV data; summarize key metrics (workstations, servers, mailboxes, storage consumed).
