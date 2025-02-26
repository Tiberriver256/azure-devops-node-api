**Navigation**: [Home](../../index.md) > [Tutorials](../index.md) > Creating Work Items

# Creating Work Items Tutorial

**Progress**: [✓] Setup → [✓] Authentication → [ ] Creating Work Items → [ ] Advanced Options → [ ] Error Handling

## Contents
- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Step 1: Setup](#step-1-setup-️-5-minutes)
- [Step 2: Authentication](#step-2-authentication-️-10-minutes)
- [Step 3: Creating Work Items](#step-3-creating-work-items-️-15-minutes)
- [Step 4: Advanced Options](#step-4-advanced-options-️-10-minutes)
- [Step 5: Error Handling](#step-5-error-handling-️-10-minutes)
- [Next Steps](#next-steps)
- [Troubleshooting](#troubleshooting)

## Overview

This tutorial guides you through creating work items programmatically using the Azure DevOps Node API. By the end, you'll be able to create different types of work items, add custom fields, and establish relationships between work items.

**Estimated completion time**: 50 minutes

---

## Prerequisites

📋 **Required**:
- Azure DevOps account with appropriate permissions
- Node.js installed (version 12 or higher)
- Personal Access Token with Work Items (Read, Write) scope

📋 **Optional**:
- Visual Studio Code or another code editor
- Git for version control

---

## Step 1: Setup (⏱️ 5 minutes)

In this step, you'll set up your project and install the required dependencies.

### Create a New Project

Create a new directory for your project and initialize it:

```bash
mkdir azure-devops-work-items
cd azure-devops-work-items
npm init -y
```

### Install Dependencies

Install the Azure DevOps Node API package:

```bash
npm install azure-devops-node-api
```

💡 **Tip**:
If you're using TypeScript, you can also install the TypeScript compiler and types:

```bash
npm install typescript @types/node --save-dev
```

📊 **Success Criteria**:
- You have a new Node.js project initialized
- The azure-devops-node-api package is installed

---

## Step 2: Authentication (⏱️ 10 minutes)

In this step, you'll create a connection to Azure DevOps using your Personal Access Token (PAT).

### Generate a Personal Access Token

1. Go to Azure DevOps: https://dev.azure.com/{your-organization}
2. Click on your profile picture in the top right corner
3. Select "Personal access tokens"
4. Click "New Token"
5. Give your token a name and select the "Work Items (Read, Write)" scope
6. Click "Create" and copy your token

⚠️ **Caution**:
Store your PAT securely. It provides access to your Azure DevOps account and should be treated like a password.

### Create a Connection

Create a file named `index.js` (or `index.ts` for TypeScript) with the following code:

```typescript
const azdev = require('azure-devops-node-api');

// Your organization URL and personal access token
const orgUrl = 'https://dev.azure.com/your-organization';
const token = process.env.AZURE_DEVOPS_TOKEN; // Store your token in an environment variable

// Create authorization handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Create connection
const connection = new azdev.WebApi(orgUrl, authHandler);

async function main() {
  try {
    // Test the connection
    const coreApi = await connection.getCoreApi();
    const projects = await coreApi.getProjects();
    console.log('Connected to Azure DevOps!');
    console.log(`Found ${projects.length} projects.`);
  } catch (error) {
    console.error('Error connecting to Azure DevOps:', error);
  }
}

main();
```

### Test the Connection

Run your code to test the connection:

```bash
# Set your PAT as an environment variable
export AZURE_DEVOPS_TOKEN=your-personal-access-token

# Run the code
node index.js
```

📊 **Expected Output**:
```
Connected to Azure DevOps!
Found X projects.
```

💡 **Tip**:
For Windows, use `set AZURE_DEVOPS_TOKEN=your-token` instead of export.

---

## Step 3: Creating Work Items (⏱️ 15 minutes)

In this step, you'll learn how to create work items using the Work Item Tracking API.

### Get the Work Item Tracking API

First, update your `main` function to get the Work Item Tracking API:

```typescript
async function main() {
  try {
    // Get the Work Item Tracking API
    const workItemTrackingApi = await connection.getWorkItemTrackingApi();
    console.log('Successfully connected to the Work Item Tracking API');
    
    // Continue with creating work items...
  } catch (error) {
    console.error('Error:', error);
  }
}
```

### Create a Work Item

Add the following code to create a basic work item:

```typescript
// Define the project and work item type
const project = 'YourProjectName';
const workItemType = 'Task'; // Can be Bug, User Story, etc.

// Define the work item fields using JSON Patch format
const patchDocument = [
  {
    op: 'add',
    path: '/fields/System.Title',
    value: 'My first work item created via API'
  },
  {
    op: 'add',
    path: '/fields/System.Description',
    value: 'This is a description of my work item'
  },
  {
    op: 'add',
    path: '/fields/System.AssignedTo',
    value: 'your-email@example.com'
  }
];

// Create the work item
const workItem = await workItemTrackingApi.createWorkItem(
  null, // Custom headers (not needed for basic usage)
  patchDocument,
  project,
  workItemType
);

console.log('Work item created:', workItem.id);
console.log('Title:', workItem.fields['System.Title']);
```

🔍 **Example: Complete Code**:

```typescript
const azdev = require('azure-devops-node-api');

// Your organization URL and personal access token
const orgUrl = 'https://dev.azure.com/your-organization';
const token = process.env.AZURE_DEVOPS_TOKEN;

// Create authorization handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Create connection
const connection = new azdev.WebApi(orgUrl, authHandler);

async function main() {
  try {
    // Get the Work Item Tracking API
    const workItemTrackingApi = await connection.getWorkItemTrackingApi();
    console.log('Successfully connected to the Work Item Tracking API');
    
    // Define the project and work item type
    const project = 'YourProjectName';
    const workItemType = 'Task'; // Can be Bug, User Story, etc.
    
    // Define the work item fields using JSON Patch format
    const patchDocument = [
      {
        op: 'add',
        path: '/fields/System.Title',
        value: 'My first work item created via API'
      },
      {
        op: 'add',
        path: '/fields/System.Description',
        value: 'This is a description of my work item'
      },
      {
        op: 'add',
        path: '/fields/System.AssignedTo',
        value: 'your-email@example.com'
      }
    ];
    
    // Create the work item
    const workItem = await workItemTrackingApi.createWorkItem(
      null,
      patchDocument,
      project,
      workItemType
    );
    
    console.log('Work item created:', workItem.id);
    console.log('Title:', workItem.fields['System.Title']);
  } catch (error) {
    console.error('Error:', error);
  }
}

main();
```

📊 **Expected Result**:
A new work item will be created in your Azure DevOps project, and the console will display its ID and title.

---

## Step 4: Advanced Options (⏱️ 10 minutes)

In this step, you'll learn about advanced options for creating work items.

### Adding Custom Fields

If your organization has custom fields, you can add them to the patch document:

```typescript
const patchDocument = [
  // Standard fields
  {
    op: 'add',
    path: '/fields/System.Title',
    value: 'Work item with custom fields'
  },
  // Custom field
  {
    op: 'add',
    path: '/fields/Custom.MyCustomField',
    value: 'Custom value'
  }
];
```

### Creating Work Item Links

You can link work items together when creating them:

```typescript
const patchDocument = [
  // Fields
  {
    op: 'add',
    path: '/fields/System.Title',
    value: 'Work item with links'
  },
  // Link to another work item
  {
    op: 'add',
    path: '/relations/-',
    value: {
      rel: 'System.LinkTypes.Related',
      url: `${orgUrl}/_apis/wit/workItems/123`, // ID of the related work item
      attributes: {
        comment: 'Related to this work item'
      }
    }
  }
];
```

💡 **Tip**:
Common link types include:
- `System.LinkTypes.Related`: General relationship
- `System.LinkTypes.Hierarchy-Forward`: Parent-child relationship (this item is the parent)
- `System.LinkTypes.Hierarchy-Reverse`: Child-parent relationship (this item is the child)
- `System.LinkTypes.Dependency-Forward`: This item depends on the linked item
- `System.LinkTypes.Dependency-Reverse`: The linked item depends on this item

🔗 **Reference**:
See the [Work Item Link Types Reference](../reference/work-item-link-types.md) for a complete list of link types.

---

## Step 5: Error Handling (⏱️ 10 minutes)

In this step, you'll learn how to handle errors when creating work items.

### Common Errors

When creating work items, you might encounter these common errors:

| Status Code | Description | Common Cause |
|-------------|-------------|--------------|
| 401 | Unauthorized | Authentication failed |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Project or work item type not found |
| 400 | Bad Request | Invalid fields or values |

### Implementing Error Handling

Add proper error handling to your code:

```typescript
try {
  const workItem = await workItemTrackingApi.createWorkItem(
    null,
    patchDocument,
    project,
    workItemType
  );
  
  console.log('Work item created:', workItem.id);
} catch (error) {
  if (error.statusCode === 401) {
    console.error('Authentication failed. Check your credentials.');
  } else if (error.statusCode === 403) {
    console.error('You do not have permission to create work items in this project.');
  } else if (error.statusCode === 404) {
    console.error('Project or work item type not found. Check your project name and work item type.');
  } else if (error.statusCode === 400) {
    console.error('Invalid request. Check your fields and values:', error.message);
  } else {
    console.error('Unexpected error:', error.message);
  }
}
```

⚠️ **Caution**:
Always implement proper error handling in production code to provide meaningful feedback and prevent application crashes.

🛠️ **Debugging Tips**:
- Check network requests in browser developer tools
- Verify that all required fields are included for the specific work item type
- Ensure all referenced IDs exist in your Azure DevOps instance

---

## Next Steps

Now that you've learned how to create work items, you can explore these related topics:

- [Updating Work Items](./updating-work-items.md)
- [Querying Work Items](./querying-work-items.md)
- [Working with Work Item Links](./work-item-links.md)
- [Implementing Work Item Workflows](./work-item-workflows.md)

---

## Troubleshooting

<details>
<summary><b>Quick Solutions Reference</b></summary>

- **Authentication errors**: Check PAT token validity and permissions
- **Project not found**: Verify project name and access rights
- **Invalid fields**: Ensure required fields are included with correct formats
- **Rate limiting**: Implement retry logic with exponential backoff

</details>

### Common Issues

#### Authentication Failures

🛠️ **Symptoms**:
- 401 Unauthorized errors
- "Unable to authenticate" messages

🛠️ **Solutions**:
- Ensure your PAT token is valid and has not expired
- Verify that your PAT has the correct scopes (Work Items Read & Write)
- Check that your organization URL is correct

#### Project Not Found

🛠️ **Symptoms**:
- 404 Not Found errors
- "Project not found" messages

🛠️ **Solutions**:
- Verify that the project name is spelled correctly
- Check that you have access to the specified project
- Try using the project ID instead of the project name

#### Invalid Fields

🛠️ **Symptoms**:
- 400 Bad Request errors
- Error messages mentioning specific fields

🛠️ **Solutions**:
- Ensure that all required fields for your work item type are included
- Verify that field references use the correct format (e.g., `System.Title`)
- Check that field values match the expected data types

## See Also

- [Work Item Tracking API Reference](../api-reference/work-item-tracking-api.md)
- [Work Item Fields Reference](../reference/work-item-fields.md)
- [JSON Patch Format](../concepts/json-patch.md)
- [Error Handling Guide](../guides/error-handling.md)