# Parameters Section

This document defines the standard format for documenting parameters in API methods, constructors, and functions.

## Format

The parameters section should always be structured as a table with the following columns:
1. **Parameter**: The name of the parameter
2. **Type**: The data type of the parameter
3. **Required**: Whether the parameter is required or optional
4. **Description**: A clear description of the parameter's purpose and usage

```markdown
## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| {param1} | {type1} | Yes | {description of param1} |
| {param2} | {type2} | No | {description of param2} |
| {param3} | {type3} | No | {description of param3} |
```

## Required Elements

- Parameter names must match the actual parameter names in the code
- Types must be accurate and include links to type definitions where applicable
- Required/Optional status must be clearly indicated
- Descriptions must explain the parameter's purpose and any constraints

## Optional Elements

- **Default values**: Include default values for optional parameters
- **Examples**: Include brief examples of valid values
- **Constraints**: Document min/max values, format requirements, etc.
- **Notes**: Include any additional important information

## Enhanced Format (With Optional Elements)

```markdown
## Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| {param1} | [{type1}](../types/{type1}.md) | Yes | N/A | {description of param1}. Example: `{example1}` |
| {param2} | [{type2}](../types/{type2}.md) | No | `{default2}` | {description of param2}. Must be between {min} and {max}. |
| {param3} | [{type3}](../types/{type3}.md) | No | `{default3}` | {description of param3}. **Note**: {important note}. |
```

## Special Parameter Types

### Object Parameters

For object parameters with multiple properties, use a nested table or list:

```markdown
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| options | [IRequestOptions](../types/IRequestOptions.md) | No | Options for the request. See properties below. |

#### options properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| timeout | number | No | Request timeout in milliseconds. Default: `30000`. |
| headers | object | No | Additional headers to include in the request. |
```

### Callback Parameters

For callback parameters, document the callback signature:

```markdown
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| callback | function | No | Callback function. If not provided, returns a Promise. |

#### callback signature

```typescript
(err: Error, result: Result) => void
``` 
```

## Examples

### Basic Example

```markdown
## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | number | Yes | The ID of the work item to retrieve. |
| project | string | No | The project name or ID. If not specified, uses the default project. |
| expand | WorkItemExpand | No | The work item expand options. Controls which properties are retrieved. |
```

### Complete Example

```markdown
## Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| id | [number](../types/number.md) | Yes | N/A | The ID of the work item to retrieve. Must be greater than 0. Example: `42` |
| project | [string](../types/string.md) | No | Default project | The project name or ID. Example: `"MyProject"` or `"a1b2c3d4-e5f6"` |
| expand | [WorkItemExpand](../types/WorkItemExpand.md) | No | `None` | The work item expand options. Controls which properties are retrieved. **Note**: Using `All` may impact performance for large work items. |

#### expand options

| Value | Description |
|-------|-------------|
| `None` | Default. Retrieves the work item with minimal properties. |
| `Relations` | Includes the work item's relations. |
| `Fields` | Includes all work item fields. |
| `All` | Includes all work item data (relations and fields). |
```

## Usage Guidelines

1. Always maintain the standard format for consistency
2. Order parameters as they appear in the method signature
3. Provide clear, concise descriptions
4. Link to relevant type definitions
5. Include examples for complex parameters
6. Document constraints and validation requirements
7. Note default values for optional parameters

## See Also

- [Return Type Section](./return-type-section.md)
- [Example Section](./example-section.md)
- [Error Handling Section](./error-handling-section.md) 