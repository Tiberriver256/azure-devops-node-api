**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > [WorkItemTrackingApi](./README.md) > Common Scenarios

# Common Scenarios for WorkItemTrackingApi

This document provides examples for common usage scenarios of the WorkItemTrackingApi class, demonstrating how to accomplish real-world tasks using the API.

## Contents

- [Creating and Linking Work Items](#creating-and-linking-work-items)
- [Running a Saved Query](#running-a-saved-query)
- [Batch Updating Work Items](#batch-updating-work-items)
- [Creating Work Item Hierarchies](#creating-work-item-hierarchies)
- [Working with Attachments](#working-with-attachments)

---

## Creating and Linking Work Items

This scenario demonstrates how to create related work items and establish links between them.

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

💡 **Tip**: Use "System.LinkTypes.Hierarchy-Reverse" to create a child-to-parent relationship and "System.LinkTypes.Hierarchy-Forward" to create a parent-to-child relationship.

---

## Running a Saved Query

This scenario shows how to execute a saved work item query and process the results.

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

📝 **Note**: Query results only contain work item references. To get the full work item details, you need to make a separate call to getWorkItems().

---

## Batch Updating Work Items

This scenario demonstrates how to update multiple work items in a single batch operation.

```typescript
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

// Usage example
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

⚠️ **Caution**: While this approach is more efficient than updating items sequentially, be aware that failures in one update won't stop the other updates. Make sure to handle partial success scenarios.

---

## Creating Work Item Hierarchies

This scenario shows how to create a work item hierarchy with a parent Epic, child Feature, and grandchild User Stories.

```typescript
async function createWorkItemHierarchy(connection, projectName) {
  const workItemTrackingApi = await connection.getWorkItemTrackingApi();
  
  // Create Epic (top level)
  const epicPatch = [
    { op: "add", path: "/fields/System.Title", value: "New Product Launch" },
    { op: "add", path: "/fields/System.Description", value: "Launch our new product in Q3" }
  ];
  const epic = await workItemTrackingApi.createWorkItem(epicPatch, projectName, "Epic");
  
  // Create Feature (child of Epic)
  const featurePatch = [
    { op: "add", path: "/fields/System.Title", value: "User Authentication System" },
    { op: "add", path: "/fields/System.Description", value: "Implement secure user authentication" },
    { 
      op: "add", 
      path: "/relations/-", 
      value: {
        rel: "System.LinkTypes.Hierarchy-Reverse", // Child to parent link
        url: epic.url
      }
    }
  ];
  const feature = await workItemTrackingApi.createWorkItem(featurePatch, projectName, "Feature");
  
  // Create User Stories (children of Feature)
  const story1Patch = [
    { op: "add", path: "/fields/System.Title", value: "Login Page" },
    { op: "add", path: "/fields/System.Description", value: "Create login page with username/password" },
    { 
      op: "add", 
      path: "/relations/-", 
      value: {
        rel: "System.LinkTypes.Hierarchy-Reverse", // Child to parent link
        url: feature.url
      }
    }
  ];
  
  const story2Patch = [
    { op: "add", path: "/fields/System.Title", value: "Password Reset" },
    { op: "add", path: "/fields/System.Description", value: "Implement password reset functionality" },
    { 
      op: "add", 
      path: "/relations/-", 
      value: {
        rel: "System.LinkTypes.Hierarchy-Reverse", // Child to parent link
        url: feature.url
      }
    }
  ];
  
  // Create both stories in parallel
  const [story1, story2] = await Promise.all([
    workItemTrackingApi.createWorkItem(story1Patch, projectName, "User Story"),
    workItemTrackingApi.createWorkItem(story2Patch, projectName, "User Story")
  ]);
  
  return {
    epic,
    feature,
    stories: [story1, story2]
  };
}
```

📊 **Result Structure**:
```
Epic: "New Product Launch"
└── Feature: "User Authentication System"
    ├── User Story: "Login Page"
    └── User Story: "Password Reset"
```

---

## Working with Attachments

This scenario demonstrates how to add an attachment to a work item.

```typescript
async function addAttachmentToWorkItem(
  connection, 
  projectName, 
  workItemId, 
  filePath, 
  attachmentComment
) {
  const workItemTrackingApi = await connection.getWorkItemTrackingApi();
  const fs = require('fs');
  
  // Read the file
  const fileContent = fs.readFileSync(filePath);
  const fileName = filePath.split('/').pop();
  
  try {
    // 1. Upload the attachment
    const attachment = await workItemTrackingApi.createAttachment(
      fileContent,
      fileName
    );
    
    // 2. Add the attachment reference to the work item
    const patchDocument = [
      {
        op: "add",
        path: "/relations/-",
        value: {
          rel: "AttachedFile",
          url: attachment.url,
          attributes: {
            comment: attachmentComment || "File attachment"
          }
        }
      }
    ];
    
    // Update the work item with the attachment reference
    const updatedWorkItem = await workItemTrackingApi.updateWorkItem(
      patchDocument,
      workItemId,
      projectName
    );
    
    return updatedWorkItem;
  } catch (error) {
    console.error("Error attaching file:", error.message);
    throw error;
  }
}

// Usage example
addAttachmentToWorkItem(
  connection,
  "MyProject",
  42,
  "./screenshots/error.png",
  "Screenshot showing the error message"
)
  .then(workItem => {
    console.log(`Attachment added to work item #${workItem.id}`);
  })
  .catch(error => {
    console.error("Failed to add attachment:", error);
  });
```

💡 **Tip**: Attachment uploads are a two-step process: first upload the file to get an attachment reference, then update the work item to link to that attachment.

## See Also

- [WorkItemTrackingApi Class](./README.md)
- [Methods Documentation](./methods/README.md)
- [Error Handling](./error-handling.md)
- [Work Item Tracking Concepts](../../concepts/work-item-tracking.md)