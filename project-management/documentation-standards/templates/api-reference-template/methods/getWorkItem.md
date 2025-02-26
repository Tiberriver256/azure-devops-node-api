**Navigation**: [Home](../../../index.md) > [API Reference](../../index.md) > [WorkItemTrackingApi](../index.md) > getWorkItem

# ⭐ getWorkItem

Retrieves a single work item by ID.

## Contents

- [Signature](#signature)
- [Parameters](#parameters)
- [Return Value](#return-value)
- [Examples](#examples)
- [Error Handling](#error-handling)
- [Related Methods](#related-methods)

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

| Name | Type | Required | Description |
|------|------|----------|-------------|
| id | number | Yes | The ID of the work item to retrieve |
| project | string | No | Project ID or project name |
| fields | string[] | No | The fields to return in the work item |
| asOf | Date | No | Date to retrieve the work item as it existed at this point |
| expand | WorkItemExpand | No | The expand options for additional details |

### Parameter Details

#### fields

If not specified, all fields will be returned. You can specify a subset of fields to reduce the amount of data returned.

Common fields include:
- `System.Id`
- `System.Title`
- `System.State`
- `System.AssignedTo`
- `System.CreatedDate`
- `System.ChangedDate`

#### expand

The `WorkItemExpand` enum can have the following values:
- `None`: Default behavior
- `Relations`: Include related work items
- `Fields`: Include all fields
- `Links`: Include external links
- `All`: Include all expansion options

---

## Return Value

Returns a Promise that resolves to a `WorkItem` object with the following structure:

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

---

## Examples

🔍 **Basic Usage**:

```typescript
const workItemTrackingApi = await connection.getWorkItemTrackingApi();
const workItem = await workItemTrackingApi.getWorkItem(1);
console.log(workItem.fields["System.Title"]);
```

🔍 **Retrieving Specific Fields**:

```typescript
const workItemTrackingApi = await connection.getWorkItemTrackingApi();
const workItem = await workItemTrackingApi.getWorkItem(
  1,
  "MyProject",
  ["System.Title", "System.State", "System.AssignedTo"]
);
console.log(workItem.fields["System.Title"]);
console.log(workItem.fields["System.State"]);
```

🔍 **Retrieving a Work Item as of a Specific Date**:

```typescript
const workItemTrackingApi = await connection.getWorkItemTrackingApi();
const asOfDate = new Date("2023-01-01");
const workItem = await workItemTrackingApi.getWorkItem(1, undefined, undefined, asOfDate);
console.log(workItem.fields["System.Title"]);
```

---

## Error Handling

⚠️ **Common Errors**:

The method may throw the following errors:

```typescript
try {
  const workItem = await workItemTrackingApi.getWorkItem(1);
  // Process work item
} catch (error) {
  if (error.statusCode === 401) {
    // Handle authentication error
    console.error("Authentication failed. Check your credentials.");
  } else if (error.statusCode === 404) {
    // Handle not found error
    console.error("Work item not found. Verify the ID exists.");
  } else if (error.statusCode === 403) {
    // Handle permission error
    console.error("You don't have permission to access this work item.");
  } else {
    // Handle unexpected errors
    console.error(`Unexpected error: ${error.message}`);
  }
}
```

---

## Related Methods

- [getWorkItems](./getWorkItems.md): Retrieve multiple work items
- [createWorkItem](./createWorkItem.md): Create a new work item
- [updateWorkItem](./updateWorkItem.md): Update an existing work item
- [deleteWorkItem](./deleteWorkItem.md): Delete a work item

## See Also

- [Work Item Tracking Concepts](../../concepts/work-item-tracking.md)
- [Creating Work Items Tutorial](../../tutorials/creating-work-items.md)
- [Work Item Fields Reference](../../reference/work-item-fields.md) 