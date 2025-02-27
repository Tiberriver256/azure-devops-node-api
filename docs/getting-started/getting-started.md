# Getting Started with Azure DevOps Node API

## Overview

This guide will help you get started with the Azure DevOps Node API, a JavaScript/TypeScript client library for interacting with Azure DevOps Services and Azure DevOps Server. Whether you're building DevOps tools, integrating with your existing systems, or automating your workflows, this guide will help you take your first steps with the API.

## Prerequisites

Before you begin, ensure you have the following:

- **Node.js**: Version 10.x or later installed
- **npm** or **yarn**: For package management
- **Azure DevOps Organization**: Access to an organization in Azure DevOps
- **Personal Access Token (PAT)**: Generated from your Azure DevOps account with appropriate scopes

## Step 1: Create a New Project

First, let's create a new Node.js project and initialize it:

```bash
# Create a directory for your project
mkdir azure-devops-client
cd azure-devops-client

# Initialize npm package
npm init -y
```

## Step 2: Install the Azure DevOps Node API Package

Install the official Azure DevOps Node API package:

```bash
npm install azure-devops-node-api
```

For TypeScript projects, you might also want to install TypeScript and necessary type definitions:

```bash
npm install --save-dev typescript @types/node
```

## Step 3: Set Up Authentication

The Azure DevOps Node API supports multiple authentication methods. Personal Access Tokens (PATs) are recommended for most scenarios.

### Generate a Personal Access Token

1. Sign in to your Azure DevOps organization: `https://dev.azure.com/{your-organization}`
2. Click on your profile icon in the top-right corner
3. Select **Personal access tokens**
4. Click **New Token**
5. Give your token a name and set an expiration date
6. Select the appropriate scopes based on what you need to access:
   - For read-only access, select "Read" scopes
   - For read/write access, select scopes without the "Read" suffix
7. Click **Create** and save your token securely

### Create a Configuration File

Create a file named `config.js` to store your configuration:

```javascript
// config.js
module.exports = {
    organization: "https://dev.azure.com/your-organization",
    token: process.env.AZURE_DEVOPS_PAT || "your-personal-access-token"
};
```

> **Security Note**: For production applications, always use environment variables or a secure secret management solution instead of hardcoding tokens.

Set your PAT as an environment variable:

```bash
# For Linux/macOS
export AZURE_DEVOPS_PAT="your-personal-access-token"

# For Windows (Command Prompt)
set AZURE_DEVOPS_PAT=your-personal-access-token

# For Windows (PowerShell)
$env:AZURE_DEVOPS_PAT="your-personal-access-token"
```

## Step 4: Establish Your First Connection

Create a file named `connect.js` with the following code to establish a connection:

```javascript
// connect.js
const azdev = require("azure-devops-node-api");
const config = require("./config");

async function connect() {
    try {
        console.log("Connecting to Azure DevOps...");
        
        // Create authentication handler
        const authHandler = azdev.getPersonalAccessTokenHandler(config.token);
        
        // Create a connection to Azure DevOps
        const connection = new azdev.WebApi(config.organization, authHandler);
        
        // Test the connection
        const connectionData = await connection.connect();
        console.log("Connected successfully!");
        console.log(`Connection established to: ${connectionData.serverUrl}`);
        console.log(`Authenticated as: ${connectionData.authenticatedUser.providerDisplayName}`);
        
        return connection;
    } catch (error) {
        console.error("Connection failed:", error.message);
        throw error;
    }
}

// Self-executing async function
(async () => {
    try {
        const connection = await connect();
        console.log("Connection object is ready to use");
    } catch (error) {
        console.error("Error:", error);
    }
})();
```

Run the script to test your connection:

```bash
node connect.js
```

If everything is configured correctly, you should see confirmation of a successful connection.

## Step 5: Access Your First API Client

Now that we have established a connection, let's access an API client to interact with Azure DevOps:

```javascript
// project-info.js
const azdev = require("azure-devops-node-api");
const config = require("./config");

async function getProjects() {
    try {
        // Create authentication handler
        const authHandler = azdev.getPersonalAccessTokenHandler(config.token);
        
        // Create a connection to Azure DevOps
        const connection = new azdev.WebApi(config.organization, authHandler);
        
        // Get Core API client (for projects, teams, etc.)
        const coreApi = await connection.getCoreApi();
        
        // Get all projects
        const projects = await coreApi.getProjects();
        
        console.log(`Found ${projects.length} projects:`);
        projects.forEach(project => {
            console.log(`- ${project.name} (${project.id})`);
        });
    } catch (error) {
        console.error("Error:", error.message);
    }
}

getProjects();
```

Run the script to list all projects in your organization:

```bash
node project-info.js
```

## Step 6: Work with Common API Clients

The Azure DevOps Node API provides access to multiple API clients for different services. Here are examples of accessing some common API clients:

### Work Item Tracking

```javascript
// Get Work Item Tracking API client
const witApi = await connection.getWorkItemTrackingApi();

// Get a work item by ID
const workItem = await witApi.getWorkItem(42); // Replace with a valid work item ID
console.log(`Work Item Title: ${workItem.fields["System.Title"]}`);
```

### Git Repositories

```javascript
// Get Git API client
const gitApi = await connection.getGitApi();

// Get all repositories
const repositories = await gitApi.getRepositories();
repositories.forEach(repo => {
    console.log(`Repository: ${repo.name}`);
});
```

### Build Pipelines

```javascript
// Get Build API client
const buildApi = await connection.getBuildApi();

// Get build definitions
const definitions = await buildApi.getDefinitions("YourProject");
definitions.forEach(def => {
    console.log(`Build Definition: ${def.name}`);
});
```

### Release Management

```javascript
// Get Release API client
const releaseApi = await connection.getReleaseApi();

// Get release definitions
const releaseDefinitions = await releaseApi.getReleaseDefinitions("YourProject");
releaseDefinitions.forEach(def => {
    console.log(`Release Definition: ${def.name}`);
});
```

## Step 7: Create a TypeScript Version (Optional)

If you're using TypeScript, create a `tsconfig.json` file:

```json
{
    "compilerOptions": {
        "target": "es2018",
        "module": "commonjs",
        "outDir": "./dist",
        "strict": true,
        "esModuleInterop": true,
        "skipLibCheck": true,
        "forceConsistentCasingInFileNames": true
    },
    "include": ["src/**/*"],
    "exclude": ["node_modules"]
}
```

And here's a TypeScript example:

```typescript
// src/connect.ts
import * as azdev from "azure-devops-node-api";

interface Config {
    organization: string;
    token: string;
}

const config: Config = {
    organization: "https://dev.azure.com/your-organization",
    token: process.env.AZURE_DEVOPS_PAT || "your-personal-access-token"
};

async function connect(): Promise<azdev.WebApi> {
    try {
        console.log("Connecting to Azure DevOps...");
        
        // Create authentication handler
        const authHandler = azdev.getPersonalAccessTokenHandler(config.token);
        
        // Create a connection to Azure DevOps
        const connection = new azdev.WebApi(config.organization, authHandler);
        
        // Test the connection
        const connectionData = await connection.connect();
        console.log("Connected successfully!");
        console.log(`Connection established to: ${connectionData.serverUrl}`);
        console.log(`Authenticated as: ${connectionData.authenticatedUser.providerDisplayName}`);
        
        return connection;
    } catch (error) {
        console.error("Connection failed:", error.message);
        throw error;
    }
}

// Self-executing async function
(async () => {
    try {
        const connection = await connect();
        
        // Get Git API client
        const gitApi = await connection.getGitApi();
        
        // Get repositories
        const repositories = await gitApi.getRepositories();
        console.log(`Found ${repositories.length} repositories`);
    } catch (error) {
        if (error instanceof Error) {
            console.error("Error:", error.message);
        } else {
            console.error("Unknown error occurred");
        }
    }
})();
```

Compile and run your TypeScript code:

```bash
# Compile TypeScript
npx tsc

# Run compiled JavaScript
node dist/connect.js
```

## Troubleshooting

Common issues you might encounter and how to solve them:

- **Authentication Errors (401)**: 
  - Verify your PAT is valid and hasn't expired
  - Check if your PAT has the correct scopes
  - Ensure the organization URL is correct

- **Resource Not Found (404)**:
  - Verify the entity (project, repository, etc.) exists
  - Check if you have access permissions to the resource
  - Ensure you're using the correct organization URL format: `https://dev.azure.com/{organization}`

- **SSL Certificate Errors**:
  - If behind a corporate proxy, you might need to configure proxy settings
  - In development environments, you can use the `ignoreSslError` option:
    ```javascript
    const connection = new azdev.WebApi(config.organization, authHandler, {
        ignoreSslError: true // Not recommended for production
    });
    ```

## Next Steps

Now that you have set up your first connection to Azure DevOps and learned how to access various API clients, you can explore:

- [Connect to Azure DevOps Tutorial](../tutorials/connect-to-azure-devops.md) - Detailed connection tutorial with more examples
- [Authentication Guide](./authentication.md) - In-depth guide to authentication options
- [Authentication Cheat Sheet](./authentication-cheat-sheet.md) - Quick reference for authentication methods
- [WebApi Core Documentation](../api-reference/webapi-core/webapi-core.md) - Details on the core WebApi class
- [Work Item Tracking Guide](../api-reference/work-item-tracking/work-item-tracking.md) - Guide to working with work items
- [Git API Guide](../api-reference/git/git-api.md) - Guide to working with repositories and code

For complete API details, visit the [Azure DevOps Services REST API Reference](https://docs.microsoft.com/en-us/rest/api/azure/devops/?view=azure-devops-rest-6.0). 