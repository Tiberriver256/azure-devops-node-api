# updateWorkItem Method

**Navigation**: [Home](../../../index.md) > [API Reference](../../index.md) > [Work Item Tracking API](../README.md) > updateWorkItem Method

## Overview

The `updateWorkItem` method modifies an existing work item in Azure DevOps. It uses JSON Patch documents to specify the changes to make to the work item, allowing for flexible updates to any field or property of the work item.

## Signature

```typescript
updateWorkItem(
  customHeaders: any,
  document: JsonPatchDocument,
  id: number,
  project?: string,
  validateOnly?: boolean,
  bypassRules?: boolean,
  suppressNotifications?: boolean,
  expand?: WorkItemExpand
): Promise<WorkItem>
```

## Parameters

| Name | Type | Description | Required |
|------|------|-------------|:--------:|
| `customHeaders` | `any` | Custom headers for the request (can be empty `{}`) | Yes |
| `document` | `JsonPatchDocument` | Array of JSON Patch operations defining the changes | Yes |
| `id` | `number` | The ID of the work item to update | Yes |
| `project` | `string` | The project containing the work item (recommended for performance) | No |
| `validateOnly` | `boolean` | If true, validates the update without applying it | No |
| `bypassRules` | `boolean` | If true, bypasses the work item type rules | No |
| `suppressNotifications` | `boolean` | If true, suppresses notifications for this update | No |
| `expand` | `WorkItemExpand` | The expand options for the returned work item | No |

## Returns

`Promise<WorkItem>`: A promise that resolves to the updated work item.

The updated `WorkItem` object includes:

- `id`: The work item ID
- `rev`: The new revision number (incremented after update)
- `fields`: Dictionary of field names and values (including updates)
- `relations`: Array of related work items (if expand includes Relations)
- `_links`: Dictionary of related resources

## JSON Patch Document

The JSON Patch document is an array of operations that define the changes to make. Each operation has:

- `op`: The operation type ("add", "replace", "remove", "test", "move", "copy")
- `path`: The path to the field (e.g., "/fields/System.Title")
- `value`: The new value for the field (required for "add", "replace", "test")
- `from`: The source path (required for "move", "copy")

Common operations:

- `add`: Add a value to a field that doesn't exist or update an existing field
- `replace`: Replace the value of an existing field
- `remove`: Remove a field or value

## Examples

### Basic Update

```typescript
// Update the state of a work item
const patchDocument = [
  {
    op: "replace",
    path: "/fields/System.State",
    value: "Active"
  }
];

// Update work item with ID 42
const updatedWorkItem = await witApi.updateWorkItem(
  {}, // Custom headers (can be empty)
  patchDocument,
  42,
  "MyProject"
);

console.log(`Updated work item #${updatedWorkItem.id} to state: ${updatedWorkItem.fields["System.State"]}`);
```

### Multiple Field Updates

```typescript
// Update multiple fields in a single request
const patchDocument = [
  {
    op: "replace",
    path: "/fields/System.Title",
    value: "Updated title for this bug"
  },
  {
    op: "replace",
    path: "/fields/System.AssignedTo",
    value: "user@example.com"
  },
  {
    op: "add",
    path: "/fields/System.Tags",
    value: "API; Updated; Critical"
  },
  {
    op: "replace",
    path: "/fields/Microsoft.VSTS.Common.Priority",
    value: 1
  }
];

const updatedWorkItem = await witApi.updateWorkItem(
  {}, 
  patchDocument,
  42
);

console.log(`Updated work item #${updatedWorkItem.id}`);
console.log(`New title: ${updatedWorkItem.fields["System.Title"]}`);
console.log(`Assigned to: ${updatedWorkItem.fields["System.AssignedTo"]}`);
```

### Validation Only

```typescript
// Validate an update without applying it
const patchDocument = [
  {
    op: "replace",
    path: "/fields/System.State",
    value: "Done"
  }
];

try {
  const validationResult = await witApi.updateWorkItem(
    {}, 
    patchDocument,
    42,
    "MyProject",
    true // validateOnly = true
  );
  
  console.log("Update is valid and can be applied");
} catch (error) {
  console.error("Update validation failed:", error.message);
}
```

### Conditional Update

```typescript
// Only update if current value matches expected value
const patchDocument = [
  // Test operation ensures the current title matches expected value
  {
    op: "test",
    path: "/fields/System.Title",
    value: "Expected current title"
  },
  // If test passes, replace the title
  {
    op: "replace",
    path: "/fields/System.Title",
    value: "New title after verification"
  }
];

try {
  const updatedWorkItem = await witApi.updateWorkItem(
    {}, 
    patchDocument,
    42
  );
  console.log("Work item updated successfully");
} catch (error) {
  console.error("Conditional update failed - current value did not match expected:", error.message);
}
```

## Common Errors

| Error | Description | Solution |
|-------|-------------|----------|
| 400 Bad Request | Invalid field values or operations | Check field names, values, and operation types |
| 404 Not Found | Work item doesn't exist | Verify the work item ID exists |
| 401 Unauthorized | Authentication token is invalid | Refresh your authentication token |
| 403 Forbidden | User doesn't have permission to update the work item | Request appropriate permissions |
| 412 Precondition Failed | "test" operation in patch document failed | The current value doesn't match the expected value in a test operation |

## Field Value Formats

Different fields require different value formats:

- **Text fields** (Title, Description): String values, some support HTML
- **Person fields** (AssignedTo): Email address or display name
- **Date fields**: ISO date strings (YYYY-MM-DD) or date-time strings
- **Numeric fields**: Numbers without quotes
- **Tags**: Semicolon-separated values as a single string

## Best Practices

1. **Field Validation**: Use `validateOnly` to check if your update is valid before applying it
2. **Concurrency**: Use "test" operations to ensure the field hasn't changed since you retrieved it
3. **Batching**: Group related field changes in a single update operation for better performance
4. **HTML Content**: For Description and other HTML fields, ensure proper HTML formatting
5. **Notifications**: Use `suppressNotifications` for bulk operations to avoid notification spam
6. **Permissions**: Ensure the authenticating user has permissions to update the fields you're changing
7. **Rules**: Be aware of work item rules that may prevent certain field combinations

## See Also

- [createWorkItem Method](./create-work-item.md)
- [getWorkItem Method](./get-work-item.md)
- [Work Item Tracking API Reference](../work-item-tracking-api.md)
- [JSON Patch Specification](http://jsonpatch.com/) 