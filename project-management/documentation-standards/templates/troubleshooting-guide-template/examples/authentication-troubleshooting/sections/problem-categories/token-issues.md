# Token Issues

> **Navigation**: [Home](../../index.md) > [Authentication Issues](../../index.md#common-authentication-issue-categories) > Token Issues

This page covers troubleshooting for common token-related issues when authenticating with the Azure DevOps Node API.

## In This Category

- [Invalid Token](#invalid-token)
- [Token Expiration](#token-expiration)
- [Token Format Problems](#token-format-problems)
- [Token Revocation](#token-revocation)
- [Basic Authentication vs PAT](#basic-authentication-vs-pat)

---

## Invalid Token
<a id="invalid-token"></a>

> **Applies to:** All API versions

### Symptoms

- 401 Unauthorized HTTP responses
- Error message: `TF400813: The user '00000000-0000-0000-0000-000000000000' is not authorized to access this resource.`
- Authentication fails immediately when trying to connect

### Root Causes

- The Personal Access Token (PAT) is incorrectly copied or malformed
- The token string includes whitespace or newline characters
- The token has been manually revoked in Azure DevOps settings
- Using an empty or null token value

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

// Check for empty token
if (!token) {
    throw new Error("Azure DevOps token is empty or not set");
}

const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

3. Verify the token works with a simple connection test:

```typescript
try {
    const connectionData = await connection.connect();
    console.log(`Connected successfully to ${connectionData.authenticatedUser.providerDisplayName}`);
} catch (err) {
    console.error("Authentication error:", err.message);
    // Check for specific error patterns
    if (err.message.includes("TF400813") || err.message.includes("authorized")) {
        console.error("Invalid token or insufficient permissions");
    }
}
```

### Prevention

- Store tokens securely in environment variables or a secret management system
- Trim whitespace from token values before use
- Don't hardcode tokens in source code
- Implement validation to check for empty or malformed tokens

### Related Issues

- [Token Expiration](#token-expiration)
- [Token Format Problems](#token-format-problems)
- [Scope & Permission Issues](./scope-permission-issues.md)

---

## Token Expiration
<a id="token-expiration"></a>

> **Applies to:** All API versions

### Symptoms

- Authentication suddenly stops working after previously working
- 401 Unauthorized errors appearing in a previously working application
- Error message includes "VS402965: The token has expired" or similar

### Root Causes

- Personal Access Tokens have an expiration date set during creation
- Default PAT expiration is typically 30 days unless specified otherwise
- Token has reached its expiration date

### Solution Steps

1. Check token expiration in Azure DevOps:
   - Go to Azure DevOps > User settings > Personal access tokens
   - Verify if the token is listed as "expired"

2. Generate a new token with a longer expiration if needed:
   - When creating the token, set the "Expiration" field appropriately
   - For automated systems, consider longer expiration periods (up to 1 year)

3. Implement token refresh logic for long-running applications:

<details>
<summary><b>Token Expiration Check Example</b></summary>

```typescript
import * as azdev from "azure-devops-node-api";

class TokenManager {
    private token: string;
    private expirationDate: Date;
    private orgUrl: string;
    
    constructor(initialToken: string, tokenExpirationDate: Date, orgUrl: string) {
        this.token = initialToken;
        this.expirationDate = tokenExpirationDate;
        this.orgUrl = orgUrl;
    }
    
    async getConnection(): Promise<azdev.WebApi> {
        // Check if token is expired or about to expire (within 7 days)
        const sevenDaysFromNow = new Date();
        sevenDaysFromNow.setDate(sevenDaysFromNow.getDate() + 7);
        
        if (this.expirationDate < sevenDaysFromNow) {
            // Log warning or trigger notification about expiring token
            console.warn(`PAT token will expire on ${this.expirationDate.toISOString()}`);
            
            if (this.expirationDate < new Date()) {
                throw new Error("Token has expired. Please generate a new token.");
            }
        }
        
        const authHandler = azdev.getPersonalAccessTokenHandler(this.token);
        return new azdev.WebApi(this.orgUrl, authHandler);
    }
}

// Usage example:
// const tokenExpirationDate = new Date("2023-12-31");
// const tokenManager = new TokenManager(
//     process.env.AZURE_DEVOPS_TOKEN,
//     tokenExpirationDate,
//     "https://dev.azure.com/your-organization"
// );
```

</details>

### Prevention

- Set appropriate expiration times based on security requirements
- Document token expiration dates in a secure location
- Add monitoring to alert before token expiration
- Implement automated token renewal processes for critical systems

### Related Issues

- [Invalid Token](#invalid-token)
- [Token Revocation](#token-revocation)

---

## Token Format Problems
<a id="token-format-problems"></a>

> **Applies to:** All API versions

### Symptoms

- Authentication fails with generic errors
- Error message about invalid credentials
- No clear error message but authentication fails

### Root Causes

- Extra whitespace or newline characters in the token
- Truncated token due to copy/paste errors
- Token includes unauthorized characters
- Using the token with the wrong authentication scheme

### Solution Steps

1. Verify the token format:
   - Ensure no whitespace at the beginning or end of the token
   - Check that the token wasn't truncated during copying
   - Verify the token is being used with the correct authentication handler

2. Clean token values before use:

```typescript
// Clean and validate token before use
function prepareToken(rawToken: string): string {
    if (!rawToken) {
        throw new Error("Token is empty or not provided");
    }
    
    // Trim whitespace
    const cleanToken = rawToken.trim();
    
    // Basic validation - PATs are typically long strings
    if (cleanToken.length < 30) {
        throw new Error("Token appears to be malformed or truncated");
    }
    
    return cleanToken;
}

// Usage
const token = prepareToken(process.env.AZURE_DEVOPS_TOKEN);
const authHandler = azdev.getPersonalAccessTokenHandler(token);
```

3. Regenerate the token if needed:
   - Create a new token in the Azure DevOps portal
   - Copy directly without intermediate editors that might add formatting

### Prevention

- Always trim token values before use
- Use secure environment variables or secret managers to store tokens
- Implement token validation before attempting authentication
- Use copy buttons in the Azure DevOps UI when available

### Related Issues

- [Invalid Token](#invalid-token)
- [Basic Authentication vs PAT](#basic-authentication-vs-pat)

---

## Token Revocation
<a id="token-revocation"></a>

> **Applies to:** All API versions

### Symptoms

- Authentication suddenly stops working
- 401 Unauthorized errors with previously working token
- No changes made to the code but authentication fails

### Root Causes

- Token was manually revoked in Azure DevOps settings
- Organization security policies automatically revoked tokens
- User account permissions changed or account was disabled
- Organization-wide security incident caused token revocation

### Solution Steps

1. Check token status in Azure DevOps:
   - Go to Azure DevOps > User settings > Personal access tokens
   - Verify if the token is still listed and not revoked

2. Create a new token:
   - If the previous token is missing or revoked, create a new one
   - Ensure appropriate scopes are selected

3. Update application configuration with the new token:
   - Update environment variables, configuration files, or secrets storage
   - Restart applications to ensure they pick up the new token

4. Implement resilient token handling:

```typescript
import * as azdev from "azure-devops-node-api";

async function tryConnect(token: string, orgUrl: string): Promise<azdev.WebApi | null> {
    try {
        const authHandler = azdev.getPersonalAccessTokenHandler(token);
        const connection = new azdev.WebApi(orgUrl, authHandler);
        
        // Test the connection
        await connection.connect();
        return connection;
    } catch (err) {
        if (err.message.includes("authorized") || err.message.includes("401")) {
            console.error("Token appears to be revoked or invalid");
            return null;
        }
        throw err; // Re-throw other errors
    }
}

// Usage
// const connection = await tryConnect(token, orgUrl);
// if (!connection) {
//     // Handle revoked token scenario - notify administrator, etc.
// }
```

### Prevention

- Regularly audit and rotate tokens
- Document who created tokens and for what purpose
- Use service accounts for automated systems instead of personal accounts
- Implement monitoring to detect and alert on authentication failures

### Related Issues

- [Token Expiration](#token-expiration)
- [Identity Issues](./identity-issues.md)

---

## Basic Authentication vs PAT
<a id="basic-authentication-vs-pat"></a>

> **Applies to:** All API versions

### Symptoms

- Confusion about which authentication method to use
- Authentication fails when using username/password
- Receiving "Basic authentication is not supported" errors

### Root Causes

- Attempting to use basic username/password authentication instead of PAT
- Azure DevOps no longer supports basic authentication with password
- Using incorrect authentication handler in the API client

### Solution Steps

1. Switch from basic authentication to PAT:
   - Generate a PAT in Azure DevOps > User settings > Personal access tokens
   - Use the PAT with the correct authentication handler

2. Update authentication code:

```typescript
import * as azdev from "azure-devops-node-api";

// INCORRECT: Basic authentication with password
// const basicAuthHandler = azdev.getBasicHandler("username", "password"); // Don't use this

// CORRECT: PAT authentication
const token = process.env.AZURE_DEVOPS_TOKEN;
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi("https://dev.azure.com/your-organization", authHandler);
```

3. For OAuth authentication scenarios:
   - Implement proper OAuth flow for Azure AD integration
   - Use the appropriate OAuth handler provided by the API

```typescript
// For OAuth bearer token scenarios
const bearerToken = "your-oauth-bearer-token";
const authHandler = azdev.getBearerHandler(bearerToken);
const connection = new azdev.WebApi("https://dev.azure.com/your-organization", authHandler);
```

### Prevention

- Always use PAT tokens instead of username/password for Azure DevOps API
- Document authentication approaches for different scenarios
- Follow security best practices and use appropriate authentication handlers
- Stay updated on Azure DevOps authentication changes and deprecations

### Related Issues

- [Invalid Token](#invalid-token)
- [Identity Issues](./identity-issues.md)

---

<div align="left">
  <a href="../../quick-solutions.md">← Back to Quick Solutions</a>
</div>

<div align="right">
  <a href="./identity-issues.md">Next: Identity Issues →</a>
</div> 