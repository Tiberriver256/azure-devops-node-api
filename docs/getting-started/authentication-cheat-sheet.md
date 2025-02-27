# Authentication Cheat Sheet

This quick reference guide provides concise examples for common authentication scenarios with the Azure DevOps Node API.

## Personal Access Token (PAT) Authentication

```typescript
// Quick PAT authentication example
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const pat = process.env.AZURE_DEVOPS_PAT; // Get from environment variable

const authHandler = azdev.getPersonalAccessTokenHandler(pat);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Test the connection
const connectionData = await connection.connect();
console.log(`Connected to ${connectionData.authenticatedUser.customDisplayName}'s organization`);
```

## Basic Authentication

```typescript
// Quick basic authentication example
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const username = "your-username";
const password = "your-password"; // Or use a PAT here

const authHandler = azdev.getBasicHandler(username, password);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

## Bearer Token (OAuth)

```typescript
// Quick bearer token example
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const bearerToken = "your-oauth-access-token"; // From your OAuth flow

// Method 1
const authHandler = azdev.getBearerHandler(bearerToken);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Method 2 (convenience method)
const connection2 = azdev.WebApi.createWithBearerToken(orgUrl, bearerToken);
```

## NTLM Authentication

```typescript
// Quick NTLM authentication example
import * as azdev from "azure-devops-node-api";

const serverUrl = "https://azuredevops.yourcompany.com/tfs";
const username = "domain\\username";
const password = "your-password";
const domain = "your-domain"; // Optional

const authHandler = azdev.getNtlmHandler(username, password, undefined, domain);
const connection = new azdev.WebApi(serverUrl, authHandler);
```

## Environment Variables

Recommended environment variables for secure credential storage:

```bash
# PAT authentication
export AZURE_DEVOPS_PAT="your-personal-access-token"

# Organization URL
export AZURE_DEVOPS_ORG_URL="https://dev.azure.com/your-organization"
```

## Creating API Clients

```typescript
// After creating a connection, get API clients:
const connection = new azdev.WebApi(orgUrl, authHandler);

// Work Item Tracking API
const witApi = await connection.getWorkItemTrackingApi();

// Git API
const gitApi = await connection.getGitApi();

// Build API
const buildApi = await connection.getBuildApi();

// Core API (Projects, Teams, etc.)
const coreApi = await connection.getCoreApi();

// Release API
const releaseApi = await connection.getReleaseApi();
```

## Connection Options

```typescript
// Authentication with connection options
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const pat = process.env.AZURE_DEVOPS_PAT;

// Options object
const options = {
    proxy: {
        proxyUrl: "http://your-proxy-server:8080",
        proxyUsername: "proxy-username", // Optional
        proxyPassword: "proxy-password", // Optional
        proxyBypassHosts: ["localhost"]  // Optional
    },
    ignoreSslError: false,               // Whether to ignore SSL errors
    allowRedirects: true,                // Whether to follow redirects
    maxRetries: 3                        // Maximum request retry count
};

const authHandler = azdev.getPersonalAccessTokenHandler(pat);
const connection = new azdev.WebApi(orgUrl, authHandler, options);
```

## Connectivity Test

```typescript
// Quick connectivity test
import * as azdev from "azure-devops-node-api";

async function testConnection() {
    try {
        const pat = process.env.AZURE_DEVOPS_PAT;
        const orgUrl = process.env.AZURE_DEVOPS_ORG_URL;
        
        // Validate environment variables
        if (!pat || !orgUrl) {
            throw new Error("Missing required environment variables");
        }
        
        const authHandler = azdev.getPersonalAccessTokenHandler(pat);
        const connection = new azdev.WebApi(orgUrl, authHandler);
        
        // Try to connect
        console.log("Connecting to Azure DevOps...");
        const connectionData = await connection.connect();
        
        console.log("Connection successful!");
        console.log(`Connected to: ${connectionData.authenticatedUser.customDisplayName}`);
        console.log(`Organization: ${connectionData.instanceId}`);
        console.log(`API Version: ${connectionData.apiVersion}`);
        
        return true;
    } catch (error) {
        console.error("Connection failed:", error.message);
        return false;
    }
}

// Run the test
testConnection();
```

## See Also

For more detailed information, refer to:

- [Authentication Guide](authentication.md)
- [OAuth Authentication with Azure DevOps](oauth-authentication.md)
- [Security Best Practices](security-best-practices.md)
- [WebApi Core Documentation](../api-reference/webapi-core/webapi-core.md)
- [Troubleshooting Connection Issues](../troubleshooting/connection-issues.md) 