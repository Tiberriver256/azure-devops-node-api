# Work Item Tracking API Troubleshooting Guide

## Common Issues

### Issue 1: Authentication Failures

**Symptoms**:
- 401 Unauthorized responses
- Error message: `TF400813: The user 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' is not authorized to access this resource.`
- Unable to initialize the API client

**Solution**:
1. Verify your Personal Access Token (PAT) has not expired
2. Ensure your PAT includes the "Work Items (Read, Write)" scope
3. Check that your organization URL is correct
4. Validate that the token is being passed correctly in the authentication handler

**Example**:
```typescript
// Correct way to set up authentication
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Check that your organization URL is formatted correctly
// Correct: https://dev.azure.com/your-organization
// Incorrect: https://your-organization.visualstudio.com
```

### Issue 2: Work Item Not Found

**Symptoms**:
- 404 Not Found responses
- Error message: `TF401232: Work item xxxx does not exist, or you do not have permissions to read it.`
- Null or undefined returned when attempting to retrieve work items

**Solution**:
1. Verify the work item ID exists in your organization
2. Check that you have permissions to access the work item
3. Ensure the work item is in the project you're specifying (if providing a project parameter)
4. For cross-project work item access, ensure your PAT has organization-wide access

**Example**:
```typescript
try {
    // Add error handling when retrieving work items
    const workItem = await witApi.getWorkItem(id);
    // Process work item
} catch (error) {
    if (error.statusCode === 404) {
        console.log(`Work item ${id} does not exist or you don't have permission to access it`);
    } else {
        console.log(`Error: ${error.message}`);
    }
}
```

### Issue 3: Invalid Work Item Fields

**Symptoms**:
- 400 Bad Request responses when creating or updating work items
- Error message: `VS403330: The value 'X' is not a valid value for field 'Y'`
- Error message: `TF401090: The field 'Z' does not exist`

**Solution**:
1. Verify field reference names (use System.Title, not Title)
2. Check that the field exists for the work item type you're using
3. Ensure the field value matches the expected type (string, number, etc.)
4. For enumerated fields (like State), verify the value is valid for that field

**Example**:
```typescript
// Correct field reference names and values
const patchDocument = [
    {
        op: "add",
        path: "/fields/System.Title", // Correct: use full reference name
        value: "My work item"
    },
    {
        op: "add",
        path: "/fields/System.State",
        value: "New" // Ensure this matches a valid state for your work item type
    }
];

// Work item type names are case-sensitive
const newWorkItem = await witApi.createWorkItem(
    patchDocument,
    "MyProject",
    "Task" // Must match the exact work item type name (Task, Bug, etc.)
);
```

## Prevention Tips

1. Always use try/catch blocks when making API calls to handle errors gracefully
2. Validate work item IDs before sending them to the API
3. Use constants for field reference names to avoid typos
4. Check project and work item type existence before creating work items
5. Test your code against a dev/test Azure DevOps organization before production

## See Also

- [Work Item Tracking API Reference](../api-reference/work-item-tracking-api.md)
- [Authentication Tutorial](../tutorials/authentication-setup.md)
- [Microsoft Azure DevOps REST API Documentation](https://docs.microsoft.com/en-us/rest/api/azure/devops/?view=azure-devops-rest-6.0) 