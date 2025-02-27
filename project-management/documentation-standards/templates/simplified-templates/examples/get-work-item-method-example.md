# getWorkItem

**Client**: Work Item Tracking API

## Purpose

Retrieves a single work item by its ID. Use this method when you need to access a specific work item's details.

## Signature

```typescript
function getWorkItem(
    id: number,
    project?: string,
    fields?: string[],
    asOf?: Date,
    expand?: WorkItemExpand
): Promise<WorkItem>;
```

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | number | Yes | The ID of the work item to retrieve |
| project | string | No | Project ID or name containing the work item |
| fields | string[] | No | Specific fields to retrieve. If not specified, all fields are returned |
| asOf | Date | No | Date to retrieve work item as of (historical lookup) |
| expand | WorkItemExpand | No | The expand options for work item properties |

## Returns

A Promise that resolves to a WorkItem object containing the requested fields and data.

## Example

```typescript
import * as azdev from "azure-devops-node-api";

// Connection setup
const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get Work Item Tracking API client
const witApi = await connection.getWorkItemTrackingApi();

// Get a work item with all fields
const workItem = await witApi.getWorkItem(42);
console.log(`Title: ${workItem.fields["System.Title"]}`);
console.log(`State: ${workItem.fields["System.State"]}`);

// Get only specific fields
const workItemLimited = await witApi.getWorkItem(
    42,
    undefined,
    ["System.Title", "System.State", "System.AssignedTo"]
);
console.log(`Assigned To: ${workItemLimited.fields["System.AssignedTo"]?.displayName}`);
```

## Common Errors

```typescript
try {
    const workItem = await witApi.getWorkItem(99999);
} catch (error) {
    if (error.statusCode === 404) {
        console.log("Work item not found. Please check the ID.");
    } else if (error.statusCode === 401) {
        console.log("Authentication error. Check your PAT token permissions.");
    } else if (error.statusCode === 403) {
        console.log("Authorization error. You don't have permissions to access this work item.");
    } else {
        console.log(`Error retrieving work item: ${error.message}`);
    }
}
```

## See Also

- [getWorkItems](./get-work-items-method.md) - Method to retrieve multiple work items at once
- [Work Item Tracking API Overview](./work-item-tracking-api.md)
- [Work Item Fields Reference](./work-item-fields-reference.md) 