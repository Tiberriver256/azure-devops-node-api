# Authentication Guide for Azure DevOps Node API

## Overview

This guide describes the authentication methods available in the Azure DevOps Node API. Authentication is the process of securely identifying your application or user to Azure DevOps services, allowing you to access resources with the appropriate permissions.

> **🔑 RECOMMENDATION**: For most scenarios, Personal Access Tokens (PATs) are the recommended authentication method due to their security, flexibility, and scoped access control.

## Authentication Methods for Azure DevOps Node API

The Azure DevOps Node API supports multiple authentication methods:

| Method | Use Case | Security Level | Implementation Complexity |
|--------|----------|----------------|---------------------------|
| Personal Access Token (PAT) | Automated tools, CI/CD pipelines | High | Low |
| Basic Authentication | Development, testing | Medium | Low |
| Bearer Token (OAuth) | Web applications, user-centric tools | High | Medium-High |
| NTLM | On-premises deployments | Medium | Medium |

### Personal Access Token (PAT) Authentication

Personal Access Tokens (PATs) are the recommended authentication method for the Azure DevOps Node API. They provide a secure way to authenticate with Azure DevOps services without using your password directly.

#### Advantages of PATs:
- Scoped permissions (limit access to specific resources)
- Revocable without changing your password
- Expiration dates for enhanced security
- No need to handle multi-factor authentication in your code

#### Generating a PAT:
1. Navigate to your Azure DevOps organization: `https://dev.azure.com/{organization}`
2. Click on your profile icon in the top right corner
3. Select "Personal access tokens"
4. Click "New Token"
5. Configure the token scope based on your application's needs
6. Set an expiration date
7. Create the token and save it securely

#### Using PAT Authentication (TypeScript):

```typescript
import * as azdev from "azure-devops-node-api";

// Organization URL and Personal Access Token
const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";

// Create a connection using Personal Access Token authentication
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Use the connection to access Azure DevOps APIs
async function getRepositories() {
    try {
        // Get Git API client
        const gitApi = await connection.getGitApi();
        
        // Get repositories from the project
        const repositories = await gitApi.getRepositories("your-project");
        console.log(repositories);
    } catch (error) {
        console.error("Error connecting to Azure DevOps:", error);
    }
}

getRepositories();
```

#### Security Best Practices for PATs:

1. **Store tokens securely**: Never hardcode tokens in your application or commit them to source control
2. **Use environment variables**: Store tokens in environment variables or secure configuration systems
3. **Set appropriate scopes**: Limit token permissions to only what your application needs
4. **Rotate tokens regularly**: Create new tokens and invalidate old ones periodically
5. **Set expiration dates**: Use the shortest practical expiration period for your use case

### Basic Authentication

Basic authentication is a simple authentication scheme built into the HTTP protocol. With the Azure DevOps Node API, you can use Basic authentication with your username and password or with a Personal Access Token (PAT) as the password.

#### Using Basic Authentication (TypeScript):

```typescript
import * as azdev from "azure-devops-node-api";

// Organization URL and credentials
const orgUrl = "https://dev.azure.com/your-organization";
const username = "your-username";
const password = "your-password-or-pat"; // Using PAT as password is recommended

// Create a connection using Basic authentication
const authHandler = azdev.getBasicHandler(username, password);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Use the connection to access Azure DevOps APIs
async function getProjects() {
    try {
        // Get Core API client
        const coreApi = await connection.getCoreApi();
        
        // Get projects
        const projects = await coreApi.getProjects();
        console.log(projects);
    } catch (error) {
        console.error("Error connecting to Azure DevOps:", error);
    }
}

getProjects();
```

#### When to Use Basic Authentication:
- Development environments
- Simple scripts and utilities
- When using a Personal Access Token as the password

#### Limitations of Basic Authentication:
- Security risks if using actual password (prefer PAT as password)
- No granular permission control (unless using PAT as password)
- Subject to organizational password policies and complexity requirements

### OAuth 2.0 Authentication

OAuth 2.0 provides a secure way for your application to access Azure DevOps resources on behalf of a user without exposing their credentials to your application.

#### Typical OAuth Flow for Azure DevOps:

1. Register your application with Azure AD
2. Implement authorization code flow to obtain an access token
3. Use the access token for API requests
4. Refresh the token when it expires

#### Using Bearer Token Authentication (TypeScript):

```typescript
import * as azdev from "azure-devops-node-api";

// Organization URL and OAuth access token
const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-oauth-access-token";

// Create a connection using Bearer Token authentication
const authHandler = azdev.getBearerHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Use the connection to access Azure DevOps APIs
async function getTeams() {
    try {
        // Get Core API client
        const coreApi = await connection.getCoreApi();
        
        // Get teams
        const teams = await coreApi.getAllTeams();
        console.log(teams);
    } catch (error) {
        console.error("Error connecting to Azure DevOps:", error);
    }
}

getTeams();
```

#### OAuth Token Management

For production applications, you'll need to implement token refresh logic:

```typescript
import * as azdev from "azure-devops-node-api";

/**
 * Client for Azure DevOps API with OAuth token management
 * Handles token refresh and connection management
 */
class AzureDevOpsClient {
    private orgUrl: string;
    private accessToken: string;
    private tokenExpiration: Date;
    private connection: azdev.WebApi | null = null;
    private refreshToken: string;
    
    /**
     * Creates a new Azure DevOps client with OAuth token management
     * @param orgUrl - The Azure DevOps organization URL
     * @param accessToken - The OAuth access token
     * @param expiresIn - Token expiration time in seconds
     * @param refreshToken - The OAuth refresh token
     */
    constructor(
        orgUrl: string, 
        accessToken: string, 
        expiresIn: number,
        refreshToken: string
    ) {
        this.orgUrl = orgUrl;
        this.accessToken = accessToken;
        this.tokenExpiration = new Date(Date.now() + expiresIn * 1000);
        this.refreshToken = refreshToken;
    }
    
    /**
     * Gets a WebApi connection, refreshing the token if necessary
     * @returns A Promise that resolves to a WebApi connection
     */
    async getConnection(): Promise<azdev.WebApi> {
        try {
            // Check if token needs to be refreshed
            if (this.isTokenExpired()) {
                await this.refreshAccessToken();
            }
            
            // Create or return existing connection
            if (!this.connection) {
                const authHandler = azdev.getBearerHandler(this.accessToken);
                this.connection = new azdev.WebApi(this.orgUrl, authHandler);
            }
            
            return this.connection;
        } catch (error) {
            console.error("Error getting connection:", error.message);
            throw new Error(`Failed to get Azure DevOps connection: ${error.message}`);
        }
    }
    
    /**
     * Checks if the current token is expired or about to expire
     * @returns True if token is expired or will expire within 5 minutes
     */
    private isTokenExpired(): boolean {
        // Check if token is expired or about to expire (within 5 minutes)
        const fiveMinutesFromNow = new Date(Date.now() + 5 * 60 * 1000);
        return this.tokenExpiration < fiveMinutesFromNow;
    }
    
    /**
     * Refreshes the access token using the refresh token
     * @returns A Promise that resolves when the token is refreshed
     */
    private async refreshAccessToken(): Promise<void> {
        try {
            // Implement your token refresh logic here
            // This typically involves calling your auth server with a refresh token
            
            // Example (pseudo-code):
            // const response = await fetch('https://your-auth-server.com/token', {
            //     method: 'POST',
            //     headers: { 'Content-Type': 'application/json' },
            //     body: JSON.stringify({
            //         grant_type: 'refresh_token',
            //         refresh_token: this.refreshToken,
            //         client_id: 'your-client-id'
            //     })
            // });
            // const data = await response.json();
            // this.accessToken = data.access_token;
            // this.tokenExpiration = new Date(Date.now() + data.expires_in * 1000);
            // this.refreshToken = data.refresh_token || this.refreshToken;
            
            // Reset connection so it will be recreated with the new token
            this.connection = null;
        } catch (error) {
            console.error("Failed to refresh access token:", error.message);
            throw new Error("Authentication failed: Unable to refresh access token");
        }
    }
    
    /**
     * Gets Git repositories from a project
     * @param project - The project name
     * @returns A Promise that resolves to an array of repositories
     */
    async getGitRepositories(project: string): Promise<any[]> {
        try {
            const connection = await this.getConnection();
            const gitApi = await connection.getGitApi();
            return await gitApi.getRepositories(project);
        } catch (error) {
            // Handle specific error types
            if (error.statusCode === 401) {
                console.error("Authentication error. Your token may be invalid or expired.");
                throw new Error("Authentication failed. Please re-authenticate.");
            } else if (error.statusCode === 404) {
                console.error(`Project '${project}' not found.`);
                throw new Error(`Project '${project}' not found. Check the project name.`);
            } else {
                console.error("Error getting repositories:", error.message);
                throw error;
            }
        }
    }
    
    /**
     * Gets work items by IDs
     * @param ids - Array of work item IDs
     * @returns A Promise that resolves to an array of work items
     */
    async getWorkItems(ids: number[]): Promise<any[]> {
        try {
            const connection = await this.getConnection();
            const workItemTrackingApi = await connection.getWorkItemTrackingApi();
            return await workItemTrackingApi.getWorkItems(ids);
        } catch (error) {
            console.error("Error getting work items:", error.message);
            throw error;
        }
    }
}

// Usage example
(async () => {
    try {
        // Initialize client with OAuth tokens
        const client = new AzureDevOpsClient(
            "https://dev.azure.com/your-organization",
            "your-access-token",
            3600, // Token expires in 1 hour
            "your-refresh-token"
        );
        
        // Use the client to get repositories
        const repositories = await client.getGitRepositories("YourProject");
        console.log(`Found ${repositories.length} repositories`);
        
        // Use the client to get work items
        const workItems = await client.getWorkItems([1, 2, 3]);
        console.log(`Retrieved ${workItems.length} work items`);
    } catch (error) {
        console.error("Error:", error.message);
    }
})();
```

#### When to Use OAuth:
- Web applications with user login
- Multi-tenant applications
- When you need user-based permissions
- Integration with other Azure services

### NTLM Authentication

For on-premises Azure DevOps Server installations that use Windows authentication:

```typescript
import * as azdev from "azure-devops-node-api";

// Server URL and credentials
const serverUrl = "http://tfs-server:8080/tfs/DefaultCollection";
const username = "domain\\username";
const password = "password";
const domain = "domain"; // Optional if included in username
const workstation = "workstation"; // Optional

// Create a connection using NTLM authentication
const authHandler = azdev.getNtlmHandler(username, password, domain, workstation);
const connection = new azdev.WebApi(serverUrl, authHandler);

// Use the connection to access Azure DevOps Server APIs
async function getProjects() {
    try {
        const coreApi = await connection.getCoreApi();
        const projects = await coreApi.getProjects();
        console.log(projects);
    } catch (error) {
        console.error("Error connecting to Azure DevOps Server:", error);
    }
}

getProjects();
```

### Cross-Origin Authentication (Browser)

For browser-based applications that need to authenticate with Azure DevOps:

```typescript
import * as azdev from "azure-devops-node-api";

// Organization URL and Personal Access Token
const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";

// Create a connection with cross-origin support
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const options = {
    allowCrossOriginAuthentication: true
};
const connection = new azdev.WebApi(orgUrl, authHandler, options);

// Use the connection in your browser application
async function getProjects() {
    try {
        const coreApi = await connection.getCoreApi();
        const projects = await coreApi.getProjects();
        console.log(projects);
    } catch (error) {
        console.error("Error connecting to Azure DevOps:", error);
    }
}

getProjects();
```

### Custom Authentication Handlers

You can create custom authentication handlers for specialized scenarios:

```typescript
import * as azdev from "azure-devops-node-api";
import { IRequestHandler } from "azure-devops-node-api/interfaces/common/VsoBaseInterfaces";

class CustomAuthHandler implements IRequestHandler {
    private token: string;
    private scheme: string;
    
    constructor(token: string, scheme: string = "Bearer") {
        this.token = token;
        this.scheme = scheme;
    }
    
    // Prepare request with custom authentication
    prepareRequest(options: any): void {
        options.headers = options.headers || {};
        options.headers["Authorization"] = `${this.scheme} ${this.token}`;
        
        // Add any additional headers or processing
        options.headers["Custom-Auth-Header"] = "custom-value";
    }

    // Handle the response (usually just returning it)
    handleResponse(response: Response): Promise<Response> {
        // You can add logging, error handling, or token refresh logic here
        return Promise.resolve(response);
    }
}

// Use the custom handler
const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-custom-token";
const authHandler = new CustomAuthHandler(token, "CustomScheme");
const connection = new azdev.WebApi(orgUrl, authHandler);
```

## Troubleshooting Authentication Issues

### Common Authentication Errors

| Error | Description | Solution |
|-------|-------------|----------|
| 401 Unauthorized | Invalid credentials or insufficient permissions | Verify your token/credentials and scopes |
| 403 Forbidden | Authentication successful but access denied | Check resource permissions and token scopes |
| TF400813 | Invalid organization name or credentials | Confirm organization URL and credentials |
| VS800075 | Personal Access Token has expired | Generate a new Personal Access Token |
| AADSTS700016 | Application not found in tenant | Verify app registration in Azure AD |

### Debugging Tips

1. **Enable verbose logging**:
   ```typescript
   process.env.AZURE_DEVOPS_NODE_API_DEBUG = "true";
   ```

2. **Check connection explicitly**:
   ```typescript
   const connectionData = await connection.connect();
   console.log(JSON.stringify(connectionData, null, 2));
   ```

3. **Test with curl**:
   ```bash
   curl -u "username:pat" https://dev.azure.com/{org}/_apis/projects?api-version=7.0
   ```

4. **Verify token scopes**: Ensure your Personal Access Token has appropriate scopes for the APIs you're using

## Environment Configuration

### Environment Variables

The Azure DevOps Node API client looks for authentication information in environment variables:

```bash
# Personal Access Token authentication
export AZURE_DEVOPS_EXT_PAT="your-personal-access-token"

# Organization URL
export AZURE_DEVOPS_ORG_URL="https://dev.azure.com/your-organization"
```

Using environment variables in code:

```typescript
import * as azdev from "azure-devops-node-api";

// Get credentials from environment
const orgUrl = process.env.AZURE_DEVOPS_ORG_URL || "https://dev.azure.com/your-organization";
const token = process.env.AZURE_DEVOPS_EXT_PAT;

if (!token) {
    throw new Error("Azure DevOps Personal Access Token not found in environment variables");
}

// Create connection using environment variables
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

## See Also

- [WebApi Core Documentation](../api-reference/webapi-core/webapi-core.md)
- [Authentication Handlers Reference](../api-reference/webapi-core/authentication-handlers.md)
- [Connection Options](../api-reference/webapi-core/connection-options.md)
- [Connecting to Azure DevOps Tutorial](../tutorials/connect-to-azure-devops.md)
- [Troubleshooting Connection Issues](../troubleshooting/connection-issues.md) 