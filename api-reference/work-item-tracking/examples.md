# WorkItemTrackingApi Examples

[◀ Back to WorkItemTrackingApi](./README.md)

---

## Overview

This page provides comprehensive examples of using the `WorkItemTrackingApi` class for common work item tracking operations in Azure DevOps.

## Basic Examples

### Getting a Work Item

```typescript
import * as azdev from "azure-devops-node-api";

async function getWorkItem() {
    // Create a connection to Azure DevOps
    const orgUrl = "https://dev.azure.com/myorganization";
    const token = "YOUR_PERSONAL_ACCESS_TOKEN";
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get the WorkItemTrackingApi instance
    const workItemTrackingApi = await connection.getWorkItemTrackingApi();
    
    // Get a work item by ID
    const workItem = await workItemTrackingApi.getWorkItem(42);
    
    console.log(`Work Item ${workItem.id}: ${workItem.fields["System.Title"]}`);
    console.log(`State: ${workItem.fields["System.State"]}`);
    console.log(`Assigned To: ${workItem.fields["System.AssignedTo"]?.displayName || "Unassigned"}`);
}

getWorkItem().catch(error => console.error(error));
```

### Creating a Work Item

```typescript
import * as azdev from "azure-devops-node-api";
import { JsonPatchDocument, JsonPatchOperation, Operation } from "azure-devops-node-api/interfaces/common/VSSInterfaces";

async function createWorkItem() {
    // Create a connection to Azure DevOps
    const orgUrl = "https://dev.azure.com/myorganization";
    const token = "YOUR_PERSONAL_ACCESS_TOKEN";
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get the WorkItemTrackingApi instance
    const workItemTrackingApi = await connection.getWorkItemTrackingApi();
    
    // Create a patch document with the work item fields
    const patchDocument: JsonPatchDocument = [
        {
            op: Operation.Add,
            path: "/fields/System.Title",
            value: "New Bug: Application crashes on startup"
        },
        {
            op: Operation.Add,
            path: "/fields/System.Description",
            value: "The application crashes when started on Windows 11."
        },
        {
            op: Operation.Add,
            path: "/fields/System.Tags",
            value: "Bug; Crash; Windows 11"
        }
    ];
    
    // Create a new bug work item
    const workItem = await workItemTrackingApi.createWorkItem(
        patchDocument,
        "MyProject",
        "Bug"
    );
    
    console.log(`Created Bug #${workItem.id}: ${workItem.fields["System.Title"]}`);
}

createWorkItem().catch(error => console.error(error));
```

### Updating a Work Item

```typescript
import * as azdev from "azure-devops-node-api";
import { JsonPatchDocument, JsonPatchOperation, Operation } from "azure-devops-node-api/interfaces/common/VSSInterfaces";

async function updateWorkItem() {
    // Create a connection to Azure DevOps
    const orgUrl = "https://dev.azure.com/myorganization";
    const token = "YOUR_PERSONAL_ACCESS_TOKEN";
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get the WorkItemTrackingApi instance
    const workItemTrackingApi = await connection.getWorkItemTrackingApi();
    
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
            value: "Assigning to developer for investigation."
        }
    ];
    
    // Update the work item
    const workItem = await workItemTrackingApi.updateWorkItem(
        patchDocument,
        42  // Work item ID
    );
    
    console.log(`Updated Work Item #${workItem.id}`);
    console.log(`New State: ${workItem.fields["System.State"]}`);
    console.log(`Assigned To: ${workItem.fields["System.AssignedTo"].displayName}`);
}

updateWorkItem().catch(error => console.error(error));
```

## Advanced Examples

### Querying Work Items with WIQL

```typescript
import * as azdev from "azure-devops-node-api";
import { Wiql } from "azure-devops-node-api/interfaces/WorkItemTrackingInterfaces";

async function queryWorkItems() {
    // Create a connection to Azure DevOps
    const orgUrl = "https://dev.azure.com/myorganization";
    const token = "YOUR_PERSONAL_ACCESS_TOKEN";
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get the WorkItemTrackingApi instance
    const workItemTrackingApi = await connection.getWorkItemTrackingApi();
    
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
    
    if (workItemIds.length === 0) {
        console.log("No work items found.");
        return;
    }
    
    // Get the work items with all fields
    const workItems = await workItemTrackingApi.getWorkItems(workItemIds);
    
    // Display the work items
    workItems.forEach(workItem => {
        console.log(`${workItem.id}: ${workItem.fields["System.Title"]} (${workItem.fields["System.State"]})`);
    });
}

queryWorkItems().catch(error => console.error(error));
```

### Adding an Attachment to a Work Item

```typescript
import * as azdev from "azure-devops-node-api";
import { JsonPatchDocument, Operation } from "azure-devops-node-api/interfaces/common/VSSInterfaces";
import * as fs from "fs";

async function addAttachment() {
    // Create a connection to Azure DevOps
    const orgUrl = "https://dev.azure.com/myorganization";
    const token = "YOUR_PERSONAL_ACCESS_TOKEN";
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get the WorkItemTrackingApi instance
    const workItemTrackingApi = await connection.getWorkItemTrackingApi();
    
    // Read the file to upload
    const filePath = "./screenshot.png";
    const fileContent = fs.readFileSync(filePath);
    
    // Upload the attachment
    const attachment = await workItemTrackingApi.createAttachment(
        fileContent,
        "screenshot.png",
        "image/png"
    );
    
    console.log(`Attachment created: ${attachment.url}`);
    
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
    
    console.log(`Attachment added to Work Item #${workItem.id}`);
}

addAttachment().catch(error => console.error(error));
```

### Working with Work Item Relations

```typescript
import * as azdev from "azure-devops-node-api";
import { JsonPatchDocument, Operation } from "azure-devops-node-api/interfaces/common/VSSInterfaces";

async function addWorkItemRelation() {
    // Create a connection to Azure DevOps
    const orgUrl = "https://dev.azure.com/myorganization";
    const token = "YOUR_PERSONAL_ACCESS_TOKEN";
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get the WorkItemTrackingApi instance
    const workItemTrackingApi = await connection.getWorkItemTrackingApi();
    
    // Create a patch document to add a parent-child relationship
    const patchDocument: JsonPatchDocument = [
        {
            op: Operation.Add,
            path: "/relations/-",
            value: {
                rel: "System.LinkTypes.Hierarchy-Reverse",
                url: `${orgUrl}/_apis/wit/workItems/40`,  // Parent work item ID
                attributes: {
                    comment: "This task implements part of the user story"
                }
            }
        }
    ];
    
    // Update the work item with the relation
    const workItem = await workItemTrackingApi.updateWorkItem(
        patchDocument,
        42  // Child work item ID
    );
    
    console.log(`Relation added to Work Item #${workItem.id}`);
    
    // Get the work item with relations expanded
    const workItemWithRelations = await workItemTrackingApi.getWorkItem(
        workItem.id,
        undefined,
        undefined,
        undefined,
        4  // WorkItemExpand.Relations
    );
    
    // Display the relations
    if (workItemWithRelations.relations) {
        console.log("Work Item Relations:");
        workItemWithRelations.relations.forEach(relation => {
            console.log(`- Type: ${relation.rel}`);
            console.log(`  URL: ${relation.url}`);
            console.log(`  Comment: ${relation.attributes?.comment || "No comment"}`);
        });
    }
}

addWorkItemRelation().catch(error => console.error(error));
```

## See Also

- [Common Scenarios](./common-scenarios.md)
- [Error Handling](./error-handling.md)
- [WorkItemTrackingApi Methods](./methods/README.md)

---

## Navigation

- [WorkItemTrackingApi Overview](./README.md)
- [Constructor](./constructor.md)
- [Properties](./properties.md)
- [Methods](./methods/README.md)
- [Common Scenarios](./common-scenarios.md)
- [Error Handling](./error-handling.md) 