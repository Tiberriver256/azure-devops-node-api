# {ApiClassName} Constructor

[◀ Back to {ApiClassName}](./README.md)

---

## Syntax

```typescript
constructor(
    {param1}: {type1},
    {param2}?: {type2}
)
```

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| {param1} | {type1} | Yes | {description of param1} |
| {param2} | {type2} | No | {description of param2} |

## Description

The `{ApiClassName}` constructor initializes a new instance of the {ApiClassName} class. {Additional context about what the constructor does and when to use it}.

## Example

```typescript
// Example of creating a new instance
const {apiInstanceName} = new {ApiClassName}(
    {param1Value},
    {param2Value}
);
```

## Usage Notes

- {Note 1 about using the constructor}
- {Note 2 about using the constructor}
- {Note 3 about using the constructor}

## Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| {error1} | {cause1} | {solution1} |
| {error2} | {cause2} | {solution2} |

## Recommended Approach

The recommended way to get an instance of `{ApiClassName}` is through the WebApi class:

```typescript
import * as azdev from "azure-devops-node-api";

// Create a connection to Azure DevOps
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get the {ApiClassName} instance
const {apiInstanceName}: {ApiClassName} = await connection.get{ApiClassName}();
```

This approach ensures proper authentication and connection setup.

---

## Navigation

- [{ApiClassName} Overview](./README.md)
- [Properties](./properties.md)
- [Methods](./methods/README.md)
- [Examples](./examples.md)
- [Common Scenarios](./common-scenarios.md)
- [Error Handling](./error-handling.md) 