# WebApi Core

## Overview

The WebApi Core is the foundation of the Azure DevOps Node API. It provides essential connection and authentication functionality to connect to Azure DevOps services and access various API clients. It serves as the entry point for all interactions with the Azure DevOps REST APIs.

## Installation

```bash
npm install azure-devops-node-api
```

## Authentication

The WebApi Core supports multiple authentication methods for connecting to Azure DevOps, including Personal Access Tokens (PAT), Basic Authentication, Bearer Tokens, and NTLM.

```typescript
import * as azdev from "azure-devops-node-api";

// Your organization URL
const orgUrl = "https://dev.azure.com/your-organization";

// Authentication using Personal Access Token (recommended)
const token = "your-personal-access-token";
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

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";
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

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-bearer-token";

// Create connection with bearer token
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

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Connect and get instance information
const connectionData = await connection.connect();
console.log(`Connected to organization: ${connectionData.instanceId}`);
console.log(`API version: ${connectionData.deploymentType}`);
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

**Returns**: A Promise that resolves to a WorkItemTrackingApi client.

**Example**:
```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get Work Item Tracking API client
const witApi = await connection.getWorkItemTrackingApi();

// Get work item
const workItem = await witApi.getWorkItem(42);
console.log(`Work Item Title: ${workItem.fields["System.Title"]}`);
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

**Returns**: A Promise that resolves to a GitApi client.

**Example**:
```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get Git API client
const gitApi = await connection.getGitApi();

// Get repositories
const repositories = await gitApi.getRepositories();
repositories.forEach(repo => {
    console.log(`Repository: ${repo.name}`);
});
```

## Common Errors

Common errors when working with the WebApi Core:

```typescript
try {
    const connection = new azdev.WebApi(orgUrl, authHandler);
    await connection.connect();
} catch (error) {
    if (error.statusCode === 401) {
        console.log("Authentication error. Check your credentials or token.");
    } else if (error.statusCode === 404) {
        console.log("Organization not found. Check your organization URL.");
    } else if (error.message.includes("unable to get local issuer certificate")) {
        console.log("SSL certificate error. Consider setting ignoreSslError option to true.");
    } else {
        console.log(`Error connecting: ${error.message}`);
    }
}
```

## See Also

- [Authentication Guide](../../getting-started/authentication.md)
- [Connection Options](./connection-options.md)
- [Authentication Handlers](./authentication-handlers.md) 