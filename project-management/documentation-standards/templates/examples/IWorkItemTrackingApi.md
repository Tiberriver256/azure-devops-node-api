# Interface: IWorkItemTrackingApi

## Overview

The IWorkItemTrackingApi interface defines the contract for interacting with Azure DevOps work item tracking functionality. It provides methods for creating, retrieving, updating, and deleting work items, as well as managing work item types, queries, and fields. This interface is essential for applications that need to programmatically manage work items in Azure DevOps.

**Package:** azure-devops-node-api  
**Extends:** IVssApi  
**Implemented by:** WorkItemTrackingApi  

## Description

The IWorkItemTrackingApi interface serves as the primary contract for work item tracking operations in the Azure DevOps Node API. It defines a comprehensive set of methods that map to the Azure DevOps REST API endpoints for work item tracking. This interface follows the promise-based asynchronous pattern, with most methods returning promises that resolve to strongly-typed objects.

The interface is designed to provide a complete and consistent API surface for all work item tracking operations, abstracting away the underlying HTTP requests and response handling. It includes methods for the entire work item lifecycle, from creation to deletion, as well as methods for working with work item types, fields, and queries.

### Key Features

- Comprehensive work item CRUD operations
- Work item query (WIQL) execution and management
- Work item type and field definition retrieval
- Work item link and attachment management
- Support for batch operations on multiple work items

## Properties

This interface does not define any properties.

## Methods

- [getWorkItem](#getworkitem)
- [getWorkItems](#getworkitems)
- [createWorkItem](#createworkitem)
- [updateWorkItem](#updateworkitem)
- [deleteWorkItem](#deleteworkitem)
- [queryByWiql](#querybywiql)
- [getWorkItemType](#getworkitemtype)
- [getWorkItemTypes](#getworkitemtypes)
- [getFields](#getfields)
- [getField](#getfield)

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

### updateWorkItem

```typescript
updateWorkItem(document: JsonPatchDocument, id: number, project?: string, validateOnly?: boolean, bypassRules?: boolean): Promise<WorkItem>
```

Updates an existing work item.

#### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `document` | `JsonPatchDocument` | The JSON Patch document defining the field updates | Yes |
| `id` | `number` | The ID of the work item to update | Yes |
| `project` | `string` | The project containing the work item | No |
| `validateOnly` | `boolean` | Whether to only validate the update without applying it | No |
| `bypassRules` | `boolean` | Whether to bypass work item rules | No |

#### Returns

`Promise<WorkItem>`: A promise that resolves to the updated work item

### deleteWorkItem

```typescript
deleteWorkItem(id: number, project?: string, destroy?: boolean): Promise<void>
```

Deletes a work item.

#### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `id` | `number` | The ID of the work item to delete | Yes |
| `project` | `string` | The project containing the work item | No |
| `destroy` | `boolean` | Whether to permanently delete the work item | No |

#### Returns

`Promise<void>`: A promise that resolves when the work item is deleted

### queryByWiql

```typescript
queryByWiql(wiql: Wiql, project?: string, team?: string, timePrecision?: boolean, top?: number): Promise<WorkItemQueryResult>
```

Executes a work item query using the Work Item Query Language (WIQL).

#### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `wiql` | `Wiql` | The WIQL query to execute | Yes |
| `project` | `string` | The project in which to execute the query | No |
| `team` | `string` | The team context for the query | No |
| `timePrecision` | `boolean` | Whether to include time precision in date fields | No |
| `top` | `number` | The maximum number of results to return | No |

#### Returns

`Promise<WorkItemQueryResult>`: A promise that resolves to the query results

### getWorkItemType

```typescript
getWorkItemType(project: string, type: string): Promise<WorkItemType>
```

Retrieves a work item type definition.

#### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `project` | `string` | The project containing the work item type | Yes |
| `type` | `string` | The name of the work item type | Yes |

#### Returns

`Promise<WorkItemType>`: A promise that resolves to the work item type definition

### getWorkItemTypes

```typescript
getWorkItemTypes(project: string): Promise<WorkItemType[]>
```

Retrieves all work item type definitions in a project.

#### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `project` | `string` | The project for which to retrieve work item types | Yes |

#### Returns

`Promise<WorkItemType[]>`: A promise that resolves to an array of work item type definitions

### getFields

```typescript
getFields(): Promise<WorkItemField[]>
```

Retrieves all work item fields.

#### Returns

`Promise<WorkItemField[]>`: A promise that resolves to an array of work item field definitions

### getField

```typescript
getField(fieldNameOrRefName: string): Promise<WorkItemField>
```

Retrieves a specific work item field.

#### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `fieldNameOrRefName` | `string` | The name or reference name of the field | Yes |

#### Returns

`Promise<WorkItemField>`: A promise that resolves to the field definition

## Type Definitions

### WorkItemExpand

```typescript
enum WorkItemExpand {
    None = 0,
    Relations = 1,
    Fields = 2,
    Links = 3,
    All = 4
}
```

Defines the expansion options for work item retrieval.

| Value | Description |
|-------|-------------|
| `None` | No expansion, returns basic work item data only |
| `Relations` | Includes work item relations |
| `Fields` | Includes all work item fields |
| `Links` | Includes work item links |
| `All` | Includes all expandable data |

### Wiql

```typescript
interface Wiql {
    query: string;
}
```

Represents a Work Item Query Language (WIQL) query.

| Property | Type | Description | Required |
|----------|------|-------------|----------|
| `query` | `string` | The WIQL query string | Yes |

### JsonPatchDocument

```typescript
type JsonPatchDocument = JsonPatchOperation[];
```

Represents a JSON Patch document for creating or updating work items.

### JsonPatchOperation

```typescript
interface JsonPatchOperation {
    op: string;
    path: string;
    value?: any;
    from?: string;
}
```

Represents a single operation in a JSON Patch document.

| Property | Type | Description | Required |
|----------|------|-------------|----------|
| `op` | `string` | The operation type (add, remove, replace, etc.) | Yes |
| `path` | `string` | The target path for the operation | Yes |
| `value` | `any` | The value to apply in the operation | No |
| `from` | `string` | The source path for copy/move operations | No |

## Usage Examples

### Implementation Example

```typescript
// Example showing how to implement this interface
import * as azdev from "azure-devops-node-api";
import { IRequestHandler } from "azure-devops-node-api/interfaces/common/VsoBaseInterfaces";
import { 
    IWorkItemTrackingApi, 
    WorkItem, 
    WorkItemExpand, 
    Wiql 
} from "azure-devops-node-api/interfaces/WorkItemTrackingInterfaces";

class CustomWorkItemTrackingApi implements IWorkItemTrackingApi {
    private baseUrl: string;
    private handler: IRequestHandler;
    
    constructor(baseUrl: string, handler: IRequestHandler) {
        this.baseUrl = baseUrl;
        this.handler = handler;
    }
    
    async getWorkItem(id: number, project?: string, fields?: string[], asOf?: Date, expand?: WorkItemExpand): Promise<WorkItem> {
        // Implementation would make HTTP request to Azure DevOps API
        console.log(`Getting work item ${id} from project ${project || 'any'}`);
        
        // This is a simplified mock implementation
        return {
            id: id,
            rev: 1,
            fields: {
                "System.Title": "Example Work Item",
                "System.State": "Active"
            },
            url: `${this.baseUrl}/_apis/wit/workItems/${id}`
        };
    }
    
    // Implement other methods...
    // For brevity, not all methods are implemented in this example
}
```

### Usage with API

```typescript
// Example showing how to use this interface with the API
import * as azdev from "azure-devops-node-api";
import { IWorkItemTrackingApi } from "azure-devops-node-api/interfaces/WorkItemTrackingInterfaces";

async function manageWorkItems() {
    const organizationUrl = "https://dev.azure.com/your-organization";
    const token = "your-personal-access-token";

    // Initialize the auth handler
    const authHandler = azdev.getPersonalAccessTokenHandler(token);

    // Initialize the connection to Azure DevOps Services
    const connection = new azdev.WebApi(organizationUrl, authHandler);

    // Get the work item tracking API client
    const workItemTrackingApi: IWorkItemTrackingApi = await connection.getWorkItemTrackingApi();

    // Use the interface methods
    const workItem = await workItemTrackingApi.getWorkItem(42, 
        "MyProject", 
        ["System.Title", "System.State"]
    );
    
    console.log(`Work Item #${workItem.id}: ${workItem.fields["System.Title"]}`);
    
    // Query for work items
    const wiqlQuery = {
        query: "SELECT [System.Id], [System.Title], [System.State] FROM WorkItems WHERE [System.TeamProject] = @project AND [System.WorkItemType] = 'Bug' AND [System.State] = 'Active'"
    };
    
    const queryResults = await workItemTrackingApi.queryByWiql(wiqlQuery, "MyProject");
    console.log(`Found ${queryResults.workItems.length} active bugs`);
}

manageWorkItems().catch(error => console.error(error));
```

## Common Scenarios

### Scenario 1: Retrieving and Updating Work Items

A common scenario is retrieving work items and updating their fields.

```typescript
async function updateWorkItemState(
    workItemTrackingApi: IWorkItemTrackingApi, 
    workItemId: number, 
    newState: string
): Promise<WorkItem> {
    // First, get the current work item
    const workItem = await workItemTrackingApi.getWorkItem(workItemId);
    console.log(`Current state: ${workItem.fields["System.State"]}`);
    
    // Create a JSON patch document to update the state
    const patchDocument = [
        {
            op: "replace",
            path: "/fields/System.State",
            value: newState
        }
    ];
    
    // Update the work item
    const updatedWorkItem = await workItemTrackingApi.updateWorkItem(
        patchDocument,
        workItemId
    );
    
    console.log(`Updated state: ${updatedWorkItem.fields["System.State"]}`);
    return updatedWorkItem;
}
```

### Scenario 2: Creating Work Items with Links

Another common scenario is creating work items with links to other work items.

```typescript
async function createLinkedWorkItem(
    workItemTrackingApi: IWorkItemTrackingApi,
    project: string,
    parentId: number,
    title: string,
    description: string
): Promise<WorkItem> {
    // Create a JSON patch document for a new task linked to a parent
    const patchDocument = [
        {
            op: "add",
            path: "/fields/System.Title",
            value: title
        },
        {
            op: "add",
            path: "/fields/System.Description",
            value: description
        },
        {
            op: "add",
            path: "/relations/-",
            value: {
                rel: "System.LinkTypes.Hierarchy-Reverse",
                url: `https://dev.azure.com/organization/_apis/wit/workItems/${parentId}`,
                attributes: {
                    comment: "Linked during creation"
                }
            }
        }
    ];
    
    // Create the work item
    const newWorkItem = await workItemTrackingApi.createWorkItem(
        patchDocument,
        project,
        "Task"
    );
    
    console.log(`Created task #${newWorkItem.id} linked to parent #${parentId}`);
    return newWorkItem;
}
```

## Interface Extensions

The IWorkItemTrackingApi interface can be extended to add custom functionality:

```typescript
// Example of extending the interface
interface ExtendedWorkItemTrackingApi extends IWorkItemTrackingApi {
    // Add methods for common operations
    getActiveWorkItems(project: string, workItemType: string): Promise<WorkItem[]>;
    assignWorkItem(id: number, assignToIdentity: string): Promise<WorkItem>;
    addComment(id: number, comment: string): Promise<WorkItemComment>;
}

// Implementation of the extended interface
class EnhancedWorkItemTrackingApi implements ExtendedWorkItemTrackingApi {
    private baseApi: IWorkItemTrackingApi;
    
    constructor(baseApi: IWorkItemTrackingApi) {
        this.baseApi = baseApi;
    }
    
    // Implement the extended methods
    async getActiveWorkItems(project: string, workItemType: string): Promise<WorkItem[]> {
        const wiql = {
            query: `SELECT [System.Id] FROM WorkItems WHERE [System.TeamProject] = '${project}' AND [System.WorkItemType] = '${workItemType}' AND [System.State] = 'Active'`
        };
        
        const result = await this.baseApi.queryByWiql(wiql, project);
        const ids = result.workItems.map(wi => wi.id);
        
        if (ids.length === 0) {
            return [];
        }
        
        return await this.baseApi.getWorkItems(ids, project);
    }
    
    // Delegate the original interface methods to the base API
    getWorkItem(id: number, project?: string, fields?: string[], asOf?: Date, expand?: WorkItemExpand): Promise<WorkItem> {
        return this.baseApi.getWorkItem(id, project, fields, asOf, expand);
    }
    
    // Implement other methods by delegation...
}
```

## Type Guards

Type guards can be useful when working with objects that might implement the IWorkItemTrackingApi interface:

```typescript
// Example of a type guard for this interface
function isWorkItemTrackingApi(obj: any): obj is IWorkItemTrackingApi {
    return (
        obj &&
        typeof obj === 'object' &&
        'getWorkItem' in obj &&
        typeof obj.getWorkItem === 'function' &&
        'createWorkItem' in obj &&
        typeof obj.createWorkItem === 'function'
    );
}

// Usage example
function processWorkItemApi(api: any) {
    if (isWorkItemTrackingApi(api)) {
        // TypeScript now knows this is an IWorkItemTrackingApi
        api.getWorkItem(42).then(workItem => {
            console.log(workItem.fields["System.Title"]);
        });
    } else {
        console.error("The provided object is not a work item tracking API");
    }
}
```

## Related Interfaces

- [IVssApi](./IVssApi.md) - Base interface for all Azure DevOps API clients
- [IGitApi](./IGitApi.md) - Interface for Git operations, often used alongside work item tracking
- [IBuildApi](./IBuildApi.md) - Interface for build operations, which can be linked to work items

## Implementing Classes

- [WorkItemTrackingApi](../classes/WorkItemTrackingApi.md) - The primary implementation of this interface
- [WorkItemTrackingRestClient](../classes/WorkItemTrackingRestClient.md) - Lower-level REST client used by the WorkItemTrackingApi

## See Also

- [API Reference](../../api-reference/index.md)
- [Getting Started with Work Items](../../getting-started/work-items.md)
- [Work Item Tracking Concepts](../../concepts/work-item-tracking.md)
- [Azure DevOps REST API: Work Item Tracking](https://docs.microsoft.com/en-us/rest/api/azure/devops/wit/?view=azure-devops-rest-6.0) 