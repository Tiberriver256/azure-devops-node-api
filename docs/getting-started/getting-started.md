# Getting Started with Azure DevOps Node API

**Navigation**: [Home](../index.md) > [Getting Started](./index.md) > Getting Started Guide

## Overview

This guide will help you get started with the Azure DevOps Node API, a JavaScript/TypeScript client library for interacting with Azure DevOps. Whether you're building DevOps tools, integrating with your existing systems, or automating your workflows, this guide will help you take your first steps with the API.

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

### Generate a Personal Access Token (PAT)

Follow these steps to create a PAT:

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

### Set Environment Variables

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
        
        // Create WebApi instance with organization URL and auth handler
        const connection = new azdev.WebApi(config.orgUrl, authHandler);
        
        // Connect and get connection data to verify connection works
        console.log("Connecting...");
        const connectionData = await connection.connect();
        
        console.log("Connection successful!");
        console.log(`Connected to organization: ${connectionData.instanceId}`);
        console.log(`API Version: ${connectionData.apiVersion}`);
        console.log(`Server deployment type: ${connectionData.deploymentType}`);
        
        return connection;
    } catch (error) {
        console.error("Connection failed", error);
        throw error;
    }
}

// Self-executing async function to run the example
(async () => {
    try {
        const connection = await connect();
        
        // Get a client for a specific API area
        const workItemTrackingApi = await connection.getWorkItemTrackingApi();
        console.log("Work Item Tracking API client created successfully");
        
        // List some projects to verify the connection works
        const projects = await workItemTrackingApi.getProjects();
        console.log("\nProjects in your organization:");
        
        projects.slice(0, 5).forEach(project => {
            console.log(`- ${project.name}`);
        });
        
        if (projects.length > 5) {
            console.log(`  ... and ${projects.length - 5} more projects`);
        }
    } catch (error) {
        console.error("Error:", error.message);
    }
})();
```

## Step 5: Run the Connection Script

Run the script to test your connection:

```bash
node connect.js
```

If everything is set up correctly, you should see output similar to:

```
Creating connection to Azure DevOps...
Connecting...
Connection successful!
Connected to organization: 01234567-89ab-cdef-0123-456789abcdef
API Version: 7.1
Server deployment type: Hosted
Work Item Tracking API client created successfully

Projects in your organization:
- Project Alpha
- Project Beta
- Project Gamma
...
```

## Step 6: Explore Different Authentication Methods (Optional)

You can try different authentication methods by modifying your connection code:

### Basic Authentication

```javascript
// Using username and password (not recommended for production)
const username = "your-username";
const password = "your-password";
const basicAuthHandler = azdev.getBasicHandler(username, password);
const connection = new azdev.WebApi(config.orgUrl, basicAuthHandler);
```

### Bearer Token Authentication

```javascript
// Using bearer token
const bearerToken = "your-bearer-token";
const connection = azdev.WebApi.createWithBearerToken(config.orgUrl, bearerToken);
```

### NTLM Authentication (for on-premises)

```javascript
// Using NTLM for on-premises Azure DevOps Server
const username = "domain\\username";
const password = "your-password";
const ntlmAuthHandler = azdev.getNtlmHandler(username, password);
const connection = new azdev.WebApi(config.orgUrl, ntlmAuthHandler);
```

## Step 7: Configure Connection Options (Optional)

You can configure various connection options to customize the behavior of the WebApi client:

### Proxy Configuration

```javascript
const options = {
    proxy: {
        proxyUrl: "http://proxy-server:8080",
        proxyUsername: "proxy-user",
        proxyPassword: "proxy-password",
        proxyBypassHosts: ["localhost"]
    }
};
```

### SSL Options

```javascript
const options = {
    ignoreSslError: false,  // Set to true to ignore SSL errors (not recommended for production)
    cert: {
        caFile: "/path/to/ca-file.pem",  // Path to CA certificate file
        certFile: "/path/to/cert.pem",   // Path to client certificate file
        keyFile: "/path/to/key.pem",     // Path to client key file
        passphrase: "certificate-passphrase" // Passphrase for the key file
    }
};
```

### HTTP Options

```javascript
const options = {
    socketTimeout: 30000,   // 30 seconds
    allowRetries: true,
    maxRetries: 3
};
```

### Complete Options Example

```javascript
// Create connection with options
const options = {
    // Proxy configuration
    proxy: {
        proxyUrl: "http://proxy-server:8080",
        proxyUsername: "proxy-user",
        proxyPassword: "proxy-password",
        proxyBypassHosts: ["localhost"]
    },
    
    // SSL options
    ignoreSslError: false,  // Set to true to ignore SSL errors (not recommended for production)
    
    // HTTP options
    socketTimeout: 30000,   // 30 seconds
    allowRetries: true,
    maxRetries: 3
};

const connection = new azdev.WebApi(config.orgUrl, authHandler, options);
```

## Troubleshooting

If you encounter any issues:

### Authentication Errors (401)
Verify your Personal Access Token (PAT) is valid and has the correct scopes.

### Organization Not Found (404)
Check your organization URL is correct.

### Certificate Errors
If you're behind a corporate firewall with SSL inspection, you might need to set `ignoreSslError: true` or configure the appropriate certificates.

### Proxy Errors
If you're behind a proxy, configure the proxy settings as shown in Step 7.

## Next Steps

Now that you've successfully connected to Azure DevOps, you can:

1. Explore other API clients available from the WebApi instance
2. Create work items, manage builds, or interact with repositories
3. Build a complete application that integrates with Azure DevOps

Check out the other tutorials and API references for more information on working with specific Azure DevOps services.

## Complete Code Example

Here's a complete example that puts everything together:

```typescript
// Import the Azure DevOps Node API package
import * as azdev from "azure-devops-node-api";
// Optional: Load environment variables from .env file
require("dotenv").config();

/**
 * Connects to Azure DevOps and returns API clients
 * @returns {Promise<object>} Object containing connection and API clients
 */
async function connectToAzureDevOps() {
    // Get configuration from environment variables
    // Security Note: In production, always store tokens in environment variables
    // or a secure secret management solution, not in your code.
    const orgUrl = process.env.AZURE_DEVOPS_ORG_URL;
    const token = process.env.AZURE_DEVOPS_TOKEN;

    // Validate required configuration
    if (!orgUrl || !token) {
        throw new Error("Please set AZURE_DEVOPS_ORG_URL and AZURE_DEVOPS_TOKEN environment variables");
    }

    try {
        // Create authentication handler using Personal Access Token (PAT)
        const authHandler = azdev.getPersonalAccessTokenHandler(token);
        
        // Connection options
        const options = {
            allowRetries: true,
            maxRetries: 2
        };
        
        // Create WebApi instance
        const connection = new azdev.WebApi(orgUrl, authHandler, options);
        
        // Connect and verify
        const connectionData = await connection.connect();
        console.log(`Connected to ${connectionData.instanceId}`);
        console.log(`API Version: ${connectionData.apiVersion}`);
        console.log(`Server deployment type: ${connectionData.deploymentType}`);
        
        // Get API clients
        // These clients provide access to different Azure DevOps services
        const gitApi = await connection.getGitApi();
        console.log("Git API client created successfully");
        
        const workItemTrackingApi = await connection.getWorkItemTrackingApi();
        console.log("Work Item Tracking API client created successfully");
        
        const buildApi = await connection.getBuildApi();
        console.log("Build API client created successfully");
        
        // Return all clients for use in the application
        return {
            connection,
            gitApi,
            workItemTrackingApi,
            buildApi
        };
    } catch (error) {
        // Handle specific error types
        if (error.statusCode === 401) {
            console.error("Authentication failed. Check your Personal Access Token.");
        } else if (error.statusCode === 404) {
            console.error("Organization not found. Check your organization URL.");
        } else if (error.message && error.message.includes("certificate")) {
            console.error("SSL Certificate error. You may need to configure SSL options.");
        } else {
            console.error("Failed to connect to Azure DevOps:", error.message);
        }
        throw error;
    }
}

// Usage example
(async () => {
    try {
        // Connect and get API clients
        const { gitApi, workItemTrackingApi, buildApi } = await connectToAzureDevOps();
        
        // Now you can use these API clients to interact with Azure DevOps
        
        // Example 1: List repositories
        const repositories = await gitApi.getRepositories();
        console.log(`Found ${repositories.length} repositories`);
        repositories.slice(0, 3).forEach(repo => {
            console.log(`- ${repo.name} (${repo.defaultBranch || 'no default branch'})`);
        });
        
        // Example 2: List work items
        // Note: Replace with actual work item IDs from your project
        const workItemIds = [1, 2, 3];
        const workItems = await workItemTrackingApi.getWorkItems(workItemIds);
        console.log(`Retrieved ${workItems.length} work items`);
        workItems.forEach(item => {
            console.log(`- #${item.id}: ${item.fields["System.Title"]}`);
        });
        
        // Example 3: List build pipelines
        // Replace with your actual project name
        const projectName = "YourProject";
        const buildPipelines = await buildApi.getDefinitions(projectName);
        console.log(`Found ${buildPipelines.length} build pipelines in ${projectName}`);
        buildPipelines.slice(0, 3).forEach(pipeline => {
            console.log(`- ${pipeline.name} (${pipeline.id})`);
        });
    } catch (error) {
        console.error("Error:", error.message);
        process.exit(1);
    }
})();
```

## See Also

- [Authentication Guide](./authentication.md) - Detailed information about authentication methods
- [API Reference](../api-reference/index.md) - Complete API reference documentation
- [Working with Work Items Tutorial](../tutorials/working-with-work-items.md) - Learn how to work with work items
- [Glossary](../glossary.md) - Standardized terminology for the Azure DevOps Node API 