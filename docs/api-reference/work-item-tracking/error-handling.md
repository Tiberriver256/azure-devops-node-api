**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > [WorkItemTrackingApi](./README.md) > Error Handling

# WorkItemTrackingApi Error Handling

This document provides guidance on handling errors that may occur when using the WorkItemTrackingApi class.

## Contents

- [Common Error Categories](#common-error-categories)
- [Status Code Reference](#status-code-reference)
- [Error Handling Patterns](#error-handling-patterns)
- [Advanced Error Handling](#advanced-error-handling)

---

## Common Error Categories

When working with the WorkItemTrackingApi, you may encounter several categories of errors:

### Authentication Errors

These errors occur when the API client cannot authenticate with Azure DevOps due to invalid or expired credentials.

```typescript
try {
  const workItem = await workItemTrackingApi.getWorkItem(42);
} catch (error) {
  if (error.statusCode === 401) {
    console.error("Authentication failed. Your credentials may be invalid or expired.");
    // Handle authentication error (e.g., prompt for new token)
  }
}
```

### Authorization Errors

These errors occur when the authenticated user doesn't have sufficient permissions to perform the requested operation.

```typescript
try {
  const workItem = await workItemTrackingApi.updateWorkItem(
    patchDocument, 
    workItemId, 
    projectName
  );
} catch (error) {
  if (error.statusCode === 403) {
    console.error("You don't have permission to update this work item.");
    // Handle permission error (e.g., request access or notify user)
  }
}
```

### Resource Not Found Errors

These errors occur when the requested resource (work item, query, etc.) doesn't exist.

```typescript
try {
  const workItem = await workItemTrackingApi.getWorkItem(999999);
} catch (error) {
  if (error.statusCode === 404) {
    console.error("Work item not found. It may have been deleted or never existed.");
    // Handle not found error (e.g., show user-friendly message)
  }
}
```

### Validation Errors

These errors occur when the provided data doesn't meet the requirements of the API.

```typescript
try {
  const patchDocument = [
    { op: "add", path: "/fields/System.Title", value: "" } // Empty title
  ];
  const workItem = await workItemTrackingApi.createWorkItem(
    patchDocument,
    projectName,
    "Bug"
  );
} catch (error) {
  if (error.statusCode === 400) {
    console.error("Validation error:", error.message);
    // Extract validation details if available
    if (error.body && error.body.value) {
      console.error("Details:", error.body.value);
    }
    // Handle validation error (e.g., show form errors to user)
  }
}
```

### Rate Limiting Errors

These errors occur when you've exceeded the API rate limits.

```typescript
try {
  // Making many API calls in a loop
  const results = await Promise.all(
    workItemIds.map(id => workItemTrackingApi.getWorkItem(id))
  );
} catch (error) {
  if (error.statusCode === 429) {
    console.error("Rate limit exceeded. Try again later or reduce request frequency.");
    // Handle rate limiting (e.g., implement exponential backoff)
  }
}
```

---

## Status Code Reference

| Status Code | Error Type | Common Causes | Resolution Strategies |
|-------------|------------|--------------|----------------------|
| 400 | Bad Request | Invalid data, malformed JSON, missing required fields | Validate inputs before sending, check for required fields |
| 401 | Unauthorized | Invalid/expired token, missing authentication | Refresh token, check authentication headers |
| 403 | Forbidden | Insufficient permissions | Request appropriate permissions, check project access |
| 404 | Not Found | Resource doesn't exist | Verify IDs are correct, check if resource was deleted |
| 409 | Conflict | Concurrent modification, version conflict | Fetch latest version, implement retry logic |
| 429 | Too Many Requests | Exceeded rate limits | Implement backoff strategy, batch requests |
| 500 | Server Error | Internal Azure DevOps error | Retry with exponential backoff |
| 503 | Service Unavailable | Service outage or maintenance | Retry later, check Azure DevOps status page |

---

## Error Handling Patterns

### Basic Try/Catch

The simplest pattern for handling errors:

```typescript
try {
  const workItem = await workItemTrackingApi.getWorkItem(42);
  console.log(workItem.fields["System.Title"]);
} catch (error) {
  console.error("Error retrieving work item:", error.message);
}
```

### Detailed Error Handling

A more comprehensive approach that handles different error types:

```typescript
async function getWorkItemSafely(api, workItemId, projectName) {
  try {
    return await api.getWorkItem(workItemId, projectName);
  } catch (error) {
    switch (error.statusCode) {
      case 401:
        console.error("Authentication error. Please sign in again.");
        // Trigger authentication flow
        break;
      case 403:
        console.error("You don't have permission to access this work item.");
        break;
      case 404:
        console.error(`Work item ${workItemId} was not found.`);
        break;
      case 429:
        console.error("Rate limit exceeded. Retrying after delay...");
        // Wait and retry
        await new Promise(resolve => setTimeout(resolve, 5000));
        return getWorkItemSafely(api, workItemId, projectName); // Retry
      default:
        console.error(`Unexpected error (${error.statusCode}): ${error.message}`);
    }
    
    // Optionally rethrow or return null/default value
    return null;
  }
}
```

### Retry Pattern

For operations that might fail due to transient issues:

```typescript
async function withRetry(operation, maxRetries = 3, initialDelay = 1000) {
  let lastError;
  
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await operation();
    } catch (error) {
      lastError = error;
      
      // Only retry on specific status codes that might be transient
      const retryableCodes = [429, 500, 503];
      if (!retryableCodes.includes(error.statusCode)) {
        throw error; // Don't retry client errors
      }
      
      console.warn(`Attempt ${attempt} failed, retrying in ${initialDelay / 1000}s...`);
      
      // Exponential backoff
      await new Promise(resolve => setTimeout(resolve, initialDelay));
      initialDelay *= 2; // Double the delay for next attempt
    }
  }
  
  throw lastError; // All retries failed
}

// Usage
try {
  const workItem = await withRetry(() => 
    workItemTrackingApi.getWorkItem(42)
  );
  console.log(workItem.fields["System.Title"]);
} catch (error) {
  console.error("All retry attempts failed:", error.message);
}
```

---

## Advanced Error Handling

### Custom Error Classes

Create custom error classes for more structured error handling:

```typescript
class WorkItemApiError extends Error {
  constructor(statusCode, message, details) {
    super(message);
    this.name = 'WorkItemApiError';
    this.statusCode = statusCode;
    this.details = details;
  }
  
  isAuthError() {
    return this.statusCode === 401 || this.statusCode === 403;
  }
  
  isNotFoundError() {
    return this.statusCode === 404;
  }
  
  isValidationError() {
    return this.statusCode === 400;
  }
  
  isRateLimitError() {
    return this.statusCode === 429;
  }
  
  isServerError() {
    return this.statusCode >= 500;
  }
}

// Usage
async function safeGetWorkItem(api, workItemId, projectName) {
  try {
    return await api.getWorkItem(workItemId, projectName);
  } catch (error) {
    const details = error.body ? error.body.value : undefined;
    throw new WorkItemApiError(
      error.statusCode || 500, 
      error.message, 
      details
    );
  }
}

// Using the custom error
try {
  const workItem = await safeGetWorkItem(workItemTrackingApi, 42, "MyProject");
  // Process work item
} catch (error) {
  if (error.isAuthError()) {
    // Handle authentication/authorization error
  } else if (error.isNotFoundError()) {
    // Handle not found error
  } else if (error.isServerError()) {
    // Handle server error, possibly retry
  } else {
    // Handle other errors
  }
}
```

### Batch Operation Error Handling

When performing batch operations, you may want to continue even if some operations fail:

```typescript
async function updateWorkItemsBatch(api, projectName, updates) {
  const results = {
    successful: [],
    failed: []
  };
  
  // Process in parallel but handle errors individually
  const updatePromises = updates.map(async update => {
    try {
      const patchDocument = update.fields.map(field => ({
        op: "add",
        path: `/fields/${field.name}`,
        value: field.value
      }));
      
      const updatedWorkItem = await api.updateWorkItem(
        patchDocument,
        update.id,
        projectName
      );
      
      results.successful.push({
        id: update.id,
        workItem: updatedWorkItem
      });
      
      return updatedWorkItem;
    } catch (error) {
      results.failed.push({
        id: update.id,
        error: {
          statusCode: error.statusCode,
          message: error.message,
          details: error.body ? error.body.value : undefined
        }
      });
      
      return null; // Don't throw, allowing other operations to continue
    }
  });
  
  // Wait for all operations to complete (successful or not)
  await Promise.all(updatePromises);
  
  return results;
}

// Usage
const results = await updateWorkItemsBatch(workItemTrackingApi, "MyProject", [
  { id: 42, fields: [{ name: "System.State", value: "Active" }] },
  { id: 43, fields: [{ name: "System.State", value: "Resolved" }] }
]);

console.log(`Updated ${results.successful.length} work items successfully`);

if (results.failed.length > 0) {
  console.error(`Failed to update ${results.failed.length} work items:`);
  results.failed.forEach(failure => {
    console.error(`Work Item #${failure.id}: ${failure.error.message}`);
  });
}
```

## See Also

- [WorkItemTrackingApi Class](./README.md)
- [Common Scenarios](./common-scenarios.md)
- [Azure DevOps API Rate Limits](../../concepts/api-limits.md)
- [Authentication Guide](../../guides/authentication.md)