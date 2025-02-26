**Navigation**: [Home](../../../index.md) > [Troubleshooting Guide](../../index.md) > Authentication Issues

# Authentication Issues

This page covers troubleshooting for common authentication issues when using the Azure DevOps Node API.

## Contents

- [Quick Fix Summary](#quick-fix-summary)
- [High Severity Issues](#high-severity-issues)
- [Medium Severity Issues](#medium-severity-issues)
- [Low Severity Issues](#low-severity-issues)
- [Diagnostic Tools](#diagnostic-tools)

## Quick Fix Summary

<details>
<summary><b>Quick Fix Summary</b></summary>

- 🔴 **Invalid Token**: Regenerate your PAT token in Azure DevOps
- 🔴 **Token Expiration**: Create a new token with a longer expiration
- 🟡 **Insufficient Scopes**: Create a new token with the correct scopes
- 🟡 **Incorrect Organization URL**: Verify your organization URL format
- 🟢 **Token Format**: Remove any whitespace or newlines from your token

</details>

---

## High Severity Issues

### Invalid Token
🔴 **High Severity** | ⏱️ 5 minutes

#### Symptoms

- 401 Unauthorized HTTP responses
- Error message: `TF400813: The user '00000000-0000-0000-0000-000000000000' is not authorized to access this resource.`
- Authentication fails immediately when trying to connect

#### Root Causes

- The Personal Access Token (PAT) is incorrectly copied or malformed
- The token string includes whitespace or newline characters
- The token has been manually revoked in Azure DevOps settings
- Using an empty or null token value

#### Solution Steps

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

// Create authorization handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Test the connection
try {
    const connData = await connection.connect();
    console.log("Connection successful:", connData.authenticatedUser.providerDisplayName);
} catch (err) {
    console.error("Authentication failed:", err.message);
}
```

3. Verify the token works by testing a simple API call:

```typescript
// Get a client and make a simple request
const coreApi = await connection.getCoreApi();
const projects = await coreApi.getProjects();
console.log(`Successfully retrieved ${projects.length} projects`);
```

### Token Expiration
🔴 **High Severity** | ⏱️ 5 minutes

#### Symptoms

- Authentication suddenly fails after working previously
- Error message: `TF400813: Resource not available for anonymous access. Client authentication required.`
- API calls start failing with 401 errors after a period of successful operation

#### Root Causes

- The Personal Access Token has reached its expiration date
- The token was created with a short lifespan
- The token was manually revoked by an administrator

#### Solution Steps

1. Check token expiration in Azure DevOps:
   - Go to Azure DevOps > User settings > Personal access tokens
   - Look for your token in the list and check its expiration date

2. Generate a new token with appropriate expiration:

```typescript
// Update your code with the new token
const token = process.env.AZURE_DEVOPS_TOKEN; // Your new PAT token
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

3. Consider implementing token refresh logic:

<details>
<summary><b>Token Refresh Implementation</b></summary>

```typescript
// Example token refresh monitoring
class TokenManager {
  private token: string;
  private expirationDate: Date;
  
  constructor(token: string, expirationDays: number) {
    this.token = token;
    // Calculate expiration date
    this.expirationDate = new Date();
    this.expirationDate.setDate(this.expirationDate.getDate() + expirationDays);
  }
  
  getToken(): string {
    // Check if token is about to expire (within 3 days)
    const now = new Date();
    const threeDaysFromNow = new Date();
    threeDaysFromNow.setDate(now.getDate() + 3);
    
    if (this.expirationDate < threeDaysFromNow) {
      console.warn("Token is about to expire. Please generate a new token.");
    }
    
    return this.token;
  }
}

// Usage
const tokenManager = new TokenManager(process.env.AZURE_DEVOPS_TOKEN, 30); // 30-day token
const authHandler = azdev.getPersonalAccessTokenHandler(tokenManager.getToken());
```

</details>

---

## Medium Severity Issues

### Insufficient Scopes
🟡 **Medium Severity** | ⏱️ 10 minutes

#### Symptoms

- 403 Forbidden responses
- Error message: `TF400813: The user does not have permission to access this resource.`
- Authentication succeeds, but specific API operations fail

#### Root Causes

- The PAT token was created with insufficient scopes
- The user account lacks necessary permissions
- The API operation requires additional scopes

#### Solution Steps

1. Check the scopes of your PAT in Azure DevOps:
   - Go to Azure DevOps > User settings > Personal access tokens
   - Review the scopes assigned to your token

2. Create a new PAT with the required scopes:
   - For full access: `vso.full_access`
   - For specific resources: Select the appropriate scopes (e.g., `vso.work`, `vso.code`)

3. Update your application code with the new token:

```typescript
// Update with new token that has appropriate scopes
const token = process.env.AZURE_DEVOPS_TOKEN; // Your new PAT token
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

💡 **Tip**: Required scopes for common operations:

| Operation | Required Scope |
|-----------|---------------|
| Work Items | `vso.work` |
| Git Repositories | `vso.code` |
| Build Pipelines | `vso.build` |
| Release Pipelines | `vso.release` |

### Incorrect Organization URL
🟡 **Medium Severity** | ⏱️ 5 minutes

#### Symptoms

- 404 Not Found responses
- Error message: `TF400813: Resource not available.`
- Unable to connect to Azure DevOps

#### Root Causes

- The organization URL is incorrectly formatted
- The organization name is misspelled
- The organization no longer exists or has been renamed

#### Solution Steps

1. Verify the organization URL format:
   - The correct format is: `https://dev.azure.com/{organization}`
   - Replace `{organization}` with your organization name

2. Update your code with the correct URL:

```typescript
// Correct format for organization URL
const orgUrl = 'https://dev.azure.com/your-organization';
const connection = new azdev.WebApi(orgUrl, authHandler);
```

3. Test the connection:

```typescript
try {
  const connectionData = await connection.connect();
  console.log("Connected to:", connectionData.authenticatedUser.providerDisplayName);
} catch (error) {
  console.error("Connection failed:", error.message);
}
```

---

## Low Severity Issues

### Token Format Problems
🟢 **Low Severity** | ⏱️ 3 minutes

#### Symptoms

- Authentication fails with format-related errors
- Error message: `TypeError: Invalid token format`
- Error message: `Error: Token must be a non-empty string`

#### Root Causes

- Extra whitespace or newline characters in the token
- Token includes formatting characters from copy/paste
- Token is not properly encoded
- Token is empty or undefined

#### Solution Steps

1. Clean the token string:

```typescript
// Trim whitespace and ensure token is a string
const token = String(process.env.AZURE_DEVOPS_TOKEN).trim();
```

2. Verify token format:

```typescript
// Validate token before using
function validateToken(token: string): boolean {
  if (!token || token.length === 0) {
    console.error("Token is empty");
    return false;
  }
  
  // Check for common formatting issues
  if (token.includes('\n') || token.includes('\r')) {
    console.error("Token contains newline characters");
    return false;
  }
  
  return true;
}

const token = process.env.AZURE_DEVOPS_TOKEN;
if (validateToken(token)) {
  const authHandler = azdev.getPersonalAccessTokenHandler(token);
  // Continue with connection
}
```

---

## Diagnostic Tools

<details>
<summary><b>Authentication Diagnostic Utility</b></summary>

```typescript
// Authentication diagnostic utility
async function diagnoseAuthenticationIssues(token: string, orgUrl: string): Promise<void> {
  console.log("Running authentication diagnostics...");
  
  // Check token format
  if (!token || token.length === 0) {
    console.error("❌ Token is empty or undefined");
    return;
  }
  
  if (token.includes('\n') || token.includes('\r')) {
    console.warn("⚠️ Token contains newline characters that may cause issues");
    token = token.trim();
  }
  
  console.log("✅ Token format appears valid");
  
  // Test connection
  try {
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    console.log("Attempting to connect to Azure DevOps...");
    const connData = await connection.connect();
    
    console.log("✅ Connection successful!");
    console.log(`Authenticated as: ${connData.authenticatedUser.providerDisplayName}`);
    console.log(`User ID: ${connData.authenticatedUser.id}`);
    
    // Test a simple API call
    try {
      const coreApi = await connection.getCoreApi();
      const projects = await coreApi.getProjects();
      console.log(`✅ Successfully retrieved ${projects.length} projects`);
    } catch (err) {
      console.error("❌ API call failed despite successful authentication");
      console.error(`Error: ${err.message}`);
      console.log("This suggests a permissions issue rather than an authentication problem");
    }
  } catch (err) {
    console.error("❌ Connection failed");
    console.error(`Error: ${err.message}`);
    
    if (err.message.includes("TF400813")) {
      console.log("This error indicates an invalid or revoked token");
    } else if (err.message.includes("unable to get local issuer certificate")) {
      console.log("This error indicates a network or SSL certificate issue");
    }
  }
}

// Usage
diagnoseAuthenticationIssues("your-pat-token", "https://dev.azure.com/your-organization");
```

</details>

## See Also

- [Authentication Overview](../../concepts/authentication.md)
- [Personal Access Tokens Guide](../../guides/personal-access-tokens.md)
- [Connection Issues](./connection-issues.md)
- [Permission Errors](./permission-errors.md) 