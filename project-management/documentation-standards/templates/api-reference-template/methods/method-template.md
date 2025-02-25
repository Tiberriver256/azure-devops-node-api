# {ApiClassName}.{methodName} Method

[◀ Back to {ApiClassName}](../README.md) | [◀ Back to Methods](./README.md)

---

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