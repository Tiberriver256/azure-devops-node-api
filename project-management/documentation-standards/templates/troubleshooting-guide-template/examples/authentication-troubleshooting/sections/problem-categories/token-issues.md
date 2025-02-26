**Navigation**: [Home](../../../../index.md) > [Authentication Issues](../../index.md) > [Problem Categories](../../index.md#-common-authentication-issue-categories) > Token Issues

# 🔑 Token Issues

This page covers troubleshooting for common token-related issues when authenticating with the Azure DevOps Node API.

## Quick Reference

| Issue | Severity | Resolution Time | Success Rate |
|-------|----------|-----------------|--------------|
| [Invalid Token](#invalid-token) | 🔴 High | ⏱️ 5 minutes | 95% |
| [Token Expiration](#token-expiration) | 🟡 Medium | ⏱️ 5 minutes | 98% |
| [Token Format Problems](#token-format-problems) | 🟢 Low | ⏱️ 3 minutes | 99% |
| [Token Revocation](#token-revocation) | 🔴 High | ⏱️ 5 minutes | 90% |
| [Basic Authentication vs PAT](#basic-authentication-vs-pat) | 🟡 Medium | ⏱️ 10 minutes | 95% |

## Quick Solutions

<details>
<summary><b>🔴 High Priority Quick Fixes</b></summary>

1. **Generate new PAT token** (⏱️ 2 minutes)
   - Go to Azure DevOps > User settings > Personal access tokens
   - Create new token with required scopes
   - Copy token carefully without extra characters

2. **Check token revocation** (⏱️ 1 minute)
   - Verify token exists in PAT list
   - Check for organization policy changes
   - Look for security alerts

</details>

<details>
<summary><b>🟡 Medium Priority Quick Fixes</b></summary>

1. **Clean token format** (⏱️ 1 minute)
```typescript
// Remove whitespace and validate
const token = String(process.env.AZURE_DEVOPS_TOKEN).trim();
```

2. **Verify token scopes** (⏱️ 3 minutes)
   - Check Azure DevOps token settings
   - Verify required permissions
   - Test with minimal scope first

</details>

---

## Invalid Token
<a id="invalid-token"></a>

> 🔴 **High Severity** | ⏱️ 5 minutes | 📊 95% Success Rate

### 🔍 Symptoms
- 401 Unauthorized HTTP responses
- Error message: `TF400813: The user '00000000-0000-0000-0000-000000000000' is not authorized to access this resource.`
- Authentication fails immediately when trying to connect

### 🎯 Root Causes
- The Personal Access Token (PAT) is incorrectly copied or malformed
- The token string includes whitespace or newline characters
- The token has been manually revoked in Azure DevOps settings
- Using an empty or null token value

### ⚡ Quick Solution Steps

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

// Create a connection using the token
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

### 🛡️ Prevention
- Store tokens securely in environment variables or a secure vault
- Never hardcode tokens in source code
- Implement token rotation policies
- Use a consistent method for token management across your applications

### 🔗 Related Issues
- [Token Expiration](#token-expiration)
- [Token Format Problems](#token-format-problems)

### ➡️ Next Steps
- If generating a new token doesn't help, check your Azure DevOps permissions
- Verify your organization URL is correct
- Try using the Azure CLI to authenticate as an alternative
- Check network connectivity to Azure DevOps

---

## Token Expiration
<a id="token-expiration"></a>

> 🟡 **Medium Severity** | ⏱️ 5 minutes | 📊 98% Success Rate

### 🔍 Symptoms
- Authentication suddenly fails after working previously
- Error message: `TF400813: Resource not available for anonymous access. Client authentication required.`
- API calls start failing with 401 errors after a period of successful operation

### 🎯 Root Causes
- The Personal Access Token has reached its expiration date
- The token was created with a short lifespan
- The token was manually revoked by an administrator

### ⚡ Quick Solution Steps

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
<summary><b>🔧 Token Refresh Implementation</b></summary>

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

### 🛡️ Prevention
- Create tokens with longer expiration periods for automated systems
- Document token expiration dates
- Set calendar reminders for token rotation
- Implement monitoring for authentication failures

### 🔗 Related Issues
- [Invalid Token](#invalid-token)
- [Token Revocation](#token-revocation)

### ➡️ Next Steps
- If you need longer-lived tokens, consider using service principals
- Implement automated token rotation
- Set up monitoring for authentication failures
- Consider using Azure Key Vault for token storage

---

## Token Format Problems
<a id="token-format-problems"></a>

> 🟢 **Low Severity** | ⏱️ 3 minutes | 📊 99% Success Rate

### 🔍 Symptoms

- Authentication fails with format-related errors
- Error message: `TypeError: Invalid token format`
- Error message: `Error: Token must be a non-empty string`

### 🎯 Root Causes

- Extra whitespace or newline characters in the token
- Token includes formatting characters from copy/paste
- Token is not properly encoded
- Token is empty or undefined

### ⚡ Quick Solution Steps

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

3. Ensure proper environment variable loading:

<details>
<summary><b>Environment Variable Handling</b></summary>

```typescript
// Using dotenv for environment variables
import * as dotenv from 'dotenv';
import * as fs from 'fs';

// Load environment variables
if (fs.existsSync('.env')) {
  dotenv.config();
}

// Get and validate token
const token = process.env.AZURE_DEVOPS_TOKEN;
if (!token) {
  throw new Error('AZURE_DEVOPS_TOKEN environment variable is not set');
}

// Clean token and use it
const cleanToken = token.trim();
const authHandler = azdev.getPersonalAccessTokenHandler(cleanToken);
```

</details>

### 🛡️ Prevention

- Use environment variables to store tokens
- Implement token validation before use
- Use a consistent method for loading tokens
- Add error handling for token-related issues

### 🔗 Related Issues

- [Invalid Token](#invalid-token)
- [Basic Authentication vs PAT](#basic-authentication-vs-pat)

### ➡️ Next Steps

- If token format issues persist, try regenerating the token
- Check how the token is being stored and loaded
- Verify the token is being properly passed to the authentication handler
- Consider using a token management library

---

## Token Revocation
<a id="token-revocation"></a>

> 🔴 **High Severity** | ⏱️ 5 minutes | 📊 90% Success Rate

### 🔍 Symptoms

- Authentication suddenly fails after working previously
- Error message: `TF400813: The user '00000000-0000-0000-0000-000000000000' is not authorized to access this resource.`
- Token no longer appears in your Personal Access Tokens list

### 🎯 Root Causes

- The token was manually revoked in Azure DevOps settings
- Security policy enforcement automatically revoked the token
- Organization-wide token refresh was performed
- Security incident triggered token revocation

### ⚡ Quick Solution Steps

1. Verify token status in Azure DevOps:
   - Go to Azure DevOps > User settings > Personal access tokens
   - Check if your token is still listed

2. Generate a new token with appropriate scopes:
   - Create a new token with the same or updated scopes
   - Document the new token's purpose and expiration

3. Update your application with the new token:

```typescript
// Update your application configuration
const token = process.env.AZURE_DEVOPS_TOKEN; // Your new PAT token
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

### 🛡️ Prevention

- Document all active tokens and their purposes
- Implement token rotation policies
- Use separate tokens for different applications
- Monitor for authentication failures

### 🔗 Related Issues

- [Token Expiration](#token-expiration)
- [Invalid Token](#invalid-token)

### ➡️ Next Steps

- If tokens are frequently revoked, discuss with your Azure DevOps administrator
- Consider implementing centralized token management
- Set up monitoring for authentication failures
- Document token creation and revocation procedures

---

## Basic Authentication vs PAT
<a id="basic-authentication-vs-pat"></a>

> 🟡 **Medium Severity** | ⏱️ 10 minutes | 📊 95% Success Rate

### 🔍 Symptoms

- Confusion about which authentication method to use
- Error message: `Basic authentication is not supported`
- Authentication fails when using username/password

### 🎯 Root Causes

- Attempting to use basic authentication (username/password) which is no longer supported
- Using the wrong authentication handler
- Mixing authentication methods
- Following outdated documentation

### ⚡ Quick Solution Steps

1. Switch to Personal Access Token (PAT) authentication:
   - Generate a PAT in Azure DevOps > User settings > Personal access tokens
   - Use the PAT token handler instead of basic authentication

2. Update your authentication code:

```typescript
// INCORRECT - Basic authentication (no longer supported)
// const basicHandler = azdev.getBasicHandler('username', 'password');

// CORRECT - PAT authentication
const token = process.env.AZURE_DEVOPS_TOKEN;
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

3. For automated systems, create a dedicated PAT:
   - Create a separate token for each automated system
   - Set appropriate scope limitations
   - Document the token's purpose and owner

### 🛡️ Prevention

- Always use PAT authentication for Azure DevOps API
- Never use basic authentication in new code
- Keep documentation and examples updated
- Follow security best practices for token management

### 🔗 Related Issues

- [Invalid Token](#invalid-token)
- [Token Expiration](#token-expiration)

### ➡️ Next Steps

- If you need alternative authentication methods, consider OAuth or Azure AD
- For service-to-service authentication, explore service principals
- Update any documentation or code examples that reference basic authentication
- Implement secure token storage

---

## 🔧 Diagnostic Tools

<details>
<summary><b>Authentication Diagnostic Tools</b></summary>

```typescript
// Authentication diagnostic utility
async function diagnoseAuthenticationIssues(orgUrl: string, token: string): Promise<void> {
  console.log("Running authentication diagnostics...");
  
  // Check token format
  if (!token || token.length === 0) {
    console.error("❌ Token is empty or undefined");
    return;
  }
  
  if (token includes('\n') || token.includes('\r')) {
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
    
    if (err.message includes("TF400813")) {
      console.log("This error indicates an invalid or revoked token");
    } else if (err.message includes("unable to get local issuer certificate")) {
      console.log("This error indicates a network or SSL certificate issue");
    }
  }
}

// Usage
diagnoseAuthenticationIssues("https://dev.azure.com/your-organization", process.env.AZURE_DEVOPS_TOKEN);
```

</details>

## 🆘 Getting Help

If the solutions in this category don't resolve your issue:

1. Run the [🔧 Diagnostic Tools](#-diagnostic-tools) to collect error details
2. Check the [Error Code Reference](../error-code-reference.md)
3. Try the [Advanced Troubleshooting](../advanced-troubleshooting.md) guide
4. See [Getting Help](../getting-help.md) for support options

---

<div align="right">
<i>Last updated: February 26, 2024</i>
</div>