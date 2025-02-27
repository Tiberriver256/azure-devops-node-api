# Work Item Tracking API Reference

**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > [Work Item Tracking API](./README.md) > API Reference

## Overview

The `WorkItemTrackingApi` class provides a comprehensive set of methods for interacting with work items in Azure DevOps. This API client allows you to create, retrieve, update, and delete work items, as well as execute queries and access work item type information.

## Obtaining an Instance

The preferred way to obtain a `WorkItemTrackingApi` instance is through the WebApi Core:

```typescript
import * as azdev from "azure-devops-node-api";

// Initialize authentication
const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token"; 
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Create a connection to Azure DevOps
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get the Work Item Tracking API client
const witApi = await connection.getWorkItemTrackingApi();
```

## Key Methods

Here are the most frequently used methods in the Work Item Tracking API:

### Work Item Operations

| Method | Description | Complexity |
|--------|-------------|:----------:|
| [**getWorkItem**](./methods/get-work-item.md) | Retrieves a specific work item by ID | Low |
| [**getWorkItems**](./methods/get-work-items.md) | Retrieves multiple work items by their IDs | Medium |
| [**createWorkItem**](./methods/create-work-item.md) | Creates a new work item | High |
| [**updateWorkItem**](./methods/update-work-item.md) | Updates an existing work item | High |
| [**queryByWiql**](./methods/query-work-items.md) | Executes a WIQL query to retrieve work items | High |

### Work Item Type Operations

| Method | Description | Complexity |
|--------|-------------|:----------:|
| **getWorkItemType** | Retrieves a specific work item type definition | Low |
| **getWorkItemTypes** | Retrieves all work item types in a project | Low |
| **getFields** | Gets all work item fields | Low |

## Common Work Item Fields

When working with work items, you'll frequently access these common fields:

| Field | Reference Name | Description |
|-------|---------------|-------------|
| Title | `System.Title` | The title/name of the work item |
| State | `System.State` | Current state (e.g., "Active", "Closed") |
| Assigned To | `System.AssignedTo` | User assigned to the work item |
| Description | `System.Description` | Full description/content |
| Work Item Type | `System.WorkItemType` | Type of work item (e.g., "Bug", "User Story") |
| Created Date | `System.CreatedDate` | When the work item was created |
| Created By | `System.CreatedBy` | User who created the work item |
| Changed Date | `System.ChangedDate` | When the work item was last modified |
| Changed By | `System.ChangedBy` | User who last modified the work item |
| Area Path | `System.AreaPath` | Area classification |
| Iteration Path | `System.IterationPath` | Iteration/sprint assignment |

## Example Scenarios

### Basic Work Item Retrieval

```typescript
// Get a single work item with ID 42
const workItem = await witApi.getWorkItem(42);
console.log(`Title: ${workItem.fields["System.Title"]}`);
console.log(`State: ${workItem.fields["System.State"]}`);
```

### Retrieving Multiple Work Items

```typescript
// Get multiple work items by their IDs
const workItems = await witApi.getWorkItems([42, 43, 44]);
workItems.forEach(workItem => {
  console.log(`ID: ${workItem.id}, Title: ${workItem.fields["System.Title"]}`);
});
```

### Creating a New Work Item

```typescript
// Create a document with fields to set
const patchDocument = [
  {
    op: "add",
    path: "/fields/System.Title",
    value: "New bug from API"
  },
  {
    op: "add",
    path: "/fields/System.Description",
    value: "This bug was created via the API"
  },
  {
    op: "add",
    path: "/fields/System.AssignedTo",
    value: "user@example.com"
  }
];

// Create a new Bug work item
const newWorkItem = await witApi.createWorkItem(
  {}, // Custom headers (can be empty)
  patchDocument,
  "MyProject",
  "Bug"
);

console.log(`Created Bug #${newWorkItem.id}`);
```

### Updating an Existing Work Item

```typescript
// Create a document with field updates
const patchDocument = [
  {
    op: "replace",
    path: "/fields/System.State",
    value: "Active"
  },
  {
    op: "add",
    path: "/fields/System.Tags",
    value: "API; Updated"
  }
];

// Update work item with ID 42
const updatedWorkItem = await witApi.updateWorkItem(
  {}, // Custom headers (can be empty)
  patchDocument,
  42,
  "MyProject"
);

console.log(`Updated work item #${updatedWorkItem.id}`);
```

### Querying Work Items with WIQL

```typescript
// Create a WIQL query to find active bugs
const wiql = {
  query: `SELECT [System.Id], [System.Title], [System.State]
          FROM WorkItems
          WHERE [System.WorkItemType] = 'Bug'
          AND [System.State] = 'Active'
          ORDER BY [System.ChangedDate] DESC`
};

// Execute the query
const queryResult = await witApi.queryByWiql(wiql, "MyProject");

// If the query returns work item IDs, you'll need to get the work items
if (queryResult.workItems && queryResult.workItems.length > 0) {
  const ids = queryResult.workItems.map(wi => wi.id);
  const workItems = await witApi.getWorkItems(ids);
  
  workItems.forEach(workItem => {
    console.log(`ID: ${workItem.id}, Title: ${workItem.fields["System.Title"]}`);
  });
}
```

## Common Errors

| Error | Common Causes | Solutions |
|-------|--------------|-----------|
| 401 Unauthorized | Invalid or expired token | Refresh your authentication token |
| 403 Forbidden | Insufficient permissions | Request access to the project or work item |
| 404 Not Found | Work item doesn't exist or invalid ID | Verify the work item ID and project |
| 400 Bad Request | Invalid field values or operations | Check the field names and values in your request |
| 429 Too Many Requests | Exceeding API rate limits | Implement exponential backoff retry logic |

## Related Documentation

For more detailed information on specific methods, please refer to:

- [getWorkItem Method](./methods/get-work-item.md)
- [getWorkItems Method](./methods/get-work-items.md)
- [createWorkItem Method](./methods/create-work-item.md)
- [updateWorkItem Method](./methods/update-work-item.md)
- [queryByWiql Method](./methods/query-work-items.md)

## See Also

- [WebApi Core Documentation](../webapi-core/webapi-core.md)
- [Authentication Handlers](../webapi-core/authentication-handlers.md)
- [Work Items REST API](https://learn.microsoft.com/en-us/rest/api/azure/devops/wit/?view=azure-devops-rest-7.1) 