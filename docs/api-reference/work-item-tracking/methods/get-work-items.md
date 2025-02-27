# getWorkItems Method

**Navigation**: [Home](../../../index.md) > [API Reference](../../index.md) > [Work Item Tracking API](../README.md) > getWorkItems Method

## Overview

The `getWorkItems` method retrieves multiple work items by their IDs in a single request. This method is significantly more efficient than making separate calls to retrieve individual work items, especially when retrieving many work items at once.

## Signature

```typescript
getWorkItems(
  ids: number[],
  fields?: string[],
  asOf?: Date,
  expand?: WorkItemExpand,
  errorPolicy?: WorkItemErrorPolicy,
  project?: string
): Promise<WorkItem[]>
```

## Parameters

| Name | Type | Description | Required |
|------|------|-------------|:--------:|
| `ids` | `number[]` | Array of work item IDs to retrieve (maximum 200) | Yes |
| `fields` | `string[]` | Array of field names to include (if omitted, all fields are returned) | No |
| `asOf` | `Date` | Retrieves the work items as they were at this date/time | No |
| `expand` | `WorkItemExpand` | The expand options for work item attributes (None, Relations, Fields, Links, All) | No |
| `errorPolicy` | `WorkItemErrorPolicy` | Controls how errors are handled (Fail, Omit) | No |
| `project` | `string` | Project ID or name containing the work items | No |

## Returns

`Promise<WorkItem[]>`: A promise that resolves to an array of work item objects.

Each `WorkItem` object includes:

- `id`: The work item ID
- `rev`: The revision number
- `fields`: Dictionary of field names and values
- `relations`: Array of related work items (if expand includes Relations)
- `_links`: Dictionary of related resources

## Example

### Basic Usage

```typescript
// Get multiple work items by their IDs
const workItems = await witApi.getWorkItems([42, 43, 44]);

workItems.forEach(workItem => {
  console.log(`ID: ${workItem.id}, Title: ${workItem.fields["System.Title"]}`);
});
```

### Specifying Fields

```typescript
// Only retrieve specific fields for multiple work items
const fields = [
  "System.Id",
  "System.Title", 
  "System.State", 
  "System.AssignedTo"
];

const workItems = await witApi.getWorkItems([42, 43, 44], fields);

workItems.forEach(workItem => {
  console.log(`ID: ${workItem.id}`);
  console.log(`Title: ${workItem.fields["System.Title"]}`);
  console.log(`State: ${workItem.fields["System.State"]}`);
  console.log(`Assigned To: ${workItem.fields["System.AssignedTo"]}`);
  console.log("---");
});
```

### Handling Missing Work Items

```typescript
// Use the error policy to omit work items that aren't found
// instead of failing the entire request
const workItems = await witApi.getWorkItems(
  [42, 999, 44], // Assume 999 doesn't exist
  undefined,
  undefined,
  undefined,
  1 // WorkItemErrorPolicy.Omit
);

console.log(`Retrieved ${workItems.length} work items`);
workItems.forEach(workItem => {
  console.log(`ID: ${workItem.id}, Title: ${workItem.fields["System.Title"]}`);
});
```

### Retrieving Historical Versions

```typescript
// Get the work items as they were on a specific date
const asOfDate = new Date("2023-01-15");
const workItems = await witApi.getWorkItems([42, 43, 44], undefined, asOfDate);

console.log(`Work items as of ${asOfDate.toDateString()}:`);
workItems.forEach(workItem => {
  console.log(`ID: ${workItem.id}, Title: ${workItem.fields["System.Title"]}`);
});
```

## Error Handling

By default, if any work item ID in the request cannot be found, the entire request will fail. You can use the `errorPolicy` parameter to change this behavior:

- `WorkItemErrorPolicy.Fail` (default): The entire request fails if any work item is not found
- `WorkItemErrorPolicy.Omit`: Work items that cannot be found are omitted from the result

## Limitations

- Maximum of 200 work item IDs per request
- For very large numbers of work items, consider using WIQL queries or multiple batched calls
- Retrieving a large number of work items with all fields can impact performance

## Performance Considerations

- Include only the fields you need in the `fields` parameter for better performance
- Use `getWorkItems` instead of multiple `getWorkItem` calls whenever possible
- Batch your requests to stay within the 200 item limit if you need more than 200 items
- Provide the `project` parameter when you know the project, as it can improve query performance
- Only include `expand` options when you need the additional data

## See Also

- [getWorkItem Method](./get-work-item.md)
- [queryByWiql Method](./query-work-items.md)
- [Work Item Tracking API Reference](../work-item-tracking-api.md) 