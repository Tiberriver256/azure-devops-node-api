# Tutorial: Connecting to Azure DevOps

**Navigation**: [Home](../index.md) > [Tutorials](./index.md) > Connect to Azure DevOps

This tutorial walks you through the process of connecting to Azure DevOps using the Azure DevOps Node API. By the end of this guide, you'll be able to establish a connection and verify that it works correctly.

## Prerequisites

Before you begin, make sure you have:

1. Node.js installed (version 10 or later)
2. An Azure DevOps organization
3. A Personal Access Token (PAT) with appropriate permissions

## Step 1: Create a new Node.js project

First, create a new directory for your project and initialize it:

```bash
mkdir azure-devops-connection
cd azure-devops-connection
npm init -y
```

## Step 2: Install the Azure DevOps Node API package

Install the `azure-devops-node-api` package:

```bash
npm install azure-devops-node-api
```

## Step 3: Create a configuration file

Create a new file named `config.js` to store your Azure DevOps connection information:

```javascript
// config.js
module.exports = {
    orgUrl: "https://dev.azure.com/your-organization",
    token: "your-personal-access-token"
};
```

Replace `your-organization` with your organization name and `your-personal-access-token` with your PAT.

> 🔒 Security Note: In a real application, you should store sensitive information like tokens in environment variables or a secure vault, not in your code files.

## Step 4: Create a connection script

Create a new file named `connect.js` with the following code:

```javascript
// connect.js
const azdev = require("azure-devops-node-api");
const config = require("./config");

async function connect() {
    try {
        console.log("Creating connection to Azure DevOps...");
        
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

## Step 5: Run the connection script

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

## Step 6: Explore different authentication methods (optional)

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

## Step 7: Configure connection options (optional)

You can configure various connection options to customize the behavior of the WebApi client:

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

1. **Authentication Errors (401)**: Verify your Personal Access Token (PAT) is valid and has the correct scopes.
2. **Organization Not Found (404)**: Check your organization URL is correct.
3. **Certificate Errors**: If you're behind a corporate firewall with SSL inspection, you might need to set `ignoreSslError: true` or configure the appropriate certificates.
4. **Proxy Errors**: If you're behind a proxy, configure the proxy settings as shown in Step 7.

## Next Steps

Now that you've successfully connected to Azure DevOps, you can:

1. Explore other API clients available from the WebApi instance
2. Create work items, manage builds, or interact with repositories
3. Build a complete application that integrates with Azure DevOps

Check out the other tutorials and API references for more information on working with specific Azure DevOps services.

## Complete Code Example

Here's a complete example that puts everything together:

```javascript
const azdev = require("azure-devops-node-api");
require("dotenv").config();  // Load environment variables from .env file

// Get configuration from environment variables
const orgUrl = process.env.AZURE_DEVOPS_ORG_URL;
const token = process.env.AZURE_DEVOPS_TOKEN;

if (!orgUrl || !token) {
    console.error("Please set AZURE_DEVOPS_ORG_URL and AZURE_DEVOPS_TOKEN environment variables");
    process.exit(1);
}

async function connectToAzureDevOps() {
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
        
        // Example: Get Git API client
        const gitApi = await connection.getGitApi();
        console.log("Git API client created successfully");
        
        // Example: Get Work Item Tracking API client
        const workItemTrackingApi = await connection.getWorkItemTrackingApi();
        console.log("Work Item Tracking API client created successfully");
        
        // Example: Get Build API client
        const buildApi = await connection.getBuildApi();
        console.log("Build API client created successfully");
        
        return {
            connection,
            gitApi,
            workItemTrackingApi,
            buildApi
        };
    } catch (error) {
        console.error("Failed to connect to Azure DevOps", error);
        throw error;
    }
}

// Usage
(async () => {
    try {
        const { gitApi, workItemTrackingApi, buildApi } = await connectToAzureDevOps();
        
        // Now you can use these API clients to interact with Azure DevOps
        // Example: List repositories
        const repositories = await gitApi.getRepositories();
        console.log(`Found ${repositories.length} repositories`);
        
        // Example: List work items
        const workItems = await workItemTrackingApi.getWorkItems([1, 2, 3]);
        console.log(`Retrieved ${workItems.length} work items`);
        
        // Example: List build pipelines
        const buildPipelines = await buildApi.getDefinitions("YourProject");
        console.log(`Found ${buildPipelines.length} build pipelines`);
    } catch (error) {
        console.error("Error:", error.message);
    }
})();
```

## See Also

- [Authentication Guide](../getting-started/authentication.md) - Detailed information about authentication methods
- [API Reference](../api-reference/index.md) - Complete API reference documentation
- [Working with Work Items Tutorial](./working-with-work-items.md) - Learn how to work with work items
- [Glossary](../glossary.md) - Standardized terminology for the Azure DevOps Node API 