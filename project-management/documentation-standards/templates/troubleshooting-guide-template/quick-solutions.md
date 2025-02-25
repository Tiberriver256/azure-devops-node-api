# Quick Solutions for Common Issues

> **Navigation**: [Home](./index.md) > Quick Solutions

This page provides immediate fixes for the most common issues encountered with the [Component Name]. If your issue isn't listed here, check the appropriate category in the [issue index](./index.md).

## Common Issues Table

| Issue | Symptom | Quick Solution |
|-------|---------|---------------|
| **Invalid authentication token** | 401 Unauthorized error | Refresh your authentication token or create a new personal access token with appropriate scopes. [More details](./sections/problem-categories/authentication-issues.md#invalid-token) |
| **Token permissions insufficient** | 403 Forbidden error | Ensure your token has the required scopes. [More details](./sections/problem-categories/permission-errors.md#insufficient-scopes) |
| **Network connection failures** | ECONNREFUSED or timeout errors | Check your network connectivity, proxy settings, and confirm the Azure DevOps service is available. [More details](./sections/problem-categories/connection-problems.md#connection-timeout) |
| **Rate limiting** | 429 Too Many Requests error | Implement exponential backoff or reduce request frequency. [More details](./sections/problem-categories/performance-issues.md#rate-limiting) |
| **Incorrect API endpoint** | 404 Not Found error | Verify the API endpoint URL and ensure it matches your Azure DevOps organization. [More details](./sections/problem-categories/connection-problems.md#incorrect-endpoint) |
| **Malformed request data** | 400 Bad Request error | Check your request payload against the API documentation to ensure it's properly formatted. [More details](./sections/problem-categories/data-modification-errors.md#malformed-data) |
| **API version mismatch** | Unexpected errors or missing features | Ensure you're using a compatible API version with your client library. [More details](./sections/problem-categories/configuration-problems.md#version-mismatch) |
| **Missing required fields** | 422 Unprocessable Entity error | Check the API documentation for required fields and ensure all are provided. [More details](./sections/problem-categories/data-modification-errors.md#missing-fields) |

## Authentication Quick Fix

Most authentication issues can be resolved by generating a new Personal Access Token (PAT):

```typescript
// Example: Setting up authentication with a fresh PAT
import * as azdev from "azure-devops-node-api";

// The new PAT token should have appropriate scopes
const token: string = "your-new-pat-token";
const orgUrl: string = "https://dev.azure.com/your-organization";

// Creating a connection with the new token
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Test the connection
try {
    const connData = await connection.connect();
    console.log("Authentication successful");
} catch (err) {
    console.error("Authentication failed:", err.message);
}
```

## Connection Quick Fix

For connection issues, try this diagnostic approach:

<details>
<summary><b>Connection Diagnostic Code</b></summary>

```typescript
import * as azdev from "azure-devops-node-api";
import * as https from "https";
import * as http from "http";

// Function to check Azure DevOps connectivity
async function checkConnection(orgUrl: string, token: string) {
    console.log(`Testing connection to ${orgUrl}...`);
    
    // 1. Basic HTTP request to check network
    try {
        await new Promise((resolve, reject) => {
            const url = new URL(orgUrl);
            const options = {
                hostname: url.hostname,
                port: url.port || 443,
                path: url.pathname,
                method: 'HEAD',
                timeout: 5000
            };
            
            const req = https.request(options, (res) => {
                console.log(`✅ Basic HTTP connectivity: OK (Status: ${res.statusCode})`);
                resolve(res);
            });
            
            req.on('error', (e) => {
                console.error(`❌ Basic HTTP connectivity failed: ${e.message}`);
                reject(e);
            });
            
            req.on('timeout', () => {
                console.error('❌ Connection timed out');
                req.destroy();
                reject(new Error('Connection timed out'));
            });
            
            req.end();
        });
    } catch (err) {
        console.error('Network connectivity issue detected!');
        console.log('Suggestions:');
        console.log('- Check your internet connection');
        console.log('- Verify proxy settings if using a proxy');
        console.log('- Ensure Azure DevOps is not blocked by firewall');
        return;
    }
    
    // 2. Try API connection with token
    try {
        const authHandler = azdev.getPersonalAccessTokenHandler(token);
        const connection = new azdev.WebApi(orgUrl, authHandler);
        const connData = await connection.connect();
        console.log(`✅ API authentication: OK (Connected to ${connData.authenticatedUser.providerDisplayName})`);
    } catch (err) {
        console.error(`❌ API authentication failed: ${err.message}`);
        console.log('Suggestions:');
        console.log('- Check if your PAT token is valid and not expired');
        console.log('- Ensure the PAT has appropriate scopes');
        console.log('- Verify your organization URL is correct');
    }
}

// Usage
// checkConnection("https://dev.azure.com/your-organization", "your-pat-token");
```

</details>

## Next Steps

If these quick solutions didn't resolve your issue:

1. Find your specific issue category in the [main index](./index.md)
2. Check the [Error Code Reference](./sections/error-code-reference.md) if you have a specific error code
3. Try the [Issue Identification](./sections/issue-identification.md) guide if you're unsure which category applies
4. For complex issues, see [Advanced Troubleshooting](./sections/advanced-troubleshooting.md)

---

<div align="right">
  <a href="./sections/issue-identification.md">Next: Issue Identification →</a>
</div> 