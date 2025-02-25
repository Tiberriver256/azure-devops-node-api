# Troubleshooting

This section addresses common issues you might encounter when implementing work item automation with the Azure DevOps Node API.

## Authentication Issues

### Problem: 401 Unauthorized Error

**Symptoms:**
```
Error: TF400813: The user 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' is not authorized to access this resource.
```

**Solutions:**
1. **Check your Personal Access Token (PAT):**
   - Ensure your PAT hasn't expired
   - Verify it has the correct scopes (`Work Items: Read, Write, & Manage`)
   - Generate a new token if necessary

2. **Verify organization access:**
   - Ensure you have access to the organization specified in your connection URL
   - Check that the organization name is spelled correctly

3. **Check project permissions:**
   - Verify you have the necessary permissions in the specific project

### Problem: Unable to Connect to Azure DevOps

**Symptoms:**
```
Error: connect ETIMEDOUT xxx.xxx.xxx.xxx:443
```

**Solutions:**
1. **Check network connectivity:**
   - Verify your network can reach Azure DevOps
   - Check if a proxy is required for your network

2. **Verify the organization URL:**
   - Ensure the URL format is correct: `https://dev.azure.com/your-organization`
   - Try accessing the URL in a browser to confirm it's valid

## Work Item Creation Issues

### Problem: Invalid Field Reference

**Symptoms:**
```
Error: TF401232: Field 'Microsoft.VSTS.Common.AcceptanceCriteria' cannot be found.
```

**Solutions:**
1. **Check process template:**
   - Different process templates (Agile, Scrum, CMMI) have different field names
   - Use the Azure DevOps web interface to identify the correct field names for your process

2. **Verify field reference:**
   - Ensure you're using the correct field reference path
   - Field references are case-sensitive

3. **Check field applicability:**
   - Some fields only apply to specific work item types
   - For example, Story Points might only apply to User Stories, not Tasks

### Problem: Required Fields Missing

**Symptoms:**
```
Error: TF401149: You must provide a value for the required field 'System.Title'.
```

**Solutions:**
1. **Add all required fields:**
   - Each work item type has required fields that must be provided
   - At minimum, include `System.Title` for all work item types

2. **Check process customizations:**
   - Your organization might have customized the process to require additional fields

## Relationship Issues

### Problem: Invalid Work Item Relationship

**Symptoms:**
```
Error: TF401347: The link type 'System.LinkTypes.Hierarchy-Reverse' is not valid.
```

**Solutions:**
1. **Check relationship type:**
   - Ensure you're using a valid relationship type
   - Common types include:
     - `System.LinkTypes.Hierarchy-Reverse` (child → parent)
     - `System.LinkTypes.Hierarchy-Forward` (parent → child)
     - `System.LinkTypes.Related` (related work items)

2. **Verify work item existence:**
   - Ensure the work item you're linking to exists
   - Check that the URL in the relationship is correct

### Problem: Circular Relationship

**Symptoms:**
```
Error: TF201036: You cannot add a link of type 'Parent' because it would create a circular link hierarchy.
```

**Solutions:**
1. **Check relationship direction:**
   - Ensure you're not creating circular relationships
   - A work item cannot be both a parent and a child (directly or indirectly) of another work item

## Attachment Issues

### Problem: File Not Found

**Symptoms:**
```
Error: File not found: ./docs/diagram.png
```

**Solutions:**
1. **Check file path:**
   - Ensure the file exists at the specified path
   - Use absolute paths or verify relative paths are correct

2. **Check file permissions:**
   - Ensure your application has permission to read the file

### Problem: Attachment Too Large

**Symptoms:**
```
Error: TF400724: The attachment exceeds the maximum allowed size of 60 MB.
```

**Solutions:**
1. **Reduce file size:**
   - Compress the file or reduce its resolution/quality
   - Split large files into multiple smaller attachments

2. **Use external storage:**
   - For very large files, consider storing them elsewhere (like Azure Blob Storage)
   - Add a link to the external storage in the work item description

## API Rate Limiting

### Problem: Too Many Requests

**Symptoms:**
```
Error: TF429: Too many requests. Please try again later.
```

**Solutions:**
1. **Implement retry logic:**
   - Use the retry utility from the complete example
   - Add exponential backoff between retries

2. **Batch operations:**
   - Reduce the number of API calls by batching operations where possible

3. **Add delays:**
   - Add small delays between API calls to avoid triggering rate limits

## Debugging Tips

### Enable Verbose Logging

Add detailed logging to help diagnose issues:

```typescript
// Configure logging
const config = {
  // ... other config
  logLevel: "debug" // Options: debug, info, warn, error
};

// Logger implementation
const logger = {
  debug: (message: string) => {
    if (config.logLevel === "debug") console.log(`[DEBUG] ${message}`);
  },
  // ... other log levels
};

// Use throughout your code
logger.debug("Detailed information for debugging");
```

### Inspect API Responses

Examine the full response from API calls:

```typescript
const workItem = await witClient.getWorkItem(workItemId);
console.log(JSON.stringify(workItem, null, 2)); // Pretty print
```

### Check Azure DevOps API Version

The Azure DevOps Node API uses a specific API version. If you encounter unexpected behavior, check which API version is being used:

```typescript
// Get the API version being used
const connection = new azdev.WebApi(orgUrl, authHandler);
console.log(`API Version: ${connection.serverUrl}`);
```

## Getting Help

If you're still experiencing issues:

1. **Check Azure DevOps Node API documentation:**
   - [GitHub Repository](https://github.com/Microsoft/azure-devops-node-api)
   - [API Reference](https://github.com/Microsoft/azure-devops-node-api/blob/master/api/README.md)

2. **Review Azure DevOps REST API documentation:**
   - The Node API is a wrapper around the REST API
   - [REST API Reference](https://docs.microsoft.com/en-us/rest/api/azure/devops/?view=azure-devops-rest-6.0)

3. **Search for issues on GitHub:**
   - [Azure DevOps Node API Issues](https://github.com/Microsoft/azure-devops-node-api/issues)

4. **Ask on Stack Overflow:**
   - Use the tags `azure-devops-node-api` and `azure-devops`

---

**Navigation**:
- [Back to Index](../index.md)
- Previous: [Complete Example](./06-complete-example.md)
- Next: [Next Steps & Resources](./08-next-steps-resources.md) 