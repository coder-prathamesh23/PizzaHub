# Data Science Landing Zone — Naming Standards

> **Standard (adopted):** `resource-subname-env-projectname-location`  
> **Example:** `mlw-train-dev-churn-eastus2`  
> **Owner:** <Team/Role>  
> **Status:** Draft (pending Cloud Services review)  
> **Last Updated:** 2026-02-03

---

## Table of contents
- [1. Purpose](#1-purpose)
- [2. Scope](#2-scope)
- [3. Naming principles](#3-naming-principles)
- [4. Standard naming schema](#4-standard-naming-schema)
- [5. Allowed values (controlled vocabulary)](#5-allowed-values-controlled-vocabulary)
- [6. Abbreviations](#6-abbreviations)
- [7. Standards by resource type](#7-standards-by-resource-type)
- [8. Exceptions and constraints](#8-exceptions-and-constraints)
- [9. Tags vs names](#9-tags-vs-names)
- [10. Governance and enforcement](#10-governance-and-enforcement)
- [11. Examples](#11-examples)
- [12. Appendix](#12-appendix)

---

## 1. Purpose

This document defines **organization naming standards** for the **Data Science Landing Zone (DS LZ)** to ensure resource names are:
- consistent across teams and environments
- predictable for operations, troubleshooting, cost reporting, and audits
- compatible with Azure naming restrictions (length, characters, uniqueness scope)
- automatable via IaC (Terraform/Bicep) and policy

This standard is based on Microsoft’s Cloud Adoption Framework (CAF) naming guidance and adapts it to our **previously adopted** organization pattern.

---

## 2. Scope

Applies to all resources in the **Data Science Landing Zone**, including:
- management groups & subscriptions (**display names**)
- resource groups
- networking (VNet/subnets/NSGs/Private Endpoints/DNS)
- security (Key Vault, identities, secrets)
- observability (Log Analytics, App Insights)
- data foundations (Storage)
- DS/ML platform resources (Azure ML workspace, compute, registries, etc.)

---

## 3. Naming principles

### 3.1 Keep names stable
Many Azure resource names are difficult or impossible to change after creation. Names must contain **stable identifiers only** (purpose, env, project, region).

### 3.2 Standard component order
Names must always follow:
`resource-subname-env-projectname-location`

### 3.3 Use tags for dynamic metadata
Owner, ticket, sprint, temporary notes, and operational context must go in **tags**, not names.

### 3.4 Prefer readable abbreviations
Use standard abbreviations for resource type (`resource`) and keep the rest concise.

---

## 4. Standard naming schema

### 4.1 Canonical pattern
`resource-subname-env-projectname-location`

| Component | Meaning | Examples | Required |
|---|---|---|---|
| `resource` | Resource type abbreviation | `rg`, `vnet`, `snet`, `nsg`, `pep`, `mlw`, `kv`, `st`, `cr`, `log`, `appi` | Yes |
| `subname` | Purpose / component within DS LZ | `shared`, `hub`, `spoke`, `train`, `infer`, `data`, `sec`, `mon`, `mlops01` | Yes |
| `env` | Environment | `dev`, `test`, `qa`, `uat`, `stage`, `prod`, `dr` | Yes |
| `projectname` | Project / workload / platform identifier | `dslz`, `dsplatform`, `churn`, `forecast`, `rcm` | Yes |
| `location` | Region code | `eastus2`, `centralindia`, `westeurope` (or approved short codes) | Yes |

### 4.2 Instance numbering
If multiple instances exist, encode it inside `subname`:
- `train01`, `train02`
- `pepmlw01`, `pepmlw02`
- `data01`, `data02`

This avoids breaking the adopted 5-component pattern.

---

## 5. Allowed values (controlled vocabulary)

### 5.1 Environments (`env`)
Allowed:
`dev`, `test`, `qa`, `uat`, `stage`, `prod`, `dr`, `poc`, `sand`

### 5.2 Project names (`projectname`)
Rules:
- must be portfolio-approved
- keep it short (recommended 3–12 characters)
- for shared landing zone platform resources use a reserved ID like:
  - `dslz` or `dsplatform` (choose one and enforce)

### 5.3 Locations (`location`)
Prefer Azure region codes (e.g., `eastus2`).  
If your org uses short codes, define a mapping and enforce it.

**Example mapping (replace with org standard):**

| Azure region | Allowed `location` |
|---|---|
| East US 2 | `eastus2` (or `eus2`) |
| Central India | `centralindia` (or `cin`) |
| West Europe | `westeurope` (or `weu`) |

### 5.4 Subnames (`subname`)
Rules:
- no spaces
- no additional hyphens inside `subname`
- should reflect **purpose** and optionally **instance**
- keep it consistent across environments

Recommended DS LZ subnames:
- platform/shared: `shared`, `hub`, `spoke`, `net`, `sec`, `mon`
- DS/ML: `train`, `infer`, `mlops`, `nb`, `reg`, `data`, `feat`

---

## 6. Abbreviations

Use these as the `resource` component.

### 6.1 Core landing zone
| Resource type | `resource` |
|---|---|
| Management group (display name prefix) | `mg` |
| Subscription (display name prefix) | `sub` |
| Resource group | `rg` |

### 6.2 Networking
| Resource type | `resource` |
|---|---|
| Virtual network | `vnet` |
| Subnet | `snet` |
| Network security group | `nsg` |
| Private endpoint | `pep` |
| Private DNS zone | `pdns` |

### 6.3 Security & monitoring
| Resource type | `resource` |
|---|---|
| Key Vault | `kv` |
| Log Analytics workspace | `log` |
| Application Insights | `appi` |
| User-assigned managed identity | `id` |

### 6.4 Data science / ML foundations
| Resource type | `resource` |
|---|---|
| Azure ML workspace | `mlw` |
| Storage account | `st` *(see exceptions)* |
| Container registry | `cr` |

> Note: If Cloud Services already maintains an official abbreviation list, that list is the source of truth. Align to it during review.

---

## 7. Standards by resource type

This section is the “rules you follow while creating resources”.

### 7.1 Management groups & subscriptions (DISPLAY NAMES)
> IDs may be governed differently; we standardize display names for clarity.

- Management group display name: `mg-subname-env-projectname-location`
  - Example: `mg-platform-prod-dslz-eastus2`
- Subscription display name: `sub-subname-env-projectname-location`
  - Example: `sub-shared-prod-dslz-eastus2`

### 7.2 Resource groups
- `rg-subname-env-projectname-location`
  - `rg-shared-prod-dslz-eastus2`
  - `rg-mon-prod-dslz-eastus2`
  - `rg-core-dev-churn-eastus2`

### 7.3 Networking
- VNet: `vnet-subname-env-projectname-location`
  - `vnet-hub-prod-dslz-eastus2`
- Subnet: `snet-subname-env-projectname-location`
  - `snet-pe-prod-dslz-eastus2`
- NSG: `nsg-subname-env-projectname-location`
  - `nsg-aks-prod-dslz-eastus2`
- Private endpoint: `pep-subname-env-projectname-location`
  - `pep-mlw01-prod-churn-eastus2`
- Private DNS zone: `pdns-subname-env-projectname-location`
  - `pdns-privatelink-prod-dslz-eastus2`

### 7.4 Data Science / ML platform resources
- Azure ML workspace: `mlw-subname-env-projectname-location`
  - `mlw-train-dev-churn-eastus2`
  - `mlw-shared-prod-dslz-eastus2`
- Container registry: `cr-subname-env-projectname-location`
  - `cr-mlops-prod-dslz-eastus2`
- Key Vault: `kv-subname-env-projectname-location`
  - `kv-sec-prod-dslz-eastus2`

### 7.5 Observability
- Log Analytics: `log-subname-env-projectname-location`
  - `log-core-prod-dslz-eastus2`
- Application Insights: `appi-subname-env-projectname-location`
  - `appi-infer-prod-churn-eastus2`

### 7.6 Identity (user-assigned managed identities)
- `id-subname-env-projectname-location`
  - `id-aml-prod-churn-eastus2`
  - `id-mlops-prod-dslz-eastus2`

---

## 8. Exceptions and constraints

Not all Azure resources allow hyphens or long names. This section defines controlled exceptions.

### 8.1 Storage accounts (strict naming; NO hyphens)
Storage accounts often have strict rules (length, lowercase, numbers). Therefore they cannot follow the hyphenated pattern.

**Storage account exception pattern:**
`st{subname}{env}{projectname}{location}{nn}`

Where:
- everything is lowercase
- `{nn}` is optional 2-digit instance number (`01`, `02`)

Examples:
- `stdataprodchurneastus201`
- `stmlopsproddslzeastus201`

**Shortening rule if name is too long (in order):**
1) shorten `subname`
2) shorten `projectname`
3) use approved short `location` code
4) keep `env` unchanged

### 8.2 Globally unique / DNS-based resources
Some services require global uniqueness or form public DNS names. For these:
- keep `subname` + `projectname` short
- consider an approved short region code if length becomes a constraint
- if a suffix is required for uniqueness, add it in `subname` (e.g., `chat01`, `chat02`)

### 8.3 Rule of thumb
If a resource type cannot comply with the standard pattern due to restrictions:
- use the closest compliant representation
- document the exception in this section (add resource type + final pattern)
- enforce via IaC validation

---

## 9. Tags vs names

### 9.1 Required tags (minimum)
Names identify the resource; tags provide operational governance.

Minimum recommended tags:
- `Owner` (team DL / team name)
- `CostCenter`
- `Environment` (same as `env`)
- `Project` (same as `projectname`)
- `DataClassification` (e.g., `public`, `internal`, `confidential`, `restricted`)
- `ManagedBy` (e.g., `terraform`, `bicep`, `portal`)

### 9.2 What NOT to put in names
Do not include:
- person name
- incident/ticket numbers
- dates
- “temp” / “test123”
- sprint identifiers

---

## 10. Governance and enforcement

### 10.1 Review & approval
- This document is a DS LZ naming standard draft.
- Cloud Services is the review authority for final approval/alignment with the central Azure Wiki.

### 10.2 Implementation (recommended)
- Enforce naming via IaC “name builder” module
- Validate:
  - allowed env values
  - allowed location codes
  - max length and character constraints (resource-specific)
- Use policy primarily for tag enforcement (naming constraints vary by resource type)

---

## 11. Examples

### 11.1 Shared Data Science Landing Zone (platform assets)
- `rg-shared-prod-dslz-eastus2`
- `vnet-hub-prod-dslz-eastus2`
- `snet-pe-prod-dslz-eastus2`
- `kv-sec-prod-dslz-eastus2`
- `log-core-prod-dslz-eastus2`
- `cr-mlops-prod-dslz-eastus2`
- `stdataprod dslz eastus2 01` → `stdataprod dslz eastus2 01` (flattened) → `stdataprod dslzeastus201` *(example pattern only; validate final tokenization in IaC)*

### 11.2 Workload project (Churn model example)
- `rg-core-dev-churn-eastus2`
- `mlw-train-dev-churn-eastus2`
- `pep-mlw01-dev-churn-eastus2`
- `appi-infer-dev-churn-eastus2`
- `stdataprodchurneastus201`

---

## 12. Appendix

### 12.1 Quick “how to name” checklist
Before creating a resource:
1) Pick `resource` abbreviation from Section 6
2) Choose a meaningful `subname` (purpose + optional instance)
3) Use approved `env`
4) Use approved `projectname`
5) Use approved `location`
6) If the resource cannot use hyphens, apply exception rules (Section 8)

### 12.2 Reserved values (suggested)
- Reserved `projectname` for platform/shared assets: `dslz` (or `dsplatform`)
- Reserved `subname` values for platform layers:
  - `shared`, `net`, `sec`, `mon`, `hub`, `spoke`

---
