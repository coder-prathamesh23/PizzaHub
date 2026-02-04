# Azure Naming Standards

> **Standard (adopted):** `resource-subname-env-projectname-location`  
> **Example:** `mlw-train-dev-churn-eastus2`  
> **Owner:** -  
> **Status:** Draft (pending Cloud Services review)  

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

This document defines organization naming standards for Azure resources to ensure names are:
- consistent across teams and environments
- predictable for operations, troubleshooting, cost reporting, and audits
- compatible with Azure naming restrictions (length, characters, uniqueness scope)
- automatable through IaC (Terraform and Bicep) and policy

This standard is based on Microsoft Cloud Adoption Framework (CAF) naming guidance and adapts it to our previously adopted organization pattern.

---

## 2. Scope

This standard applies across Azure. It includes:
- management groups and subscriptions (display names)
- resource groups
- networking (virtual networks, subnets, network security groups, route tables, firewalls, gateways, private endpoints, private DNS)
- identity and security (Key Vault, managed identities, access controls, certificates)
- observability (Log Analytics, Application Insights, alerts, action groups)
- storage (Storage accounts, files, blob containers, data lake file systems)
- compute (virtual machines, scale sets, functions, app services, container apps, AKS)
- data services (SQL, Cosmos DB, PostgreSQL, MySQL, cache)
- integration and messaging (Event Hubs, Service Bus, Event Grid)
- data engineering and analytics (Data Factory, Databricks, Synapse, Fabric components where applicable)
- governance and management (policy, initiative, assignments, managed resource groups)
- common platform services (container registry, automation, backup, recovery)

If a service is not explicitly listed in Section 7, it still follows the same naming schema unless its service rules require an exception, in which case it must be documented in Section 8.

---

## 3. Naming principles

### 3.1 Keep names stable
Many Azure resource names are difficult or impossible to change after creation. Names must contain stable identifiers only such as purpose, environment, project, region.

### 3.2 Standard component order
Names must always follow:
`resource-subname-env-projectname-location`

### 3.3 Use tags for dynamic metadata
Owner, ticket, sprint, temporary notes, and operational context must go in tags, not names.

### 3.4 Prefer readable abbreviations
Use standard abbreviations for the resource type (resource) and keep the rest concise.

### 3.5 Minimize exceptions
Exceptions are unavoidable for some services. Any exception must be:
- explicit
- consistent
- enforced through IaC validation

---

## 4. Standard naming schema

### 4.1 Canonical pattern
`resource-subname-env-projectname-location`

| Component | Meaning | Examples | Required |
|---|---|---|---|
| `resource` | Resource type abbreviation | `rg`, `vnet`, `snet`, `nsg`, `pep`, `mlw`, `kv`, `st`, `cr`, `log`, `appi` | Yes |
| `subname` | Purpose or component name inside the project | `shared`, `hub`, `spoke`, `train`, `infer`, `data`, `sec`, `mon`, `mlops01` | Yes |
| `env` | Environment | `dev`, `test`, `qa`, `uat`, `stage`, `prod`, `dr` | Yes |
| `projectname` | Project, workload, platform identifier | `dslz`, `dsplatform`, `churn`, `forecast`, `rcm` | Yes |
| `location` | Region code | `eastus2`, `centralindia`, `westeurope` (or approved short codes) | Yes |

### 4.2 Instance numbering
If multiple instances exist, encode it inside `subname`:
- `train01`, `train02`
- `pepmlw01`, `pepmlw02`
- `data01`, `data02`

This avoids breaking the adopted 5-component pattern.

### 4.3 Child objects inside a service
Some services contain non-ARM child objects that still need names, such as Storage containers, Event Hub entities, Service Bus queues, Key Vault secrets.

Default child object pattern:
`subname-env-projectname`

Example:
- Storage container: `raw-prod-churn`
- Service Bus queue: `ingest-prod-dslz`
- Event Hub: `telemetry-prod-rcm`

If the service requires a different pattern, document it in Section 8.

---

## 5. Allowed values (controlled vocabulary)

### 5.1 Environments (`env`)
Allowed:
`dev`, `test`, `qa`, `uat`, `stage`, `prod`, `dr`, `poc`, `sand`

### 5.2 Project names (`projectname`)
Rules:
- must be portfolio approved
- keep it short (recommended 3 to 12 characters)
- for shared platform resources use a reserved ID like:
  - `dslz` or `dsplatform` (choose one and enforce)

### 5.3 Locations (`location`)
Prefer Azure region codes such as `eastus2`.
If your organization uses short codes, define a mapping and enforce it.

Example mapping (replace with organization standard):

| Azure region | Allowed `location` |
|---|---|
| East US 2 | `eastus2` (or `eus2`) |
| Central India | `centralindia` (or `cin`) |
| West Europe | `westeurope` (or `weu`) |

### 5.4 Subnames (`subname`)
Rules:
- no spaces
- no additional hyphens inside `subname`
- should reflect purpose and optionally instance
- keep it consistent across environments
- avoid person names and ticket numbers

Recommended subnames by theme:
- shared platform: `shared`, `hub`, `spoke`, `net`, `sec`, `mon`
- data engineering: `ingest`, `etl`, `orches`, `raw`, `curated`, `gold`
- data science: `train`, `infer`, `feat`, `nb`, `mlops`, `reg`
- application: `api`, `web`, `worker`, `batch`

---

## 6. Abbreviations

Use these as the `resource` component.

> Note: If Cloud Services maintains an official abbreviation list, that list is the source of truth. Align to it during review.

### 6.1 Organization level
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
| Route table | `rt` |
| NAT gateway | `nat` |
| Load balancer | `lb` |
| Application gateway | `agw` |
| Firewall | `afw` |
| VPN gateway | `vpngw` |
| ExpressRoute gateway | `ergw` |
| Private endpoint | `pep` |
| Private DNS zone | `pdns` |
| Public IP | `pip` |

### 6.3 Security and identity
| Resource type | `resource` |
|---|---|
| Key Vault | `kv` |
| User assigned managed identity | `id` |
| Recovery Services vault | `rsv` |
| Disk encryption set | `des` |

### 6.4 Observability and operations
| Resource type | `resource` |
|---|---|
| Log Analytics workspace | `log` |
| Application Insights | `appi` |
| Action group | `ag` |
| Alert rule | `alrt` |
| Dashboard or workbook | `wb` |

### 6.5 Storage
| Resource type | `resource` |
|---|---|
| Storage account | `st` (see exceptions) |
| Data Lake file system | `fs` |
| File share | `share` |

### 6.6 Compute and containers
| Resource type | `resource` |
|---|---|
| Virtual machine | `vm` |
| Virtual machine scale set | `vmss` |
| Availability set | `as` |
| Function App | `func` |
| App Service | `app` |
| App Service plan | `asp` |
| Container Apps environment | `cae` |
| Container App | `ca` |
| AKS cluster | `aks` |
| Container registry | `cr` |

### 6.7 Data services and integration
| Resource type | `resource` |
|---|---|
| Azure SQL server | `sql` |
| SQL database | `sqldb` |
| Cosmos DB account | `cosmos` |
| PostgreSQL server | `psql` |
| MySQL server | `mysql` |
| Redis cache | `redis` |
| Data Factory | `adf` |
| Databricks workspace | `dbw` |
| Synapse workspace | `syn` |
| Event Hub namespace | `ehns` |
| Event Hub | `eh` |
| Service Bus namespace | `sbns` |
| Service Bus queue | `sbq` |
| Service Bus topic | `sbt` |
| Event Grid topic | `egt` |

### 6.8 Data science and machine learning
| Resource type | `resource` |
|---|---|
| Azure Machine Learning workspace | `mlw` |
| ML compute (managed) | `mlc` |
| ML online endpoint | `mle` |
| ML batch endpoint | `mlbe` |
| ML registry | `mlr` |

---

## 7. Standards by resource type

This section is the rules you follow while creating resources.

### 7.1 Management groups and subscriptions (display names)
IDs may be governed differently. We standardize display names for clarity.

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
- Route table: `rt-subname-env-projectname-location`
  - `rt-spoke-prod-dslz-eastus2`
- Private endpoint: `pep-subname-env-projectname-location`
  - `pep-mlw01-prod-churn-eastus2`
- Private DNS zone: `pdns-subname-env-projectname-location`
  - `pdns-privatelink-prod-dslz-eastus2`

### 7.4 Security and identity
- Key Vault: `kv-subname-env-projectname-location`
  - `kv-sec-prod-dslz-eastus2`
- User assigned managed identity: `id-subname-env-projectname-location`
  - `id-mlops-prod-dslz-eastus2`
- Recovery Services vault: `rsv-subname-env-projectname-location`
  - `rsv-backup-prod-dslz-eastus2`

### 7.5 Observability
- Log Analytics: `log-subname-env-projectname-location`
  - `log-core-prod-dslz-eastus2`
- Application Insights: `appi-subname-env-projectname-location`
  - `appi-infer-prod-churn-eastus2`
- Action group: `ag-subname-env-projectname-location`
  - `ag-ops-prod-dslz-eastus2`

### 7.6 Storage
ARM resources:
- Storage account: follows exception pattern in Section 8
- Private endpoint to storage: `pep-subname-env-projectname-location`
  - `pep-stdata01-prod-churn-eastus2`

Child objects:
- Blob container: `subname-env-projectname`
  - `raw-prod-churn`
  - `curated-prod-churn`
- Data Lake file system: `subname-env-projectname`
  - `lake-prod-dslz`
- File share: `subname-env-projectname`
  - `share-prod-rcm`

### 7.7 Compute
- Virtual machine: `vm-subname-env-projectname-location`
  - `vm-jump-prod-dslz-eastus2`
- VM scale set: `vmss-subname-env-projectname-location`
  - `vmss-worker-prod-rcm-eastus2`
- Function App: `func-subname-env-projectname-location`
  - `func-etl-dev-churn-eastus2`
- App Service plan: `asp-subname-env-projectname-location`
  - `asp-web-prod-rcm-eastus2`
- App Service: `app-subname-env-projectname-location`
  - `app-api-prod-rcm-eastus2`

### 7.8 Containers
- Container registry: `cr-subname-env-projectname-location`
  - `cr-mlops-prod-dslz-eastus2`
- AKS cluster: `aks-subname-env-projectname-location`
  - `aks-mlops-prod-dslz-eastus2`
- Container Apps environment: `cae-subname-env-projectname-location`
  - `cae-shared-prod-dslz-eastus2`
- Container App: `ca-subname-env-projectname-location`
  - `ca-infer-prod-churn-eastus2`

### 7.9 Databases and caches
- Azure SQL server: `sql-subname-env-projectname-location`
  - `sql-core-prod-rcm-eastus2`
- SQL database: `sqldb-subname-env-projectname-location`
  - `sqldb-app-prod-rcm-eastus2`
- Cosmos DB account: `cosmos-subname-env-projectname-location`
  - `cosmos-app-prod-rcm-eastus2`
- PostgreSQL server: `psql-subname-env-projectname-location`
  - `psql-core-prod-rcm-eastus2`
- MySQL server: `mysql-subname-env-projectname-location`
  - `mysql-core-prod-rcm-eastus2`
- Redis cache: `redis-subname-env-projectname-location`
  - `redis-cache-prod-rcm-eastus2`

### 7.10 Integration and messaging
ARM resources:
- Event Hub namespace: `ehns-subname-env-projectname-location`
  - `ehns-stream-prod-rcm-eastus2`
- Service Bus namespace: `sbns-subname-env-projectname-location`
  - `sbns-msg-prod-rcm-eastus2`
- Event Grid topic: `egt-subname-env-projectname-location`
  - `egt-events-prod-rcm-eastus2`
- Data Factory: `adf-subname-env-projectname-location`
  - `adf-orches-prod-dslz-eastus2`

Child objects:
- Event Hub: `subname-env-projectname`
  - `telemetry-prod-rcm`
- Service Bus queue: `subname-env-projectname`
  - `ingest-prod-dslz`
- Service Bus topic: `subname-env-projectname`
  - `notify-prod-rcm`

### 7.11 Analytics and data engineering platforms
- Databricks workspace: `dbw-subname-env-projectname-location`
  - `dbw-eng-prod-dslz-eastus2`
- Synapse workspace: `syn-subname-env-projectname-location`
  - `syn-analytics-prod-dslz-eastus2`

Child objects:
- Where the platform enforces its own naming, follow the platform rules first, then map to our subname, env, projectname.

### 7.12 Data science and machine learning
ARM resources:
- Azure ML workspace: `mlw-subname-env-projectname-location`
  - `mlw-train-dev-churn-eastus2`
  - `mlw-shared-prod-dslz-eastus2`
- ML registry: `mlr-subname-env-projectname-location`
  - `mlr-shared-prod-dslz-eastus2`

Child objects:
- Online endpoint: `subname-env-projectname`
  - `infer-prod-churn`
- Batch endpoint: `subname-env-projectname`
  - `batch-prod-churn`
- Compute cluster: `subname-env-projectname`
  - `cpu-prod-churn`
  - `gpu-prod-churn`

---

## 8. Exceptions and constraints

Not all Azure resources allow hyphens or long names. This section defines controlled exceptions.

### 8.1 Storage accounts (strict naming, no hyphens)
Storage accounts often have strict rules. Therefore they cannot follow the hyphenated pattern.

Storage account exception pattern:
`st{subname}{env}{projectname}{location}{nn}`

Where:
- everything is lowercase
- `{nn}` is optional 2-digit instance number such as `01`, `02`

Examples:
- `stdataprodchurneastus201`
- `stmlopsproddslzeastus201`

Shortening rule if name is too long (in order):
1) shorten `subname`
2) shorten `projectname`
3) use approved short `location` code
4) keep `env` unchanged

### 8.2 DNS based and globally unique resources
Some services require global uniqueness or form public DNS names. For these:
- keep `subname` and `projectname` short
- consider an approved short region code if length becomes a constraint
- if a suffix is required for uniqueness, add it in `subname` such as `api01`, `api02`

### 8.3 Service enforced naming
Some services enforce their own naming rules for internal objects. In that case:
- follow the service rules first
- map to `subname-env-projectname` where possible
- document the final pattern here if it deviates

### 8.4 Rule of thumb
If a resource type cannot comply with the standard pattern due to restrictions:
- use the closest compliant representation
- document the exception in this section (resource type and final pattern)
- enforce via IaC validation

---

## 9. Tags vs names

### 9.1 Required tags (minimum)
Names identify the resource. Tags provide operational governance.

Minimum recommended tags:
- `Owner` (team distribution list, team name)
- `CostCenter`
- `Environment` (same as `env`)
- `Project` (same as `projectname`)
- `DataClassification` (for example `public`, `internal`, `confidential`, `restricted`)
- `ManagedBy` (for example `terraform`, `bicep`, `portal`)

### 9.2 What not to put in names
Do not include:
- person name
- incident and ticket numbers
- dates
- temporary labels such as `temp`, `test123`
- sprint identifiers

---

## 10. Governance and enforcement

### 10.1 Review and approval
- This document is a draft naming standard.
- Cloud Services is the review authority for final approval and alignment with the central Azure Wiki.

### 10.2 Implementation (recommended)
- Enforce naming via IaC name builder module
- Validate:
  - allowed env values
  - allowed location codes
  - max length and character constraints (resource specific)
- Use policy primarily for tag enforcement (naming constraints vary by resource type)

### 10.3 Update process
When a new Azure service is adopted:
1) add the resource type to Section 6 abbreviations if needed
2) add the naming pattern and examples in Section 7
3) add an exception in Section 8 if the service rules require it

---

## 11. Examples

### 11.1 Shared platform examples
- `rg-shared-prod-dslz-eastus2`
- `vnet-hub-prod-dslz-eastus2`
- `snet-pe-prod-dslz-eastus2`
- `kv-sec-prod-dslz-eastus2`
- `log-core-prod-dslz-eastus2`
- `cr-mlops-prod-dslz-eastus2`
- `ehns-stream-prod-rcm-eastus2`
- `sbns-msg-prod-rcm-eastus2`
- Storage account example: `stdataprodchurneastus201`

### 11.2 Workload examples (Churn model)
- `rg-core-dev-churn-eastus2`
- `mlw-train-dev-churn-eastus2`
- `pep-mlw01-dev-churn-eastus2`
- `appi-infer-dev-churn-eastus2`
- Event Hub entity example: `telemetry-dev-churn`
- Storage container example: `raw-dev-churn`

---

## 12. Appendix

### 12.1 Quick checklist
Before creating a resource:
1) pick `resource` abbreviation from Section 6
2) choose a meaningful `subname` (purpose and optional instance)
3) use approved `env`
4) use approved `projectname`
5) use approved `location`
6) if the resource cannot use hyphens, apply exception rules (Section 8)
7) for child objects use `subname-env-projectname` unless the service enforces a different rule

### 12.2 Reserved values (suggested)
- Reserved `projectname` for shared platform assets: `dslz` (or `dsplatform`)
- Reserved `subname` values for platform layers:
  - `shared`, `net`, `sec`, `mon`, `hub`, `spoke`

---
