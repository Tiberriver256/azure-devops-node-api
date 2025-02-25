# API Method Template Guide

This guide explains how to use the API method template to document individual API methods in the Azure DevOps Node API.

## Purpose

The API method template provides a standardized structure for documenting individual API methods. It ensures that all method documentation includes consistent sections for:

- Method signature
- Parameters
- Return type
- Examples
- Error handling
- Related methods

## When to Use

Use this template when documenting individual API methods, especially:

- Public methods that are part of the API's interface
- Methods with complex parameters or return types
- Methods that require detailed examples or error handling information
- Methods that don't belong to a complex component requiring the split view approach

For methods that are part of a complex API component with 15+ methods, consider using the [API Reference Template](../api-reference-template/) instead.

## Template Structure

The API method template includes the following sections:

```
# Method Name

## Syntax

## Parameters

## Returns

## Example

## Return Type Structure

## Common Errors

## Related Methods

## See Also
```

## How to Fill Out the Template

### Method Name

Use the format `ClassName.methodName` for the heading, e.g., `WorkItemTrackingApi.getWorkItem`.

### Syntax

Include the method signature with types:

```typescript
methodName(
    param1: type1, 
    param2?: type2, 
    param3?: type3
): Promise<ReturnType>
```

### Parameters

Use the standardized [Parameters Section](../shared-components/parameters-section.md) format:

```markdown
## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| param1 | type1 | Yes | Description of param1 |
| param2 | type2 | No | Description of param2 |
| param3 | type3 | No | Description of param3 |
```

For detailed guidance, see the [Parameters Section](../shared-components/parameters-section.md) in the shared components.

### Returns

Describe the return value using the standardized [Return Type Section](../shared-components/return-type-section.md) format:

```markdown
## Returns

`Promise<ReturnType>`: Description of the return value
```

### Example

Provide a practical example using the standardized [Example Section](../shared-components/example-section.md) format:

```markdown
## Example

```typescript
// Example usage of the method
const result = await api.methodName(
    param1Value,              // param1
    param2Value,              // param2
    param3Value               // param3
);

console.log(`Output: ${result.property}`);
```
```

For complex examples, consider using collapsible sections:

```markdown
<details>
<summary>View complete example with error handling</summary>

```typescript
try {
    const result = await api.methodName(
        param1Value,                // param1
        param2Value,                // param2
        param3Value                 // param3
    );
    
    console.log(`Output: ${result.property}`);
    return result;
} catch (error) {
    console.error(`Error calling methodName: ${error.message}`);
    // Handle specific error types
    if (error.statusCode === 404) {
        console.error("Resource not found");
    }
    throw error;
}
```
</details>
```

### Return Type Structure

For complex return types, document the structure:

```markdown
## Return Type Structure

The returned `ReturnType` object has the following structure:

```typescript
interface ReturnType {
    property1: propertyType1;
    property2: propertyType2;
    property3?: propertyType3;
    property4?: propertyType4;
}
```
```

### Common Errors

Document common errors using the standardized [Error Handling Section](../shared-components/error-handling-section.md) format:

```markdown
## Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| 404 Not Found | Resource doesn't exist | Solution |
| 401 Unauthorized | Invalid authentication | Solution |
| 403 Forbidden | Insufficient permissions | Solution |
```

### Related Methods

List related methods:

```markdown
## Related Methods

- [relatedMethod1](./related-method1.md) - Brief description
- [relatedMethod2](./related-method2.md) - Brief description
- [relatedMethod3](./related-method3.md) - Brief description
```

### See Also

Include links to related resources:

```markdown
## See Also

- [relatedResource1](../related-resource1.md)
- [relatedResource2](../related-resource2.md)
- [Common Scenario: relatedScenario](../common-scenario.md#related-scenario)
```

## Navigation

Include consistent navigation links:

```markdown
[◀ Back to API Reference](../README.md)
```

## Formatting Guidelines

1. **Code Blocks**: Use triple backticks with language specifier (`typescript`, `json`, etc.)
2. **Links**: Use standard markdown links: `[text](url)`
3. **Lists**: Use - for unordered lists and 1. for ordered lists
4. **Tables**: Align table columns for readability
5. **Headings**: Use ## for major sections and ### for subsections

## Examples

See the [getWorkItems.md](./examples/getWorkItems.md) example for a complete implementation of this template.

## Navigation Patterns

Follow the standardized [Navigation Patterns](../shared-components/navigation-patterns.md) for consistency across all documentation.

## Common Sections

Several sections in this template use standardized formats from the shared components directory:

- [Parameters Section](../shared-components/parameters-section.md)
- [Return Type Section](../shared-components/return-type-section.md)
- [Example Section](../shared-components/example-section.md)
- [Error Handling Section](../shared-components/error-handling-section.md)
- [Navigation Patterns](../shared-components/navigation-patterns.md)

## Need Help?

If you have questions or need assistance with using this template, please contact the documentation team. 