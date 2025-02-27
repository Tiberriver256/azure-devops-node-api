# Creating Work Items with the Node API

## Overview

This tutorial will guide you through the process of creating work items in Azure DevOps using the Node API. You'll learn how to authenticate, create a basic work item, and add custom fields.

## Prerequisites

- Azure DevOps account with project admin or contributor permissions
- Node.js 14.0 or later
- Personal Access Token (PAT) with Work Items (Read, Write) scope
- Basic knowledge of TypeScript/JavaScript

## Step 1: Set Up Your Project

First, create a new Node.js project and install the required packages.

```bash
mkdir azure-devops-work-items
cd azure-devops-work-items
npm init -y
npm install azure-devops-node-api
npm install dotenv
```

Create a `.env` file to store your credentials:

```
AZURE_DEVOPS_ORG=https://dev.azure.com/your-organization
AZURE_DEVOPS_TOKEN=your-personal-access-token
AZURE_DEVOPS_PROJECT=your-project-name
```

## Step 2: Create a Connection to Azure DevOps

Create a file named `index.ts` (or `index.js`) with the following code to set up the connection:

```typescript
import * as azdev from 'azure-devops-node-api';
import * as dotenv from 'dotenv';

// Load environment variables
dotenv.config();

async function main() {
  try {
    // Get environment variables
    const orgUrl = process.env.AZURE_DEVOPS_ORG;
    const token = process.env.AZURE_DEVOPS_TOKEN;
    const project = process.env.AZURE_DEVOPS_PROJECT;
    
    if (!orgUrl || !token || !project) {
      throw new Error('Please set all required environment variables');
    }
    
    // Create authorization handler
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    
    // Create a connection to your organization
    console.log('Creating connection...');
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get the Work Item Tracking API client
    const witApi = await connection.getWorkItemTrackingApi();
    console.log('Connected successfully to Azure DevOps!');
    
    // Your code will go here in the next steps
    
  } catch (error) {
    console.error('Error:', error.message);
  }
}

main();
```

## Step 3: Create a Basic Work Item

Now, extend the code to create a basic work item (a Task):

```typescript
// Inside the main function, after getting witApi
// Create a new work item
const patchDocument = [
  {
    op: 'add',
    path: '/fields/System.Title',
    value: 'My first task created via API'
  },
  {
    op: 'add',
    path: '/fields/System.Description',
    value: 'This task was created using the Azure DevOps Node API'
  },
  {
    op: 'add',
    path: '/fields/System.State',
    value: 'To Do'
  }
];

console.log('Creating work item...');
const newWorkItem = await witApi.createWorkItem(
  patchDocument,
  project,
  'Task'
);

console.log(`Successfully created Task #${newWorkItem.id}`);
console.log(`Title: ${newWorkItem.fields['System.Title']}`);
```

## Troubleshooting

Common issues you might encounter and how to solve them:

- **Authentication Error**: If you receive a 401 error, make sure your PAT token has the correct scopes and has not expired.
  
  ```
  Error: TF400813: The user 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' is not authorized to access this resource.
  ```
  
  Solution: Generate a new PAT with the "Work Items (Read, Write)" scope.

- **Project Not Found**: If you get a 404 error when creating work items, double-check your project name.
  
  ```
  Error: TF200016: The following project does not exist: MyProject.
  ```
  
  Solution: Ensure the project name in your .env file matches exactly (it's case-sensitive).

## Next Steps

What to do after completing this tutorial:

- [Learn how to retrieve and update work items](./retrieving-updating-work-items.md)
- [Add attachments to work items](./adding-attachments-to-work-items.md)
- [Create work item relationships](./work-item-relationships.md) 