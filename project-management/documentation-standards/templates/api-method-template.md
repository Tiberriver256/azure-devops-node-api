# API Method Documentation Template

## Overview

This template should be used for documenting all public methods in the Azure DevOps Node API. Replace the placeholder text with actual content for the specific method being documented.

---

# `methodName`

**Signature:**
```typescript
public async methodName(param1: Type1, param2?: Type2, options?: OptionsType): Promise<ReturnType>
```

## Description

[Provide a clear, concise description of what the method does. Explain its purpose and when it should be used. Use present tense and active voice. 1-3 sentences for simple methods, more for complex ones.]

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `param1` | `Type1` | Yes | [Describe the parameter, its purpose, and any constraints or accepted values.] |
| `param2` | `Type2` | No | [Describe the parameter, including default value if applicable.] Default: `defaultValue`. |
| `options` | `OptionsType` | No | [Describe the options object and its purpose.] See [Options](#options) below. |

### Options

[If the method accepts an options object, document each property of the options object in a table similar to the parameters table above.]

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `property1` | `PropertyType1` | Yes | [Describe the property and its purpose.] |
| `property2` | `PropertyType2` | No | [Describe the property, including default value if applicable.] Default: `defaultValue`. |

## Return Value

**Type:** `Promise<ReturnType>`

[Describe what the method returns. Include information about the structure of the return value, any important properties, and what different return values might indicate.]

### Return Object Properties

[If the method returns a complex object, document its key properties in a table.]

| Property | Type | Description |
|----------|------|-------------|
| `property1` | `PropertyType1` | [Describe the property and its meaning.] |
| `property2` | `PropertyType2` | [Describe the property and its meaning.] |

## Exceptions

[Document all exceptions that the method might throw.]

| Exception | Condition |
|-----------|-----------|
| `UnauthorizedError` | Thrown when the user doesn't have permission to perform the operation. |
| `ResourceNotFoundError` | Thrown when the requested resource doesn't exist. |
| `ValidationError` | Thrown when the provided parameters don't meet validation requirements. |
| `ServiceError` | Thrown when the service returns an unexpected error. |

## Examples

### Basic Usage

```typescript
// Import required modules
import * as azdev from "azure-devops-node-api";

// Set up authentication and connection
const orgUrl = "https://dev.azure.com/yourorganization";
const token = "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Use the method (specific client)
const client = await connection.getClientName();
const result = await client.methodName(param1Value);
console.log(result);
```

### With Optional Parameters

```typescript
// Example showing usage with optional parameters
const result = await client.methodName(
    param1Value,
    param2Value,
    {
        property1: value1,
        property2: value2
    }
);
```

### Error Handling

```typescript
try {
    const result = await client.methodName(param1Value);
    // Process successful result
    console.log(`Operation succeeded: ${result.property1}`);
} catch (error) {
    // Handle specific error types
    if (error.statusCode === 401) {
        console.error("Authentication failed. Check your token and permissions.");
    } else if (error.statusCode === 404) {
        console.error(`Resource not found: ${param1Value}`);
    } else {
        console.error(`Operation failed: ${error.message}`);
    }
}
```

### Advanced Scenario

[Provide an example of a more complex or real-world scenario using this method.]

```typescript
// Example of a more complex scenario
```

## Response Example

```json
{
    "property1": "value1",
    "property2": 123,
    "nestedProperty": {
        "subProperty1": "value",
        "subProperty2": true
    }
}
```

## Related Methods

[List related methods with brief descriptions of how they relate to this method.]

- [`relatedMethod1`](link-to-related-method1) - [Brief description of relationship]
- [`relatedMethod2`](link-to-related-method2) - [Brief description of relationship]

## See Also

[List related documentation, conceptual guides, or tutorials that might be helpful.]

- [Conceptual Guide: relevant concept](link-to-conceptual-guide)
- [Tutorial: related task](link-to-tutorial)
- [API Reference: related client](link-to-client-docs)

## API Details

**Client:** `ClientName`  
**API Version:** [Version where this method was introduced or significantly changed]  
**Permission Scope:** [Required permission scope to use this method]

---

## Template Usage Notes

1. Replace all placeholder text with actual content specific to the method being documented.
2. Remove any sections that are not applicable (e.g., if there are no options, remove the Options section).
3. Ensure all code examples are complete, correct, and follow the style guide standards.
4. Verify all links to related documentation are working.
5. For very simple methods, sections can be combined or simplified, but maintain comprehensive documentation.
6. For very complex methods, additional sections or examples may be added as needed.
7. Always include error handling examples for methods that might throw exceptions.
8. Remove these template usage notes before publishing. 