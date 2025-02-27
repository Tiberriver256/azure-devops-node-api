# createWorkItem Method

**Navigation**: [Home](../../../index.md) > [API Reference](../../index.md) > [Work Item Tracking API](../README.md) > createWorkItem Method

## Overview

The `createWorkItem` method creates a new work item in Azure DevOps. This method uses JSON Patch documents to specify the fields and values for the new work item, allowing for flexible creation of different work item types with various fields set during creation.

## Signature

```typescript
createWorkItem(
  customHeaders: any,
  document: JsonPatchDocument,
  project: string,
  type: string,
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
| `document` | `JsonPatchDocument` | Array of JSON Patch operations defining the work item fields | Yes |
| `project` | `string` | The project in which to create the work item | Yes |
| `type` | `string` | The work item type (e.g., "Bug", "Task", "User Story") | Yes |
| `validateOnly` | `boolean` | If true, validates the work item without creating it | No |
| `bypassRules` | `boolean` | If true, bypasses the work item type rules | No |
| `suppressNotifications` | `boolean` | If true, suppresses notifications for this creation | No |
| `expand` | `WorkItemExpand` | The expand options for the created work item | No |

## Returns

`Promise<WorkItem>`: A promise that resolves to the newly created work item.

The created `WorkItem` object includes:

- `id`: The new work item ID
- `rev`: The revision number (always 1 for new items)
- `fields`: Dictionary of field names and values
- `relations`: Array of related work items (if expand includes Relations)
- `_links`: Dictionary of related resources

## JSON Patch Document

The JSON Patch document is an array of operations that define the fields to set on the new work item. Each operation has:

- `op`: The operation type (usually "add" for creation)
- `path`: The path to the field (e.g., "/fields/System.Title")
- `value`: The value to set for the field

Common fields to set include:

- `System.Title`: Title of the work item (required)
- `System.Description`: Description or details
- `System.AssignedTo`: Person assigned to the work item
- `System.Tags`: Tags for the work item
- `System.AreaPath`: Area classification
- `System.IterationPath`: Iteration/sprint assignment

## Examples

### Basic Creation

```typescript
// Create a document with fields to set
const patchDocument = [
  {
    op: "add",
    path: "/fields/System.Title",
    value: "New bug from API"
  },
  {
    op: "add",
    path: "/fields/System.Description",
    value: "This bug was created via the API"
  }
];

// Create a new Bug work item
const newWorkItem = await witApi.createWorkItem(
  {}, // Custom headers (can be empty)
  patchDocument,
  "MyProject",
  "Bug"
);

console.log(`Created Bug #${newWorkItem.id}`);
```

### Creating with Multiple Fields

```typescript
// Create a Task with multiple fields set
const patchDocument = [
  {
    op: "add",
    path: "/fields/System.Title",
    value: "Implement feature X"
  },
  {
    op: "add",
    path: "/fields/System.Description",
    value: "<div>Steps to implement feature X:</div><ul><li>Step 1</li><li>Step 2</li></ul>"
  },
  {
    op: "add",
    path: "/fields/System.AssignedTo",
    value: "user@example.com"
  },
  {
    op: "add",
    path: "/fields/System.Tags",
    value: "API; Feature; Backend"
  },
  {
    op: "add",
    path: "/fields/Microsoft.VSTS.Scheduling.StoryPoints",
    value: 5
  }
];

const newTask = await witApi.createWorkItem(
  {}, 
  patchDocument,
  "MyProject",
  "Task"
);

console.log(`Created Task #${newTask.id}`);
```

### Validation Only

```typescript
// Validate a work item without creating it
const patchDocument = [
  {
    op: "add",
    path: "/fields/System.Title",
    value: "Title to validate"
  }
];

try {
  const validationResult = await witApi.createWorkItem(
    {}, 
    patchDocument,
    "MyProject",
    "Bug",
    true // validateOnly = true
  );
  
  console.log("Work item is valid and can be created");
} catch (error) {
  console.error("Work item validation failed:", error.message);
}
```

## Common Errors

| Error | Description | Solution |
|-------|-------------|----------|
| 400 Bad Request | Invalid field values or required fields missing | Check field names and values, ensure required fields are included |
| 404 Not Found | Project or work item type not found | Verify project name and work item type exist |
| 401 Unauthorized | Authentication token is invalid | Refresh your authentication token |
| 403 Forbidden | User doesn't have permission to create work items | Request appropriate permissions for the project |

## Required Fields

Different work item types have different required fields. At minimum, most work item types require:

- `System.Title`: The title of the work item

Other fields may be required based on the work item type and process template being used. When in doubt, use the `validateOnly` parameter to check if your document is valid without creating the work item.

## Best Practices

1. **Required Fields**: Always include all required fields for the work item type
2. **Field Validation**: Use `validateOnly` first if you're unsure about field requirements
3. **Permissions**: Ensure the authenticating user has permissions to create work items in the project
4. **HTML Content**: Some fields like Description support HTML content for rich formatting
5. **Notifications**: Use `suppressNotifications` for bulk operations to avoid notification spam

## See Also

- [updateWorkItem Method](./update-work-item.md)
- [getWorkItem Method](./get-work-item.md)
- [Work Item Tracking API Reference](../work-item-tracking-api.md)
- [Work Item Field Reference](https://learn.microsoft.com/en-us/azure/devops/boards/work-items/guidance/work-item-field) 