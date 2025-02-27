# Authentication Guide for Azure DevOps Node API

## Overview

This guide describes the authentication methods available in the Azure DevOps Node API. Authentication is the process of securely identifying your application or user to Azure DevOps services, allowing you to access resources with the appropriate permissions.

> **🔑 RECOMMENDATION**: For most scenarios, Personal Access Tokens (PATs) are the recommended authentication method due to their security, flexibility, and scoped access control.

## Authentication Methods

The Azure DevOps Node API supports multiple authentication methods:

| Method | Use Case | Security Level | Implementation Complexity |
|--------|----------|----------------|---------------------------|
| Personal Access Token (PAT) | Automated tools, CI/CD pipelines | High | Low |
| Basic Authentication | Development, testing | Medium | Low |
| Bearer Token (OAuth) | Web applications, user-centric tools | High | Medium-High |
| NTLM | On-premises deployments | Medium | Medium |

## Personal Access Token (PAT) Authentication

### What are PATs?

Personal Access Tokens (PATs) are alternatives to passwords that provide a secure way to authenticate with Azure DevOps services. PATs offer several advantages:

- **Scoped permissions**: Limit token access to specific resources and operations
- **Expiration**: Set an expiration date for each token
- **Revocation**: Easily revoke tokens if they are compromised
- **Auditability**: Track token usage in audit logs

### Generating a PAT

To use PAT authentication, you first need to generate a token:

1. Sign in to your Azure DevOps organization: `https://dev.azure.com/{your-organization}`
2. Click on your profile picture in the top right corner and select "Security"
3. Select "Personal access tokens" and click "New Token"
4. Name your token and set an expiration date
5. Select the scopes (permissions) your application needs
   - For most API operations, "Read" scope is sufficient
   - For write operations, select the appropriate scopes
6. Click "Create" and save your token securely (it will only be shown once)

### Using PAT Authentication

```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";

// Create a PAT authentication handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Create the WebApi connection
const connection = new azdev.WebApi(orgUrl, authHandler);

// Use the connection
async function useConnection() {
    try {
        // Test the connection
        const connData = await connection.connect();
        console.log(`Connected to organization: ${connData.authenticatedUser.customDisplayName}`);
        
        // Get a client for a specific API
        const gitApi = await connection.getGitApi();
        
        // Use the client
        const repos = await gitApi.getRepositories();
        console.log(`Found ${repos.length} repositories`);
    } catch (error) {
        console.error("Error connecting to Azure DevOps:", error);
    }
}

useConnection();
```

### Security Best Practices for PATs

- **Store tokens securely**: Never hardcode tokens in your source code
- **Use environment variables or a secret manager**:
  ```typescript
  const token = process.env.AZURE_DEVOPS_PAT;
  ```
- **Set appropriate scopes**: Only request the permissions your application needs
- **Set reasonable expiration dates**: Balance security with maintenance overhead
- **Use different tokens for different applications**: Limit the impact if a token is compromised
- **Rotate tokens regularly**: Establish a process for token rotation

## Basic Authentication

Basic authentication uses a username and password to authenticate requests. While simple to implement, it has security limitations and is primarily recommended for development and testing.

### Using Basic Authentication

```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const username = "your-username";
const password = "your-password"; // or PAT used as password

// Create a Basic authentication handler
const authHandler = azdev.getBasicHandler(username, password);

// Create the WebApi connection
const connection = new azdev.WebApi(orgUrl, authHandler);

// Use the connection
async function useConnection() {
    try {
        const connData = await connection.connect();
        console.log("Connection successful");
    } catch (error) {
        console.error("Error connecting to Azure DevOps:", error);
    }
}

useConnection();
```

### When to Use Basic Authentication

- **Development environments**: For quick testing during development
- **Simple scripts**: For one-off automation tasks
- **PAT as password**: You can use a PAT as the password with your username:
  ```typescript
  const authHandler = azdev.getBasicHandler("username", "your-pat-token");
  ```

### Basic Authentication Limitations

- **Security risk**: Sending credentials with each request creates more exposure
- **No granular permissions**: Unlike PATs, basic auth doesn't support scopes
- **Password policies**: Subject to organizational password complexity and rotation policies

## OAuth Authentication (Bearer Token)

OAuth 2.0 is an industry-standard protocol for authorization that enables secure delegated access. It's ideal for web applications where users want to grant your application access to their Azure DevOps resources without sharing credentials.

### OAuth Flow Overview

The typical OAuth flow for Azure DevOps:

1. **Register your application** with Azure AD
2. **Implement authorization code flow** to obtain an access token
3. **Use the token** to authenticate API requests
4. **Refresh the token** when it expires

### Using Bearer Token Authentication

Once you have obtained an OAuth access token:

```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const bearerToken = "your-oauth-access-token";

// Create a Bearer Token authentication handler
const authHandler = azdev.getBearerHandler(bearerToken);

// Create the WebApi connection
const connection = new azdev.WebApi(orgUrl, authHandler);

// Alternative: Use the built-in factory method
const connection2 = azdev.WebApi.createWithBearerToken(orgUrl, bearerToken);

// Use the connection
async function useConnection() {
    try {
        const connData = await connection.connect();
        console.log("Connection successful");
    } catch (error) {
        console.error("Error connecting to Azure DevOps:", error);
    }
}

useConnection();
```

### OAuth Token Management

For production applications, implement token refresh logic:

```typescript
import * as azdev from "azure-devops-node-api";

class AzureDevOpsClient {
    private connection: azdev.WebApi | null = null;
    private tokenExpirationTime: Date | null = null;
    
    constructor(
        private orgUrl: string,
        private clientId: string,
        private clientSecret: string,
        private refreshToken: string
    ) {}
    
    async getConnection(): Promise<azdev.WebApi> {
        // Check if token needs refresh
        if (!this.connection || this.isTokenExpired()) {
            const token = await this.refreshAccessToken();
            const authHandler = azdev.getBearerHandler(token.accessToken);
            this.connection = new azdev.WebApi(this.orgUrl, authHandler);
            
            // Set expiration time
            const expiresIn = token.expiresIn || 3600; // Default 1 hour
            this.tokenExpirationTime = new Date(Date.now() + expiresIn * 1000);
        }
        
        return this.connection;
    }
    
    private isTokenExpired(): boolean {
        if (!this.tokenExpirationTime) return true;
        
        // Refresh if less than 5 minutes until expiration
        const fiveMinutesFromNow = new Date(Date.now() + 5 * 60 * 1000);
        return this.tokenExpirationTime < fiveMinutesFromNow;
    }
    
    private async refreshAccessToken() {
        // Implementation would use refresh token to get a new access token
        // from your OAuth provider
        
        // This is a simplified example - real implementation would call
        // your OAuth token endpoint
        return {
            accessToken: "new-access-token",
            refreshToken: "new-refresh-token",
            expiresIn: 3600
        };
    }
}
```

### When to Use OAuth

- **Web applications**: Where users log in with their own credentials
- **Multi-tenant applications**: Applications that serve multiple organizations
- **User-based permissions**: When you need to act on behalf of the user
- **Integration with other Azure services**: When part of a broader Azure ecosystem

## Advanced Authentication Scenarios

### NTLM Authentication (On-premises)

For on-premises Azure DevOps Server installations that use Windows authentication:

```typescript
import * as azdev from "azure-devops-node-api";

const serverUrl = "https://azuredevops.yourcompany.com/tfs";
const username = "domain\\username";
const password = "your-password";
const domain = "your-domain";
const workstation = "your-workstation"; // Optional

// Create an NTLM authentication handler
const authHandler = azdev.getNtlmHandler(username, password, workstation, domain);

// Create the WebApi connection
const connection = new azdev.WebApi(serverUrl, authHandler);
```

### Cross-Origin Authentication

For browser-based applications that need to make authenticated requests:

```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";

// Enable cross-origin authentication
const authHandler = azdev.getPersonalAccessTokenHandler(token, true);

// Create the WebApi connection
const connection = new azdev.WebApi(orgUrl, authHandler);
```

### Custom Authentication Handlers

For specialized authentication requirements, you can implement custom handlers:

```typescript
import * as azdev from "azure-devops-node-api";
import { IRequestHandler, IRequestOptions } from "typed-rest-client/Interfaces";

class CustomAuthHandler implements IRequestHandler {
    constructor(private token: string, private scheme: string) {}

    // Add authentication headers to the request
    prepareRequest(options: IRequestOptions): void {
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
| VS800075 | PAT has expired | Generate a new PAT |
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

4. **Verify token scopes**: Ensure your PAT has appropriate scopes for the APIs you're using

## Environment Configuration

### Environment Variables

The Azure DevOps Node API looks for authentication information in environment variables:

```bash
# PAT authentication
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
    throw new Error("Azure DevOps PAT token not found in environment variables");
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