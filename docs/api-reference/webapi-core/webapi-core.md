# WebApi Core

## Overview

The WebApi Core is the foundation of the Azure DevOps Node API client. It provides essential connection and authentication functionality to connect to Azure DevOps services and access various API clients. It serves as the entry point for all interactions with the Azure DevOps REST APIs.

## Installation

```bash
npm install azure-devops-node-api
```

## Authentication

The WebApi Core supports multiple authentication methods for connecting to Azure DevOps, including Personal Access Tokens (PATs), Basic Authentication, Bearer Tokens, and NTLM.

```typescript
import * as azdev from "azure-devops-node-api";

// Your organization URL
const orgUrl = "https://dev.azure.com/your-organization";

// Authentication using Personal Access Token (recommended)
// Security Note: In production, always store tokens in environment variables
// or a secure secret management solution, not in your code.
const token = process.env.AZURE_DEVOPS_PAT || "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Alternative: Basic Authentication
const username = "your-username";
const password = "your-password";
const basicAuthHandler = azdev.getBasicHandler(username, password);
const basicConnection = new azdev.WebApi(orgUrl, basicAuthHandler);
```

## Common Methods

### ⭐ constructor

**Purpose**: Creates a new WebApi connection to Azure DevOps.

**Signature**:
```typescript
constructor(
    defaultUrl: string, 
    authHandler: IRequestHandler, 
    options?: IRequestOptions, 
    requestSettings?: IWebApiRequestSettings
)
```

**Parameters**:
- `defaultUrl`: The URL of your Azure DevOps organization
- `authHandler`: Authentication handler (from one of the handler factory methods)
- `options`: (Optional) Request options for the REST client
- `requestSettings`: (Optional) Settings for the user agent string

**Returns**: A new WebApi instance that can be used to access API clients.

**Example**:
```typescript
import * as azdev from "azure-devops-node-api";

// Organization URL and authentication information
const orgUrl = "https://dev.azure.com/your-organization";
// Security Note: In production, always store tokens in environment variables
// or a secure secret management solution, not in your code.
const token = process.env.AZURE_DEVOPS_PAT || "your-personal-access-token";

// Create authentication handler using Personal Access Token
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Basic connection
const connection = new azdev.WebApi(orgUrl, authHandler);

// Connection with custom options
const options = {
    proxy: {
        proxyUrl: "http://proxy-url:8080",
        proxyUsername: "proxy-username",
        proxyPassword: "proxy-password",
        proxyBypassHosts: ["localhost"]
    },
    ignoreSslError: false
};
const connectionWithOptions = new azdev.WebApi(orgUrl, authHandler, options);
```

### ⭐ createWithBearerToken

**Purpose**: Static factory method to create a WebApi connection using a bearer token.

**Signature**:
```typescript
static createWithBearerToken(
    defaultUrl: string, 
    token: string, 
    options?: IRequestOptions
): WebApi
```

**Parameters**:
- `defaultUrl`: The URL of your Azure DevOps organization
- `token`: The bearer token for authentication
- `options`: (Optional) Request options for the REST client

**Returns**: A new WebApi instance configured with bearer token authentication.

**Example**:
```typescript
import * as azdev from "azure-devops-node-api";

// Organization URL and bearer token
const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-bearer-token";

// Create connection with bearer token
// This is useful when using OAuth authentication
const connection = azdev.WebApi.createWithBearerToken(orgUrl, token);
```

### ⭐ connect

**Purpose**: Establishes a connection to Azure DevOps and retrieves the connection data.

**Signature**:
```typescript
async connect(): Promise<ConnectionData>
```

**Parameters**: None

**Returns**: A Promise that resolves to the ConnectionData object containing information about the Azure DevOps instance.

**Example**:
```typescript
import * as azdev from "azure-devops-node-api";

// Organization URL and authentication information
const orgUrl = "https://dev.azure.com/your-organization";
const token = process.env.AZURE_DEVOPS_PAT || "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Connect and get instance information
try {
    const connectionData = await connection.connect();
    console.log(`Connected to organization: ${connectionData.instanceId}`);
    console.log(`API version: ${connectionData.apiVersion}`);
    console.log(`Server deployment type: ${connectionData.deploymentType}`);
} catch (error) {
    // Handle connection errors
    if (error.statusCode === 401) {
        console.error("Authentication failed. Check your credentials or token.");
    } else if (error.statusCode === 404) {
        console.error("Organization not found. Check your organization URL.");
    } else {
        console.error(`Connection error: ${error.message}`);
    }
}
```

### 🔍 getWorkItemTrackingApi

**Purpose**: Gets a client for accessing the Work Item Tracking API.

**Signature**:
```typescript
async getWorkItemTrackingApi(
    serverUrl?: string,
    handlers?: IRequestHandler[]
): Promise<IWorkItemTrackingApi>
```

**Parameters**:
- `serverUrl`: (Optional) Override the server URL for this API client
- `handlers`: (Optional) Override the request handlers for this API client

**Returns**: A Promise that resolves to a Work Item Tracking API client.

**Example**:
```typescript
import * as azdev from "azure-devops-node-api";

// Organization URL and authentication information
const orgUrl = "https://dev.azure.com/your-organization";
const token = process.env.AZURE_DEVOPS_PAT || "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get Work Item Tracking API client
try {
    // Get the Work Item Tracking API client
    const workItemTrackingApi = await connection.getWorkItemTrackingApi();
    
    // Use the client to get a work item
    const workItem = await workItemTrackingApi.getWorkItem(42);
    console.log(`Work Item Title: ${workItem.fields["System.Title"]}`);
} catch (error) {
    // Handle errors
    if (error.statusCode === 404) {
        console.error("Work item not found.");
    } else {
        console.error(`Error: ${error.message}`);
    }
}
```

### 🔍 getGitApi

**Purpose**: Gets a client for accessing the Git API.

**Signature**:
```typescript
async getGitApi(
    serverUrl?: string,
    handlers?: IRequestHandler[]
): Promise<IGitApi>
```

**Parameters**:
- `serverUrl`: (Optional) Override the server URL for this API client
- `handlers`: (Optional) Override the request handlers for this API client

**Returns**: A Promise that resolves to a Git API client.

**Example**:
```typescript
import * as azdev from "azure-devops-node-api";

// Organization URL and authentication information
const orgUrl = "https://dev.azure.com/your-organization";
const token = process.env.AZURE_DEVOPS_PAT || "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get Git API client and list repositories
async function listRepositories() {
    try {
        // Get the Git API client
        const gitApi = await connection.getGitApi();
        
        // Get repositories
        const repositories = await gitApi.getRepositories();
        console.log(`Found ${repositories.length} repositories:`);
        
        // Display repository information
        repositories.forEach(repo => {
            console.log(`Repository: ${repo.name} (${repo.id})`);
            console.log(`  Default branch: ${repo.defaultBranch}`);
            console.log(`  URL: ${repo.remoteUrl}`);
        });
    } catch (error) {
        // Handle specific error types
        if (error.statusCode === 401) {
            console.error("Authentication error. Check your credentials or token.");
        } else if (error.statusCode === 403) {
            console.error("Authorization error. You don't have permission to access repositories.");
        } else {
            console.error(`Error listing repositories: ${error.message}`);
        }
    }
}

// Call the function
listRepositories();
```

## Common Errors

Common errors when working with the WebApi Core:

```typescript
try {
    const connection = new azdev.WebApi(orgUrl, authHandler);
    await connection.connect();
} catch (error) {
    if (error.statusCode === 401) {
        console.error("Authentication error. Check your credentials or token.");
    } else if (error.statusCode === 404) {
        console.error("Organization not found. Check your organization URL.");
    } else if (error.message.includes("unable to get local issuer certificate")) {
        console.error("SSL certificate error. Consider setting ignoreSslError option to true.");
    } else if (error.code === 'ECONNREFUSED') {
        console.error("Connection refused. Check your network or proxy settings.");
    } else if (error.code === 'ETIMEDOUT') {
        console.error("Connection timed out. Check your network or try again later.");
    } else {
        console.error(`Error connecting: ${error.message}`);
    }
}
```

## See Also

- [Authentication Guide](../../getting-started/authentication.md)
- [Connection Options](./connection-options.md)
- [Authentication Handlers](./authentication-handlers.md)
- [Glossary](../../glossary.md) - Standardized terminology for the Azure DevOps Node API 