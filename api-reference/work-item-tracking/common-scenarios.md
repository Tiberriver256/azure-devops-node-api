# WorkItemTrackingApi Common Scenarios

[◀ Back to WorkItemTrackingApi](./README.md)

---

## Overview

This page covers common scenarios and patterns for using the `WorkItemTrackingApi` class in real-world applications.

## Authentication

### Using Personal Access Tokens (Recommended)

```typescript
import * as azdev from "azure-devops-node-api";

// Create a connection with a Personal Access Token
const orgUrl = "https://dev.azure.com/myorganization";
const token = "YOUR_PERSONAL_ACCESS_TOKEN"; // https://dev.azure.com/YOUR_ORG/_usersSettings/tokens
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
const workItemTrackingApi = await connection.getWorkItemTrackingApi();
```

### Using OAuth

```typescript
import * as azdev from "azure-devops-node-api";

// Create a connection with an OAuth token
const orgUrl = "https://dev.azure.com/myorganization";
const token = "YOUR_OAUTH_ACCESS_TOKEN";
const authHandler = azdev.getBearerHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
const workItemTrackingApi = await connection.getWorkItemTrackingApi();
```

## Working with Work Items

### Retrieving Work Items

#### Getting a Single Work Item with Specific Fields

```typescript
// Get a work item with specific fields
const workItem = await workItemTrackingApi.getWorkItem(
    42,                                      // Work item ID
    "MyProject",                             // Project name
    ["System.Title", "System.Description"],  // Fields to include
    undefined,                               // Current version (no asOf date)
    4                                        // WorkItemExpand.Relations
);
```

#### Getting Multiple Work Items in a Batch

```typescript
// Get multiple work items in a single request
const workItemIds = [42, 43, 44, 45];
const workItems = await workItemTrackingApi.getWorkItems(
    workItemIds,
    "MyProject",
    ["System.Title", "System.State", "System.AssignedTo"]
);

workItems.forEach(workItem => {
    console.log(`${workItem.id}: ${workItem.fields["System.Title"]}`);
});
```

### Creating Work Items

#### Creating a Basic Work Item

```typescript
import { JsonPatchDocument, Operation } from "azure-devops-node-api/interfaces/common/VSSInterfaces";

// Create a patch document with the work item fields
const patchDocument: JsonPatchDocument = [
    {
        op: Operation.Add,
        path: "/fields/System.Title",
        value: "New Task: Implement feature X"
    },
    {
        op: Operation.Add,
        path: "/fields/System.Description",
        value: "Implement the new feature X as described in the requirements document."
    },
    {
        op: Operation.Add,
        path: "/fields/System.AssignedTo",
        value: "user@example.com"
    }
];

// Create a new task work item
const workItem = await workItemTrackingApi.createWorkItem(
    patchDocument,
    "MyProject",
    "Task"
);

console.log(`Created Task #${workItem.id}: ${workItem.fields["System.Title"]}`);
```

#### Creating a Work Item with a Parent Link

```typescript
import { JsonPatchDocument, Operation } from "azure-devops-node-api/interfaces/common/VSSInterfaces";

// Create a patch document with fields and a parent link
const patchDocument: JsonPatchDocument = [
    {
        op: Operation.Add,
        path: "/fields/System.Title",
        value: "Implement login form validation"
    },
    {
        op: Operation.Add,
        path: "/fields/System.Description",
        value: "Add client-side validation to the login form."
    },
    {
        op: Operation.Add,
        path: "/relations/-",
        value: {
            rel: "System.LinkTypes.Hierarchy-Reverse",
            url: `${orgUrl}/_apis/wit/workItems/40`,  // Parent work item ID (User Story)
            attributes: {
                comment: "This task implements part of the user story"
            }
        }
    }
];

// Create a new task work item with a parent link
const workItem = await workItemTrackingApi.createWorkItem(
    patchDocument,
    "MyProject",
    "Task"
);
```

### Updating Work Items

#### Updating Work Item Fields

```typescript
import { JsonPatchDocument, Operation } from "azure-devops-node-api/interfaces/common/VSSInterfaces";

// Create a patch document with the updates
const patchDocument: JsonPatchDocument = [
    {
        op: Operation.Replace,
        path: "/fields/System.State",
        value: "Active"
    },
    {
        op: Operation.Replace,
        path: "/fields/System.AssignedTo",
        value: "user@example.com"
    },
    {
        op: Operation.Add,
        path: "/fields/System.History",
        value: "Assigning to developer for implementation."
    }
];

// Update the work item
const workItem = await workItemTrackingApi.updateWorkItem(
    patchDocument,
    42  // Work item ID
);
```

#### Adding a Comment to a Work Item

```typescript
import { JsonPatchDocument, Operation } from "azure-devops-node-api/interfaces/common/VSSInterfaces";

// Create a patch document to add a comment
const patchDocument: JsonPatchDocument = [
    {
        op: Operation.Add,
        path: "/fields/System.History",
        value: "This issue is blocked by the network configuration. Waiting for IT to resolve."
    }
];

// Update the work item with the comment
const workItem = await workItemTrackingApi.updateWorkItem(
    patchDocument,
    42  // Work item ID
);
```

## Querying Work Items

### Basic WIQL Query

```typescript
import { Wiql } from "azure-devops-node-api/interfaces/WorkItemTrackingInterfaces";

// Create a WIQL query
const wiql: Wiql = {
    query: `SELECT [System.Id], [System.Title], [System.State]
            FROM WorkItems
            WHERE [System.TeamProject] = 'MyProject'
            AND [System.WorkItemType] = 'Bug'
            AND [System.State] <> 'Closed'
            ORDER BY [System.ChangedDate] DESC`
};

// Execute the query
const queryResult = await workItemTrackingApi.queryByWiql(wiql);

// Get the work item IDs from the query result
const workItemIds = queryResult.workItems.map(wi => wi.id);

if (workItemIds.length > 0) {
    // Get the work items with all fields
    const workItems = await workItemTrackingApi.getWorkItems(workItemIds);
    
    // Process the work items
    workItems.forEach(workItem => {
        console.log(`${workItem.id}: ${workItem.fields["System.Title"]}`);
    });
}
```

### Query with Pagination

```typescript
import { Wiql } from "azure-devops-node-api/interfaces/WorkItemTrackingInterfaces";

async function queryWorkItemsWithPagination() {
    // Create a WIQL query
    const wiql: Wiql = {
        query: `SELECT [System.Id]
                FROM WorkItems
                WHERE [System.TeamProject] = 'MyProject'
                AND [System.WorkItemType] = 'Bug'
                ORDER BY [System.Id]`
    };
    
    // Execute the query
    const queryResult = await workItemTrackingApi.queryByWiql(wiql);
    
    // Get all work item IDs from the query result
    const allWorkItemIds = queryResult.workItems.map(wi => wi.id);
    
    // Process work items in batches of 200
    const batchSize = 200;
    for (let i = 0; i < allWorkItemIds.length; i += batchSize) {
        const batchIds = allWorkItemIds.slice(i, i + batchSize);
        
        // Get the batch of work items
        const workItems = await workItemTrackingApi.getWorkItems(
            batchIds,
            undefined,
            ["System.Id", "System.Title", "System.State"]
        );
        
        // Process the batch
        console.log(`Processing batch ${i / batchSize + 1} (${workItems.length} items)`);
        workItems.forEach(workItem => {
            // Process each work item
            console.log(`${workItem.id}: ${workItem.fields["System.Title"]}`);
        });
    }
}
```

## Working with Attachments

### Adding an Attachment to a Work Item

```typescript
import { JsonPatchDocument, Operation } from "azure-devops-node-api/interfaces/common/VSSInterfaces";
import * as fs from "fs";

async function addAttachmentToWorkItem() {
    // Read the file to upload
    const filePath = "./screenshot.png";
    const fileContent = fs.readFileSync(filePath);
    
    // Upload the attachment
    const attachment = await workItemTrackingApi.createAttachment(
        fileContent,
        "screenshot.png",
        "image/png"
    );
    
    // Create a patch document to add the attachment to a work item
    const patchDocument: JsonPatchDocument = [
        {
            op: Operation.Add,
            path: "/relations/-",
            value: {
                rel: "AttachedFile",
                url: attachment.url,
                attributes: {
                    comment: "Screenshot of the error"
                }
            }
        }
    ];
    
    // Update the work item with the attachment
    const workItem = await workItemTrackingApi.updateWorkItem(
        patchDocument,
        42  // Work item ID
    );
}
```

## Working with Work Item Types

### Getting Work Item Types for a Project

```typescript
async function getWorkItemTypes() {
    // Get all work item types for a project
    const workItemTypes = await workItemTrackingApi.getWorkItemTypes("MyProject");
    
    console.log("Available Work Item Types:");
    workItemTypes.forEach(wit => {
        console.log(`- ${wit.name} (${wit.description})`);
    });
}
```

### Getting Work Item Type Fields

```typescript
async function getWorkItemTypeFields() {
    // Get fields for a specific work item type
    const fields = await workItemTrackingApi.getWorkItemTypeFields(
        "MyProject",
        "Bug"
    );
    
    console.log("Fields for Bug work item type:");
    fields.forEach(field => {
        console.log(`- ${field.name} (${field.referenceName})`);
    });
}
```

## See Also

- [Examples](./examples.md)
- [Error Handling](./error-handling.md)
- [WorkItemTrackingApi Methods](./methods/README.md)

---

## Navigation

- [WorkItemTrackingApi Overview](./README.md)
- [Constructor](./constructor.md)
- [Properties](./properties.md)
- [Methods](./methods/README.md)
- [Examples](./examples.md)
- [Error Handling](./error-handling.md) 