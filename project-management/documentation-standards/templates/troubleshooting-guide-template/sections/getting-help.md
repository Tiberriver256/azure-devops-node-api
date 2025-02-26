# Getting Help

> **Navigation**: [Home](../index.md) > Getting Help

This page provides resources and guidance for getting additional help when the troubleshooting guide doesn't resolve your issue.

> **TL;DR:** If you've tried all the troubleshooting steps and still need help, use the resources on this page.

## Before Seeking Help

Before reaching out for additional help, ensure you've:

1. Reviewed the [Quick Solutions](../quick-solutions.md) for common issues
2. Followed the relevant troubleshooting steps in the [problem categories](../index.md#common-issue-categories)
3. Tried the techniques in the [Advanced Troubleshooting](./advanced-troubleshooting.md) guide
4. Checked the [Error Code Reference](./error-code-reference.md) for your specific error

## Preparing Your Help Request

To get the most effective help, prepare the following information:

### Environment Details

```typescript
// Run this code to gather environment information
function collectEnvironmentInfo() {
  const info = {
    nodeVersion: process.version,
    platform: process.platform,
    architecture: process.arch,
    apiPackageVersion: "unknown", // Will be populated below
    dependencies: {}
  };
  
  // Try to get package versions
  try {
    const packageJson = require("./package.json");
    info.apiPackageVersion = packageJson.dependencies["azure-devops-node-api"] || "not found";
    
    // Get relevant dependencies
    const relevantDeps = [
      "azure-devops-node-api",
      "vso-node-api", // Legacy package
      "typescript",
      "node-fetch",
      "tunnel",
      "proxy-agent"
    ];
    
    relevantDeps.forEach(dep => {
      if (packageJson.dependencies && packageJson.dependencies[dep]) {
        info.dependencies[dep] = packageJson.dependencies[dep];
      } else if (packageJson.devDependencies && packageJson.devDependencies[dep]) {
        info.dependencies[dep] = packageJson.devDependencies[dep];
      }
    });
  } catch (error) {
    console.log("Could not read package.json:", error.message);
  }
  
  // Format as markdown for easy sharing
  let markdown = "## Environment Information\n\n";
  markdown += `- Node.js Version: ${info.nodeVersion}\n`;
  markdown += `- Platform: ${info.platform} (${info.architecture})\n`;
  markdown += `- Azure DevOps Node API Version: ${info.apiPackageVersion}\n\n`;
  
  if (Object.keys(info.dependencies).length > 0) {
    markdown += "### Relevant Dependencies\n\n";
    Object.keys(info.dependencies).forEach(dep => {
      markdown += `- ${dep}: ${info.dependencies[dep]}\n`;
    });
  }
  
  console.log(markdown);
  return markdown;
}

// Run the function to collect information
collectEnvironmentInfo();
```

### Reproduction Steps

Document clear steps to reproduce the issue:

1. Provide a minimal code example that demonstrates the problem
2. Include exact error messages and stack traces
3. Note any specific conditions that trigger the issue
4. Specify the expected behavior versus the actual behavior

Example format:

```markdown
## Issue Description

When attempting to update a work item using the WorkItemTrackingApi, I receive a 400 Bad Request error.

## Steps to Reproduce

1. Initialize the API client with a PAT token
2. Retrieve an existing work item
3. Attempt to update the "System.Title" field
4. Error occurs during the update operation

## Code Example

```typescript
import * as azdev from "azure-devops-node-api";

async function updateWorkItem() {
  const orgUrl = "https://dev.azure.com/my-organization";
  const token = process.env.AZURE_DEVOPS_TOKEN;
  const workItemId = 12345;
  
  const authHandler = azdev.getPersonalAccessTokenHandler(token);
  const connection = new azdev.WebApi(orgUrl, authHandler);
  
  try {
    const witApi = await connection.getWorkItemTrackingApi();
    
    // Create patch document
    const patchDocument = [
      {
        op: "add",
        path: "/fields/System.Title",
        value: "Updated Title"
      }
    ];
    
    // Update work item
    const result = await witApi.updateWorkItem(
      null, // No context
      patchDocument,
      workItemId
    );
    
    console.log("Work item updated:", result.id);
  } catch (error) {
    console.error("Error updating work item:", error.message);
    console.error("Status code:", error.statusCode);
    console.error("Stack trace:", error.stack);
  }
}
```

## Error Message

```
Error updating work item: TF401232: Work item 12345 does not exist, or you do not have permissions to access it.
Status code: 400
```

## Expected Behavior

The work item should be updated with the new title.

## Troubleshooting Steps Already Tried

1. Verified the work item exists and is accessible via the web UI
2. Confirmed the PAT token has "Work Items (Read, Write)" permissions
3. Successfully retrieved the work item using the same API client
4. Tried with different field values and operations
```

## Where to Get Help

### Official Support Channels

1. **Azure DevOps Support**
   - For service-related issues: [Azure DevOps Support](https://azure.microsoft.com/support/devops/)
   - For billing or account issues: [Azure Billing Support](https://azure.microsoft.com/support/options/)

2. **GitHub Issues**
   - For API client bugs or feature requests: [azure-devops-node-api Issues](https://github.com/microsoft/azure-devops-node-api/issues)
   - Search existing issues before creating a new one

3. **Microsoft Q&A**
   - For general questions: [Microsoft Q&A for Azure DevOps](https://docs.microsoft.com/en-us/answers/topics/azure-devops.html)
   - Tag your question with `azure-devops` and `node-api`

### Community Resources

1. **Stack Overflow**
   - Tag questions with: [azure-devops-api](https://stackoverflow.com/questions/tagged/azure-devops-api)
   - Include the `node.js` tag for Node.js specific questions

2. **Azure DevOps Community**
   - [Developer Community](https://developercommunity.visualstudio.com/AzureDevOps)
   - [Azure DevOps Blog](https://devblogs.microsoft.com/devops/)

3. **Twitter**
   - Follow [@AzureDevOps](https://twitter.com/azuredevops) for updates and support

### Internal Support (For Enterprise Users)

If you're using Azure DevOps within a large organization:

1. Contact your internal Azure DevOps administrator or DevOps team
2. Check your organization's internal knowledge base or support portal
3. Reach out to your Microsoft account representative

## Support Request Templates

### GitHub Issue Template

```markdown
## Issue Description

[Concise description of the issue]

## Environment

- Node.js Version: [e.g., v14.17.0]
- OS: [e.g., Windows 10, Ubuntu 20.04]
- azure-devops-node-api Version: [e.g., 11.0.0]
- Azure DevOps Service: [e.g., dev.azure.com or on-premises server version]

## Steps to Reproduce

1. [First step]
2. [Second step]
3. [And so on...]

## Code Example

```typescript
// Minimal code that reproduces the issue
```

## Error Message

```
[Exact error message and stack trace]
```

## Expected Behavior

[What you expected to happen]

## Actual Behavior

[What actually happened]

## Additional Context

[Any other information that might be relevant]
```

### Stack Overflow Question Template

```markdown
# Azure DevOps Node API: [Brief description of issue]

I'm experiencing an issue with the Azure DevOps Node API when [brief description of what you're trying to do].

## Environment

- Node.js: v14.17.0
- azure-devops-node-api: 11.0.0
- OS: Windows 10

## What I'm trying to do

[Detailed explanation of what you're trying to achieve]

## Code

```typescript
// Your code here
```

## Error

```
Error message and stack trace
```

## What I've tried

- [List troubleshooting steps you've already taken]
- [Include links to documentation you've consulted]

Any help would be greatly appreciated!

Tags: azure-devops-api, node.js, typescript
```

## Next Steps

If you've exhausted all troubleshooting options and support channels, consider these alternatives:

1. **Implement a workaround**:
   - Use the REST API directly instead of the Node.js client
   - Try a different approach to achieve your goal

2. **Contribute a fix**:
   - The Azure DevOps Node API is open source
   - Consider fixing the issue yourself and submitting a pull request

3. **Engage with the community**:
   - Join the [Azure DevOps Community](https://azure.microsoft.com/en-us/services/devops/community/)
   - Participate in community calls and events

---

**Related Pages:**
- [Issue Identification](./issue-identification.md)
- [Decision Tree](./decision-tree.md)
- [Advanced Troubleshooting](./advanced-troubleshooting.md)
- [Error Code Reference](./error-code-reference.md) 