# `getWorkItems`

**Signature:**
```typescript
public async getWorkItems(
    ids: number[], 
    project?: string,
    fields?: string[],
    asOf?: Date,
    expand?: WorkItemExpand
): Promise<WorkItem[]>
```

## Description

Gets work items for a list of work item IDs. This method retrieves the specified work items with their fields, relations, and history depending on the expand parameter value. Use this method when you need to retrieve multiple work items in a single call.

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `ids` | `number[]` | Yes | Array of work item IDs to retrieve. Maximum of 200 IDs allowed in a single request. |
| `project` | `string` | No | Project ID or project name. Used to validate that the work items exist in the specified project. |
| `fields` | `string[]` | No | Array of field names to retrieve. If not specified, all fields are returned. |
| `asOf` | `Date` | No | Historical version filter that specifies the work item version to retrieve. Default: current version. |
| `expand` | `WorkItemExpand` | No | Flag that controls which properties are retrieved. See [Expand Options](#expand-options) below. Default: `WorkItemExpand.None`. |

### Expand Options

The `expand` parameter controls which related data is returned with the work items.

| Value | Description |
|-------|-------------|
| `WorkItemExpand.None` | Returns basic work item information only. |
| `WorkItemExpand.Relations` | Returns work item relationship references. |
| `WorkItemExpand.Fields` | Returns all work item fields. |
| `WorkItemExpand.Links` | Returns links to related artifacts. |
| `WorkItemExpand.All` | Returns all work item data including relationships, fields, and links. |

## Return Value

**Type:** `Promise<WorkItem[]>`

Returns a Promise that resolves to an array of WorkItem objects. Each WorkItem object contains the requested work item fields and other properties based on the expand parameter.

### Return Object Properties

| Property | Type | Description |
|----------|------|-------------|
| `id` | `number` | The ID of the work item. |
| `rev` | `number` | The revision number of the work item. |
| `fields` | `{ [key: string]: any }` | Dictionary of work item field values. Keys are field reference names. |
| `relations` | `WorkItemRelation[]` | Array of related work items (only returned when expand includes Relations). |
| `_links` | `any` | Object containing links to related resources (only returned when expand includes Links). |
| `url` | `string` | URL to the work item in the REST API. |

## Exceptions

| Exception | Condition |
|-----------|-----------|
| `UnauthorizedError` | Thrown when the user doesn't have permission to view the requested work items (HTTP 401 or 403). |
| `WorkItemNotFoundException` | Thrown when one or more of the requested work items don't exist (HTTP 404). |
| `TooManyRequestsError` | Thrown when the request exceeds the rate limit (HTTP 429). |
| `ValidationError` | Thrown when more than 200 IDs are provided (HTTP 400). |

## Examples

### Basic Usage

```typescript
// Import required modules
import * as azdev from "azure-devops-node-api";

// Set up authentication and connection
const orgUrl = "https://dev.azure.com/yourorganization";
const token = "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get work items by IDs
const witClient = await connection.getWorkItemTrackingApi();
const workItemIds = [1, 2, 3];
const workItems = await witClient.getWorkItems(workItemIds);

// Access work item data
workItems.forEach(workItem => {
    console.log(`Work Item ${workItem.id}: ${workItem.fields["System.Title"]}`);
});
```

### With Optional Parameters

```typescript
// Get work items with specific fields and expansion
const workItemIds = [1, 2, 3];
const projectName = "MyProject";
const fields = ["System.Id", "System.Title", "System.State", "System.AssignedTo"];
const expand = WorkItemExpand.Relations;

const workItems = await witClient.getWorkItems(
    workItemIds,
    projectName,
    fields,
    undefined, // asOf - using default (current version)
    expand
);

// Access field data
workItems.forEach(workItem => {
    console.log(`Title: ${workItem.fields["System.Title"]}`);
    console.log(`State: ${workItem.fields["System.State"]}`);
    
    // Access relations (available because we set expand to include Relations)
    if (workItem.relations) {
        console.log(`Related items: ${workItem.relations.length}`);
        workItem.relations.forEach(relation => {
            console.log(`  ${relation.rel}: ${relation.url}`);
        });
    }
});
```

### Error Handling

```typescript
try {
    const workItemIds = [1, 2, 3];
    const workItems = await witClient.getWorkItems(workItemIds);
    
    // Process work items
    console.log(`Retrieved ${workItems.length} work items`);
} catch (error) {
    // Handle specific error types
    if (error.statusCode === 401 || error.statusCode === 403) {
        console.error("Authentication failed or insufficient permissions.");
    } else if (error.statusCode === 404) {
        console.error("One or more work items not found.");
    } else if (error.statusCode === 429) {
        console.error("Rate limit exceeded. Please wait before retrying.");
    } else {
        console.error(`Error retrieving work items: ${error.message}`);
    }
}
```

### Advanced Scenario: Historical Version

```typescript
// Get work items as they existed at a specific date
const workItemIds = [1, 2, 3];
const asOfDate = new Date("2023-06-01T00:00:00Z");
const historicalWorkItems = await witClient.getWorkItems(
    workItemIds,
    undefined, // project
    undefined, // fields
    asOfDate,
    WorkItemExpand.All
);

// Compare current vs. historical state
const currentWorkItems = await witClient.getWorkItems(
    workItemIds,
    undefined,
    undefined,
    undefined,
    WorkItemExpand.All
);

// Show differences
workItemIds.forEach((id, index) => {
    const historical = historicalWorkItems[index];
    const current = currentWorkItems[index];
    
    console.log(`Work Item ${id}:`);
    console.log(`  Historical Title (${asOfDate.toDateString()}): ${historical.fields["System.Title"]}`);
    console.log(`  Current Title: ${current.fields["System.Title"]}`);
    console.log(`  Historical State: ${historical.fields["System.State"]}`);
    console.log(`  Current State: ${current.fields["System.State"]}`);
});
```

## Response Example

```json
[
    {
        "id": 1,
        "rev": 3,
        "fields": {
            "System.Id": 1,
            "System.Title": "Implement authentication feature",
            "System.State": "Active",
            "System.AssignedTo": {
                "displayName": "Jane Smith",
                "id": "d291b0c4-a05c-4ea6-8df1-4b41d5f39e8f"
            },
            "System.IterationPath": "MyProject\\Sprint 1",
            "System.WorkItemType": "User Story",
            "Microsoft.VSTS.Common.Priority": 1
        },
        "relations": [
            {
                "rel": "System.LinkTypes.Hierarchy-Forward",
                "url": "https://dev.azure.com/organization/_apis/wit/workItems/2",
                "attributes": {
                    "isLocked": false,
                    "name": "Child"
                }
            }
        ],
        "_links": {
            "self": {
                "href": "https://dev.azure.com/organization/_apis/wit/workItems/1"
            },
            "workItemUpdates": {
                "href": "https://dev.azure.com/organization/_apis/wit/workItems/1/updates"
            }
        },
        "url": "https://dev.azure.com/organization/_apis/wit/workItems/1"
    }
]
```

## Related Methods

- [`getWorkItem`](./getWorkItem.md) - Gets a single work item with more detailed options
- [`createWorkItem`](./createWorkItem.md) - Creates a new work item
- [`updateWorkItem`](./updateWorkItem.md) - Updates an existing work item
- [`getWorkItemsBatch`](./getWorkItemsBatch.md) - Gets work items with more complex filtering options

## See Also

- [Conceptual Guide: Work Item Tracking](../../concepts/work-item-tracking.md)
- [Tutorial: Working with Work Items](../../tutorials/work-with-work-items.md)
- [API Reference: WorkItemTrackingApi](../classes/WorkItemTrackingApi.md)
- [Azure DevOps REST API: Work Items](https://docs.microsoft.com/en-us/rest/api/azure/devops/wit/work-items)

## API Details

**Client:** `WorkItemTrackingApi`  
**API Version:** 5.0+  
**Permission Scope:** WorkItems.Read 