# Work Item Tracking API

## Overview

The Work Item Tracking API client provides access to Azure DevOps work items, queries, and work item types. It allows you to create, update, and manage work items programmatically.

## Installation

```bash
npm install azure-devops-node-api
```

## Authentication

Authenticate using a Personal Access Token (PAT) to access the Work Item Tracking API.

```typescript
import * as azdev from "azure-devops-node-api";

// Your organization URL and personal access token
const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";

// Connect to Azure DevOps
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get Work Item Tracking API client
const witApi = await connection.getWorkItemTrackingApi();
```

## Common Methods

### ⭐ getWorkItem

**Purpose**: Retrieves a single work item by ID.

**Signature**:
```typescript
function getWorkItem(
    id: number,
    project?: string,
    fields?: string[],
    asOf?: Date,
    expand?: WorkItemExpand
): Promise<WorkItem>;
```

**Parameters**:
- `id`: The work item ID to retrieve
- `project`: (Optional) Project ID or name containing the work item
- `fields`: (Optional) Specific fields to retrieve
- `asOf`: (Optional) Date to retrieve work item as of
- `expand`: (Optional) The expand options for work item properties

**Returns**: A Promise that resolves to a WorkItem object.

**Example**:
```typescript
// Get a work item by ID
const workItem = await witApi.getWorkItem(42);
console.log(`Work Item Title: ${workItem.fields["System.Title"]}`);

// Get specific fields
const workItemFields = await witApi.getWorkItem(
    42,
    undefined,
    ["System.Title", "System.State", "System.AssignedTo"]
);
```

### ⭐ getWorkItems

**Purpose**: Retrieves multiple work items by their IDs.

**Signature**:
```typescript
function getWorkItems(
    ids: number[],
    project?: string,
    fields?: string[],
    asOf?: Date,
    expand?: WorkItemExpand
): Promise<WorkItem[]>;
```

**Parameters**:
- `ids`: Array of work item IDs to retrieve
- `project`: (Optional) Project ID or name
- `fields`: (Optional) Specific fields to retrieve
- `asOf`: (Optional) Date to retrieve work items as of
- `expand`: (Optional) The expand options for work item properties

**Returns**: A Promise that resolves to an array of WorkItem objects.

**Example**:
```typescript
// Get multiple work items
const workItems = await witApi.getWorkItems([42, 43, 44]);
workItems.forEach(wi => {
    console.log(`Work Item ID: ${wi.id}, Title: ${wi.fields["System.Title"]}`);
});
```

### 🆕 createWorkItem

**Purpose**: Creates a new work item.

**Signature**:
```typescript
function createWorkItem(
    document: JsonPatchDocument,
    project: string,
    type: string,
    validateOnly?: boolean,
    bypassRules?: boolean
): Promise<WorkItem>;
```

**Parameters**:
- `document`: JsonPatchDocument with the fields to set
- `project`: Project ID or name
- `type`: Work item type (e.g., "Bug", "Task")
- `validateOnly`: (Optional) If true, validate without saving
- `bypassRules`: (Optional) If true, bypass work item rules

**Returns**: A Promise that resolves to the created WorkItem.

**Example**:
```typescript
import * as azdev from "azure-devops-node-api";

// Create JSON Patch document for the new work item
const patchDocument = [
    {
        op: "add",
        path: "/fields/System.Title",
        value: "New Bug: Application crashes on startup"
    },
    {
        op: "add",
        path: "/fields/System.Description",
        value: "Application crashes when starting on Windows 10."
    },
    {
        op: "add",
        path: "/fields/System.Tags",
        value: "Critical; Crash"
    }
];

// Create a new Bug
const newWorkItem = await witApi.createWorkItem(
    patchDocument,
    "MyProject",
    "Bug"
);

console.log(`Created Bug #${newWorkItem.id}`);
```

## Common Errors

Common errors when working with the Work Item Tracking API:

```typescript
try {
    const workItem = await witApi.getWorkItem(99999);
} catch (error) {
    if (error.statusCode === 404) {
        console.log("Work item not found. Please check the ID.");
    } else if (error.statusCode === 401) {
        console.log("Authentication error. Check your PAT token permissions.");
    } else {
        console.log(`Error retrieving work item: ${error.message}`);
    }
}
```

## See Also

- [Work Item Field Reference](./work-item-field-reference.md)
- [Work Item Query Tutorial](./tutorials/querying-work-items.md)
- [Azure DevOps Work Items REST API](https://docs.microsoft.com/en-us/rest/api/azure/devops/wit/?view=azure-devops-rest-6.0) 