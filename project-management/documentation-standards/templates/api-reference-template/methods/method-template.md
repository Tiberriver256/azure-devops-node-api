# {ApiClassName}.{methodName} Method

[◀ Back to {ApiClassName}](../README.md) | [◀ Back to Methods](./README.md)

---

## Table of Contents
- [Syntax](#syntax)
- [Parameters](#parameters)
- [Returns](#returns)
- [Example](#example)
- [Return Type Structure](#return-type-structure)
- [Common Errors](#common-errors)
- [Related Methods](#related-methods)
- [See Also](#see-also)

## Syntax

```typescript
{methodName}(
    {param1}: {type1}, 
    {param2}?: {type2}, 
    {param3}?: {type3}
): Promise<{returnType}>
```

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| {param1} | {type1} | Yes | {description of param1} |
| {param2} | {type2} | No | {description of param2} |
| {param3} | {type3} | No | {description of param3} |

## Returns

`Promise<{returnType}>`: {description of the return value}

## Example

```typescript
// Example usage of the method
const {resultVariable} = await {apiInstanceName}.{methodName}(
    {param1Value},                // {param1}
    {param2Value},                // {param2}
    {param3Value}                 // {param3}
);

console.log(`{ExampleOutput}: ${resultVariable.{propertyName}}`);
```

<details>
<summary>View complete example with error handling</summary>

```typescript
try {
    const {resultVariable} = await {apiInstanceName}.{methodName}(
        {param1Value},                // {param1}
        {param2Value},                // {param2}
        {param3Value}                 // {param3}
    );
    
    console.log(`{ExampleOutput}: ${resultVariable.{propertyName}}`);
    return {resultVariable};
} catch (error) {
    console.error(`Error calling {methodName}: ${error.message}`);
    // Handle specific error types
    if (error.statusCode === 404) {
        console.error("Resource not found");
    } else if (error.statusCode === 401) {
        console.error("Authentication error");
    }
    throw error;
}
```
</details>

## {ReturnType} Object Structure

The returned `{returnType}` object has the following structure:

```typescript
interface {returnType} {
    {property1}: {propertyType1};
    {property2}: {propertyType2};
    {property3}?: {propertyType3};
    {property4}?: {propertyType4};
}
```

## Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| 404 Not Found | {resource} doesn't exist | {solution} |
| 401 Unauthorized | Invalid authentication | {solution} |
| 403 Forbidden | Insufficient permissions | {solution} |
| 400 Bad Request | Invalid request format | {solution} |

## Related Methods

- [{relatedMethod1}](./{related-method1-filename}.md) - {brief description}
- [{relatedMethod2}](./{related-method2-filename}.md) - {brief description}
- [{relatedMethod3}](./{related-method3-filename}.md) - {brief description}

## See Also

- [{relatedResource1}](../{related-resource1-path}.md)
- [{relatedResource2}](../{related-resource2-path}.md)
- [Common Scenarios: {relatedScenario}](../common-scenarios.md#{related-scenario-anchor})

---

## Navigation

- [{ApiClassName} Overview](../README.md)
- [Constructor](../constructor.md)
- [Properties](../properties.md)
- [Methods](./README.md)
- [Examples](../examples.md)
- [Common Scenarios](../common-scenarios.md)
- [Error Handling](../error-handling.md) 