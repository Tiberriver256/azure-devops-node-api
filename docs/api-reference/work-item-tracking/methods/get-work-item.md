# WorkItemTrackingApi.getWorkItem Method

[◀ Back to WorkItemTrackingApi](../README.md) | [◀ Back to Methods](./README.md)

---

## Syntax

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

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | number | Yes | The ID of the work item to retrieve |
| project | string | No | Project ID or name. If not specified, uses the default project |
| fields | string[] | No | Array of field names to include in the result |
| asOf | Date | No | Date to retrieve the work item as it existed at that point |
| expand | WorkItemExpand | No | Expansion options for the work item data |

## Returns

`Promise<WorkItem>`: A promise that resolves to the requested work item.

## Example

```typescript
// Get a work item with specific fields
const workItem = await workItemTrackingApi.getWorkItem(
    42,                                      // Work item ID
    "MyProject",                             // Project name
    ["System.Title", "System.Description"],  // Fields to include
    undefined,                               // Current version (no asOf date)
    WorkItemExpand.Relations                 // Include relations
);

console.log(`Title: ${workItem.fields["System.Title"]}`);
```

## WorkItem Object Structure

The returned `WorkItem` object has the following structure:

```typescript
interface WorkItem {
    id: number;
    rev: number;
    fields: { [key: string]: any };
    relations?: WorkItemRelation[];
    _links?: any;
    url: string;
}
```

## Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| 404 Not Found | Work item doesn't exist | Verify the work item ID and project |
| 401 Unauthorized | Invalid authentication | Check your authentication credentials |
| 403 Forbidden | Insufficient permissions | Ensure you have read access to the work item |

## Related Methods

- [getWorkItems](./get-work-items.md) - Get multiple work items in a single request
- [getWorkItemRevision](./get-work-item-revision.md) - Get a specific revision of a work item
- [getWorkItemRevisions](./get-work-item-revisions.md) - Get all revisions of a work item

## See Also

- [Work Item Fields Reference](../work-item-fields.md)
- [WorkItemExpand Enum](../enums/work-item-expand.md)
- [Common Scenarios: Retrieving Work Items](../common-scenarios.md#retrieving-work-items)

---

## Navigation

- [WorkItemTrackingApi Overview](../README.md)
- [Constructor](../constructor.md)
- [Properties](../properties.md)
- [Methods](./README.md)
- [Examples](../examples.md)
- [Common Scenarios](../common-scenarios.md)
- [Error Handling](../error-handling.md) 