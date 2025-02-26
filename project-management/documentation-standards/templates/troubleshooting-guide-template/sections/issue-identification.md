# Issue Identification

> **Navigation**: [Home](../index.md) > Issue Identification

This page helps you identify the specific issue you're experiencing with the Azure DevOps Node API.

## 🔍 How to Use This Page

1. Review the common symptoms and error messages below
2. Use the diagnostic tools to gather more information about your issue
3. Once you've identified your issue type, navigate to the appropriate problem category
4. If you're still unsure, use our [Decision Tree](./decision-tree.md) to guide you

## Common Symptoms

> **TL;DR:** Match your symptoms to identify your issue category.

### 🔑 Authentication & Authorization Symptoms

- Unable to connect to Azure DevOps
- Receiving 401 Unauthorized responses
- "TF400813: The user is not authorized to access this resource"
- Authentication works in browser but not in API
- Token suddenly stopped working

### 🌐 Connection & Network Symptoms

- Timeouts when connecting to Azure DevOps
- Intermittent connection failures
- "Unable to connect to the remote server"
- Slow response times
- Connection works from some locations but not others

### 📊 Data Retrieval Symptoms

- Empty results when data should exist
- Partial data returned
- "TF401349: The query did not return any results"
- Unexpected null values in response
- Filtering not working as expected

### ✏️ Data Modification Symptoms

- Changes not being saved
- "TF401019: The work item is currently locked for changes"
- Validation errors when updating data
- Unexpected field requirements
- Version conflicts when updating

### ⚡ Performance Symptoms

- API calls taking longer than expected
- Throttling or rate limiting errors
- Memory usage growing over time
- Batch operations timing out
- "TF400733: Request has been terminated"

## Error Message Patterns

Error messages from the Azure DevOps API typically follow these patterns:

### HTTP Status Codes

| Status Code | Common Meaning |
|-------------|----------------|
| 401 | Authentication or authorization issue |
| 403 | Permission issue |
| 404 | Resource not found |
| 409 | Conflict (version mismatch) |
| 429 | Rate limiting/throttling |
| 500 | Server error |
| 503 | Service unavailable |

### Error Code Prefixes

- **TF**: Team Foundation error codes
- **VS**: Visual Studio error codes
- **AADSTS**: Azure Active Directory errors
- **VSTS**: Visual Studio Team Services errors

For a complete list of error codes and their solutions, see the [Error Code Reference](./error-code-reference.md).

## Diagnostic Tools

<details>
<summary><b>Connection Diagnostics</b></summary>

Use this code to diagnose connection issues:

```typescript
import * as azdev from "azure-devops-node-api";

async function diagnoseConnection() {
  try {
    // Basic connection test
    const orgUrl = "https://dev.azure.com/your-organization";
    const token = process.env.AZURE_DEVOPS_TOKEN;
    
    console.log("Testing connection to:", orgUrl);
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Test connection
    console.log("Attempting to connect...");
    const connectionData = await connection.connect();
    console.log("✅ Connection successful!");
    console.log("Connected to:", connectionData.authenticatedUser.providerDisplayName);
    console.log("API version:", connectionData.deploymentType);
    
    // Test a basic API call
    console.log("\nTesting API access...");
    const projectApi = await connection.getProjectApi();
    const projects = await projectApi.getProjects();
    console.log(`✅ API access successful! Retrieved ${projects.length} projects.`);
    
    return { success: true, connectionData, projects };
  } catch (error) {
    console.error("❌ Connection diagnosis failed:", error.message);
    
    // Provide more specific error information
    if (error.statusCode) {
      console.error(`HTTP Status: ${error.statusCode}`);
    }
    
    if (error.message.includes("401")) {
      console.error("This appears to be an authentication issue. Check your token.");
    } else if (error.message.includes("TF400813")) {
      console.error("This appears to be a permissions issue.");
    } else if (error.message.includes("ENOTFOUND") || error.message.includes("ETIMEDOUT")) {
      console.error("This appears to be a network connectivity issue.");
    }
    
    return { success: false, error };
  }
}
```

</details>

<details>
<summary><b>Enhanced Logging</b></summary>

Enable detailed logging to troubleshoot issues:

```typescript
import * as azdev from "azure-devops-node-api";
import * as fs from "fs";

// Configure logging
function enableDetailedLogging(webApi) {
  const logFile = "azure-devops-api.log";
  
  // Clear previous log
  fs.writeFileSync(logFile, "");
  
  // Set up logging
  webApi.setLogFunction((area, message) => {
    const timestamp = new Date().toISOString();
    const logEntry = `${timestamp} [${area}] ${message}\n`;
    
    // Log to file
    fs.appendFileSync(logFile, logEntry);
    
    // Also log to console
    console.log(logEntry);
  });
  
  console.log(`Detailed logging enabled. Log file: ${logFile}`);
}

// Usage
const orgUrl = "https://dev.azure.com/your-organization";
const token = process.env.AZURE_DEVOPS_TOKEN;
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Enable logging before making API calls
enableDetailedLogging(connection);
```

</details>

<details>
<summary><b>Environment Verification</b></summary>

Verify your environment configuration:

```typescript
function verifyEnvironment() {
  console.log("Environment Verification:");
  console.log("-----------------------");
  
  // Node.js version
  console.log(`Node.js version: ${process.version}`);
  if (process.version.startsWith("v8.") || process.version.startsWith("v6.")) {
    console.warn("⚠️ Warning: Using an older version of Node.js. Version 10+ is recommended.");
  }
  
  // Check for required environment variables
  const requiredEnvVars = ["AZURE_DEVOPS_TOKEN", "AZURE_DEVOPS_ORG"];
  for (const envVar of requiredEnvVars) {
    if (process.env[envVar]) {
      console.log(`✅ ${envVar} is set`);
    } else {
      console.error(`❌ ${envVar} is not set`);
    }
  }
  
  // Check package versions
  try {
    const packageJson = require("./package.json");
    console.log("\nPackage versions:");
    console.log(`- azure-devops-node-api: ${packageJson.dependencies["azure-devops-node-api"] || "not installed"}`);
    
    // Check for common conflicting packages
    const potentialConflicts = ["vso-node-api", "vsts-task-lib"];
    for (const pkg of potentialConflicts) {
      if (packageJson.dependencies[pkg]) {
        console.warn(`⚠️ Warning: Potentially conflicting package ${pkg} is installed.`);
      }
    }
  } catch (error) {
    console.error("❌ Unable to read package.json");
  }
  
  // Network connectivity check
  console.log("\nChecking network connectivity...");
  const https = require("https");
  const req = https.get("https://dev.azure.com", (res) => {
    console.log(`✅ Azure DevOps connectivity: HTTP ${res.statusCode}`);
    res.resume();
  });
  
  req.on("error", (error) => {
    console.error(`❌ Azure DevOps connectivity error: ${error.message}`);
  });
}
```

</details>

## Next Steps

Once you've identified your issue:

1. Navigate to the specific [problem category](../index.md#common-issue-categories) that matches your symptoms
2. Try the [Quick Solutions](../quick-solutions.md) for immediate fixes to common problems
3. Use the [Decision Tree](./decision-tree.md) if you're still unsure which category applies
4. For complex issues, see the [Advanced Troubleshooting](./advanced-troubleshooting.md) guide

---

**Related Pages:**
- [Quick Solutions](../quick-solutions.md)
- [Decision Tree](./decision-tree.md)
- [Error Code Reference](./error-code-reference.md)
- [Getting Help](./getting-help.md) 