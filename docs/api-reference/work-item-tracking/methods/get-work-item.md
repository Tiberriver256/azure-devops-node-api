# getWorkItem Method

**Navigation**: [Home](../../../index.md) > [API Reference](../../index.md) > [Work Item Tracking API](../README.md) > getWorkItem Method

## Overview

The `getWorkItem` method retrieves a single work item by its ID. This method provides granular control over which fields are returned and can retrieve work items as they were at a specific point in time.

## Signature

```typescript
getWorkItem(
  id: number,
  fields?: string[],
  asOf?: Date,
  expand?: WorkItemExpand,
  project?: string
): Promise<WorkItem>
```

## Parameters

| Name | Type | Description | Required |
|------|------|-------------|:--------:|
| `id` | `number` | The ID of the work item to retrieve | Yes |
| `fields` | `string[]` | Array of field names to include (if omitted, all fields are returned) | No |
| `asOf` | `Date` | Retrieves the work item as it was at this date/time | No |
| `expand` | `WorkItemExpand` | The expand options for work item attributes (None, Relations, Fields, Links, All) | No |
| `project` | `string` | Project ID or name containing the work item (recommended for performance reasons) | No |

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

### Specifying Fields

```typescript
// Only retrieve specific fields
const fields = [
  "System.Id",
  "System.Title", 
  "System.State", 
  "System.AssignedTo", 
  "System.CreatedDate"
];

const workItem = await witApi.getWorkItem(42, fields);
console.log(`Title: ${workItem.fields["System.Title"]}`);
console.log(`State: ${workItem.fields["System.State"]}`);
console.log(`Assigned To: ${workItem.fields["System.AssignedTo"]}`);
```

### Retrieving a Historical Version

```typescript
// Get the work item as it was on a specific date
const asOfDate = new Date("2023-01-15");
const workItem = await witApi.getWorkItem(42, undefined, asOfDate);
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

## See Also

- [getWorkItems Method](./get-work-items.md)
- [Work Item Tracking API Reference](../work-item-tracking-api.md)
- [Work Item Fields Reference](https://learn.microsoft.com/en-us/azure/devops/boards/work-items/guidance/work-item-field) 