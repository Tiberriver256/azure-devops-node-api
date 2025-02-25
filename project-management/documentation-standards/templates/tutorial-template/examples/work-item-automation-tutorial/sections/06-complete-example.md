# Complete Example

This section provides a comprehensive example that combines all the concepts covered in this tutorial. The example demonstrates a complete work item automation solution that:

1. Connects to Azure DevOps
2. Creates a user story
3. Creates child tasks
4. Adds an attachment
5. Implements error handling and logging

## Full Implementation

```typescript
// work-item-automation.ts
import * as azdev from "azure-devops-node-api";
import * as witApi from "azure-devops-node-api/WorkItemTrackingApi";
import * as witInterfaces from "azure-devops-node-api/interfaces/WorkItemTrackingInterfaces";
import * as fs from "fs";
import * as path from "path";
import * as dotenv from "dotenv";

// Load environment variables
dotenv.config();

// Configuration
const config = {
  orgUrl: process.env.AZURE_DEVOPS_ORG,
  token: process.env.AZURE_DEVOPS_PAT,
  project: process.env.AZURE_DEVOPS_PROJECT,
  // Optional configuration
  logLevel: process.env.LOG_LEVEL || "info", // debug, info, warn, error
  maxRetries: parseInt(process.env.MAX_RETRIES || "3"),
  retryDelay: parseInt(process.env.RETRY_DELAY || "1000") // ms
};

// Validate configuration
if (!config.orgUrl || !config.token || !config.project) {
  console.error("Missing required environment variables. Please check your .env file.");
  process.exit(1);
}

// Logger
const logger = {
  debug: (message: string) => {
    if (config.logLevel === "debug") console.log(`[DEBUG] ${message}`);
  },
  info: (message: string) => {
    if (["debug", "info"].includes(config.logLevel)) console.log(`[INFO] ${message}`);
  },
  warn: (message: string) => {
    if (["debug", "info", "warn"].includes(config.logLevel)) console.warn(`[WARN] ${message}`);
  },
  error: (message: string) => {
    console.error(`[ERROR] ${message}`);
  }
};

// Connect to Azure DevOps
async function getConnection(): Promise<azdev.WebApi> {
  logger.debug("Creating authentication handler");
  const authHandler = azdev.getPersonalAccessTokenHandler(config.token);
  
  logger.debug(`Connecting to ${config.orgUrl}`);
  return new azdev.WebApi(config.orgUrl, authHandler);
}

// Get Work Item Tracking API client
async function getWorkItemTrackingApi(connection: azdev.WebApi): Promise<witApi.IWorkItemTrackingApi> {
  logger.debug("Getting Work Item Tracking API client");
  return connection.getWorkItemTrackingApi();
}

// Retry utility
async function withRetry<T>(
  operation: () => Promise<T>,
  operationName: string,
  maxRetries: number = config.maxRetries,
  delayMs: number = config.retryDelay
): Promise<T> {
  let lastError: Error;
  
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      logger.debug(`${operationName}: Attempt ${attempt}/${maxRetries}`);
      return await operation();
    } catch (error) {
      logger.warn(`${operationName}: Attempt ${attempt}/${maxRetries} failed: ${error.message}`);
      lastError = error;
      
      if (attempt < maxRetries) {
        const backoffMs = delayMs * Math.pow(2, attempt - 1);
        logger.debug(`${operationName}: Retrying in ${backoffMs / 1000} seconds...`);
        await new Promise(resolve => setTimeout(resolve, backoffMs));
      }
    }
  }
  
  throw new Error(`${operationName}: All ${maxRetries} attempts failed. Last error: ${lastError.message}`);
}

// Create a user story
async function createUserStory(
  witClient: witApi.IWorkItemTrackingApi,
  project: string,
  title: string,
  description: string,
  acceptanceCriteria: string,
  priority: number = 2,
  storyPoints: number = 5,
  tags: string[] = []
): Promise<witInterfaces.WorkItem> {
  logger.info(`Creating User Story: "${title}"`);
  
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
  
  // Add tags if provided
  if (tags && tags.length > 0) {
    patchDocument.push({
      op: "add",
      path: "/fields/System.Tags",
      value: tags.join("; ")
    });
  }
  
  return witClient.createWorkItem(null, patchDocument, project, "User Story");
}

// Create a child task
async function createChildTask(
  witClient: witApi.IWorkItemTrackingApi,
  project: string,
  parentId: number,
  title: string,
  description: string,
  activity: string = "Development",
  remainingWork: number = 4,
  iterationPath?: string
): Promise<witInterfaces.WorkItem> {
  logger.info(`Creating Task: "${title}" (parent: ${parentId})`);
  
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
        url: `${config.orgUrl}/_apis/wit/workItems/${parentId}`,
        attributes: {
          comment: "Child of parent work item"
        }
      }
    }
  ];
  
  // Add iteration path if provided
  if (iterationPath) {
    patchDocument.push({
      op: "add",
      path: "/fields/System.IterationPath",
      value: iterationPath
    });
  }
  
  return witClient.createWorkItem(null, patchDocument, project, "Task");
}

// Add an attachment to a work item
async function addAttachment(
  witClient: witApi.IWorkItemTrackingApi,
  project: string,
  workItemId: number,
  filePath: string,
  comment: string = "Attachment added via API"
): Promise<witInterfaces.AttachmentReference> {
  logger.info(`Adding attachment "${filePath}" to work item ${workItemId}`);
  
  // Check if file exists
  if (!fs.existsSync(filePath)) {
    throw new Error(`File not found: ${filePath}`);
  }
  
  // Read the file
  const fileBuffer = fs.readFileSync(filePath);
  const fileName = path.basename(filePath);
  
  // Upload the attachment
  const attachment = await witClient.createAttachment(
    fileBuffer,
    fileName,
    project
  );
  
  logger.debug(`Attachment uploaded: ${attachment.url}`);
  
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
  
  await witClient.updateWorkItem(null, patchDocument, workItemId, project);
  
  return attachment;
}

// Main function to create a work item hierarchy
async function createWorkItemHierarchy(
  featureName: string,
  description: string,
  acceptanceCriteria: string,
  tasks: Array<{
    title: string;
    description: string;
    activity: string;
    hours: number;
  }>,
  attachmentPath?: string
): Promise<{
  userStory: witInterfaces.WorkItem;
  tasks: witInterfaces.WorkItem[];
  attachment?: witInterfaces.AttachmentReference;
}> {
  try {
    logger.info("Starting work item hierarchy creation");
    
    // Connect to Azure DevOps
    const connection = await withRetry(
      () => getConnection(),
      "Connect to Azure DevOps"
    );
    
    // Get Work Item Tracking API client
    const witClient = await withRetry(
      () => getWorkItemTrackingApi(connection),
      "Get Work Item Tracking API"
    );
    
    // Create user story
    const userStory = await withRetry(
      () => createUserStory(
        witClient,
        config.project,
        featureName,
        description,
        acceptanceCriteria,
        2, // Priority
        8, // Story Points
        ["Automated", "API-Created"]
      ),
      "Create User Story"
    );
    
    logger.info(`Created User Story #${userStory.id}: ${featureName}`);
    
    // Create child tasks
    const createdTasks: witInterfaces.WorkItem[] = [];
    
    for (const task of tasks) {
      const createdTask = await withRetry(
        () => createChildTask(
          witClient,
          config.project,
          userStory.id,
          task.title,
          task.description,
          task.activity,
          task.hours
        ),
        `Create Task: ${task.title}`
      );
      
      createdTasks.push(createdTask);
      logger.info(`Created Task #${createdTask.id}: ${task.title}`);
    }
    
    // Add attachment if provided
    let attachment: witInterfaces.AttachmentReference = null;
    
    if (attachmentPath && fs.existsSync(attachmentPath)) {
      attachment = await withRetry(
        () => addAttachment(
          witClient,
          config.project,
          userStory.id,
          attachmentPath,
          "Supporting documentation"
        ),
        "Add Attachment"
      );
      
      logger.info(`Added attachment: ${attachment.name}`);
    }
    
    logger.info("Work item hierarchy creation completed successfully");
    
    return {
      userStory,
      tasks: createdTasks,
      attachment
    };
    
  } catch (error) {
    logger.error(`Failed to create work item hierarchy: ${error.message}`);
    throw error;
  }
}

// Example usage
async function main() {
  try {
    const result = await createWorkItemHierarchy(
      "Implement user authentication system",
      "<div>Create a secure authentication system for the application</div>",
      "<div><ul><li>Support email/password login</li><li>Implement password reset functionality</li><li>Add multi-factor authentication</li><li>Ensure GDPR compliance</li></ul></div>",
      [
        {
          title: "Design authentication flow",
          description: "<div>Create sequence diagrams for the authentication process</div>",
          activity: "Design",
          hours: 4
        },
        {
          title: "Implement login API",
          description: "<div>Create backend API endpoints for authentication</div>",
          activity: "Development",
          hours: 8
        },
        {
          title: "Create login UI",
          description: "<div>Implement the user interface for login and registration</div>",
          activity: "Development",
          hours: 6
        },
        {
          title: "Implement password reset functionality",
          description: "<div>Create the password reset flow and email notifications</div>",
          activity: "Development",
          hours: 4
        },
        {
          title: "Write authentication tests",
          description: "<div>Create unit and integration tests for the authentication system</div>",
          activity: "Testing",
          hours: 6
        }
      ],
      "./docs/auth-flow-diagram.png" // Optional attachment
    );
    
    // Output results
    console.log("\nWork Item Hierarchy Created:");
    console.log(`User Story: #${result.userStory.id} - ${result.userStory.fields["System.Title"]}`);
    console.log(`URL: ${config.orgUrl}/${config.project}/_workitems/edit/${result.userStory.id}`);
    
    console.log("\nChild Tasks:");
    result.tasks.forEach(task => {
      console.log(`- #${task.id} - ${task.fields["System.Title"]}`);
      console.log(`  URL: ${config.orgUrl}/${config.project}/_workitems/edit/${task.id}`);
    });
    
    if (result.attachment) {
      console.log(`\nAttachment: ${result.attachment.name}`);
    }
    
  } catch (error) {
    console.error("Error in main function:", error.message);
    process.exit(1);
  }
}

// Run the example
main();
```

## Running the Complete Example

To run this example:

1. Save the code to a file named `work-item-automation.ts`
2. Ensure you have the required packages installed:
   ```bash
   npm install azure-devops-node-api dotenv
   npm install typescript @types/node --save-dev
   ```
3. Create a `.env` file with your Azure DevOps credentials:
   ```
   AZURE_DEVOPS_ORG=https://dev.azure.com/your-organization
   AZURE_DEVOPS_PAT=your-personal-access-token
   AZURE_DEVOPS_PROJECT=YourProjectName
   LOG_LEVEL=info
   MAX_RETRIES=3
   RETRY_DELAY=1000
   ```
4. Create a sample attachment file (optional):
   ```bash
   mkdir -p docs
   # Add an image file to the docs directory
   ```
5. Run the example:
   ```bash
   npx ts-node work-item-automation.ts
   ```

## Code Structure Explanation

This complete example demonstrates several important patterns:

### 1. Configuration Management

The example uses environment variables loaded via `dotenv` for configuration, making it easy to adapt to different environments without changing code.

### 2. Logging

A simple logging system allows for different levels of verbosity, which is useful for debugging and production use.

### 3. Error Handling and Retries

The `withRetry` function provides robust error handling with exponential backoff, making the automation more resilient to transient failures.

### 4. Modular Design

Each function has a single responsibility, making the code easier to understand, test, and maintain.

### 5. Type Safety

TypeScript interfaces ensure that the code is type-safe, reducing runtime errors and improving developer experience.

### 6. Comprehensive Documentation

Each function includes clear documentation about its purpose and parameters.

## Extending the Example

This example can be extended in several ways:

- **Batch Processing**: Add functionality to process multiple work item hierarchies in batch
- **Status Updates**: Add functions to update existing work items
- **Query Integration**: Add functions to query existing work items and update them based on criteria
- **Webhook Receiver**: Create an API endpoint that receives webhooks from external systems and creates work items

---

**Navigation**:
- [Back to Index](../index.md)
- Previous: [Common Scenarios](./05-common-scenarios.md)
- Next: [Troubleshooting](./07-troubleshooting.md) 