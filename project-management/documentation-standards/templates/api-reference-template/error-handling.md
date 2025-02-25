# {ApiClassName} Error Handling

[◀ Back to {ApiClassName}](./README.md)

---

## Overview

This document provides guidance on handling errors and exceptions when working with the `{ApiClassName}` class. It covers common error types, error handling patterns, and best practices.

## Common Errors

### Authentication Errors

| Error | Description | Solution |
|-------|-------------|----------|
| Unauthorized (401) | Invalid or expired authentication token | Verify that your Personal Access Token (PAT) is valid and has not expired. Ensure it has the necessary scopes. |
| Forbidden (403) | Insufficient permissions | Ensure your authentication token has the required permissions for the operation. Check project-level and organization-level permissions. |

### {ResourceType} Errors

| Error | Description | Solution |
|-------|-------------|----------|
| Not Found (404) | {ResourceType} doesn't exist | Verify the {resourceType} ID and project. Check if the {resourceType} has been deleted or moved. |
| Bad Request (400) | Invalid request format | Check the request payload format. Ensure all required fields are provided and have valid values. |
| Conflict (409) | Concurrent update conflict | Retrieve the latest version of the {resourceType} and retry the operation with the updated data. |

### {AnotherResourceType} Errors

| Error | Description | Solution |
|-------|-------------|----------|
| Not Found (404) | {AnotherResourceType} doesn't exist | Verify the {anotherResourceType} ID or path. Check if it has been deleted or moved. |
| Bad Request (400) | Invalid {anotherResourceType} format | Check the {anotherResourceType} syntax. Ensure it follows the required format. |

## Error Handling Patterns

### Basic Error Handling

```typescript
try {
    const result = await {apiInstanceName}.{methodName}({params});
    console.log("Operation successful:", result);
} catch (error) {
    console.error("Operation failed:", error.message);
}
```

### Detailed Error Handling

```typescript
try {
    const result = await {apiInstanceName}.{methodName}({params});
    console.log("Operation successful:", result);
} catch (error) {
    if (error.statusCode === 401) {
        console.error("Authentication error. Please check your credentials.");
    } else if (error.statusCode === 403) {
        console.error("Permission denied. You don't have access to this resource.");
    } else if (error.statusCode === 404) {
        console.error("Resource not found. Please check the {resourceType} ID.");
    } else if (error.statusCode === 400) {
        console.error("Bad request. Please check your input parameters:", error.message);
    } else {
        console.error("Unexpected error:", error.message);
    }
}
```

### Handling Concurrent Updates

```typescript
async function update{ResourceType}WithRetry({resourceType}Id, updates, maxRetries = 3) {
    let retryCount = 0;
    
    while (retryCount < maxRetries) {
        try {
            // Get the latest version of the {resourceType}
            const {resourceType} = await {apiInstanceName}.get{ResourceType}({resourceType}Id);
            
            // Apply updates
            // ...
            
            // Save the updated {resourceType}
            const result = await {apiInstanceName}.update{ResourceType}({resourceType}Id, updates, {resourceType}.rev);
            
            console.log("{ResourceType} updated successfully:", result);
            return result;
        } catch (error) {
            if (error.statusCode === 409 && retryCount < maxRetries - 1) {
                // Concurrent update conflict, retry
                console.log(`Concurrent update detected, retrying (${retryCount + 1}/${maxRetries})...`);
                retryCount++;
            } else {
                // Other error or max retries reached
                console.error("Failed to update {resourceType}:", error.message);
                throw error;
            }
        }
    }
}
```

### Handling Rate Limiting

```typescript
async function executeWithRateLimitHandling(operation, maxRetries = 3) {
    let retryCount = 0;
    
    while (retryCount < maxRetries) {
        try {
            return await operation();
        } catch (error) {
            if (error.statusCode === 429 && retryCount < maxRetries - 1) {
                // Rate limited, wait and retry
                const retryAfterSeconds = error.headers?.["Retry-After"] || 5;
                console.log(`Rate limited, retrying after ${retryAfterSeconds} seconds...`);
                
                await new Promise(resolve => setTimeout(resolve, retryAfterSeconds * 1000));
                retryCount++;
            } else {
                // Other error or max retries reached
                console.error("Operation failed:", error.message);
                throw error;
            }
        }
    }
}

// Usage
const result = await executeWithRateLimitHandling(async () => {
    return await {apiInstanceName}.{methodName}({params});
});
```

### Validation Before API Calls

```typescript
function validate{ResourceType}Fields({resourceType}Fields) {
    const errors = [];
    
    // Validate required fields
    if (!{resourceType}Fields.{requiredField1}) {
        errors.push("{requiredField1} is required");
    }
    
    if (!{resourceType}Fields.{requiredField2}) {
        errors.push("{requiredField2} is required");
    }
    
    // Validate field formats
    if ({resourceType}Fields.{field1} && !{validationRegex}.test({resourceType}Fields.{field1})) {
        errors.push("{field1} has an invalid format");
    }
    
    return errors;
}

async function create{ResourceType}WithValidation(projectName, {resourceType}Type, {resourceType}Fields) {
    // Validate fields before API call
    const validationErrors = validate{ResourceType}Fields({resourceType}Fields);
    
    if (validationErrors.length > 0) {
        throw new Error(`Validation failed: ${validationErrors.join(", ")}`);
    }
    
    try {
        // Proceed with API call
        const {resourceType} = await {apiInstanceName}.create{ResourceType}(projectName, {resourceType}Type, {resourceType}Fields);
        console.log("{ResourceType} created successfully:", {resourceType});
        return {resourceType};
    } catch (error) {
        console.error("Failed to create {resourceType}:", error.message);
        throw error;
    }
}
```

## Debugging Tips

### Enable Verbose Logging

```typescript
// Enable verbose logging for debugging
const connection = new azdev.WebApi(orgUrl, authHandler, {
    allowRetries: true,
    maxRetries: 3,
    socketTimeout: 60000,
    userAgent: "my-app/1.0",
    logLevel: "verbose" // Set to "verbose" for detailed logging
});
```

### Inspect Request/Response Details

```typescript
try {
    const result = await {apiInstanceName}.{methodName}({params});
    console.log("Operation successful:", result);
} catch (error) {
    console.error("Error details:");
    console.error("- Status code:", error.statusCode);
    console.error("- Message:", error.message);
    console.error("- Request URL:", error.request?.url);
    console.error("- Request method:", error.request?.method);
    console.error("- Request headers:", error.request?.headers);
    console.error("- Request body:", error.request?.body);
    console.error("- Response headers:", error.response?.headers);
    console.error("- Response body:", error.response?.body);
}
```

## See Also

- [Examples](./examples.md)
- [Common Scenarios](./common-scenarios.md)
- [Methods Reference](./methods/README.md)

---

## Navigation

- [{ApiClassName} Overview](./README.md)
- [Constructor](./constructor.md)
- [Properties](./properties.md)
- [Methods](./methods/README.md)
- [Examples](./examples.md)
- [Common Scenarios](./common-scenarios.md) 