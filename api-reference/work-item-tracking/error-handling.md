# WorkItemTrackingApi Error Handling

[◀ Back to WorkItemTrackingApi](./README.md)

---

## Overview

This page provides guidance on handling errors and exceptions when using the `WorkItemTrackingApi` class.

## Common Errors

### Authentication Errors

| Error | HTTP Status | Description | Solution |
|-------|-------------|-------------|----------|
| Unauthorized | 401 | Invalid or expired authentication token | Verify your authentication token is valid and not expired |
| Forbidden | 403 | Insufficient permissions | Ensure your token has the necessary scopes and permissions |

### Work Item Errors

| Error | HTTP Status | Description | Solution |
|-------|-------------|-------------|----------|
| Not Found | 404 | Work item doesn't exist | Verify the work item ID and project |
| Bad Request | 400 | Invalid request format | Check the format of your request payload |
| Conflict | 409 | Concurrent update conflict | Retrieve the latest version and retry the update |

### Query Errors

| Error | HTTP Status | Description | Solution |
|-------|-------------|-------------|----------|
| Bad Request | 400 | Invalid WIQL syntax | Check your WIQL query syntax |
| Not Found | 404 | Query doesn't exist | Verify the query ID or path |

## Error Handling Patterns

### Basic Error Handling

```typescript
try {
    const workItem = await workItemTrackingApi.getWorkItem(42);
    console.log(`Work Item ${workItem.id}: ${workItem.fields["System.Title"]}`);
} catch (error) {
    console.error("Error retrieving work item:", error.message);
}
```

### Detailed Error Handling

```typescript
try {
    const workItem = await workItemTrackingApi.getWorkItem(42);
    console.log(`Work Item ${workItem.id}: ${workItem.fields["System.Title"]}`);
} catch (error) {
    if (error.statusCode === 401) {
        console.error("Authentication error. Please check your credentials.");
    } else if (error.statusCode === 403) {
        console.error("Permission denied. You don't have access to this work item.");
    } else if (error.statusCode === 404) {
        console.error("Work item not found. Please check the ID.");
    } else {
        console.error("Unexpected error:", error.message);
    }
}
```

### Handling Concurrent Updates

```typescript
async function updateWorkItemWithRetry(id, patchDocument, maxRetries = 3) {
    let retries = 0;
    
    while (retries < maxRetries) {
        try {
            // Get the latest version of the work item
            const workItem = await workItemTrackingApi.getWorkItem(id);
            const rev = workItem.rev;
            
            // Add the revision to ensure we're updating the latest version
            patchDocument.push({
                op: Operation.Test,
                path: "/rev",
                value: rev
            });
            
            // Attempt to update the work item
            const updatedWorkItem = await workItemTrackingApi.updateWorkItem(
                patchDocument,
                id
            );
            
            return updatedWorkItem;
        } catch (error) {
            if (error.statusCode === 409) {
                // Conflict - another user updated the work item
                console.log(`Concurrent update detected, retrying (${retries + 1}/${maxRetries})...`);
                retries++;
                
                // Remove the test operation for the next attempt
                patchDocument = patchDocument.filter(op => op.path !== "/rev");
                
                // Wait a short time before retrying
                await new Promise(resolve => setTimeout(resolve, 1000));
            } else {
                // Other error - rethrow
                throw error;
            }
        }
    }
    
    throw new Error(`Failed to update work item after ${maxRetries} attempts due to concurrent updates.`);
}
```

## Handling Rate Limiting

Azure DevOps API has rate limits that may cause requests to be throttled. Here's how to handle rate limiting:

```typescript
async function getWorkItemWithRateLimitHandling(id) {
    const maxRetries = 5;
    let retries = 0;
    
    while (retries < maxRetries) {
        try {
            return await workItemTrackingApi.getWorkItem(id);
        } catch (error) {
            if (error.statusCode === 429) {
                // Rate limited - get retry-after header
                const retryAfter = error.headers?.["retry-after"] || 1;
                const waitTime = parseInt(retryAfter, 10) * 1000;
                
                console.log(`Rate limited, waiting ${waitTime}ms before retrying...`);
                retries++;
                
                // Wait for the specified time
                await new Promise(resolve => setTimeout(resolve, waitTime));
            } else {
                // Other error - rethrow
                throw error;
            }
        }
    }
    
    throw new Error(`Failed to get work item after ${maxRetries} attempts due to rate limiting.`);
}
```

## Validation Before API Calls

Validating inputs before making API calls can prevent many errors:

```typescript
function validateWorkItemFields(fields) {
    // Validate required fields
    if (!fields["System.Title"]) {
        throw new Error("Work item title is required");
    }
    
    // Validate field length
    if (fields["System.Title"].length > 255) {
        throw new Error("Work item title cannot exceed 255 characters");
    }
    
    // Validate field format
    if (fields["System.Tags"] && !fields["System.Tags"].match(/^[^,;]+(?:; [^,;]+)*$/)) {
        throw new Error("Tags must be semicolon-separated values");
    }
    
    return true;
}

async function createValidatedWorkItem(fields, project, type) {
    // Validate fields before API call
    validateWorkItemFields(fields);
    
    // Create patch document
    const patchDocument = Object.entries(fields).map(([key, value]) => ({
        op: Operation.Add,
        path: `/fields/${key}`,
        value
    }));
    
    // Create work item
    return await workItemTrackingApi.createWorkItem(
        patchDocument,
        project,
        type
    );
}
```

## Debugging Tips

### Enabling Verbose Logging

```typescript
// Enable verbose logging for debugging
const connection = new azdev.WebApi(orgUrl, authHandler, {
    allowRetries: true,
    maxRetries: 5,
    logLevel: "verbose"  // Options: "error", "warning", "info", "verbose"
});
```

### Inspecting Request/Response Details

```typescript
// Custom request handler for logging
const loggingHandler = {
    prepareRequest: (options) => {
        console.log("Request URL:", options.url);
        console.log("Request Method:", options.method);
        console.log("Request Headers:", options.headers);
        return options;
    },
    processResponse: async (response, body) => {
        console.log("Response Status:", response.statusCode);
        console.log("Response Headers:", response.headers);
        return { response, body };
    }
};

// Add the logging handler to the auth handler chain
const handlers = [authHandler, loggingHandler];
const connection = new azdev.WebApi(orgUrl, handlers);
```

## See Also

- [Examples](./examples.md)
- [Common Scenarios](./common-scenarios.md)
- [WorkItemTrackingApi Methods](./methods/README.md)

---

## Navigation

- [WorkItemTrackingApi Overview](./README.md)
- [Constructor](./constructor.md)
- [Properties](./properties.md)
- [Methods](./methods/README.md)
- [Examples](./examples.md)
- [Common Scenarios](./common-scenarios.md) 