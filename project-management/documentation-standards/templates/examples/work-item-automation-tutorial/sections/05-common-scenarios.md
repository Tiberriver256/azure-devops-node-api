# Common Scenarios

This section covers common real-world scenarios where you might apply the techniques from this tutorial.

## Scenario 1: Importing Work Items from External Systems

A common use case is importing issues or tickets from external systems (like JIRA, GitHub Issues, or a customer support system) into Azure DevOps.

```typescript
import * as azdev from "azure-devops-node-api";
import * as witApi from "azure-devops-node-api/WorkItemTrackingApi";
import * as witInterfaces from "azure-devops-node-api/interfaces/WorkItemTrackingInterfaces";
import * as fs from "fs";

// Assume we have a JSON file with tickets exported from another system
interface ExternalTicket {
  id: string;
  title: string;
  description: string;
  priority: string;
  status: string;
  assignee: string;
  created: string;
  tags: string[];
}

async function importTicketsFromJson(
  witClient: witApi.IWorkItemTrackingApi,
  project: string,
  jsonFilePath: string
): Promise<witInterfaces.WorkItem[]> {
  // Read and parse the JSON file
  const ticketsJson = fs.readFileSync(jsonFilePath, 'utf8');
  const tickets: ExternalTicket[] = JSON.parse(ticketsJson);
  
  console.log(`Importing ${tickets.length} tickets from ${jsonFilePath}`);
  
  // Map external priorities to Azure DevOps priorities
  const priorityMap: Record<string, number> = {
    "critical": 1,
    "high": 1,
    "medium": 2,
    "low": 3,
    "trivial": 4
  };
  
  // Import each ticket
  const createdWorkItems: witInterfaces.WorkItem[] = [];
  
  for (const ticket of tickets) {
    // Map external ticket fields to Azure DevOps work item fields
    const patchDocument: witInterfaces.JsonPatchOperation[] = [
      {
        op: "add",
        path: "/fields/System.Title",
        value: ticket.title
      },
      {
        op: "add",
        path: "/fields/System.Description",
        value: `<div>${ticket.description}</div>`
      },
      {
        op: "add",
        path: "/fields/Microsoft.VSTS.Common.Priority",
        value: priorityMap[ticket.priority.toLowerCase()] || 3
      },
      {
        op: "add",
        path: "/fields/System.Tags",
        value: [...ticket.tags, "Imported"].join("; ")
      },
      {
        op: "add",
        path: "/fields/System.History",
        value: `<div>Imported from external system. Original ID: ${ticket.id}, Created: ${ticket.created}</div>`
      }
    ];
    
    // Create the work item (as a Bug)
    try {
      const workItem = await witClient.createWorkItem(
        null,
        patchDocument,
        project,
        "Bug"
      );
      
      console.log(`Created Bug #${workItem.id} from ticket ${ticket.id}: ${ticket.title}`);
      createdWorkItems.push(workItem);
    } catch (error) {
      console.error(`Failed to import ticket ${ticket.id}: ${error.message}`);
    }
  }
  
  return createdWorkItems;
}
```

### When to Use This Approach

This approach is ideal when:
- Migrating from another issue tracking system to Azure DevOps
- Creating an integration between Azure DevOps and another system
- Implementing a customer feedback portal that creates work items

## Scenario 2: Creating Sprint Tasks from a Template

When starting a new sprint, teams often create similar tasks for each user story. This scenario automates that process:

```typescript
async function createTasksFromTemplate(
  witClient: witApi.IWorkItemTrackingApi,
  project: string,
  userStoryId: number,
  sprintPath: string // e.g., "MyProject\\Sprint 5"
): Promise<witInterfaces.WorkItem[]> {
  
  // Define template tasks that should be created for each user story
  const taskTemplates = [
    {
      title: "Design",
      description: "<div>Create design documents and mockups</div>",
      activity: "Design",
      hours: 4
    },
    {
      title: "Implementation",
      description: "<div>Implement the functionality</div>",
      activity: "Development",
      hours: 8
    },
    {
      title: "Unit Tests",
      description: "<div>Create unit tests for the functionality</div>",
      activity: "Testing",
      hours: 4
    },
    {
      title: "Documentation",
      description: "<div>Update documentation</div>",
      activity: "Documentation",
      hours: 2
    }
  ];
  
  const createdTasks: witInterfaces.WorkItem[] = [];
  
  // Get the user story details to include in task titles
  const userStory = await witClient.getWorkItem(userStoryId, undefined, undefined, witInterfaces.WorkItemExpand.All);
  const userStoryTitle = userStory.fields["System.Title"];
  
  // Create each template task
  for (const template of taskTemplates) {
    const patchDocument: witInterfaces.JsonPatchOperation[] = [
      {
        op: "add",
        path: "/fields/System.Title",
        value: `${template.title} - ${userStoryTitle}`
      },
      {
        op: "add",
        path: "/fields/System.Description",
        value: template.description
      },
      {
        op: "add",
        path: "/fields/Microsoft.VSTS.Common.Activity",
        value: template.activity
      },
      {
        op: "add",
        path: "/fields/Microsoft.VSTS.Scheduling.RemainingWork",
        value: template.hours
      },
      {
        // Add parent relationship
        op: "add",
        path: "/relations/-",
        value: {
          rel: "System.LinkTypes.Hierarchy-Reverse",
          url: `${orgUrl}/_apis/wit/workItems/${userStoryId}`,
          attributes: {
            comment: "Child of parent work item"
          }
        }
      },
      {
        // Set the iteration path (sprint)
        op: "add",
        path: "/fields/System.IterationPath",
        value: sprintPath
      }
    ];
    
    try {
      const task = await witClient.createWorkItem(
        null,
        patchDocument,
        project,
        "Task"
      );
      
      console.log(`Created task: ${task.fields["System.Title"]}`);
      createdTasks.push(task);
    } catch (error) {
      console.error(`Failed to create task "${template.title}": ${error.message}`);
    }
  }
  
  return createdTasks;
}
```

### When to Use This Approach

This approach is useful when:
- Starting a new sprint and need to create standard tasks for each user story
- Ensuring consistency in task breakdown across the team
- Automating repetitive task creation

## Scenario 3: Synchronizing Work Item Status with External Systems

This scenario demonstrates how to update Azure DevOps work items based on status changes in an external system:

```typescript
async function updateWorkItemFromExternalSystem(
  witClient: witApi.IWorkItemTrackingApi,
  project: string,
  workItemId: number,
  externalStatus: string,
  externalComments: string[]
): Promise<witInterfaces.WorkItem> {
  
  // Map external status to Azure DevOps state
  const stateMap: Record<string, string> = {
    "open": "Active",
    "in_progress": "Active",
    "resolved": "Resolved",
    "closed": "Closed",
    "reopened": "Active"
  };
  
  // Map external status to Azure DevOps reason
  const reasonMap: Record<string, string> = {
    "resolved": "Fixed",
    "closed": "Fixed",
    "reopened": "New Information"
  };
  
  // Create patch operations
  const patchDocument: witInterfaces.JsonPatchOperation[] = [];
  
  // Add state update if we have a mapping
  if (stateMap[externalStatus]) {
    patchDocument.push({
      op: "add",
      path: "/fields/System.State",
      value: stateMap[externalStatus]
    });
  }
  
  // Add reason update if we have a mapping
  if (reasonMap[externalStatus]) {
    patchDocument.push({
      op: "add",
      path: "/fields/System.Reason",
      value: reasonMap[externalStatus]
    });
  }
  
  // Add comments as history
  if (externalComments && externalComments.length > 0) {
    const commentsHtml = externalComments
      .map(comment => `<div><p><em>External comment:</em></p><p>${comment}</p></div>`)
      .join("");
    
    patchDocument.push({
      op: "add",
      path: "/fields/System.History",
      value: commentsHtml
    });
  }
  
  // Add a tag indicating this was updated from external system
  patchDocument.push({
    op: "add",
    path: "/fields/System.Tags",
    value: "External-Update"
  });
  
  // Only update if we have operations to perform
  if (patchDocument.length > 0) {
    return witClient.updateWorkItem(
      null,
      patchDocument,
      workItemId,
      project
    );
  } else {
    // If no updates, just return the current work item
    return witClient.getWorkItem(workItemId);
  }
}
```

### When to Use This Approach

This approach is valuable when:
- Building bi-directional integrations between systems
- Keeping Azure DevOps in sync with customer support systems
- Creating dashboards that aggregate data from multiple systems

---

**Navigation**:
- [Back to Index](../index.md)
- Previous: [Advanced Features](./04-advanced-features.md)
- Next: [Complete Example](./06-complete-example.md) 