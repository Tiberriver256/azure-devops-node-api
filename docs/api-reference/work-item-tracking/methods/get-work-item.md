# getWorkItem Method

**Navigation**: [Home](../../../index.md) > [API Reference](../../index.md) > [Work Item Tracking API](../README.md) > getWorkItem Method

## Overview

The `getWorkItem` method retrieves a single work item by its ID. This method provides granular control over which fields are returned and can retrieve work items as they were at a specific point in time.

## Signature

```typescript
getWorkItem(
  id: number,
  project?: string,
  fields?: string[],
  asOf?: Date,
  expand?: WorkItemExpand
): Promise<WorkItem>
```

## Parameters

| Name | Type | Description | Required |
|------|------|-------------|:--------:|
| `id` | `number` | The ID of the work item to retrieve | Yes |
| `project` | `string` | Project ID or name containing the work item (recommended for performance reasons) | No |
| `fields` | `string[]` | Array of field names to include (if omitted, all fields are returned) | No |
| `asOf` | `Date` | Retrieves the work item as it was at this date/time | No |
| `expand` | `WorkItemExpand` | The expand options for work item attributes (None, Relations, Fields, Links, All) | No |

## Returns

`Promise<WorkItem>`: A promise that resolves to the retrieved work item.

The `WorkItem` object includes:

- `id`: The work item ID
- `rev`: The revision number
- `fields`: Dictionary of field names and values
- `relations`: Array of related work items (if expand includes Relations)
- `_links`: Dictionary of related resources

## Example

### Basic Usage

```typescript
// Get a work item by ID
const workItem = await witApi.getWorkItem(42);
console.log(`Work Item #${workItem.id}: ${workItem.fields["System.Title"]}`);
```

### With Error Handling

```typescript
try {
  // Get a work item by ID
  const workItem = await witApi.getWorkItem(42);
  console.log(`Work Item #${workItem.id}: ${workItem.fields["System.Title"]}`);
  return {
    success: true,
    workItem
  };
} catch (error) {
  console.error(`Error retrieving work item: ${error.message}`);
  return {
    success: false,
    error: error.message
  };
}
```

### Specifying Fields

```typescript
// Only retrieve specific fields
const fields = [
  "System.Id",
  "System.Title", 
  "System.State", 
  "System.AssignedTo", 
  "Microsoft.VSTS.Common.Priority"
];

const workItem = await witApi.getWorkItem(42, undefined, fields);
console.log(`Title: ${workItem.fields["System.Title"]}`);
console.log(`State: ${workItem.fields["System.State"]}`);
```

### Important Note on Field Names

**Always use fully qualified field names** when specifying fields. Field names in Azure DevOps consist of a namespace and a name, separated by a period. For example:

- `System.Title` (not just `Title`)
- `System.AssignedTo` (not just `AssignedTo`)
- `Microsoft.VSTS.Common.Priority` (not just `Priority`)

Using only the short name (e.g., `Title` instead of `System.Title`) will result in the field not being returned or found in the work item fields collection.

### Retrieving a Historical Version

```typescript
// Get the work item as it was on a specific date
const asOfDate = new Date("2023-01-15");
const workItem = await witApi.getWorkItem(42, undefined, undefined, asOfDate);
console.log(`Work Item #${workItem.id} as of ${asOfDate.toDateString()}`);
console.log(`Title: ${workItem.fields["System.Title"]}`);
```

### Including Relations

```typescript
// Get work item with its relations
const workItem = await witApi.getWorkItem(
  42, 
  undefined, 
  undefined, 
  undefined, 
  1 // WorkItemExpand.Relations
);

// List related work items
if (workItem.relations) {
  console.log("Related Work Items:");
  workItem.relations.forEach(relation => {
    console.log(`- ${relation.rel}: ${relation.url}`);
  });
}
```

## Common Errors

| Error | Description | Solution |
|-------|-------------|----------|
| 404 Not Found | Work item with specified ID does not exist | Verify the work item ID |
| 401 Unauthorized | Authentication token is invalid or expired | Refresh your authentication token |
| 403 Forbidden | User doesn't have permission to view the work item | Request access to the project or work item |

## Performance Considerations

- Include only the fields you need in the `fields` parameter for better performance
- Provide the `project` parameter when you know the project, as it can improve query performance
- Only include `expand` options when you need the additional data
- For multiple work items, consider using `getWorkItems` instead of multiple `getWorkItem` calls

## Handling Non-Existent Fields

When requesting specific fields with the `fields` parameter, be aware that:

- Non-existent fields are silently ignored without generating an error
- Field names that don't match any existing field in the work item will not appear in the returned `fields` collection
- This behavior can lead to confusion if a field is missing from the result and you're not sure if it's because the field doesn't exist or because it has no value

For example, if you request a non-existent field like "System.NonExistentField", it will simply be omitted from the response without any error:

```typescript
// Request both valid and non-existent fields
const fields = [
  "System.Title",
  "System.NonExistentField",  // This field doesn't exist
  "System.State"
];

const workItem = await witApi.getWorkItem(42, undefined, fields);

// Only existing fields will be in the result
console.log(Object.keys(workItem.fields));  // ["System.Title", "System.State"]
```

To verify if a field exists before using it, you can check the work item type definition using the `getWorkItemType` method.

## See Also

- [getWorkItems Method](./get-work-items.md)
- [Work Item Tracking API Reference](../work-item-tracking-api.md)
- [Work Item Fields Reference](https://learn.microsoft.com/en-us/azure/devops/boards/work-items/guidance/work-item-field) 