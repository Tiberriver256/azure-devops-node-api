# Class: WorkItemTrackingApi

## Overview

The WorkItemTrackingApi class provides access to Azure DevOps work item tracking operations. It enables developers to create, retrieve, update, and delete work items, queries, and work item types within Azure DevOps projects. This class is essential for applications that need to interact with Azure DevOps work item tracking functionality programmatically.

**Package:** azure-devops-node-api  
**Implements:** IWorkItemTrackingApi  

## Description

The WorkItemTrackingApi class serves as the primary entry point for interacting with work items in Azure DevOps. It implements the IWorkItemTrackingApi interface and provides methods for managing the entire work item lifecycle. This class communicates with the Azure DevOps REST API endpoints related to work item tracking and translates the responses into strongly-typed TypeScript objects.

The class handles authentication, request formatting, and response parsing, allowing developers to focus on business logic rather than API communication details. It's designed to work with the WebApi client for connection management and uses promises for asynchronous operations.

### Key Features

- Create, update, and delete work items with full field control
- Execute and manage work item queries (WIQL)
- Retrieve work item type definitions and field information
- Manage work item links and attachments
- Support for batch operations on multiple work items

## Constructor

```typescript
constructor(baseUrl: string, handlers: VsoClientRequestHandler, options?: IRequestOptions)
```

Creates a new instance of the `WorkItemTrackingApi` class.

### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `baseUrl` | `string` | The base URL for the Azure DevOps organization | Yes |
| `handlers` | `VsoClientRequestHandler` | The request handlers for authentication and request processing | Yes |
| `options` | `IRequestOptions` | Optional settings for requests | No |

### Returns

A new instance of `WorkItemTrackingApi`.

### Example

```typescript
// Example of creating an instance of the class
// Note: Typically, you would use the WebApi.getWorkItemTrackingApi() method
// instead of creating this directly
const baseUrl = "https://dev.azure.com/organization";
const handlers = new VsoClientRequestHandler(authHandler);
const workItemTrackingApi = new WorkItemTrackingApi(baseUrl, handlers);
```

## Properties

| Name | Type | Description | Access |
|------|------|-------------|--------|
| `client` | `RestClient` | The underlying REST client used for API requests | protected |
| `baseUrl` | `string` | The base URL for the Azure DevOps organization | protected |

## Methods

- [getWorkItem](#getworkitem)
- [getWorkItems](#getworkitems)
- [createWorkItem](#createworkitem)
- [updateWorkItem](#updateworkitem)
- [deleteWorkItem](#deleteworkitem)
- [queryByWiql](#querybywiql)
- [getWorkItemType](#getworkitemtype)
- [getWorkItemTypes](#getworkitemtypes)

### getWorkItem

```typescript
getWorkItem(id: number, project?: string, fields?: string[], asOf?: Date, expand?: WorkItemExpand): Promise<WorkItem>
```

Retrieves a single work item by its ID.

#### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `id` | `number` | The ID of the work item to retrieve | Yes |
| `project` | `string` | The project containing the work item | No |
| `fields` | `string[]` | The fields to include in the work item | No |
| `asOf` | `Date` | The date to retrieve the work item as of | No |
| `expand` | `WorkItemExpand` | The expansion options for the work item | No |

#### Returns

`Promise<WorkItem>`: A promise that resolves to the requested work item

#### Example

```typescript
const workItemTrackingApi = await connection.getWorkItemTrackingApi();

const workItem = await workItemTrackingApi.getWorkItem(42, 
  "MyProject", 
  ["System.Title", "System.Description", "System.State"]
);

console.log(workItem.id, workItem.fields["System.Title"]);
// Output: 42 "Fix login page bug"
```

### getWorkItems

```typescript
getWorkItems(ids: number[], project?: string, fields?: string[], asOf?: Date, expand?: WorkItemExpand): Promise<WorkItem[]>
```

Retrieves multiple work items by their IDs.

#### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `ids` | `number[]` | The IDs of the work items to retrieve | Yes |
| `project` | `string` | The project containing the work items | No |
| `fields` | `string[]` | The fields to include in the work items | No |
| `asOf` | `Date` | The date to retrieve the work items as of | No |
| `expand` | `WorkItemExpand` | The expansion options for the work items | No |

#### Returns

`Promise<WorkItem[]>`: A promise that resolves to an array of work items

#### Example

```typescript
const workItemTrackingApi = await connection.getWorkItemTrackingApi();

const workItems = await workItemTrackingApi.getWorkItems([42, 43, 44], 
  "MyProject", 
  ["System.Title", "System.State"]
);

workItems.forEach(item => {
  console.log(`${item.id}: ${item.fields["System.Title"]} (${item.fields["System.State"]})`);
});
// Output: 
// 42: Fix login page bug (Active)
// 43: Update documentation (Resolved)
// 44: Implement new feature (New)
```

### createWorkItem

```typescript
createWorkItem(document: JsonPatchDocument, project: string, type: string, validateOnly?: boolean, bypassRules?: boolean): Promise<WorkItem>
```

Creates a new work item.

#### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `document` | `JsonPatchDocument` | The JSON Patch document defining the work item fields | Yes |
| `project` | `string` | The project in which to create the work item | Yes |
| `type` | `string` | The work item type (e.g., "Bug", "Task") | Yes |
| `validateOnly` | `boolean` | Whether to only validate the work item without creating it | No |
| `bypassRules` | `boolean` | Whether to bypass work item rules | No |

#### Returns

`Promise<WorkItem>`: A promise that resolves to the created work item

#### Example

```typescript
const workItemTrackingApi = await connection.getWorkItemTrackingApi();

const patchDocument = [
  {
    op: "add",
    path: "/fields/System.Title",
    value: "New bug in login page"
  },
  {
    op: "add",
    path: "/fields/System.Description",
    value: "Users cannot log in when using special characters in passwords"
  },
  {
    op: "add",
    path: "/fields/System.Tags",
    value: "Bug; Priority 1; Frontend"
  }
];

const newWorkItem = await workItemTrackingApi.createWorkItem(
  patchDocument,
  "MyProject",
  "Bug"
);

console.log(`Created work item #${newWorkItem.id}`);
```

## Usage Examples

### Basic Usage

```typescript
// Example showing basic usage of the class
import * as azdev from "azure-devops-node-api";

const organizationUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";

// Initialize the auth handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Initialize the connection to Azure DevOps Services
const connection = new azdev.WebApi(organizationUrl, authHandler);

// Get the WorkItemTrackingApi client
const workItemTrackingApi = await connection.getWorkItemTrackingApi();

// Get a work item
const workItem = await workItemTrackingApi.getWorkItem(42);
console.log(`Work Item #${workItem.id}: ${workItem.fields["System.Title"]}`);

// Get work item types
const workItemTypes = await workItemTrackingApi.getWorkItemTypes("MyProject");
workItemTypes.forEach(type => {
  console.log(`Work Item Type: ${type.name}`);
});
```

### Advanced Usage

```typescript
// Example showing more advanced usage with error handling and batch operations
import * as azdev from "azure-devops-node-api";
import { JsonPatchDocument, JsonPatchOperation } from "azure-devops-node-api/interfaces/common/VSSInterfaces";

async function updateMultipleWorkItems(connection, projectName, updates) {
  try {
    const workItemTrackingApi = await connection.getWorkItemTrackingApi();
    
    // Process each update in parallel
    const updatePromises = updates.map(async update => {
      const patchDocument = [];
      
      // Add each field update as a patch operation
      Object.entries(update.fields).forEach(([field, value]) => {
        patchDocument.push({
          op: "add",
          path: `/fields/${field}`,
          value: value
        });
      });
      
      // Update the work item
      return workItemTrackingApi.updateWorkItem(
        patchDocument, 
        update.id, 
        projectName,
        false,  // validateOnly
        false   // bypassRules
      );
    });
    
    // Wait for all updates to complete
    const results = await Promise.all(updatePromises);
    return results;
  } catch (error) {
    console.error("Error updating work items:", error.message);
    throw error;
  }
}

// Usage
const organizationUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(organizationUrl, authHandler);

const updates = [
  {
    id: 42,
    fields: {
      "System.State": "Active",
      "System.AssignedTo": "user@example.com",
      "Microsoft.VSTS.Common.Priority": 1
    }
  },
  {
    id: 43,
    fields: {
      "System.State": "Resolved",
      "System.ResolvedBy": "user@example.com",
      "System.ResolvedReason": "Fixed"
    }
  }
];

updateMultipleWorkItems(connection, "MyProject", updates)
  .then(results => {
    console.log(`Updated ${results.length} work items successfully`);
  })
  .catch(error => {
    console.error("Failed to update work items:", error);
  });
```

## Common Scenarios

### Scenario 1: Creating and Linking Work Items

Creating related work items and establishing links between them.

```typescript
async function createLinkedWorkItems(connection, projectName) {
  const workItemTrackingApi = await connection.getWorkItemTrackingApi();
  
  // Create parent user story
  const storyPatch = [
    { op: "add", path: "/fields/System.Title", value: "Implement login page" },
    { op: "add", path: "/fields/System.Description", value: "Create a secure login page with OAuth support" },
    { op: "add", path: "/fields/System.WorkItemType", value: "User Story" }
  ];
  
  const story = await workItemTrackingApi.createWorkItem(storyPatch, projectName, "User Story");
  console.log(`Created User Story #${story.id}`);
  
  // Create child task
  const taskPatch = [
    { op: "add", path: "/fields/System.Title", value: "Implement OAuth flow" },
    { op: "add", path: "/fields/System.Description", value: "Implement the OAuth authentication flow" },
    { op: "add", path: "/fields/System.WorkItemType", value: "Task" },
    // Create a link to the parent story
    { 
      op: "add", 
      path: "/relations/-", 
      value: {
        rel: "System.LinkTypes.Hierarchy-Reverse",
        url: story.url
      }
    }
  ];
  
  const task = await workItemTrackingApi.createWorkItem(taskPatch, projectName, "Task");
  console.log(`Created Task #${task.id} linked to User Story #${story.id}`);
  
  return { story, task };
}
```

### Scenario 2: Running a Saved Query

Executing a saved work item query and processing the results.

```typescript
async function runSavedQuery(connection, projectName, queryId) {
  const workItemTrackingApi = await connection.getWorkItemTrackingApi();
  
  // Get the query by ID
  const query = await workItemTrackingApi.getQuery(projectName, queryId);
  console.log(`Running query: ${query.name}`);
  
  // Execute the query
  const queryResult = await workItemTrackingApi.queryById(queryId, projectName);
  
  // If it's a flat query, work item references are in workItems
  // If it's a hierarchical query, they're in workItemRelations
  if (queryResult.queryType === 1) { // Flat query
    const workItemIds = queryResult.workItems.map(wi => wi.id);
    
    // Get the full work items with all fields
    if (workItemIds.length > 0) {
      const workItems = await workItemTrackingApi.getWorkItems(
        workItemIds, 
        projectName,
        ["System.Id", "System.Title", "System.State", "System.AssignedTo"]
      );
      
      return workItems;
    }
  }
  
  return [];
}
```

## Error Handling

The WorkItemTrackingApi methods can throw various errors related to authentication, permissions, invalid requests, or server issues. It's important to implement proper error handling.

```typescript
try {
  const workItemTrackingApi = await connection.getWorkItemTrackingApi();
  const workItem = await workItemTrackingApi.getWorkItem(42);
  console.log(workItem);
} catch (error) {
  if (error.statusCode === 401) {
    console.error("Authentication failed. Check your credentials.");
  } else if (error.statusCode === 403) {
    console.error("You don't have permission to access this work item.");
  } else if (error.statusCode === 404) {
    console.error("Work item not found. It may have been deleted or you may have provided an incorrect ID.");
  } else {
    console.error("Unexpected error:", error.message);
  }
}
```

## Related Classes

- [WorkItem](WorkItem.md)
- [WorkItemType](WorkItemType.md)
- [WorkItemQueryResult](WorkItemQueryResult.md)
- [WebApi](WebApi.md)

## See Also

- [API Reference](../api-reference/index.md)
- [Getting Started](../getting-started/index.md)
- [Work Item Tracking Guide](../guides/work-item-tracking.md) 