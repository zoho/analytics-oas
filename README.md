# Zoho Analytics OpenAPI Specification (OAS) — v2

OpenAPI 3.1 specifications for the [Zoho Analytics REST API v2](https://www.zoho.com/analytics/api/v2/).

**10 domains · 35 categories · 119 paths · 180 operations.**

The API surface is split into **one specification file per domain**. Every operation inside a file carries an OpenAPI `tag`, and that tag is the **category** it belongs to. Domain → category → operation is the only hierarchy in this repository; everything below is an expansion of it.

Use these files to explore the API, generate client SDKs, drive Swagger UI / Postman / Redoc, or ground an AI agent in the Zoho Analytics API contract.

---

## Contents

- [Repository structure](#repository-structure)
- [Where to find an API](#where-to-find-an-api) — domain → category tree and the [domain index](#domain-index)
- [API catalog](#api-catalog) — all 180 operations
  - [01 · Organization Management](#01--organization-management) · [02 · User & Groups](#02--user--groups) · [03 · Workspace Management](#03--workspace-management) · [04 · Data Modeling & Schema](#04--data-modeling--schema) · [05 · Data Operations](#05--data-operations)
  - [06 · Views Management](#06--views-management) · [07 · Reports & Dashboards](#07--reports--dashboards) · [08 · Share & Publish](#08--share--publish) · [09 · Schedules & Alerts](#09--schedules--alerts) · [10 · DSML](#10--dsml)
- [Calling the API](#calling-the-api) — [base URLs](#1-base-url-per-data-center), [authentication](#2-authentication--oauth-20), [headers](#3-required-headers), [`CONFIG`](#4-the-config-parameter), [responses](#5-responses)
- [Common path parameters](#common-path-parameters)
- [Notes for AI agents and tooling](#notes-for-ai-agents-and-tooling)
- [Using the specs](#using-the-specs)
- [Support](#support)

---

## Repository structure

```
analytics-oas/
├── README.md                                    ← you are here
└── v2.0/
    ├── common/
    │   └── zoho-analytics-api-common.json       shared components: OAuth scheme + error responses
    ├── org-management-grouped-api.json          01 · Organization Management
    ├── user-groups-grouped-api.json             02 · User & Groups
    ├── workspace-management-grouped-api.json    03 · Workspace Management
    ├── data-modeling-schema-grouped-api.json    04 · Data Modeling & Schema
    ├── data-operations-grouped-api.json         05 · Data Operations
    ├── views-management-grouped-api.json        06 · Views Management
    ├── reports-dashboards-grouped-api.json      07 · Reports & Dashboards
    ├── share-publish-grouped-api.json           08 · Share & Publish
    ├── schedules-alerts-grouped-api.json        09 · Schedules & Alerts
    └── dsml-grouped-api.json                    10 · DSML
```

**Naming convention:** `v2.0/<domain-slug>-grouped-api.json`. The slug is stable and is what downstream tooling keys off — do not rename these files.

Each domain file is self-contained apart from the shared components, which every file references by absolute raw URL:

```
https://raw.githubusercontent.com/zoho/analytics-oas/refs/heads/main/v2.0/common/zoho-analytics-api-common.json
```

That common file holds:

| Component | Pointer | Purpose |
|---|---|---|
| OAuth 2.0 security scheme | `#/components/securitySchemes/iam-oauth2-schema` | Authorization-code flow and the full scope list |
| Error schema | `#/components/schemas/Error` | Shape of every failure response |
| Client error response | `#/components/responses/CommonErrorResponse` | Referenced by every operation as `4XX` |
| Server error response | `#/components/responses/UnexpectedErrorResponse` | Referenced by every operation as `500` |

> **Tip:** tools that cannot follow remote `$ref`s (some SDK generators, older Swagger UI builds) should bundle the spec first — e.g. `npx @redocly/cli bundle v2.0/<file>.json -o bundled.json` or `swagger-cli bundle`.

---

## Where to find an API

Pick the domain, then the category. Counts in parentheses are operations.

```
Zoho Analytics REST API v2
│
├── 01 · Organization Management  (1 category, 4 operations)
│   └── Org Info & Settings  (4)
│
├── 02 · User & Groups  (4 categories, 26 operations)
│   ├── Org Users  (7)
│   ├── Custom Roles  (4)
│   ├── Workspace Users  (8)
│   └── Workspace Groups  (7)
│
├── 03 · Workspace Management  (4 categories, 24 operations)
│   ├── Workspace Operations  (10)
│   ├── Workspace Preferences  (4)
│   ├── Domain & White Label  (2)
│   └── Workspace Folders  (8)
│
├── 04 · Data Modeling & Schema  (7 categories, 33 operations)
│   ├── Table & Schema  (2)
│   ├── Columns  (8)
│   ├── Lookups & Relationships  (2)
│   ├── Query Tables  (4)
│   ├── Formula Columns  (5)
│   ├── Aggregate Formulas  (7)
│   └── Workspace Variables  (5)
│
├── 05 · Data Operations  (6 categories, 20 operations)
│   ├── Row Operations  (3)
│   ├── Data Import (Synchronous)  (2)
│   ├── Data Import (Asynchronous)  (5)
│   ├── Data Export (Synchronous)  (1)
│   ├── Data Export (Asynchronous)  (4)
│   └── Data Sync & Connectivity  (5)
│
├── 06 · Views Management  (5 categories, 27 operations)
│   ├── View Operations  (10)
│   ├── Auto Analysis  (2)
│   ├── View Preferences  (2)
│   ├── Tags  (10)
│   └── Trash Management  (3)
│
├── 07 · Reports & Dashboards  (2 categories, 9 operations)
│   ├── Reports  (3)
│   └── Dashboard  (6)
│
├── 08 · Share & Publish  (4 categories, 20 operations)
│   ├── Sharing  (6)
│   ├── Publish  (7)
│   ├── Embed  (1)
│   └── Slideshow Management  (6)
│
├── 09 · Schedules & Alerts  (1 category, 6 operations)
│   └── Email Schedules  (6)
│
└── 10 · DSML  (1 category, 11 operations)
    └── AutoML Analysis  (11)
```

### Domain index

| # | Domain | Spec file | Categories | Operations | Scope family |
|---|---|---|---|---|---|
| 01 | Organization Management | [`org-management-grouped-api.json`](v2.0/org-management-grouped-api.json) | 1 | 4 | `metadata`, `usermanagement` |
| 02 | User & Groups | [`user-groups-grouped-api.json`](v2.0/user-groups-grouped-api.json) | 4 | 26 | `usermanagement`, `share` |
| 03 | Workspace Management | [`workspace-management-grouped-api.json`](v2.0/workspace-management-grouped-api.json) | 4 | 24 | `metadata`, `modeling` |
| 04 | Data Modeling & Schema | [`data-modeling-schema-grouped-api.json`](v2.0/data-modeling-schema-grouped-api.json) | 7 | 33 | `modeling`, `metadata` |
| 05 | Data Operations | [`data-operations-grouped-api.json`](v2.0/data-operations-grouped-api.json) | 6 | 20 | `data`, `metadata` |
| 06 | Views Management | [`views-management-grouped-api.json`](v2.0/views-management-grouped-api.json) | 5 | 27 | `modeling`, `metadata`, `embed` |
| 07 | Reports & Dashboards | [`reports-dashboards-grouped-api.json`](v2.0/reports-dashboards-grouped-api.json) | 2 | 9 | `modeling`, `metadata` |
| 08 | Share & Publish | [`share-publish-grouped-api.json`](v2.0/share-publish-grouped-api.json) | 4 | 20 | `embed`, `share` |
| 09 | Schedules & Alerts | [`schedules-alerts-grouped-api.json`](v2.0/schedules-alerts-grouped-api.json) | 1 | 6 | `modeling`, `metadata` |
| 10 | DSML | [`dsml-grouped-api.json`](v2.0/dsml-grouped-api.json) | 1 | 11 | `modeling`, `metadata` |

The exact scope required by an operation is always declared on the operation itself, under `security[].iam-oauth2-schema`. The column above is only a hint for which file to open first.

---

## API catalog

Every operation in the repository, grouped by domain and category. `operationId` values are unique across all files and are what generated SDKs use as method names.

### 01 · Organization Management

**Spec file:** [`v2.0/org-management-grouped-api.json`](v2.0/org-management-grouped-api.json) &nbsp;·&nbsp; **1 category** &nbsp;·&nbsp; **4 paths** &nbsp;·&nbsp; **4 operations**

APIs that return information about the organisations, resource usage, subscription plan, and workspace or view identity lookups available to the authenticated user.

#### Org Info & Settings

APIs for retrieving organisation information, resource usage, subscription details, and workspace or view metadata.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get Organizations | `GET` | `/restapi/v2/orgs` | `getOrganizations` |
| Get Resource Details | `GET` | `/restapi/v2/resources` | `getResourceDetails` |
| Get Subscription Details | `GET` | `/restapi/v2/subscription` | `getSubscriptionDetails` |
| Get Meta Details From Name | `GET` | `/restapi/v2/metadetails` | `getMetaDetails` |

---

### 02 · User & Groups

**Spec file:** [`v2.0/user-groups-grouped-api.json`](v2.0/user-groups-grouped-api.json) &nbsp;·&nbsp; **4 categories** &nbsp;·&nbsp; **14 paths** &nbsp;·&nbsp; **26 operations**

API for managing users and groups in Zoho Analytics — covering org-level users, workspace-level users, and workspace groups.

#### Org Users

APIs that allow the Account Admin and the Organization Admins of a Zoho Analytics organization to manage its users - listing, adding, removing, activating, deactivating and changing the org-level role of the users, and...

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get Org Admins | `GET` | `/restapi/v2/orgadmins` | `getOrgAdmins` |
| Get Users | `GET` | `/restapi/v2/users` | `getUsers` |
| Add Users | `POST` | `/restapi/v2/users` | `addUsers` |
| Remove Users | `DELETE` | `/restapi/v2/users` | `removeUsers` |
| Activate Users | `PUT` | `/restapi/v2/users/active` | `activateUsers` |
| Deactivate Users | `PUT` | `/restapi/v2/users/inactive` | `deActivateUsers` |
| Change User Role | `PUT` | `/restapi/v2/users/role` | `changeUserRole` |

#### Custom Roles

APIs that allow the Account Admin and the Organization Admins of a Zoho Analytics organization to define custom roles - named permission bundles that can be granted to users in place of the built-in roles.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get Custom Roles | `GET` | `/restapi/v2/orgs/roles` | `getCustomRoles` |
| Create Custom Role | `POST` | `/restapi/v2/orgs/roles` | `createCustomRole` |
| Update Custom Role | `PUT` | `/restapi/v2/orgs/roles/{role-id}` | `updateCustomRole` |
| Delete Custom Role | `DELETE` | `/restapi/v2/orgs/roles/{role-id}` | `deleteCustomRole` |

#### Workspace Users

APIs that manage the users and the administrators of a single Zoho Analytics workspace - controlling who has access to the workspace, the role they hold within it, and whether that access is currently active.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get Workspace Users | `GET` | `/restapi/v2/workspaces/{workspace-id}/users` | `getWorkspaceUsers` |
| Add Workspace Users | `POST` | `/restapi/v2/workspaces/{workspace-id}/users` | `addWorkspaceUsers` |
| Remove Workspace Users | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/users` | `deleteWorkspaceUsers` |
| Change Workspace Users Role | `PUT` | `/restapi/v2/workspaces/{workspace-id}/users/role` | `changeWorkspaceUsersRole` |
| Change Workspace Users Status | `PUT` | `/restapi/v2/workspaces/{workspace-id}/users/status` | `changeWorkspaceUsersStatus` |
| Get Workspace Admins | `GET` | `/restapi/v2/workspaces/{workspace-id}/admins` | `getWorkspaceAdmins` |
| Add Workspace Admins | `POST` | `/restapi/v2/workspaces/{workspace-id}/admins` | `addWorkspaceAdmins` |
| Remove Workspace Admins | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/admins` | `removeWorkspaceAdmins` |

#### Workspace Groups

APIs that manage the groups of a Zoho Analytics workspace.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get Group List | `GET` | `/restapi/v2/workspaces/{workspace-id}/groups` | `getGroups` |
| Create Group | `POST` | `/restapi/v2/workspaces/{workspace-id}/groups` | `createGroup` |
| Get Group Details | `GET` | `/restapi/v2/workspaces/{workspace-id}/groups/{group-id}` | `getGroupDetails` |
| Rename Group | `PUT` | `/restapi/v2/workspaces/{workspace-id}/groups/{group-id}` | `renameGroup` |
| Delete Group | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/groups/{group-id}` | `deleteGroup` |
| Add Group Members | `POST` | `/restapi/v2/workspaces/{workspace-id}/groups/{group-id}/members` | `addGroupMembers` |
| Remove Group Members | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/groups/{group-id}/members` | `removeGroupMembers` |

---

### 03 · Workspace Management

**Spec file:** [`v2.0/workspace-management-grouped-api.json`](v2.0/workspace-management-grouped-api.json) &nbsp;·&nbsp; **4 categories** &nbsp;·&nbsp; **15 paths** &nbsp;·&nbsp; **24 operations**

API for managing workspaces in Zoho Analytics — covering workspace operations, preferences, domain access, and folder management.

#### Workspace Operations

APIs that manage the lifecycle and the metadata of workspaces, covering the creation, copying, renaming, deletion and inspection of a workspace.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get All Workspace List | `GET` | `/restapi/v2/workspaces` | `getAllWorkspaces` |
| Create Workspace | `POST` | `/restapi/v2/workspaces` | `createWorkspace` |
| Get Owned Workspace List | `GET` | `/restapi/v2/workspaces/owned` | `getOwnedWorkspaces` |
| Get Shared Workspace List | `GET` | `/restapi/v2/workspaces/shared` | `getSharedWorkspaces` |
| Get Workspace Info | `GET` | `/restapi/v2/workspaces/{workspace-id}` | `getWorkspaceDetails` |
| Copy Workspace | `POST` | `/restapi/v2/workspaces/{workspace-id}` | `copyWorkspace` |
| Rename Workspace | `PUT` | `/restapi/v2/workspaces/{workspace-id}` | `renameWorkspace` |
| Delete Workspace | `DELETE` | `/restapi/v2/workspaces/{workspace-id}` | `deleteWorkspace` |
| Get Workspace Secret Key | `GET` | `/restapi/v2/workspaces/{workspace-id}/secretkey` | `getWorkspaceSecretKey` |
| Export as Template | `GET` | `/restapi/v2/workspaces/{workspace-id}/template/data` | `exportAsTemplate` |

#### Workspace Preferences

APIs that manage the workspace preferences of a user, covering the default workspace that opens on login and the favourite workspaces starred for quick access.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Add Default Workspace | `POST` | `/restapi/v2/workspaces/{workspace-id}/default` | `addDefaultWorkspace` |
| Remove Default Workspace | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/default` | `removeDefaultWorkspace` |
| Add Favourite Workspace | `POST` | `/restapi/v2/workspaces/{workspace-id}/favorite` | `addFavoriteWorkspace` |
| Remove Favourite Workspace | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/favorite` | `removeFavoriteWorkspace` |

#### Domain & White Label

APIs that control whether a workspace is reachable through the White Label or Client Portal domain of the organization.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Enable Workspace for Domain Access | `POST` | `/restapi/v2/workspaces/{workspace-id}/wlaccess` | `enableDomainWorkspace` |
| Disable Workspace for Domain Access | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/wlaccess` | `disableDomainWorkspace` |

#### Workspace Folders

APIs that manage the folders of a workspace, covering the creation, renaming, deletion, nesting and reordering of folders, the movement of views between folders, and the selection of the default folder.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get Folder List | `GET` | `/restapi/v2/workspaces/{workspace-id}/folders` | `getFolders` |
| Create Folder | `POST` | `/restapi/v2/workspaces/{workspace-id}/folders` | `createFolder` |
| Rename Folder | `PUT` | `/restapi/v2/workspaces/{workspace-id}/folders/{folder-id}` | `renameFolder` |
| Delete Folder | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/folders/{folder-id}` | `deleteFolder` |
| Make Default Folder | `PUT` | `/restapi/v2/workspaces/{workspace-id}/folders/{folder-id}/default` | `makeDefaultFolder` |
| Change Folder Hierarchy | `PUT` | `/restapi/v2/workspaces/{workspace-id}/folders/{folder-id}/move` | `changeFolderHierarchy` |
| Change Folder Position | `PUT` | `/restapi/v2/workspaces/{workspace-id}/folders/{folder-id}/reorder` | `changeFolderPosition` |
| Move Views To Folder | `PUT` | `/restapi/v2/workspaces/{workspace-id}/views/movetofolder` | `moveViewsToFolder` |

---

### 04 · Data Modeling & Schema

**Spec file:** [`v2.0/data-modeling-schema-grouped-api.json`](v2.0/data-modeling-schema-grouped-api.json) &nbsp;·&nbsp; **7 categories** &nbsp;·&nbsp; **22 paths** &nbsp;·&nbsp; **33 operations**

API for Data Modeling & Schema in Zoho Analytics — covering tables, schema, columns, lookups, query tables, formulas, and workspace variables.

#### Table & Schema

APIs for creating tables and fetching table metadata.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Create Table | `POST` | `/restapi/v2/workspaces/{workspace-id}/tables` | `createTable` |
| Get Table Metadata | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/metadata` | `getTableMetadata` |

#### Columns

APIs for adding, renaming, reordering, showing, hiding, deleting, and sorting columns.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Add Column | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/columns` | `addColumn` |
| Rename Column | `PUT` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/columns/{column-id}` | `renameColumn` |
| Delete Column | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/columns/{column-id}` | `deleteColumn` |
| Reorder Columns | `PUT` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/columns/reorder` | `reorderColumns` |
| Hide Columns | `PUT` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/columns/hide` | `hideColumns` |
| Show Columns | `PUT` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/columns/show` | `showColumns` |
| Get Column Dependents | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/columns/{column-id}/dependents` | `getColumnDependents` |
| Sort Data by Columns | `PUT` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/data/sort` | `sortDataByColumns` |

#### Lookups & Relationships

APIs for adding and removing lookup relationships.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Add Lookup | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/columns/{column-id}/lookup` | `addLookup` |
| Remove Lookup | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/columns/{column-id}/lookup` | `removeLookup` |

#### Query Tables

APIs for creating, editing, and retrieving query table details.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get Query Tables | `GET` | `/restapi/v2/workspaces/{workspace-id}/querytables` | `getQueryTables` |
| Create Query Table | `POST` | `/restapi/v2/workspaces/{workspace-id}/querytables` | `createQueryTable` |
| Edit Query Table | `PUT` | `/restapi/v2/workspaces/{workspace-id}/querytables/{querytable-id}` | `editQueryTable` |
| Get Query Table Details | `GET` | `/restapi/v2/workspaces/{workspace-id}/querytables/{querytable-id}` | `getQueryTableDetails` |

#### Formula Columns

APIs for managing custom formula columns and copying formulas.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Add Custom Formula | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/customformulas` | `addFormulaColumn` |
| Get Custom Formulas | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/customformulas` | `getCustomFormulaList` |
| Copy Custom Formulas | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/formulas/copy` | `copyFormulas` |
| Edit Custom Formula | `PUT` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/customformulas/{formula-id}` | `editFormulaColumn` |
| Delete Custom Formula | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/customformulas/{formula-id}` | `deleteFormulaColumn` |

#### Aggregate Formulas

APIs for managing aggregate formulas and retrieving values and dependents.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Add Aggregate Formula | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/aggregateformulas` | `addAggregateFormula` |
| Get Aggregate Formula | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/aggregateformulas` | `getAggregateFormulaList` |
| Edit Aggregate Formula | `PUT` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/aggregateformulas/{formula-id}` | `editAggregateFormula` |
| Delete Aggregate Formula | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/aggregateformulas/{formula-id}` | `deleteAggregateFormula` |
| Get Aggregate Formula Value | `GET` | `/restapi/v2/workspaces/{workspace-id}/aggregateformulas/{formula-id}/value` | `getAggregateFormulaValue` |
| Get Aggregate Formula Dependents | `GET` | `/restapi/v2/workspaces/{workspace-id}/aggregateformulas/{formula-id}/dependents` | `getAggregateFormulaDependents` |
| Get Unified Metrics in Workspace | `GET` | `/restapi/v2/workspaces/{workspace-id}/aggregateformulas` | `getAggregateFormulasInWorkspace` |

#### Workspace Variables

APIs for creating, updating, deleting, and retrieving workspace variables.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Create Variable | `POST` | `/restapi/v2/workspaces/{workspace-id}/variables` | `createVariable` |
| Get Variables | `GET` | `/restapi/v2/workspaces/{workspace-id}/variables` | `getVariables` |
| Edit Variable | `PUT` | `/restapi/v2/workspaces/{workspace-id}/variables/{variable-id}` | `updateVariable` |
| Delete Variable | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/variables/{variable-id}` | `deleteVariable` |
| Get Variable Details | `GET` | `/restapi/v2/workspaces/{workspace-id}/variables/{variable-id}` | `getVariableDetails` |

---

### 05 · Data Operations

**Spec file:** [`v2.0/data-operations-grouped-api.json`](v2.0/data-operations-grouped-api.json) &nbsp;·&nbsp; **6 categories** &nbsp;·&nbsp; **15 paths** &nbsp;·&nbsp; **20 operations**

API for Data Operations in Zoho Analytics — covering row operations, import/export workflows, and datasource sync/connectivity.

#### Row Operations

APIs for adding, updating, and deleting rows in a view.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Add Row | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/rows` | `addRow` |
| Update Rows | `PUT` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/rows` | `updateRows` |
| Delete Row | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/rows` | `deleteRows` |

#### Data Import (Synchronous)

APIs for importing data directly into new or existing tables.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Import Data into a New Table | `POST` | `/restapi/v2/workspaces/{workspace-id}/data` | `importDataNewTable` |
| Import Data into an Existing Table | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/data` | `importDataExistingTable` |

#### Data Import (Asynchronous)

APIs for creating and monitoring asynchronous import jobs and batch imports.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Create Import Job for a New Table | `POST` | `/restapi/v2/bulk/workspaces/{workspace-id}/data` | `createImportJobNewTable` |
| Create Import Job for an Existing Table | `POST` | `/restapi/v2/bulk/workspaces/{workspace-id}/views/{view-id}/data` | `createImportJobExistingTable` |
| Batch Import Data into New Table | `POST` | `/restapi/v2/bulk/workspaces/{workspace-id}/data/batch` | `batchImportNewTable` |
| Batch Import Data into Existing Table | `POST` | `/restapi/v2/bulk/workspaces/{workspace-id}/views/{view-id}/data/batch` | `batchImportExistingTable` |
| Get Import Job Details | `GET` | `/restapi/v2/bulk/workspaces/{workspace-id}/importjobs/{job-id}` | `getImportJobDetails` |

#### Data Export (Synchronous)

APIs for exporting view data directly.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Export Data from a View | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/data` | `exportDataView` |

#### Data Export (Asynchronous)

APIs for creating export jobs and downloading generated exports.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Create Export Job using SQL Query | `GET` | `/restapi/v2/bulk/workspaces/{workspace-id}/data` | `createExportJobSQLQuery` |
| Create Export Job using View ID | `GET` | `/restapi/v2/bulk/workspaces/{workspace-id}/views/{view-id}/data` | `createExportJobViewId` |
| Get Export Job Details | `GET` | `/restapi/v2/bulk/workspaces/{workspace-id}/exportjobs/{job-id}` | `getExportJobDetails` |
| Download Exported Data | `GET` | `/restapi/v2/bulk/workspaces/{workspace-id}/exportjobs/{job-id}/data` | `downloadExportedData` |

#### Data Sync & Connectivity

APIs for import history, datasource sync/refetch, datasource updates, and listing datasources.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get Last Import Details | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/importdetails` | `getLastImportDetails` |
| Refetch Data | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/sync` | `refetchDatasource` |
| Sync Data | `POST` | `/restapi/v2/workspaces/{workspace-id}/datasources/{datasource-id}/sync` | `syncDatasource` |
| Update Datasource Connection | `PUT` | `/restapi/v2/workspaces/{workspace-id}/datasources/{datasource-id}` | `updateDatasourceConnection` |
| Get Datasources | `GET` | `/restapi/v2/workspaces/{workspace-id}/datasources` | `getDatasources` |

---

### 06 · Views Management

**Spec file:** [`v2.0/views-management-grouped-api.json`](v2.0/views-management-grouped-api.json) &nbsp;·&nbsp; **5 categories** &nbsp;·&nbsp; **18 paths** &nbsp;·&nbsp; **27 operations**

API for Views Management in Zoho Analytics — covering view operations, auto analysis, favorites, and trash management.

#### View Operations

APIs for creating, copying, renaming, deleting, and retrieving views.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Save As View | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/saveas` | `saveAsView` |
| Copy Views | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/copy` | `copyViews` |
| Create Similar Views | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/similarviews` | `createSimilarViews` |
| Rename View | `PUT` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}` | `renameView` |
| Delete View | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}` | `deleteView` |
| Get View List | `GET` | `/restapi/v2/workspaces/{workspace-id}/views` | `getViews` |
| Get View Details | `GET` | `/restapi/v2/views/{view-id}` | `getViewDetails` |
| Get View URL | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/publish` | `getViewUrl` |
| Get View Dependents | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/dependents` | `getViewDependents` |
| Get Recent Views | `GET` | `/restapi/v2/recentviews` | `getRecentViews` |

#### Auto Analysis

APIs to automatically generate a curated set of views - charts, pivot tables and summary views - from an entire table or from a single column of it.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Auto Analyse Column | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/columns/{column-id}/autoanalyse` | `autoAnalyseColumn` |
| Auto Analyse View | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/autoanalyse` | `autoAnalyseView` |

#### View Preferences

APIs to mark a view as a favorite for the authenticated user and to remove it from the favorites list.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Add Favorite View | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/favorite` | `addFavoriteView` |
| Remove Favorite View | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/favorite` | `removeFavoriteView` |

#### Tags

APIs to create, update, delete and list the tags of a workspace, and to attach those tags to views.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get Tags List | `GET` | `/restapi/v2/workspaces/{workspace-id}/tags` | `getTags` |
| Create Tag | `POST` | `/restapi/v2/workspaces/{workspace-id}/tags` | `createTag` |
| Update Tag | `PUT` | `/restapi/v2/workspaces/{workspace-id}/tags/{tag-id}` | `updateTag` |
| Delete Tag | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/tags/{tag-id}` | `deleteTag` |
| Get Tagged Views | `GET` | `/restapi/v2/workspaces/{workspace-id}/tags/{tag-id}/views` | `getTaggedViews` |
| Add Tag To Multiple Views | `POST` | `/restapi/v2/workspaces/{workspace-id}/tags/{tag-id}/views` | `addTagToViews` |
| Remove Tag From Multiple Views | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/tags/{tag-id}/views` | `removeTagFromViews` |
| Get View Tags | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/tags` | `getViewTags` |
| Add Multiple Tags To View | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/tags` | `addTagsToView` |
| Remove Multiple Tags From View | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/tags` | `removeTagsFromView` |

#### Trash Management

APIs to list the views available in the trash of a workspace, restore a trashed view back to the workspace, and permanently delete a view from the trash.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get Trash Views | `GET` | `/restapi/v2/workspaces/{workspace-id}/trash` | `getTrashViews` |
| Restore Trash View | `POST` | `/restapi/v2/workspaces/{workspace-id}/trash/{view-id}` | `restoreTrashView` |
| Delete Trash View | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/trash/{view-id}` | `deleteTrashView` |

---

### 07 · Reports & Dashboards

**Spec file:** [`v2.0/reports-dashboards-grouped-api.json`](v2.0/reports-dashboards-grouped-api.json) &nbsp;·&nbsp; **2 categories** &nbsp;·&nbsp; **9 paths** &nbsp;·&nbsp; **9 operations**

API for Reports and Dashboards in Zoho Analytics - covering the creation, update and metadata retrieval of analysis views, and the listing, creation, metadata retrieval and update of dashboards.

#### Reports

APIs for creating, updating and reading the metadata of analysis views (charts, pivot tables and summary views) inside a workspace.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Create Analysis View | `POST` | `/restapi/v2/workspaces/{workspace-id}/reports` | `createReport` |
| Update Analysis View | `PUT` | `/restapi/v2/workspaces/{workspace-id}/reports/{view-id}` | `updateReport` |
| Get Report Metadata | `GET` | `/restapi/v2/workspaces/{workspace-id}/reports/{view-id}/metadata` | `getReportMetadata` |

#### Dashboard

APIs for listing dashboards accessible to a user, and for creating, reading and updating dashboards inside a workspace.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get All Dashboards | `GET` | `/restapi/v2/dashboards` | `getDashboards` |
| Get Owned Dashboards | `GET` | `/restapi/v2/dashboards/owned` | `getOwnedDashboards` |
| Get Shared Dashboards | `GET` | `/restapi/v2/dashboards/shared` | `getSharedDashboards` |
| Create Dashboard | `POST` | `/restapi/v2/workspaces/{workspace-id}/dashboards` | `createDashboard` |
| Get Dashboard Metadata | `GET` | `/restapi/v2/workspaces/{workspace-id}/dashboards/{dashboard-id}/metadata` | `getDashboardMetadata` |
| Update Dashboard | `PUT` | `/restapi/v2/workspaces/{workspace-id}/dashboards/{dashboard-id}` | `updateDashboard` |

---

### 08 · Share & Publish

**Spec file:** [`v2.0/share-publish-grouped-api.json`](v2.0/share-publish-grouped-api.json) &nbsp;·&nbsp; **4 categories** &nbsp;·&nbsp; **11 paths** &nbsp;·&nbsp; **20 operations**

API for Share & Publish in Zoho Analytics — covering view sharing, publish configurations, embed URLs, and slideshow management.

#### Sharing

APIs for sharing workspaces and views with users, and retrieving share details and permissions.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get Workspace Shared Details | `GET` | `/restapi/v2/workspaces/{workspace-id}/share` | `getWorkspaceSharedDetails` |
| Share Views | `POST` | `/restapi/v2/workspaces/{workspace-id}/share` | `shareViews` |
| Remove Share | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/share` | `removeShare` |
| Update Shared Details For View | `PUT` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/share` | `UpdateSharedDetailsForView` |
| Get Shared Details For Views | `GET` | `/restapi/v2/workspaces/{workspace-id}/share/shareddetails` | `getSharedDetailsForViews` |
| Get User Permissions | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/share/mypermissions` | `getUserPermissions` |

#### Publish

APIs for making views public, managing private URLs, and updating publish configurations.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Make Views Public | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/publish/public` | `makeViewsPublic` |
| Remove Public Permission | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/publish/public` | `removePublicPermission` |
| Create Private URL | `POST` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/publish/privatelink` | `createPrivateUrl` |
| Get Private URL | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/publish/privatelink` | `getPrivateUrl` |
| Remove Private Access | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/publish/privatelink` | `removePrivateAccess` |
| Get Publish Configurations | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/publish/config` | `getPublishConfigurations` |
| Update Publish Configurations | `PUT` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/publish/config` | `updatePublishConfigurations` |

#### Embed

APIs for retrieving embeddable view URLs.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Get Embed URL | `GET` | `/restapi/v2/workspaces/{workspace-id}/views/{view-id}/publish/embed` | `getEmbedUrl` |

#### Slideshow Management

APIs for creating, updating, deleting, and listing slideshows and their URLs.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Create Slideshow | `POST` | `/restapi/v2/workspaces/{workspace-id}/slides` | `createSlideshow` |
| Get Slideshows | `GET` | `/restapi/v2/workspaces/{workspace-id}/slides` | `getSlideshows` |
| Update Slideshow | `PUT` | `/restapi/v2/workspaces/{workspace-id}/slides/{slide-id}` | `updateSlideshow` |
| Delete Slideshow | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/slides/{slide-id}` | `deleteSlideshow` |
| Get Slideshow Details | `GET` | `/restapi/v2/workspaces/{workspace-id}/slides/{slide-id}` | `getSlideshowDetails` |
| Get Slideshow URL | `GET` | `/restapi/v2/workspaces/{workspace-id}/slides/{slide-id}/publish` | `getSlideshowUrl` |

---

### 09 · Schedules & Alerts

**Spec file:** [`v2.0/schedules-alerts-grouped-api.json`](v2.0/schedules-alerts-grouped-api.json) &nbsp;·&nbsp; **1 category** &nbsp;·&nbsp; **3 paths** &nbsp;·&nbsp; **6 operations**

API for Schedules & Alerts in Zoho Analytics — covering creation, management, and triggering of email schedules.

#### Email Schedules

APIs for creating, updating, triggering, and deleting email schedules, and retrieving schedule details.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Create Email Schedule | `POST` | `/restapi/v2/workspaces/{workspace-id}/emailschedules` | `createEmailSchedule` |
| Get Email Schedules | `GET` | `/restapi/v2/workspaces/{workspace-id}/emailschedules` | `getEmailSchedules` |
| Trigger Email Schedule | `POST` | `/restapi/v2/workspaces/{workspace-id}/emailschedules/{schedule-id}` | `triggerEmailSchedule` |
| Update Email Schedule | `PUT` | `/restapi/v2/workspaces/{workspace-id}/emailschedules/{schedule-id}` | `updateEmailSchedule` |
| Delete Email Schedule | `DELETE` | `/restapi/v2/workspaces/{workspace-id}/emailschedules/{schedule-id}` | `deleteEmailSchedule` |
| Change Email Schedule Status | `PUT` | `/restapi/v2/workspaces/{workspace-id}/emailschedules/{schedule-id}/status` | `changeEmailScheduleStatus` |

---

### 10 · DSML

**Spec file:** [`v2.0/dsml-grouped-api.json`](v2.0/dsml-grouped-api.json) &nbsp;·&nbsp; **1 category** &nbsp;·&nbsp; **8 paths** &nbsp;·&nbsp; **11 operations**

API for DSML (Data Science & Machine Learning) in Zoho Analytics — covering AutoML analysis creation, execution, deployments, and what-if analysis.

#### AutoML Analysis

APIs for creating, running, deploying, and managing AutoML analyses and models.

| Operation | Method | Endpoint | `operationId` |
|---|---|---|---|
| Create AutoML Analysis | `POST` | `/restapi/v2/automl/workspaces/{workspace-id}/analysis` | `createAutoMLAnalysis` |
| Get AutoML Analysis In Workspace | `GET` | `/restapi/v2/automl/workspaces/{workspace-id}/analysis` | `getAutoMLAnalysisInWorkspace` |
| Run AutoML Analysis | `POST` | `/restapi/v2/automl/workspaces/{workspace-id}/analysis/{analysis-id}/deployments/{deployment-id}/execute` | `runAutoMLAnalysis` |
| Delete AutoML Analysis | `DELETE` | `/restapi/v2/automl/workspaces/{workspace-id}/analysis/{analysis-id}` | `deleteAutoMLAnalysis` |
| Get AutoML Analysis Details | `GET` | `/restapi/v2/automl/workspaces/{workspace-id}/analysis/{analysis-id}` | `getAutoMLAnalysisDetails` |
| Get AutoML Analysis In Org | `GET` | `/restapi/v2/automl/analysis` | `getAutoMLAnalysisInOrg` |
| Delete AutoML Analysis Model | `DELETE` | `/restapi/v2/automl/workspaces/{workspace-id}/analysis/{analysis-id}/models/{model-id}` | `deleteAutoMLAnalysisModel` |
| Get Deployments For A Model | `GET` | `/restapi/v2/automl/workspaces/{workspace-id}/analysis/{analysis-id}/models/{model-id}/deployments` | `getDeploymentsForModel` |
| Create AutoML Analysis Deployment | `POST` | `/restapi/v2/automl/workspaces/{workspace-id}/analysis/{analysis-id}/models/{model-id}/deployments` | `createAutoMLAnalysisDeployment` |
| Delete AutoML Analysis Model Deployment | `DELETE` | `/restapi/v2/automl/workspaces/{workspace-id}/analysis/{analysis-id}/deployments/{deployment-id}` | `deleteAutoMLAnalysisModelDeployment` |
| AutoML What If Analysis | `POST` | `/restapi/v2/automl/workspaces/{workspace-id}/analysis/{analysis-id}/models/{model-id}/whatif` | `autoMLWhatIfAnalysis` |

---

## Calling the API

### 1. Base URL (per data center)

Every spec declares `https://analyticsapi.zoho.com` in its `servers` block. Replace it with the host of your data center before generating clients or sending requests.

| Data center | Base URL | Zoho Accounts domain |
|---|---|---|
| US | `https://analyticsapi.zoho.com` | `https://accounts.zoho.com` |
| EU | `https://analyticsapi.zoho.eu` | `https://accounts.zoho.eu` |
| IN | `https://analyticsapi.zoho.in` | `https://accounts.zoho.in` |
| AU | `https://analyticsapi.zoho.com.au` | `https://accounts.zoho.com.au` |
| JP | `https://analyticsapi.zoho.jp` | `https://accounts.zoho.jp` |
| SA | `https://analyticsapi.zoho.sa` | `https://accounts.zoho.sa` |
| CN | `https://analyticsapi.zoho.com.cn` | `https://accounts.zoho.com.cn` |
| CA | `https://analyticsapi.zohocloud.ca` | `https://accounts.zohocloud.ca` |

When you change the data center, also update `authorizationUrl`, `tokenUrl` and `refreshUrl` inside the `iam-oauth2-schema` security scheme to the matching Accounts domain — the OAuth flow fails otherwise. Full list: [Zoho Accounts Domains](https://www.zoho.com/analytics/api/v2/api-specification.html#server-uri).

### 2. Authentication — OAuth 2.0

1. Register a client in the [Zoho Developer Console](https://api-console.zoho.com) to get a `Client ID` and `Client Secret`.
2. Generate a refresh token following the [authentication guide](https://www.zoho.com/analytics/api/v2/authentication.html).
3. Exchange the refresh token for an access token at the Accounts `tokenUrl` of your data center.
4. Send the access token on every request:

```http
Authorization: Zoho-oauthtoken <access_token>
```

**Scope families** (`ZohoAnalytics.<family>.<read|create|update|delete|all>`):

| Family | Covers |
|---|---|
| `ZohoAnalytics.metadata.*` | Org info, subscription, resource usage, workspace and view metadata |
| `ZohoAnalytics.modeling.*` | Workspaces, folders, tables, columns, formulas, views, reports, dashboards, schedules, AutoML |
| `ZohoAnalytics.data.*` | Rows, import and export of data, datasource sync |
| `ZohoAnalytics.share.*` | Sharing of workspaces and views, permissions |
| `ZohoAnalytics.embed.*` | Publish, private links, embed URLs, slideshows |
| `ZohoAnalytics.usermanagement.*` | Org users, custom roles, workspace users and groups |
| `ZohoAnalytics.fullaccess.all` | Everything above |

Request the narrowest scope that covers your operations. The exact scope of each operation is in its `security` block.

### 3. Required headers

| Header | Required | Notes |
|---|---|---|
| `Authorization` | Always | `Zoho-oauthtoken <access_token>` |
| `ZANALYTICS-ORGID` | Almost always | Organization ID that owns the resource. Retrieve it with `GET /restapi/v2/orgs`. Optional on the workspace-listing APIs, and not used by `getOrganizations`, the dashboard-listing APIs and `getRecentViews` |
| `ZANALYTICS-DEST-ORGID` | Cross-org copy only | Destination organization for `copyWorkspace`, `copyViews` and `copyFormulas` |
| `Content-Type` | On `POST`/`PUT`/`DELETE` | `application/x-www-form-urlencoded`, or `multipart/form-data` for file uploads |

### 4. The `CONFIG` parameter

Zoho Analytics APIs do not take a JSON request body. Structured input is passed as a **single `CONFIG` parameter holding a JSON-encoded string** — in the query string for `GET`, and in the form-encoded request body for `POST`, `PUT` and `DELETE`. The schema of `CONFIG` for each operation is documented in that operation's `parameters` or `requestBody`.

```http
POST /restapi/v2/workspaces HTTP/1.1
Host: analyticsapi.zoho.com
Authorization: Zoho-oauthtoken 1000.xxxxxxxx.xxxxxxxx
ZANALYTICS-ORGID: 10234695
Content-Type: application/x-www-form-urlencoded

CONFIG={"workspaceName":"Sales Workspace","workspaceDesc":"Workspace for sales analytics"}
```

### 5. Responses

Success — `200` with a payload, or `204` when there is nothing to return:

```json
{
  "status": "success",
  "summary": "Create workspace",
  "data": { "workspaceId": "1767024000003145002" }
}
```

Failure — every operation declares `4XX` (client error: 400, 401, 403, 404) and `500` (server error), both `$ref`-ing the shared common file:

```json
{
  "status": "failure",
  "summary": "META_DBNAME_DUPLICATE",
  "data": {
    "errorCode": 7101,
    "errorMessage": "Workspace with the same name exists already. Provide an alternate name"
  }
}
```

Branch on `data.errorCode`, not on the HTTP status alone — the code identifies the exact condition and is stable across releases.

---

## Common path parameters

| Parameter | Appears in | Meaning |
|---|---|---|
| `{workspace-id}` | Most domains | Identifier of the workspace |
| `{view-id}` | Views, data, modeling, share | Identifier of a table, report or dashboard |
| `{column-id}` | Modeling, views | Identifier of a column in a table |
| `{folder-id}` | Workspace Management | Identifier of a workspace folder |
| `{group-id}`, `{role-id}` | User & Groups | Identifiers of a workspace group and a custom role |
| `{querytable-id}`, `{formula-id}`, `{variable-id}` | Data Modeling & Schema | Identifiers of a query table, formula and workspace variable |
| `{job-id}`, `{datasource-id}` | Data Operations | Identifiers of an async import/export job and a datasource |
| `{schedule-id}` | Schedules & Alerts | Identifier of an email schedule |
| `{slide-id}` | Share & Publish | Identifier of a slideshow |
| `{tag-id}` | Views Management | Identifier of a view tag |
| `{analysis-id}`, `{model-id}`, `{deployment-id}` | DSML | Identifiers of an AutoML analysis, model and deployment |

Identifiers are opaque numeric strings. Resolve a name to an ID with `GET /restapi/v2/metadetails` (Get Meta Details From Name) rather than hard-coding them.

---

## Notes for AI agents and tooling

Deterministic facts about this repository, so an agent can navigate it without guessing:

- **One file per domain.** `v2.0/<domain-slug>-grouped-api.json`. Do not expect a single merged spec; load only the domain you need.
- **Tag == category.** The `tags` array at the root of each file is the complete, ordered category list for that domain. Every operation has exactly one tag, and that tag always exists in the root `tags` array.
- **`operationId` is globally unique** across all ten files and is a stable identifier — prefer it over `method + path` when referring to an operation.
- **OpenAPI version is `3.1.0`** in every file.
- **Remote `$ref`s.** Error responses and the security scheme resolve to the absolute URL of `v2.0/common/zoho-analytics-api-common.json` on `raw.githubusercontent.com`. Bundle the spec if your tooling cannot fetch remote refs.
- **Response keys are `200`/`204`, `4XX` and `500`.** There is no `default` response.
- **No vendor extensions.** These files carry no `x-*` keys, so any OpenAPI 3.1 tool can consume them as-is.
- **Input is `CONFIG`, not a JSON body.** Generated SDK methods take `CONFIG` as a string of JSON; serialize your object before passing it.
- **Auth is per-operation.** Read the required scope from `security[0].iam-oauth2-schema` of the operation, not from a global default — there is no root-level `security` block.
- **Descriptions are the documentation.** Operation descriptions carry behaviour notes and permission requirements, and most also include an error-code table (code → meaning → resolution); read them before handling failures.

**Choosing a file from a task** — map the intent to the domain, then load only that file:

```
"create a workspace", "rename a folder"        → workspace-management-grouped-api.json
"add a column", "add a formula", "query table" → data-modeling-schema-grouped-api.json
"import a CSV", "export a view", "add rows"    → data-operations-grouped-api.json
"list views", "delete a view", "tag a view"    → views-management-grouped-api.json
"create a chart", "update a dashboard"         → reports-dashboards-grouped-api.json
"share a view", "embed URL", "public link"     → share-publish-grouped-api.json
"email this report every Monday"               → schedules-alerts-grouped-api.json
"add a user", "create a group", "custom role"  → user-groups-grouped-api.json
"which org am I in", "what is my plan"         → org-management-grouped-api.json
"train a model", "what-if analysis"            → dsml-grouped-api.json
```

---

## Using the specs

### Explore

Import any `v2.0/*.json` file into Swagger UI, Redoc, Postman, Insomnia or Stoplight. Use the raw URL directly:

```
https://raw.githubusercontent.com/zoho/analytics-oas/refs/heads/main/v2.0/<domain-slug>-grouped-api.json
```

### Generate an SDK

1. In SwaggerHub, go to **Design → Import API** and paste the raw URL of the domain file.
2. Go to **Codegen → Client SDK** and pick your language.

Or with the OpenAPI Generator CLI:

```bash
openapi-generator-cli generate \
  -i v2.0/workspace-management-grouped-api.json \
  -g python \
  -o ./sdk/workspace-management
```

Generate per domain, or bundle several files first if you want one client for the whole API.

---

## Support

- [Zoho Analytics API documentation](https://www.zoho.com/analytics/api/v2/)
- [Zoho Developer Console](https://api-console.zoho.com)
- Email: [support@zohoanalytics.com](mailto:support@zohoanalytics.com)
