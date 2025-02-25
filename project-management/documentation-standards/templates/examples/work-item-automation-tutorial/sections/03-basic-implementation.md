# Basic Implementation

In this section, you'll create a simple script that connects to Azure DevOps and creates a basic work item.

## Step 1: Set Up the Project Structure

Create the following files in your project directory:

```
azure-devops-work-item-automation/
├── .env                  # Environment variables (credentials)
├── package.json          # npm package configuration
├── tsconfig.json         # TypeScript configuration (if using TypeScript)
└── src/
    └── create-work-item.ts  # Our main script
```

## Step 2: Configure Environment Variables

If you haven't already, create a `.env` file with your Azure DevOps credentials:

```
# .env file
AZURE_DEVOPS_ORG=https://dev.azure.com/your-organization
AZURE_DEVOPS_PAT=your-personal-access-token
AZURE_DEVOPS_PROJECT=YourProjectName
```

## Step 3: Create the Basic Script

Create a file named `create-work-item.ts` (or `.js` if not using TypeScript) in the `src` directory:

```typescript
// src/create-work-item.ts
import * as azdev from "azure-devops-node-api";
import * as witApi from "azure-devops-node-api/WorkItemTrackingApi";
import * as witInterfaces from "azure-devops-node-api/interfaces/WorkItemTrackingInterfaces";
import * as dotenv from "dotenv";

// Load environment variables
dotenv.config();

// Azure DevOps connection details
const orgUrl = process.env.AZURE_DEVOPS_ORG;
const token = process.env.AZURE_DEVOPS_PAT;
const project = process.env.AZURE_DEVOPS_PROJECT;

if (!orgUrl || !token || !project) {
  console.error("Please set the required environment variables in .env file");
  process.exit(1);
}

// Connect to Azure DevOps
async function getConnection(): Promise<azdev.WebApi> {
  return new Promise<azdev.WebApi>((resolve, reject) => {
    try {
      // Create auth handler
      const authHandler = azdev.getPersonalAccessTokenHandler(token);
      // Create connection
      const connection = new azdev.WebApi(orgUrl, authHandler);
      resolve(connection);
    } catch (error) {
      reject(error);
    }
  });
}

// Get Work Item Tracking API client
async function getWorkItemTrackingApi(connection: azdev.WebApi): Promise<witApi.IWorkItemTrackingApi> {
  return connection.getWorkItemTrackingApi();
}

// Create a basic work item
async function createWorkItem(witClient: witApi.IWorkItemTrackingApi, project: string, title: string, description: string): Promise<witInterfaces.WorkItem> {
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
    }
  ];

  // Create the work item (using 'Task' type)
  return witClient.createWorkItem(null, patchDocument, project, "Task");
}

// Main function
async function main() {
  try {
    console.log("Connecting to Azure DevOps...");
    const connection = await getConnection();
    
    console.log("Getting Work Item Tracking API client...");
    const witClient = await getWorkItemTrackingApi(connection);
    
    console.log("Creating work item...");
    const workItemTitle = "My First Automated Task";
    const workItemDescription = "This task was created using the Azure DevOps Node API";
    
    const newWorkItem = await createWorkItem(witClient, project, workItemTitle, workItemDescription);
    
    console.log(`Successfully created work item #${newWorkItem.id}`);
    console.log(`URL: ${orgUrl}/${project}/_workitems/edit/${newWorkItem.id}`);
    
  } catch (error) {
    console.error("Error:", error.message);
  }
}

// Run the script
main();
```

## Step 4: Run the Script

Execute the script using the following command:

```bash
# For TypeScript
npx ts-node src/create-work-item.ts

# For JavaScript
node src/create-work-item.js
```

If successful, you should see output similar to:

```
Connecting to Azure DevOps...
Getting Work Item Tracking API client...
Creating work item...
Successfully created work item #123
URL: https://dev.azure.com/your-organization/YourProjectName/_workitems/edit/123
```

## Understanding the Code

Let's break down what's happening in this script:

1. **Environment Setup**: We load environment variables using `dotenv` to keep credentials secure
2. **Connection**: We create an authenticated connection to Azure DevOps using a Personal Access Token
3. **API Client**: We get the Work Item Tracking API client, which provides methods for working with work items
4. **Work Item Creation**: We create a simple Task work item with a title and description
5. **JSON Patch Operations**: We use JSON Patch format to define the fields we want to set on the work item

This basic implementation demonstrates the fundamental pattern for working with the Azure DevOps Node API:
1. Connect to Azure DevOps
2. Get the appropriate API client
3. Use the client to perform operations
4. Handle the response

In the next section, we'll expand on this foundation to add more advanced features.

---

**Navigation**:
- [Back to Index](../index.md)
- Previous: [Prerequisites](./02-prerequisites.md)
- Next: [Advanced Features](./04-advanced-features.md) 