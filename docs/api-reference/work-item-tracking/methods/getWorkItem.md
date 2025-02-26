**Navigation**: [Home](../../../index.md) > [API Reference](../../index.md) > [WorkItemTrackingApi](../README.md) > [Methods](./README.md) > getWorkItem

# getWorkItem Method

⭐ **Commonly Used**

Retrieves a single work item by its ID.

## Signature

```typescript
getWorkItem(id: number, project?: string, fields?: string[], asOf?: Date, expand?: WorkItemExpand): Promise<WorkItem>
```

## Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `id` | `number` | The ID of the work item to retrieve | Yes |
| `project` | `string` | The project containing the work item | No |
| `fields` | `string[]` | The fields to include in the work item | No |
| `asOf` | `Date` | The date to retrieve the work item as of | No |
| `expand` | `WorkItemExpand` | The expansion options for the work item | No |

### Parameter Details

#### id
The unique identifier of the work item to retrieve. This is a numeric ID assigned by Azure DevOps when the work item is created.

#### project
The name or ID of the project containing the work item. If not specified, the method will search across all accessible projects.

📝 **Note**: For best performance, it's recommended to specify the project when you know it.

#### fields
An optional array of field names to include in the response. If not specified, the default set of fields will be returned.

Common fields include:
- `System.Id`
- `System.Title`
- `System.Description`
- `System.State`
- `System.AssignedTo`
- `System.CreatedBy`
- `System.CreatedDate`
- `System.ChangedDate`

#### asOf
An optional Date object that specifies a historical version of the work item to retrieve. This allows you to get a work item as it existed at a specific point in time.

#### expand
An optional enum value to expand additional properties in the response:

```typescript
enum WorkItemExpand {
  None = 0,
  Relations = 1,
  Fields = 2,
  Links = 3,
  All = 4
}
```

## Returns

`Promise<WorkItem>`: A promise that resolves to the requested work item.

The `WorkItem` object contains:
- `id`: The work item ID
- `rev`: The revision number
- `fields`: A dictionary of field values
- `relations`: Work item relations (if expanded)
- `_links`: Related links
- `url`: The URL to the work item

## Example

```typescript
// Get a work item with specific fields
const workItemTrackingApi = await connection.getWorkItemTrackingApi();
const workItem = await workItemTrackingApi.getWorkItem(
  42, 
  "MyProject", 
  ["System.Title", "System.Description", "System.State"]
);

console.log(workItem.id, workItem.fields["System.Title"]);
// Output: 42 "Fix login page bug"

// Get a work item with all fields and relations expanded
const detailedWorkItem = await workItemTrackingApi.getWorkItem(
  42,
  "MyProject",
  undefined,
  undefined,
  WorkItemExpand.All
);

// Check if work item has parent relations
const parentRelations = detailedWorkItem.relations?.filter(
  rel => rel.rel === "System.LinkTypes.Hierarchy-Reverse"
);

if (parentRelations && parentRelations.length > 0) {
  console.log("This work item has parent items");
}
```

## Error Handling

This method can throw the following errors:

| Status Code | Reason | Handling Strategy |
|-------------|--------|-------------------|
| 401 | Unauthorized: Authentication failed | Check credentials and permissions |
| 403 | Forbidden: Insufficient permissions | Verify user has read access to the work item |
| 404 | Not Found: Work item does not exist | Verify the work item ID is correct and the item exists |

Example with error handling:

```typescript
try {
  const workItem = await workItemTrackingApi.getWorkItem(42);
  console.log(workItem.fields["System.Title"]);
} catch (error) {
  if (error.statusCode === 404) {
    console.error("Work item not found. It may have been deleted.");
  } else if (error.statusCode === 401 || error.statusCode === 403) {
    console.error("Permission error: You don't have access to this work item.");
  } else {
    console.error("Error retrieving work item:", error.message);
  }
}
```

## Related Methods

- ⭐ [getWorkItems](./getWorkItems.md) - Retrieve multiple work items at once
- 🔍 [queryByWiql](./queryByWiql.md) - Query for work items using WIQL
- 🔄 [updateWorkItem](./updateWorkItem.md) - Update a work item

## See Also

- [WorkItem Interface](../interfaces/WorkItem.md)
- [WorkItemExpand Enum](../interfaces/WorkItemExpand.md)
- [Common Scenarios: Working with Work Items](../common-scenarios.md#working-with-work-items)