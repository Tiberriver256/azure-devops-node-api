# Working with Work Items: A Practical Tutorial

**Navigation**: [Home](../index.md) > [Tutorials](./index.md) > Working with Work Items

## Overview

This tutorial provides a comprehensive guide to working with Azure DevOps work items using the Node.js API. You'll learn how to connect to Azure DevOps, retrieve work items, create and update work items, and query for work items using WIQL.

## Prerequisites

- Node.js (version 14 or later)
- An Azure DevOps organization with a project
- A Personal Access Token (PAT) with the appropriate permissions
- Basic knowledge of TypeScript/JavaScript

## Setup

1. Create a new directory for your project:

```bash
mkdir work-item-tutorial
cd work-item-tutorial
```

2. Initialize a new Node.js project:

```bash
npm init -y
```

3. Install the Azure DevOps Node.js API:

```bash
npm install azure-devops-node-api
```

4. Create a configuration file for your Azure DevOps connection details:

```bash
touch config.js
```

5. Add your connection details to the configuration file:

```javascript
// config.js
module.exports = {
  orgUrl: "https://dev.azure.com/your-organization",
  token: "your-personal-access-token", 
  project: "your-project-name"
};
```

> **Security Note**: In a production environment, store your PAT in environment variables or a secure secret storage solution, not in your code.

## Part 1: Connecting to Azure DevOps

Create a new file called `connection.js` with the following code:

```javascript
// connection.js
const azdev = require("azure-devops-node-api");
const config = require("./config");

async function getConnection() {
  // Create an authentication handler using a Personal Access Token
  const authHandler = azdev.getPersonalAccessTokenHandler(config.token);

  // Create a connection to Azure DevOps
  const connection = new azdev.WebApi(config.orgUrl, authHandler);

  try {
    // Test the connection
    const connData = await connection.connect();
    console.log(`Successfully connected to ${config.orgUrl}`);
    console.log(`Authenticated as: ${connData.authenticatedUser.providerDisplayName}`);
    return connection;
  } catch (err) {
    console.error("Error connecting to Azure DevOps:", err.message);
    throw err;
  }
}

module.exports = { getConnection };
```

## Part 2: Retrieving Work Items

Create a file called `get-work-items.js` to retrieve work items:

```javascript
// get-work-items.js
const { getConnection } = require("./connection");
const config = require("./config");

async function getWorkItem(id) {
  const connection = await getConnection();
  const workItemTrackingApi = await connection.getWorkItemTrackingApi();

  try {
    // Get a single work item
    const workItem = await workItemTrackingApi.getWorkItem(id);
    console.log(`Work Item #${workItem.id}: ${workItem.fields["System.Title"]}`);
    console.log(`State: ${workItem.fields["System.State"]}`);
    console.log(`Type: ${workItem.fields["System.WorkItemType"]}`);
    console.log(`Assigned To: ${workItem.fields["System.AssignedTo"]?.displayName || "Unassigned"}`);
    return workItem;
  } catch (err) {
    console.error(`Error retrieving work item ${id}:`, err.message);
    throw err;
  }
}

async function getMultipleWorkItems(ids) {
  const connection = await getConnection();
  const workItemTrackingApi = await connection.getWorkItemTrackingApi();

  try {
    // Get multiple work items in a single request
    const workItems = await workItemTrackingApi.getWorkItems(ids);
    console.log(`Retrieved ${workItems.length} work items:`);
    
    workItems.forEach(workItem => {
      console.log(`- #${workItem.id}: ${workItem.fields["System.Title"]} (${workItem.fields["System.State"]})`);
    });
    
    return workItems;
  } catch (err) {
    console.error(`Error retrieving work items:`, err.message);
    throw err;
  }
}

// Usage examples
// Replace 42 with an actual work item ID from your project
getWorkItem(42).then(() => console.log("Done fetching single work item"));

// Replace with actual work item IDs from your project
getMultipleWorkItems([42, 43, 44]).then(() => console.log("Done fetching multiple work items"));
```

## Part 3: Creating a Work Item

Create a file called `create-work-item.js`:

```javascript
// create-work-item.js
const { getConnection } = require("./connection");
const config = require("./config");

/**
 * Creates a new work item in Azure DevOps
 * @param {string} type - The work item type (e.g., "Bug", "Task", "User Story")
 * @param {string} title - The title of the work item
 * @param {string} [description] - Optional description of the work item
 * @param {string} [assignedTo] - Optional email of the person to assign the work item to
 * @returns {Promise<object>} The created work item
 */
async function createWorkItem(type, title, description, assignedTo) {
  try {
    // Get connection and API client
    const connection = await getConnection();
    const workItemTrackingApi = await connection.getWorkItemTrackingApi();

    // Create a document with fields to set
    // We use the JSON Patch format for updating work item fields
    const patchDocument = [
      {
        op: "add",
        path: "/fields/System.Title",
        value: title
      }
    ];

    // Add description if provided
    // Description is optional, so we only add it if a value was provided
    if (description) {
      patchDocument.push({
        op: "add",
        path: "/fields/System.Description",
        value: description
      });
    }

    // Add assigned to if provided
    // Assignment is optional, so we only add it if a value was provided
    if (assignedTo) {
      patchDocument.push({
        op: "add",
        path: "/fields/System.AssignedTo",
        value: assignedTo
      });
    }

    try {
      // Create a new work item
      // The first parameter is for custom headers (can be empty)
      // The second parameter is the patch document with field values
      // The third parameter is the project name
      // The fourth parameter is the work item type
      const newWorkItem = await workItemTrackingApi.createWorkItem(
        {}, // Custom headers (can be empty)
        patchDocument,
        config.project,
        type // e.g., "Bug", "Task", "User Story"
      );

      console.log(`Created ${type} #${newWorkItem.id}: ${newWorkItem.fields["System.Title"]}`);
      return newWorkItem;
    } catch (error) {
      // Handle specific error types
      if (error.statusCode === 400) {
        console.error(`Invalid request: ${error.message}`);
        console.error("Check that all required fields are provided and valid.");
      } else if (error.statusCode === 401) {
        console.error("Authentication error. Check your credentials or token.");
      } else if (error.statusCode === 403) {
        console.error("Authorization error. You don't have permission to create work items.");
      } else if (error.statusCode === 404) {
        console.error(`Project '${config.project}' or work item type '${type}' not found.`);
      } else {
        console.error(`Error creating work item:`, error.message);
      }
      throw error;
    }
  } catch (error) {
    console.error(`Error in createWorkItem:`, error.message);
    throw error;
  }
}

// Usage example
(async () => {
  try {
    const workItem = await createWorkItem(
      "Bug", 
      "API sample bug", 
      "This bug was created via the Azure DevOps Node.js API",
      "user@example.com" // Replace with a valid user email in your organization
    );
    
    console.log(`Work item created successfully with ID: ${workItem.id}`);
    console.log(`URL: ${workItem._links.html.href}`);
  } catch (error) {
    console.error("Error in main process:", error.message);
  }
})();
```

## Part 4: Updating a Work Item

Create a file called `update-work-item.js`:

```javascript
// update-work-item.js
const { getConnection } = require("./connection");
const config = require("./config");

async function updateWorkItem(id, updates) {
  const connection = await getConnection();
  const workItemTrackingApi = await connection.getWorkItemTrackingApi();

  // Create a document with the field updates
  const patchDocument = [];

  // Add each update to the patch document
  for (const [field, value] of Object.entries(updates)) {
    patchDocument.push({
      op: "replace", // or "add" if the field doesn't exist yet
      path: `/fields/${field}`,
      value: value
    });
  }

  try {
    // Update the work item
    const updatedWorkItem = await workItemTrackingApi.updateWorkItem(
      {}, // Custom headers (can be empty)
      patchDocument,
      id,
      config.project
    );

    console.log(`Updated Work Item #${updatedWorkItem.id}`);
    console.log(`New title: ${updatedWorkItem.fields["System.Title"]}`);
    console.log(`State: ${updatedWorkItem.fields["System.State"]}`);
    
    return updatedWorkItem;
  } catch (err) {
    console.error(`Error updating work item ${id}:`, err.message);
    throw err;
  }
}

// Usage example - replace 42 with an actual work item ID from your project
updateWorkItem(42, {
  "System.Title": "Updated title via API",
  "System.State": "Active",
  "System.Tags": "API; Updated; Tutorial"
}).then(workItem => {
  console.log(`Work item updated successfully`);
});
```

## Part 5: Querying for Work Items

Create a file called `query-work-items.js`:

```javascript
// query-work-items.js
const { getConnection } = require("./connection");
const config = require("./config");

async function queryWorkItems(workItemType, state, assignedTo, limit) {
  const connection = await getConnection();
  const workItemTrackingApi = await connection.getWorkItemTrackingApi();

  // Build the WIQL query
  let query = `SELECT [System.Id], [System.Title], [System.State], [System.AssignedTo], [System.CreatedDate]
               FROM WorkItems
               WHERE [System.TeamProject] = '${config.project}'`;

  // Add optional filters
  if (workItemType) {
    query += ` AND [System.WorkItemType] = '${workItemType}'`;
  }

  if (state) {
    query += ` AND [System.State] = '${state}'`;
  }

  if (assignedTo) {
    if (assignedTo === "me") {
      query += ` AND [System.AssignedTo] = @Me`;
    } else if (assignedTo === "unassigned") {
      query += ` AND [System.AssignedTo] = ''`;
    } else {
      query += ` AND [System.AssignedTo] = '${assignedTo}'`;
    }
  }

  // Add ordering
  query += ` ORDER BY [System.ChangedDate] DESC`;

  const wiql = { query };

  try {
    // Execute the query
    const queryResult = await workItemTrackingApi.queryByWiql(wiql, config.project, undefined, undefined, limit);

    console.log(`Query found ${queryResult.workItems?.length || 0} work items`);

    // If no work items found, return empty array
    if (!queryResult.workItems || queryResult.workItems.length === 0) {
      return [];
    }

    // Get the work items with all fields
    const ids = queryResult.workItems.map(wi => wi.id);
    const workItems = await workItemTrackingApi.getWorkItems(ids);

    workItems.forEach(workItem => {
      console.log(`- #${workItem.id}: ${workItem.fields["System.Title"]}`);
      console.log(`  State: ${workItem.fields["System.State"]}`);
      console.log(`  Assigned To: ${workItem.fields["System.AssignedTo"]?.displayName || "Unassigned"}`);
      console.log(`  Created: ${new Date(workItem.fields["System.CreatedDate"]).toLocaleDateString()}`);
      console.log(`  -----------------------------------------`);
    });

    return workItems;
  } catch (err) {
    console.error(`Error querying work items:`, err.message);
    throw err;
  }
}

// Usage examples
// Find active bugs
queryWorkItems("Bug", "Active", null, 10)
  .then(() => console.log("Completed bug query"));

// Find work items assigned to the current user
queryWorkItems(null, null, "me", 10)
  .then(() => console.log("Completed my work items query"));

// Find unassigned tasks
queryWorkItems("Task", null, "unassigned", 10)
  .then(() => console.log("Completed unassigned tasks query"));
```

## Part 6: Complete Example - Work Item Manager

Let's create a unified example that combines all the functions above into a reusable module:

```javascript
// work-item-manager.js
const { getConnection } = require("./connection");
const config = require("./config");

class WorkItemManager {
  constructor() {
    this.connection = null;
    this.workItemTrackingApi = null;
  }

  async initialize() {
    try {
      this.connection = await getConnection();
      this.workItemTrackingApi = await this.connection.getWorkItemTrackingApi();
      console.log("Work Item Manager initialized successfully");
    } catch (err) {
      console.error("Failed to initialize Work Item Manager:", err.message);
      throw err;
    }
  }

  async getWorkItem(id, fields) {
    if (!this.workItemTrackingApi) await this.initialize();
    
    try {
      return await this.workItemTrackingApi.getWorkItem(id, fields);
    } catch (err) {
      console.error(`Error getting work item ${id}:`, err.message);
      throw err;
    }
  }

  async getWorkItems(ids, fields) {
    if (!this.workItemTrackingApi) await this.initialize();
    
    try {
      return await this.workItemTrackingApi.getWorkItems(ids, fields);
    } catch (err) {
      console.error(`Error getting work items:`, err.message);
      throw err;
    }
  }

  async createWorkItem(type, fields) {
    if (!this.workItemTrackingApi) await this.initialize();
    
    const patchDocument = Object.entries(fields).map(([key, value]) => ({
      op: "add",
      path: `/fields/${key}`,
      value
    }));
    
    try {
      return await this.workItemTrackingApi.createWorkItem(
        {}, 
        patchDocument,
        config.project,
        type
      );
    } catch (err) {
      console.error(`Error creating work item:`, err.message);
      throw err;
    }
  }

  async updateWorkItem(id, fields) {
    if (!this.workItemTrackingApi) await this.initialize();
    
    const patchDocument = Object.entries(fields).map(([key, value]) => ({
      op: "add",
      path: `/fields/${key}`,
      value
    }));
    
    try {
      return await this.workItemTrackingApi.updateWorkItem(
        {},
        patchDocument,
        id
      );
    } catch (err) {
      console.error(`Error updating work item ${id}:`, err.message);
      throw err;
    }
  }

  async queryWorkItems(wiql, top = 100) {
    if (!this.workItemTrackingApi) await this.initialize();
    
    try {
      const queryResult = await this.workItemTrackingApi.queryByWiql(wiql, config.project, undefined, undefined, top);
      
      if (!queryResult.workItems || queryResult.workItems.length === 0) {
        return [];
      }
      
      const ids = queryResult.workItems.map(wi => wi.id);
      return await this.workItemTrackingApi.getWorkItems(ids);
    } catch (err) {
      console.error(`Error querying work items:`, err.message);
      throw err;
    }
  }

  // Helper method for common queries
  async findWorkItems(options = {}) {
    const { type, state, assignedTo, tags, createdAfter, updatedAfter, top } = options;
    
    let query = `SELECT [System.Id] FROM WorkItems WHERE [System.TeamProject] = '${config.project}'`;
    
    if (type) query += ` AND [System.WorkItemType] = '${type}'`;
    if (state) query += ` AND [System.State] = '${state}'`;
    
    if (assignedTo) {
      if (assignedTo === "me") {
        query += ` AND [System.AssignedTo] = @Me`;
      } else if (assignedTo === "unassigned") {
        query += ` AND [System.AssignedTo] = ''`;
      } else {
        query += ` AND [System.AssignedTo] = '${assignedTo}'`;
      }
    }
    
    if (tags) query += ` AND [System.Tags] CONTAINS '${tags}'`;
    
    if (createdAfter) {
      const date = createdAfter instanceof Date ? createdAfter.toISOString().split('T')[0] : createdAfter;
      query += ` AND [System.CreatedDate] >= '${date}'`;
    }
    
    if (updatedAfter) {
      const date = updatedAfter instanceof Date ? updatedAfter.toISOString().split('T')[0] : updatedAfter;
      query += ` AND [System.ChangedDate] >= '${date}'`;
    }
    
    query += ` ORDER BY [System.ChangedDate] DESC`;
    
    return this.queryWorkItems(query, top);
  }
}

module.exports = WorkItemManager;
```

## Part 7: Using the Work Item Manager

Create a file called `app.js` to use the Work Item Manager:

```javascript
// app.js
const WorkItemManager = require("./work-item-manager");

async function main() {
  try {
    // Initialize the Work Item Manager
    const manager = new WorkItemManager();
    await manager.initialize();

    console.log("==== Creating a new Bug ====");
    const newBug = await manager.createWorkItem("Bug", {
      "System.Title": "Sample bug from Work Item Manager",
      "System.Description": "This is a test bug created using our Work Item Manager",
      "System.Tags": "API; Test; Manager"
    });
    console.log(`Created Bug #${newBug.id}: ${newBug.fields["System.Title"]}`);

    console.log("\n==== Updating the Bug ====");
    const updatedBug = await manager.updateWorkItem(newBug.id, {
      "System.State": "Active",
      "System.Reason": "Accepted",
      "Microsoft.VSTS.Common.Priority": 2
    });
    console.log(`Updated Bug #${updatedBug.id} to state: ${updatedBug.fields["System.State"]}`);

    console.log("\n==== Finding Active Bugs ====");
    const activebugs = await manager.findWorkItems({
      type: "Bug",
      state: "Active",
      top: 5
    });
    console.log(`Found ${activebugs.length} active bugs:`);
    activebugs.forEach(bug => {
      console.log(`- #${bug.id}: ${bug.fields["System.Title"]}`);
    });

    console.log("\n==== Finding Recently Created Items ====");
    // Get items created in the last 7 days
    const lastWeek = new Date();
    lastWeek.setDate(lastWeek.getDate() - 7);
    
    const recentItems = await manager.findWorkItems({
      createdAfter: lastWeek,
      top: 5
    });
    console.log(`Found ${recentItems.length} items created in the last 7 days:`);
    recentItems.forEach(item => {
      console.log(`- #${item.id}: ${item.fields["System.Title"]} (${item.fields["System.WorkItemType"]})`);
    });

  } catch (err) {
    console.error("Error in main process:", err);
  }
}

// Run the main function
main().then(() => console.log("Done!"));
```

## Running the Examples

To run any of the examples, use:

```bash
node <filename>.js
```

For example:

```bash
node app.js
```

## Common Errors and Troubleshooting

### Authentication Issues

- **Error**: "TF400813: The user name or password is incorrect."
- **Solution**: Verify your Personal Access Token (PAT) and ensure it has the appropriate scopes.

### Permission Issues

- **Error**: "TF401019: The current user does not have permission to access the resource."
- **Solution**: Make sure your Personal Access Token (PAT) has the appropriate permissions for the operations you're trying to perform.

### Project Not Found

- **Error**: "TF200016: The following project does not exist: YourProject."
- **Solution**: Verify your project name is correct in the config file.

### Invalid Fields

- **Error**: "VS403458: You need to specify a valid Title."
- **Solution**: Ensure you're providing all required fields when creating or updating work items.

## Best Practices

1. **Error Handling**: Always include proper error handling in your applications
2. **Batch Operations**: Use `getWorkItems` instead of multiple `getWorkItem` calls
3. **Field Selection**: Only request the fields you need to improve performance
4. **Security**: Store authentication tokens securely (environment variables or secret storage)
5. **Rate Limiting**: Implement retry logic with exponential backoff for large-scale operations
6. **Optimize Queries**: Write efficient WIQL queries to reduce server load and response time

## Conclusion

This tutorial has provided a comprehensive guide to working with Azure DevOps work items using the Azure DevOps Node API. You've learned how to:

- Connect to Azure DevOps
- Retrieve individual and multiple work items
- Create new work items
- Update existing work items
- Query for work items using WIQL
- Build a reusable Work Item Manager

You can extend these examples to build more complex applications that integrate with Azure DevOps, such as custom dashboards, automation tools, or integrations with other systems.

## See Also

- [Work Item Tracking API Documentation](../api-reference/work-item-tracking/README.md)
- [Authentication Handlers](../api-reference/webapi-core/authentication-handlers.md)
- [Connection Options](../api-reference/webapi-core/connection-options.md)
- [Work Items REST API](https://learn.microsoft.com/en-us/rest/api/azure/devops/wit/?view=azure-devops-rest-7.1)
- [Glossary](../glossary.md) - Standardized terminology for the Azure DevOps Node API 