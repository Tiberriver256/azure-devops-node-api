# Advanced Features

Now that you've created a basic work item, let's enhance our implementation with advanced features:

1. Adding custom fields and work item types
2. Creating work item relationships (parent/child, related)
3. Adding attachments to work items
4. Implementing error handling and retry logic

## Custom Fields and Work Item Types

Different work item types (User Story, Bug, Epic, etc.) have different required and optional fields. Let's modify our code to create a User Story with additional fields:

```typescript
// Create a User Story with custom fields
async function createUserStory(
  witClient: witApi.IWorkItemTrackingApi, 
  project: string, 
  title: string, 
  description: string,
  acceptanceCriteria: string,
  priority: number,
  storyPoints: number
): Promise<witInterfaces.WorkItem> {
  
  // Define the fields for the work item
  const patchDocument: witInterfaces.JsonPatchOperation[] = [
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
      path: "/fields/Microsoft.VSTS.Common.AcceptanceCriteria",
      value: acceptanceCriteria
    },
    {
      op: "add",
      path: "/fields/Microsoft.VSTS.Common.Priority",
      value: priority
    },
    {
      op: "add",
      path: "/fields/Microsoft.VSTS.Scheduling.StoryPoints",
      value: storyPoints
    }
  ];

  // Create the User Story
  return witClient.createWorkItem(null, patchDocument, project, "User Story");
}
```

Usage example:

```typescript
const userStory = await createUserStory(
  witClient,
  project,
  "Implement login functionality",
  "<div>Users should be able to log in using their credentials</div>",
  "<div><ul><li>Should support email/password login</li><li>Should validate input fields</li><li>Should show appropriate error messages</li></ul></div>",
  2,  // Priority (1-4, with 1 being highest)
  5   // Story Points
);

console.log(`Created User Story #${userStory.id}`);
```

> **Note**: Field names can vary between Azure DevOps process templates (Agile, Scrum, CMMI). Ensure you're using the correct field names for your process template.

## Creating Work Item Relationships

Work items can be related to each other in various ways. Common relationships include:

- **Parent/Child**: Hierarchical relationship (Epic > Feature > User Story > Task)
- **Related**: Work items that are related but not in a hierarchy
- **Successor/Predecessor**: Work items that must be completed in sequence

Let's add a function to create child tasks for a user story:

```typescript
// Create a child task for a parent work item
async function createChildTask(
  witClient: witApi.IWorkItemTrackingApi,
  project: string,
  parentId: number,
  title: string,
  description: string,
  activity: string = "Development", // Options: Development, Testing, Documentation, etc.
  remainingWork: number = 4 // Hours of work remaining
): Promise<witInterfaces.WorkItem> {
  
  // Define the fields for the task
  const patchDocument: witInterfaces.JsonPatchOperation[] = [
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
      path: "/fields/Microsoft.VSTS.Common.Activity",
      value: activity
    },
    {
      op: "add",
      path: "/fields/Microsoft.VSTS.Scheduling.RemainingWork",
      value: remainingWork
    },
    {
      // Add parent relationship
      op: "add",
      path: "/relations/-",
      value: {
        rel: "System.LinkTypes.Hierarchy-Reverse",
        url: `${orgUrl}/_apis/wit/workItems/${parentId}`,
        attributes: {
          comment: "Child of parent work item"
        }
      }
    }
  ];

  // Create the task
  return witClient.createWorkItem(null, patchDocument, project, "Task");
}
```

Usage example:

```typescript
// First create a user story
const userStory = await createUserStory(/* parameters */);

// Then create child tasks
const task1 = await createChildTask(
  witClient,
  project,
  userStory.id,
  "Implement login form UI",
  "<div>Create the login form with email and password fields</div>",
  "Development",
  4
);

const task2 = await createChildTask(
  witClient,
  project,
  userStory.id,
  "Implement authentication logic",
  "<div>Create the backend authentication logic</div>",
  "Development",
  6
);

console.log(`Created tasks #${task1.id} and #${task2.id} as children of User Story #${userStory.id}`);
```

## Adding Attachments to Work Items

You can attach files to work items, such as screenshots, documents, or logs:

```typescript
// Add an attachment to a work item
async function addAttachment(
  witClient: witApi.IWorkItemTrackingApi,
  project: string,
  workItemId: number,
  filePath: string,
  comment: string = "Attachment added via API"
): Promise<witInterfaces.AttachmentReference> {
  
  // Read the file
  const fs = require('fs');
  const fileBuffer = fs.readFileSync(filePath);
  
  // Get file name from path
  const path = require('path');
  const fileName = path.basename(filePath);
  
  // Upload the attachment
  const attachment = await witClient.createAttachment(
    fileBuffer,
    fileName,
    project
  );
  
  // Add the attachment to the work item
  const patchDocument: witInterfaces.JsonPatchOperation[] = [
    {
      op: "add",
      path: "/relations/-",
      value: {
        rel: "AttachedFile",
        url: attachment.url,
        attributes: {
          comment: comment
        }
      }
    }
  ];
  
  // Update the work item with the attachment
  await witClient.updateWorkItem(null, patchDocument, workItemId, project);
  
  return attachment;
}
```

Usage example:

```typescript
const attachment = await addAttachment(
  witClient,
  project,
  workItemId,
  "./screenshots/login-screen.png",
  "Screenshot of the login screen design"
);

console.log(`Added attachment: ${attachment.name}`);
```

## Error Handling and Retry Logic

API calls can fail for various reasons (network issues, rate limiting, etc.). Let's implement a retry mechanism:

```typescript
// Retry utility function
async function withRetry<T>(
  operation: () => Promise<T>,
  maxRetries: number = 3,
  delayMs: number = 1000
): Promise<T> {
  let lastError: Error;
  
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await operation();
    } catch (error) {
      console.warn(`Attempt ${attempt}/${maxRetries} failed: ${error.message}`);
      lastError = error;
      
      if (attempt < maxRetries) {
        // Wait before retrying (with exponential backoff)
        const backoffMs = delayMs * Math.pow(2, attempt - 1);
        console.log(`Retrying in ${backoffMs / 1000} seconds...`);
        await new Promise(resolve => setTimeout(resolve, backoffMs));
      }
    }
  }
  
  throw new Error(`All ${maxRetries} attempts failed. Last error: ${lastError.message}`);
}
```

Usage example:

```typescript
try {
  // Use the retry function for operations that might fail
  const workItem = await withRetry(() => 
    createWorkItem(witClient, project, "Task with retry", "This task uses retry logic")
  );
  
  console.log(`Created work item #${workItem.id} with retry handling`);
} catch (error) {
  console.error("Failed after multiple retries:", error.message);
}
```

## Putting It All Together

Here's how you might combine these advanced features:

```typescript
async function createWorkItemHierarchy() {
  try {
    // Connect to Azure DevOps
    const connection = await getConnection();
    const witClient = await getWorkItemTrackingApi(connection);
    
    // Create a user story
    const userStory = await withRetry(() => 
      createUserStory(
        witClient,
        project,
        "Implement user authentication",
        "<div>Users should be able to authenticate with the system</div>",
        "<div><ul><li>Support multiple auth methods</li><li>Secure password storage</li></ul></div>",
        2,
        8
      )
    );
    
    console.log(`Created User Story #${userStory.id}`);
    
    // Create child tasks
    const tasks = await Promise.all([
      withRetry(() => createChildTask(
        witClient, project, userStory.id,
        "Implement login UI", "<div>Create the login interface</div>",
        "Development", 4
      )),
      withRetry(() => createChildTask(
        witClient, project, userStory.id,
        "Implement authentication API", "<div>Create the auth API endpoints</div>",
        "Development", 6
      )),
      withRetry(() => createChildTask(
        witClient, project, userStory.id,
        "Write authentication tests", "<div>Create unit and integration tests</div>",
        "Testing", 3
      ))
    ]);
    
    console.log(`Created ${tasks.length} child tasks`);
    
    // Add an attachment to the user story
    if (fs.existsSync("./docs/auth-flow.png")) {
      const attachment = await withRetry(() => 
        addAttachment(
          witClient, project, userStory.id,
          "./docs/auth-flow.png", "Authentication flow diagram"
        )
      );
      
      console.log(`Added attachment: ${attachment.name}`);
    }
    
    return {
      userStory,
      tasks
    };
    
  } catch (error) {
    console.error("Error creating work item hierarchy:", error.message);
    throw error;
  }
}
```

In the next section, we'll explore common real-world scenarios for work item automation.

---

**Navigation**:
- [Back to Index](../index.md)
- Previous: [Basic Implementation](./03-basic-implementation.md)
- Next: [Common Scenarios](./05-common-scenarios.md) 