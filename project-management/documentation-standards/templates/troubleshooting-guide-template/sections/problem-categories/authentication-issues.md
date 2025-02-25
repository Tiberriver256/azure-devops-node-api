# Authentication Issues

> **Navigation**: [Home](../../index.md) > [Problem Categories](../../index.md#common-issue-categories) > Authentication Issues

This page covers troubleshooting for common authentication-related issues when using the Azure DevOps Node API.

## In This Category

- [Invalid Token](#invalid-token)
- [Token Expiration](#token-expiration)
- [Insufficient Scopes](#insufficient-scopes)
- [Two-Factor Authentication Issues](#two-factor-authentication-issues)
- [Identity Provider Problems](#identity-provider-problems)

---

## Invalid Token
<a id="invalid-token"></a>

> **Applies to:** All API versions

### Symptoms

- 401 Unauthorized HTTP responses
- Error message: "The provided token is invalid or has expired"
- `Error: TF400813: The user '00000000-0000-0000-0000-000000000000' is not authorized to access this resource.`

### Root Causes

- The Personal Access Token (PAT) is incorrectly copied or malformed
- The token string includes whitespace or newline characters
- The token has been manually revoked in Azure DevOps settings
- The token uses an unsupported authentication scheme

### Solution Steps

1. Generate a new Personal Access Token in Azure DevOps:
   - Go to Azure DevOps > User settings > Personal access tokens
   - Create a new token with appropriate scopes
   - Copy the token carefully without extra whitespace

2. Update your authentication code:

```typescript
import * as azdev from "azure-devops-node-api";

// Always store tokens securely, preferably in environment variables
const token = process.env.AZURE_DEVOPS_TOKEN; // Your new PAT token
const orgUrl = "https://dev.azure.com/your-organization";

const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

3. Verify the token works:

```typescript
try {
    const connectionData = await connection.connect();
    console.log(`Connected successfully to ${connectionData.authenticatedUser.providerDisplayName}`);
} catch (err) {
    console.error("Authentication error:", err.message);
}
```

### Prevention

- Store tokens securely in environment variables or a secret management system
- Don't hardcode tokens in source code
- Implement automatic token refresh mechanisms for long-running applications
- Set appropriate token lifetimes (shorter for higher security, longer for convenience)

### Related Issues

- [Token Expiration](#token-expiration)
- [Insufficient Scopes](#insufficient-scopes)
- [Connection Problems](./connection-problems.md)

---

## Token Expiration
<a id="token-expiration"></a>

> **Applies to:** All API versions

### Symptoms

- Authentication works initially but fails after some time
- 401 Unauthorized errors appearing suddenly in a previously working application
- Error message includes "token has expired" or similar phrasing

### Root Causes

- Personal Access Tokens have an expiration date set during creation
- Default PAT expiration is typically 30 days
- OAuth tokens have shorter expiration times (usually hours)

### Solution Steps

1. Check token expiration in Azure DevOps:
   - Go to Azure DevOps > User settings > Personal access tokens
   - Verify if the token is listed as "expired"

2. Generate a new token with a longer expiration if needed:
   - When creating the token, set the "Expiration" field appropriately
   - For automated systems, consider using service principals instead of PATs

3. Implement token refresh logic for long-running applications:

<details>
<summary><b>Token Management Code Example</b></summary>

```typescript
import * as azdev from "azure-devops-node-api";
import { TokenCredentials } from "azure-devops-node-api/handlers/credentials";

class TokenManager {
    private token: string;
    private expirationDate: Date;
    private orgUrl: string;
    private connection: azdev.WebApi | null = null;
    
    constructor(initialToken: string, tokenExpirationDate: Date, orgUrl: string) {
        this.token = initialToken;
        this.expirationDate = tokenExpirationDate;
        this.orgUrl = orgUrl;
    }
    
    async getConnection(): Promise<azdev.WebApi> {
        // Check if token is expired or about to expire (within 1 day)
        const oneDayFromNow = new Date();
        oneDayFromNow.setDate(oneDayFromNow.getDate() + 1);
        
        if (this.expirationDate < oneDayFromNow) {
            await this.refreshToken();
        }
        
        if (!this.connection) {
            const authHandler = azdev.getPersonalAccessTokenHandler(this.token);
            this.connection = new azdev.WebApi(this.orgUrl, authHandler);
        }
        
        return this.connection;
    }
    
    private async refreshToken(): Promise<void> {
        // In a real implementation, this would call your token service
        // or use a refresh token flow to get a new token
        console.log("Token expired or about to expire, refreshing...");
        
        // Example implementation:
        // const newTokenData = await yourTokenService.getNewToken();
        // this.token = newTokenData.token;
        // this.expirationDate = newTokenData.expirationDate;
        
        // Reset connection so it will be recreated with the new token
        this.connection = null;
    }
}

// Usage example:
// const tokenManager = new TokenManager(
//     "your-initial-token",
//     new Date("2023-12-31"), // token expiration date
//     "https://dev.azure.com/your-organization"
// );
// 
// async function getWorkItemTrackingApi() {
//     const connection = await tokenManager.getConnection();
//     return await connection.getWorkItemTrackingApi();
// }
```

</details>

### Prevention

- Set longer expiration times for tokens used in automated systems
- Implement token refresh mechanisms for long-running applications
- Use a monitoring system to alert before token expiration
- Consider using service principals for automated systems instead of personal tokens

### Related Issues

- [Invalid Token](#invalid-token)
- [Identity Provider Problems](#identity-provider-problems)

---

## Insufficient Scopes
<a id="insufficient-scopes"></a>

> **Applies to:** All API versions

### Symptoms

- 403 Forbidden HTTP responses
- Error messages indicating permissions issues
- Able to authenticate but not perform specific actions
- Error: `TF400813: Resource not available for anonymous access. Client authentication required.`

### Root Causes

- The Personal Access Token was created with insufficient scopes
- The user account associated with the token lacks necessary permissions
- Trying to access resources outside the authorized scopes
- Organization policies restrict certain API operations

### Solution Steps

1. Check the required scopes for your operation:
   - Different API operations require different scopes
   - Common scopes include: `vso.build`, `vso.code`, `vso.work`, etc.

2. Generate a new token with the appropriate scopes:
   - Go to Azure DevOps > User settings > Personal access tokens > New Token
   - Select all required scopes for your operations
   - For full access, use `vso.full_access` (use with caution)

3. Verify user permissions in Azure DevOps:
   - Ensure the user has appropriate permissions in the organization/project
   - Check if the user is a member of the required groups
   - Review any custom security policies that might affect the user

### Prevention

- Document required scopes for different parts of your application
- Use the principle of least privilege - only request scopes you need
- Create separate tokens for different applications or functionalities
- Regularly audit token scopes and permissions

### Related Issues

- [Invalid Token](#invalid-token)
- [Permission Errors](./permission-errors.md)

---

## Two-Factor Authentication Issues
<a id="two-factor-authentication-issues"></a>

> **Applies to:** All API versions when using Microsoft accounts with 2FA

### Symptoms

- Interactive login prompts appearing unexpectedly
- Authentication failures with error messages about additional verification
- Unable to use basic authentication methods

### Root Causes

- Account has two-factor authentication (2FA) enabled
- Basic authentication doesn't support 2FA flows
- Using username/password authentication instead of tokens

### Solution Steps

1. Use Personal Access Tokens instead of password authentication:
   - PATs are specifically designed to work with 2FA
   - They act as an application-specific password

2. Create a new PAT token:
   - Go to Azure DevOps > User settings > Personal access tokens
   - Create a new token with appropriate scopes
   - Use this token for API authentication

3. Update your authentication code to use PAT:

```typescript
import * as azdev from "azure-devops-node-api";

// Use PAT instead of username/password
const token = "your-personal-access-token";
const orgUrl = "https://dev.azure.com/your-organization";

// Use the PAT handler specifically
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

### Prevention

- Always use PATs or OAuth for automated scenarios
- Never rely on basic username/password authentication for API access
- Keep authentication flows separate from your application logic
- Document 2FA requirements for your organization

### Related Issues

- [Invalid Token](#invalid-token)
- [Token Expiration](#token-expiration)
- [Identity Provider Problems](#identity-provider-problems)

---

## Identity Provider Problems
<a id="identity-provider-problems"></a>

> **Applies to:** Organizations using custom identity providers or Single Sign-On

### Symptoms

- Authentication fails with errors related to identity provider
- Error messages containing references to SAML, Azure AD, or other identity providers
- Unexpected redirects during authentication attempts
- Authentication works in browser but fails from API

### Root Causes

- Organization uses custom identity provider (IdP) integration
- Single Sign-On (SSO) requirements conflict with token authentication
- Identity provider configurations have changed
- Conditional Access Policies affecting API access

### Solution Steps

1. Check organization authentication settings:
   - Verify if your organization requires SSO
   - Identify which identity provider is being used (Azure AD, Okta, etc.)

2. For Azure AD-backed organizations:
   - Use Azure AD authentication flows instead of basic PAT
   - Implement proper OAuth2 flow with the correct Azure AD tenant

3. For SSO-required organizations using PATs:
   - Ensure the PAT was created after SSO was enforced
   - Verify the PAT creator has completed all SSO requirements

4. Contact your Azure DevOps administrator:
   - They may need to adjust identity provider settings
   - Special accommodations might be needed for API service accounts

### Prevention

- Document your organization's identity provider configuration
- Test authentication flows after any IdP changes
- Create separate service accounts for API access when possible
- Implement proper OAuth2 flows for complex identity scenarios

### Related Issues

- [Invalid Token](#invalid-token)
- [Permission Errors](./permission-errors.md)
- [Two-Factor Authentication Issues](#two-factor-authentication-issues)

---

<div align="left">
  <a href="../../quick-solutions.md">← Back to Quick Solutions</a>
</div>

<div align="right">
  <a href="./connection-problems.md">Next: Connection Problems →</a>
</div> 