**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > [WorkItemTrackingApi](./README.md) > Examples

# WorkItemTrackingApi Examples

This document provides code examples demonstrating how to use the WorkItemTrackingApi class.

## Contents

- [Authentication and Setup](#authentication-and-setup)
- [Basic Operations](#basic-operations)
- [Advanced Operations](#advanced-operations)
- [Working with WIQL](#working-with-wiql)
- [Complete Application Examples](#complete-application-examples)

---

## Authentication and Setup

Before using WorkItemTrackingApi, you need to set up authentication and initialize the client:

```typescript
import * as azdev from "azure-devops-node-api";

// Your organization URL and personal access token
const organizationUrl = "https://dev.azure.com/your-organization";
const token = process.env.AZURE_DEVOPS_TOKEN; // Store token in environment variable

// Create authentication handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Create a connection to Azure DevOps
const connection = new azdev.WebApi(organizationUrl, authHandler);

// Get the WorkItemTrackingApi instance
async function getWorkItemApi() {
  return await connection.getWorkItemTrackingApi();
}
```

💡 **Tip**: Store your personal access token in environment variables rather than hardcoding it in your source code.

---

## Basic Operations

### Get a Single Work Item

```typescript
async function getSingleWorkItem(id) {
  const workItemTrackingApi = await getWorkItemApi();
  
  // Get work item with specific fields
  const workItem = await workItemTrackingApi.getWorkItem(
    id,
    "MyProject",
    ["System.Id", "System.Title", "System.State", "System.AssignedTo"]
  );
  
  console.log(`Work Item #${workItem.id}: ${workItem.fields["System.Title"]}`);
  console.log(`State: ${workItem.fields["System.State"]}`);
  
  if (workItem.fields["System.AssignedTo"]) {
    console.log(`Assigned to: ${workItem.fields["System.AssignedTo"].displayName}`);
  } else {
    console.log("Not assigned");
  }
  
  return workItem;
}
```

### Get Multiple Work Items

```typescript
async function getMultipleWorkItems(ids) {
  const workItemTrackingApi = await getWorkItemApi();
  
  // Get multiple work items in a single call
  const workItems = await workItemTrackingApi.getWorkItems(
    ids,
    "MyProject",
    ["System.Id", "System.Title", "System.State"]
  );
  
  // Create a simple work item summary
  const summary = workItems.map(item => ({
    id: item.id,
    title: item.fields["System.Title"],
    state: item.fields["System.State"]
  }));
  
  console.table(summary);
  
  return workItems;
}
```

### Create a Work Item

```typescript
async function createNewBug(title, description, priority = 2) {
  const workItemTrackingApi = await getWorkItemApi();
  
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
      path: "/fields/Microsoft.VSTS.Common.Priority",
      value: priority
    }
  ];
  
  const newBug = await workItemTrackingApi.createWorkItem(
    patchDocument,
    "MyProject",
    "Bug"
  );
  
  console.log(`Bug created: #${newBug.id} - ${newBug.fields["System.Title"]}`);
  return newBug;
}
```

### Update a Work Item

```typescript
async function updateWorkItemState(id, newState, comment = null) {
  const workItemTrackingApi = await getWorkItemApi();
  
  const patchDocument = [
    {
      op: "add",
      path: "/fields/System.State",
      value: newState
    }
  ];
  
  // Add a comment if provided
  if (comment) {
    patchDocument.push({
      op: "add",
      path: "/fields/System.History",
      value: comment
    });
  }
  
  const updatedWorkItem = await workItemTrackingApi.updateWorkItem(
    patchDocument,
    id,
    "MyProject"
  );
  
  console.log(`Work item #${id} updated to state: ${newState}`);
  return updatedWorkItem;
}
```

---

## Advanced Operations

### Add Tags to Work Items

```typescript
async function addTagsToWorkItem(id, tags) {
  const workItemTrackingApi = await getWorkItemApi();
  
  // First get the work item to check existing tags
  const workItem = await workItemTrackingApi.getWorkItem(
    id,
    "MyProject",
    ["System.Tags"]
  );
  
  // Get existing tags or empty string if none
  const existingTags = workItem.fields["System.Tags"] || "";
  
  // Combine existing and new tags
  const existingTagList = existingTags ? existingTags.split('; ') : [];
  const uniqueTags = [...new Set([...existingTagList, ...tags])]; // Remove duplicates
  const combinedTags = uniqueTags.join('; ');
  
  // Update the work item with combined tags
  const patchDocument = [
    {
      op: "add",
      path: "/fields/System.Tags",
      value: combinedTags
    }
  ];
  
  const updatedWorkItem = await workItemTrackingApi.updateWorkItem(
    patchDocument,
    id,
    "MyProject"
  );
  
  console.log(`Tags added to work item #${id}: ${tags.join(', ')}`);
  return updatedWorkItem;
}
```

### Add Work Item Comment

```typescript
async function addWorkItemComment(id, comment) {
  const workItemTrackingApi = await getWorkItemApi();
  
  const patchDocument = [
    {
      op: "add",
      path: "/fields/System.History",
      value: comment
    }
  ];
  
  const updatedWorkItem = await workItemTrackingApi.updateWorkItem(
    patchDocument,
    id,
    "MyProject"
  );
  
  console.log(`Comment added to work item #${id}`);
  return updatedWorkItem;
}
```

### Add Work Item Relations

```typescript
async function createRelatedWorkItems(parentId, childTitles, childType = "Task") {
  const workItemTrackingApi = await getWorkItemApi();
  
  // First get the parent work item to get its URL
  const parentWorkItem = await workItemTrackingApi.getWorkItem(parentId, "MyProject");
  
  // Create child work items with relationship to parent
  const childPromises = childTitles.map(title => {
    const patchDocument = [
      {
        op: "add",
        path: "/fields/System.Title",
        value: title
      },
      {
        op: "add",
        path: "/relations/-",
        value: {
          rel: "System.LinkTypes.Hierarchy-Reverse",
          url: parentWorkItem.url
        }
      }
    ];
    
    return workItemTrackingApi.createWorkItem(
      patchDocument,
      "MyProject",
      childType
    );
  });
  
  const children = await Promise.all(childPromises);
  
  console.log(`Created ${children.length} child work items for parent #${parentId}`);
  return children;
}
```

---

## Working with WIQL

WIQL (Work Item Query Language) allows you to run complex queries against work items.

### Simple WIQL Query

```typescript
async function queryRecentBugs() {
  const workItemTrackingApi = await getWorkItemApi();
  
  // Create a WIQL query to find recent active bugs
  const wiql = {
    query: `
      SELECT [System.Id], [System.Title], [System.State], [System.CreatedDate]
      FROM WorkItems
      WHERE [System.WorkItemType] = 'Bug'
        AND [System.State] = 'Active'
        AND [System.CreatedDate] >= @Today - 7
      ORDER BY [System.CreatedDate] DESC
    `
  };
  
  // Execute the query
  const queryResult = await workItemTrackingApi.queryByWiql(wiql, "MyProject");
  
  // Get the work item IDs from the query result
  const ids = queryResult.workItems.map(item => item.id);
  
  if (ids.length === 0) {
    console.log("No matching work items found");
    return [];
  }
  
  // Get the full work items with the fields we need
  const workItems = await workItemTrackingApi.getWorkItems(
    ids,
    "MyProject",
    ["System.Id", "System.Title", "System.State", "System.CreatedDate"]
  );
  
  console.log(`Found ${workItems.length} recent active bugs`);
  return workItems;
}
```

### Advanced WIQL Query with Custom Fields

```typescript
async function queryPriorityBugsAssignedToTeam(teamMembers, minPriority = 2) {
  const workItemTrackingApi = await getWorkItemApi();
  
  // Convert team members array to WIQL format for IN clause
  const assignedToClause = teamMembers
    .map(member => `'${member}'`)
    .join(', ');
  
  // Create a WIQL query with multiple conditions
  const wiql = {
    query: `
      SELECT [System.Id], [System.Title], [System.State], [System.AssignedTo], [Microsoft.VSTS.Common.Priority]
      FROM WorkItems
      WHERE [System.WorkItemType] = 'Bug'
        AND [System.AssignedTo] IN (${assignedToClause})
        AND [Microsoft.VSTS.Common.Priority] <= ${minPriority}
        AND [System.State] <> 'Closed'
        AND [System.State] <> 'Resolved'
      ORDER BY [Microsoft.VSTS.Common.Priority] ASC, [System.CreatedDate] DESC
    `
  };
  
  // Execute the query
  const queryResult = await workItemTrackingApi.queryByWiql(wiql, "MyProject");
  
  // Get the work item IDs
  const ids = queryResult.workItems.map(item => item.id);
  
  if (ids.length === 0) {
    console.log("No matching work items found");
    return [];
  }
  
  // Get the full work items with the fields we need
  const workItems = await workItemTrackingApi.getWorkItems(
    ids,
    "MyProject",
    ["System.Id", "System.Title", "System.State", "System.AssignedTo", "Microsoft.VSTS.Common.Priority"]
  );
  
  console.log(`Found ${workItems.length} priority bugs assigned to the team`);
  return workItems;
}
```

---

## Complete Application Examples

### Work Item Dashboard Script

```typescript
import * as azdev from "azure-devops-node-api";
import * as fs from "fs";

async function generateWorkItemDashboard() {
  // Setup
  const organizationUrl = process.env.AZURE_DEVOPS_ORG_URL;
  const token = process.env.AZURE_DEVOPS_TOKEN;
  const projectName = process.env.AZURE_DEVOPS_PROJECT;
  
  if (!organizationUrl || !token || !projectName) {
    throw new Error("Environment variables for Azure DevOps not set");
  }
  
  const authHandler = azdev.getPersonalAccessTokenHandler(token);
  const connection = new azdev.WebApi(organizationUrl, authHandler);
  const workItemTrackingApi = await connection.getWorkItemTrackingApi();
  
  // Get work items in different states
  async function getWorkItemsByState(state) {
    const wiql = {
      query: `
        SELECT [System.Id], [System.Title], [System.State], [System.AssignedTo], [Microsoft.VSTS.Common.Priority]
        FROM WorkItems
        WHERE [System.TeamProject] = '${projectName}'
          AND [System.WorkItemType] IN ('Bug', 'Task', 'User Story')
          AND [System.State] = '${state}'
        ORDER BY [Microsoft.VSTS.Common.Priority] ASC
      `
    };
    
    const queryResult = await workItemTrackingApi.queryByWiql(wiql);
    const ids = queryResult.workItems.map(item => item.id);
    
    if (ids.length === 0) return [];
    
    return await workItemTrackingApi.getWorkItems(
      ids,
      projectName,
      ["System.Id", "System.Title", "System.State", "System.AssignedTo", 
       "Microsoft.VSTS.Common.Priority", "System.WorkItemType"]
    );
  }
  
  // Get work items by different states
  const [newItems, activeItems, resolvedItems] = await Promise.all([
    getWorkItemsByState("New"),
    getWorkItemsByState("Active"),
    getWorkItemsByState("Resolved")
  ]);
  
  // Format items for report
  function formatWorkItems(items) {
    return items.map(item => ({
      id: item.id,
      title: item.fields["System.Title"],
      type: item.fields["System.WorkItemType"],
      priority: item.fields["Microsoft.VSTS.Common.Priority"],
      assignedTo: item.fields["System.AssignedTo"] ? 
        item.fields["System.AssignedTo"].displayName : "Unassigned"
    }));
  }
  
  // Generate report
  const report = {
    generatedAt: new Date().toISOString(),
    project: projectName,
    summary: {
      total: newItems.length + activeItems.length + resolvedItems.length,
      new: newItems.length,
      active: activeItems.length,
      resolved: resolvedItems.length
    },
    items: {
      new: formatWorkItems(newItems),
      active: formatWorkItems(activeItems),
      resolved: formatWorkItems(resolvedItems)
    }
  };
  
  // Save report
  fs.writeFileSync(
    `work-item-dashboard-${new Date().toISOString().split('T')[0]}.json`,
    JSON.stringify(report, null, 2)
  );
  
  console.log(`Dashboard generated with ${report.summary.total} work items`);
  return report;
}

// Run the dashboard generator
generateWorkItemDashboard()
  .then(report => {
    console.log("Work Item Summary:");
    console.log(`- New: ${report.summary.new}`);
    console.log(`- Active: ${report.summary.active}`);
    console.log(`- Resolved: ${report.summary.resolved}`);
  })
  .catch(error => {
    console.error("Failed to generate dashboard:", error.message);
    process.exit(1);
  });
```

### Work Item Bulk Migration Tool

```typescript
import * as azdev from "azure-devops-node-api";
import * as fs from "fs";
import * as path from "path";

async function migrateWorkItems(sourceFile, targetProject) {
  // Setup
  const organizationUrl = process.env.AZURE_DEVOPS_ORG_URL;
  const token = process.env.AZURE_DEVOPS_TOKEN;
  
  if (!organizationUrl || !token) {
    throw new Error("Environment variables for Azure DevOps not set");
  }
  
  // Read source data
  const sourceData = JSON.parse(fs.readFileSync(sourceFile, 'utf8'));
  
  // Connect to Azure DevOps
  const authHandler = azdev.getPersonalAccessTokenHandler(token);
  const connection = new azdev.WebApi(organizationUrl, authHandler);
  const workItemTrackingApi = await connection.getWorkItemTrackingApi();
  
  // Track results
  const results = {
    successful: [],
    failed: []
  };
  
  // Process each work item
  for (const item of sourceData.workItems) {
    try {
      // Create patch document from source data
      const patchDocument = [
        {
          op: "add",
          path: "/fields/System.Title",
          value: item.title
        },
        {
          op: "add",
          path: "/fields/System.Description",
          value: item.description || ""
        }
      ];
      
      // Add additional fields if present
      if (item.priority) {
        patchDocument.push({
          op: "add",
          path: "/fields/Microsoft.VSTS.Common.Priority",
          value: item.priority
        });
      }
      
      if (item.tags && item.tags.length > 0) {
        patchDocument.push({
          op: "add",
          path: "/fields/System.Tags",
          value: item.tags.join("; ")
        });
      }
      
      // Create the work item
      const newItem = await workItemTrackingApi.createWorkItem(
        patchDocument,
        targetProject,
        item.type || "Task"
      );
      
      results.successful.push({
        originalId: item.id,
        newId: newItem.id,
        title: newItem.fields["System.Title"]
      });
      
      console.log(`Migrated: ${item.title} -> #${newItem.id}`);
    } catch (error) {
      results.failed.push({
        originalId: item.id,
        title: item.title,
        error: error.message
      });
      
      console.error(`Failed to migrate "${item.title}": ${error.message}`);
    }
  }
  
  // Save migration report
  const reportPath = path.join(
    path.dirname(sourceFile),
    `migration-report-${new Date().toISOString().split('T')[0]}.json`
  );
  
  fs.writeFileSync(reportPath, JSON.stringify(results, null, 2));
  
  console.log(`Migration complete: ${results.successful.length} successful, ${results.failed.length} failed`);
  console.log(`Report saved to: ${reportPath}`);
  
  return results;
}

// Usage example
migrateWorkItems("./work-items-to-migrate.json", "TargetProject")
  .then(results => {
    if (results.failed.length > 0) {
      process.exit(1); // Exit with error if any migration failed
    }
  })
  .catch(error => {
    console.error("Migration failed:", error.message);
    process.exit(1);
  });
```

## See Also

- [WorkItemTrackingApi Class](./README.md)
- [Methods Documentation](./methods/README.md)
- [Common Scenarios](./common-scenarios.md)
- [Error Handling](./error-handling.md)