# Authentication Concepts

<!-- 
Metadata:
- Difficulty: Beginner ⭐
- Related API Components: BasicAuthenticationCredentialHandler, BearerTokenCredentialHandler, PersonalAccessTokenCredentialHandler
- Key Use Cases: Service Connections, CLI Authentication, User Authentication
- API Version Applicability: All versions
-->

**Difficulty Level: Beginner ⭐**

Authentication in the Azure DevOps Node API provides several mechanisms to securely connect to Azure DevOps services with appropriate permissions.

> **TL;DR:** The Azure DevOps Node API supports multiple authentication methods including Personal Access Tokens (PATs), Basic Authentication, Bearer Tokens, and OAuth. PATs are recommended for most scenarios due to their security and scoping capabilities.

## What You Need to Know

Before diving into this concept, you should be familiar with:

- **Azure DevOps Organization**: Understanding of Azure DevOps organizations and how to access them
- **Security Basics**: Basic understanding of authentication vs. authorization
- **Access Control**: Familiarity with permission scopes and access tokens
- **Node.js Basics**: Understanding of JavaScript/TypeScript and async programming

## Overview

> **TL;DR:** Authentication is how your application proves its identity to Azure DevOps to access resources securely. The API offers several authentication handlers to support different scenarios.

Authentication is the process of verifying the identity of a user or application attempting to access Azure DevOps resources. The Azure DevOps Node API provides multiple authentication mechanisms to accommodate different scenarios, including:

1. **Personal Access Tokens (PATs)**: Token-based authentication with configurable scopes and expiration
2. **Basic Authentication**: Username and password authentication (not recommended for production)
3. **Bearer Token Authentication**: OAuth 2.0 token-based authentication
4. **Azure Active Directory**: Integration with Azure AD for enterprise scenarios

Choosing the right authentication method is crucial for both security and functionality. Each method has different implications for security, user experience, and application architecture.

## Key Terminology

| Term | Definition |
|------|------------|
| Personal Access Token (PAT) | A security token that functions like a password with configurable scopes and expiration |
| OAuth 2.0 | An industry-standard protocol for authorization that enables third-party applications to access resources |
| Bearer Token | An access token that grants access to a protected resource, typically obtained through OAuth |
| Credential Handler | A component that processes authentication credentials and adds them to API requests |
| Scope | A permission boundary that limits what actions a token can perform |

## Key Components

> **TL;DR:** The authentication system consists of credential handlers that process different types of authentication tokens and add them to API requests.

The authentication system in the Azure DevOps Node API consists of these key components:

- **WebApi Connection**: The primary client that uses a credential handler to authenticate requests
- **Credential Handlers**: Classes that process different types of credentials:
  - `PersonalAccessTokenCredentialHandler`: Handles PAT authentication
  - `BasicAuthenticationCredentialHandler`: Handles username/password authentication
  - `BearerTokenCredentialHandler`: Handles OAuth bearer tokens
- **Authentication Headers**: HTTP headers added to requests to verify identity
- **Scopes**: Permission boundaries applied to authentication tokens
- **Token Management**: Logic for token acquisition, refreshing, and validation

## How It Works

> **TL;DR:** You create a credential handler with your authentication information, pass it to the WebApi constructor, and the API automatically adds appropriate headers to all requests.

The authentication flow follows these steps:

1. You create an appropriate credential handler with your authentication information
2. You pass this handler to the WebApi constructor along with your organization URL
3. For each API request, the system:
   - Prepares the HTTP request
   - Calls the credential handler to add authentication headers
   - Sends the authenticated request to Azure DevOps
   - Returns the response to your application

```
┌─────────────────┐     ┌─────────────────┐     ┌──────────────────┐
│                 │     │                 │     │                  │
│ Your            │     │ Credential      │     │ WebApi           │
│ Application     │────▶│ Handler         │────▶│ Connection       │
│                 │     │                 │     │                  │
└─────────────────┘     └─────────────────┘     └──────────────────┘
                                                        │
                                                        │
                                                        ▼
                                                ┌──────────────────┐
                                                │                  │
                                                │ HTTP Request     │
                                                │ + Auth Headers   │
                                                │                  │
                                                └──────────────────┘
                                                        │
                                                        │
                                                        ▼
                                                ┌──────────────────┐
                                                │                  │
                                                │ Azure DevOps     │
                                                │ API              │
                                                │                  │
                                                └──────────────────┘
```

### Personal Access Token Authentication

PAT authentication is the most common approach:

1. You generate a PAT in the Azure DevOps portal with appropriate scopes
2. You create a `PersonalAccessTokenCredentialHandler` with your PAT
3. You pass this handler to the WebApi constructor
4. The handler adds your PAT as a Basic Authentication header to all requests

### OAuth Authentication

For web applications that need to authenticate users:

1. You implement an OAuth 2.0 flow to obtain an access token
2. You create a `BearerTokenCredentialHandler` with the token
3. You pass this handler to the WebApi constructor
4. The handler adds your token as a Bearer token header to all requests
5. You implement token refresh logic when tokens expire

## Practical Applications

> **TL;DR:** Different authentication methods support various scenarios like server applications, user-centric tools, and integrations with other Azure services.

### Common Use Cases

- **Server Applications**: Long-running applications using PATs with limited scopes
  
  ```typescript
  // Create a credential handler with your PAT
  const authHandler = azdev.getPersonalAccessTokenHandler("your-pat-token");
  
  // Create a connection to Azure DevOps
  const connection = new azdev.WebApi("https://dev.azure.com/your-organization", authHandler);
  
  // Use the connection
  const witApi = await connection.getWorkItemTrackingApi();
  ```

- **Command-Line Tools**: Interactive tools using PATs or username/password
  
  ```typescript
  // For CLI tools where the user provides credentials
  function createConnection(credentials) {
      let authHandler;
      
      if (credentials.type === "pat") {
          authHandler = azdev.getPersonalAccessTokenHandler(credentials.token);
      } else if (credentials.type === "basic") {
          authHandler = azdev.getBasicHandler(credentials.username, credentials.password);
      }
      
      return new azdev.WebApi(credentials.orgUrl, authHandler);
  }
  ```

- **Web Applications**: User-centric applications using OAuth
  
  ```typescript
  // After completing OAuth flow and obtaining a token
  function createConnectionWithOAuth(orgUrl, oauthToken) {
      const authHandler = azdev.getBearerHandler(oauthToken);
      return new azdev.WebApi(orgUrl, authHandler);
  }
  ```

## Relationship to Other Components

| Related Component | Relationship |
|-------------------|--------------|
| WebApi Connection | The connection uses the credential handler to authenticate all requests |
| API Clients | All API clients (GitApi, WorkItemTrackingApi, etc.) inherit authentication from the WebApi connection |
| Resource Areas | Authentication is applied after Resource Area resolution in the request pipeline |
| Retry Logic | Authentication failures may trigger specific retry behaviors |
| Rate Limiting | Authentication determines rate limit tiers and quotas |

## Version Considerations

This concept applies to all versions of the Azure DevOps Node API, with the following considerations:

- All authentication methods work across all API versions
- PAT authentication is available in all Azure DevOps versions
- OAuth support varies by Azure DevOps version and deployment type

### Version-Specific Notes

```typescript
// Works in all versions
const patAuth = azdev.getPersonalAccessTokenHandler("your-pat");

// Works in Azure DevOps Services, but may have limitations in older TFS versions
const bearerAuth = azdev.getBearerHandler("your-oauth-token");
```

## Best Practices

- **Use PATs for automated processes** instead of user credentials
- **Limit PAT scopes** to only what's necessary for your application
- **Set appropriate PAT expiration** based on your security requirements
- **Store credentials securely** using environment variables or a secrets manager
- **Implement token refresh logic** for OAuth tokens before they expire
- **Use different PATs for different applications** to limit the impact of compromise
- **Rotate PATs regularly** as part of your security practices

## Common Pitfalls

| Pitfall | Cause | Solution |
|---------|-------|----------|
| Hardcoded credentials | Embedding PATs or passwords in source code | Use environment variables or a secrets manager |
| Overly permissive PATs | Creating PATs with unnecessary scopes | Limit PAT scopes to only what's required |
| Token expiration | Not handling PAT or OAuth token expiration | Implement token refresh and monitoring |
| Sharing tokens | Using the same PAT across multiple applications | Create dedicated PATs for each application |
| Insecure storage | Storing PATs in plain text configuration files | Use secure credential storage solutions |

## Code Representations

```typescript
// PAT Authentication
import * as azdev from "azure-devops-node-api";

// Using environment variables for secure storage
const orgUrl = process.env.AZURE_DEVOPS_ORG_URL;
const pat = process.env.AZURE_DEVOPS_PAT;

// Create a PAT authentication handler
const authHandler = azdev.getPersonalAccessTokenHandler(pat);

// Create the WebApi connection
const connection = new azdev.WebApi(orgUrl, authHandler);

// Basic Authentication (not recommended for production)
const basicAuthHandler = azdev.getBasicHandler("username", "password");
const basicConnection = new azdev.WebApi(orgUrl, basicAuthHandler);

// Bearer Token Authentication (OAuth)
const bearerToken = "your-oauth-token";
const bearerAuthHandler = azdev.getBearerHandler(bearerToken);
const oauthConnection = new azdev.WebApi(orgUrl, bearerAuthHandler);
```

## Advanced Topics

<details>
<summary>Click to expand advanced information</summary>

### Custom Authentication Handlers

For advanced scenarios, you can implement custom authentication handlers:

```typescript
import { IRequestHandler } from "azure-devops-node-api/interfaces/common/VsoBaseInterfaces";

class CustomAuthHandler implements IRequestHandler {
    constructor(private token: string) {}

    prepareRequest(options: any): void {
        options.headers = options.headers || {};
        options.headers["Custom-Auth-Header"] = this.token;
    }

    canHandleAuthentication(): boolean {
        return false; // Let the default handler manage 401 responses
    }

    handleAuthentication(): Promise<boolean> {
        return Promise.resolve(false);
    }
}

// Usage
const customHandler = new CustomAuthHandler("custom-token");
const connection = new azdev.WebApi(orgUrl, customHandler);
```

### Combining Authentication Methods

Some scenarios may require combining authentication methods:

```typescript
class CombinedAuthHandler implements IRequestHandler {
    constructor(
        private patHandler: IRequestHandler,
        private additionalToken: string
    ) {}

    prepareRequest(options: any): void {
        // Apply PAT authentication
        this.patHandler.prepareRequest(options);
        
        // Add additional authentication header
        options.headers = options.headers || {};
        options.headers["X-Additional-Auth"] = this.additionalToken;
    }

    canHandleAuthentication(): boolean {
        return this.patHandler.canHandleAuthentication();
    }

    handleAuthentication(response: any): Promise<boolean> {
        return this.patHandler.handleAuthentication(response);
    }
}
```

### OAuth Token Refresh

Implementing token refresh for OAuth scenarios:

```typescript
async function getConnectionWithRefresh(orgUrl, tokens) {
    // Create initial handler with access token
    let authHandler = azdev.getBearerHandler(tokens.accessToken);
    let connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Setup refresh timer
    const refreshMs = (tokens.expiresIn - 300) * 1000; // Refresh 5 minutes before expiry
    setTimeout(async () => {
        // Get new tokens
        const newTokens = await refreshOAuthTokens(tokens.refreshToken);
        
        // Update the connection with a new handler
        authHandler = azdev.getBearerHandler(newTokens.accessToken);
        connection = new azdev.WebApi(orgUrl, authHandler);
        
        // Setup next refresh
        // ...
    }, refreshMs);
    
    return connection;
}

async function refreshOAuthTokens(refreshToken) {
    // Implementation depends on your OAuth provider
    // ...
}
```

</details>

## Related Resources

### Prerequisites
- [Azure DevOps Permissions Model](../permissions-model.md)
- [WebApi Client Concept](../webapi-client-concept.md)
- [Getting Started with Azure DevOps](../getting-started.md)

### Next Steps
- [Handling API Errors](../handling-api-errors.md)
- [Implementing Retry Logic](../retry-logic.md)
- [Security Best Practices](../security-best-practices.md)

### Alternative Approaches
- [Using the REST API Directly](../advanced-topics/rest-api-direct.md)
- [Azure CLI for Azure DevOps](../tools/azure-cli.md)

### External Documentation
- [Creating Personal Access Tokens](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)
- [OAuth 2.0 in Azure DevOps](https://docs.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/oauth)
- [Azure Active Directory Authentication](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-overview)

---

## Feedback

Have feedback on this documentation? Please submit an issue on our [GitHub repository](https://github.com/your-org/azure-devops-node-api).

---

[◀ Back to Documentation Templates](../../README.md) | [▲ Back to Top](#authentication-concepts) 