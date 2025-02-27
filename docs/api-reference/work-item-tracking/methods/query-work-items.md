# queryByWiql Method

**Navigation**: [Home](../../../index.md) > [API Reference](../../index.md) > [Work Item Tracking API](../README.md) > queryByWiql Method

## Overview

The `queryByWiql` method executes a Work Item Query Language (WIQL) query to search for and retrieve work items based on specific criteria. WIQL is a SQL-like query language that allows you to filter, sort, and select work items using a wide range of criteria.

## Signature

```typescript
queryByWiql(
  wiql: Wiql,
  project?: string,
  team?: string,
  timePrecision?: boolean,
  top?: number
): Promise<WorkItemQueryResult>
```

## Parameters

| Name | Type | Description | Required |
|------|------|-------------|:--------:|
| `wiql` | `Wiql` | The WIQL query object with a query property containing the WIQL statement | Yes |
| `project` | `string` | Project ID or name for project-scoped queries | No |
| `team` | `string` | Team ID or name for team-scoped queries | No |
| `timePrecision` | `boolean` | Whether to include time precision in date fields | No |
| `top` | `number` | The maximum number of results to return | No |

## Wiql Object

The `Wiql` parameter is an object with a `query` property that contains the WIQL query string:

```typescript
interface Wiql {
  query: string;
}
```

## Returns

`Promise<WorkItemQueryResult>`: A promise that resolves to the query results.

The `WorkItemQueryResult` object includes:

- `asOf`: Timestamp of when the query was executed
- `columns`: Array of column references for the returned work items
- `queryResultType`: Type of query results (workItems, workItemLinks, or none)
- `queryType`: Type of query executed (flat, oneHop, or tree)
- `workItems`: Array of work item references (in flat query results)
- `workItemRelations`: Array of work item link relations (in link query results)

## WIQL Query Language

WIQL uses a SQL-like syntax with these key components:

- **SELECT**: Specifies which fields to return
- **FROM**: Specifies the work item entity type (WorkItems or WorkItemLinks)
- **WHERE**: Filter conditions to apply
- **ORDER BY**: Field(s) to sort results by

## Examples

### Basic Flat Query

```typescript
// Create a WIQL query to find all active bugs
const wiql = {
  query: `SELECT [System.Id], [System.Title], [System.State]
          FROM WorkItems
          WHERE [System.WorkItemType] = 'Bug'
          AND [System.State] = 'Active'
          ORDER BY [System.CreatedDate] DESC`
};

// Execute the query
const queryResult = await witApi.queryByWiql(wiql, "MyProject");

// The query only returns work item references
console.log(`Found ${queryResult.workItems.length} work items`);

// To get the actual work items, fetch them using the IDs from the query result
if (queryResult.workItems && queryResult.workItems.length > 0) {
  const ids = queryResult.workItems.map(wi => wi.id);
  const workItems = await witApi.getWorkItems(ids);
  
  workItems.forEach(workItem => {
    console.log(`ID: ${workItem.id}, Title: ${workItem.fields["System.Title"]}`);
  });
}
```

### Query with Complex Filters

```typescript
// Find bugs assigned to me that were created in the last 30 days
const thirtyDaysAgo = new Date();
thirtyDaysAgo.setDate(thirtyDaysAgo.getDate() - 30);
const dateString = thirtyDaysAgo.toISOString().split('T')[0]; // YYYY-MM-DD

const wiql = {
  query: `SELECT [System.Id], [System.Title], [System.State], [System.CreatedDate]
          FROM WorkItems
          WHERE [System.WorkItemType] = 'Bug'
          AND [System.AssignedTo] = @Me
          AND [System.CreatedDate] >= '${dateString}'
          ORDER BY [System.CreatedDate] DESC`
};

const queryResult = await witApi.queryByWiql(wiql, "MyProject");
console.log(`Found ${queryResult.workItems.length} work items created since ${dateString}`);
```

### Link Query

```typescript
// Find all child user stories of a specific feature
const wiql = {
  query: `SELECT [System.Id], [System.Title], [System.State]
          FROM WorkItemLinks
          WHERE [Source].[System.WorkItemType] = 'Feature'
          AND [Source].[System.Id] = 123
          AND [Target].[System.WorkItemType] = 'User Story'
          AND [System.Links.LinkType] = 'Child'
          ORDER BY [Target].[Microsoft.VSTS.Common.Priority] ASC`
};

const queryResult = await witApi.queryByWiql(wiql, "MyProject");

// Link queries return work item relations
if (queryResult.workItemRelations) {
  console.log(`Found ${queryResult.workItemRelations.length} related work items`);
  
  // Extract the target work item IDs (ignoring the source/parent)
  const targetIds = queryResult.workItemRelations
    .filter(relation => relation.target && relation.rel === "Child")
    .map(relation => relation.target.id);
  
  // Get the actual work items
  if (targetIds.length > 0) {
    const children = await witApi.getWorkItems(targetIds);
    children.forEach(child => {
      console.log(`Child User Story: ${child.id} - ${child.fields["System.Title"]}`);
    });
  }
}
```

### Limiting Results

```typescript
// Get only the top 5 most recently updated bugs
const wiql = {
  query: `SELECT [System.Id], [System.Title], [System.ChangedDate]
          FROM WorkItems
          WHERE [System.WorkItemType] = 'Bug'
          ORDER BY [System.ChangedDate] DESC`
};

// Use the top parameter to limit results
const queryResult = await witApi.queryByWiql(wiql, "MyProject", undefined, undefined, 5);
console.log(`Found ${queryResult.workItems.length} work items (limited to top 5)`);
```

## Common WIQL Clauses

### Fields

```
[System.Id]              - Work item ID
[System.Title]           - Work item title
[System.State]           - Work item state (Active, Resolved, etc.)
[System.WorkItemType]    - Type of work item (Bug, User Story, etc.)
[System.Tags]            - Work item tags
[System.AssignedTo]      - Person assigned to the work item
[System.CreatedDate]     - Date the work item was created
[System.ChangedDate]     - Date the work item was last changed
[Microsoft.VSTS.Common.Priority] - Priority value
```

### Operators

```
=, <>, >, >=, <, <=      - Comparison operators
IN                       - Membership operator (IN ('Value1', 'Value2'))
CONTAINS, CONTAINS WORDS - Text search operators
EVER                     - Historical value operator
@Me                      - Current user macro
@Today                   - Current date macro
```

### Date Functions

```
@Today                   - Current date
@Today-1                 - Yesterday
@Today+1                 - Tomorrow
@StartOfMonth            - First day of the current month
@StartOfWeek             - First day of the current week
```

## Common Errors

| Error | Description | Solution |
|-------|-------------|----------|
| 400 Bad Request | Invalid WIQL syntax | Check the WIQL syntax for errors |
| 404 Not Found | Project or team doesn't exist | Verify the project and team names |
| 401 Unauthorized | Authentication token is invalid | Refresh your authentication token |
| 403 Forbidden | User doesn't have permission to query | Request appropriate permissions |

## Performance Considerations

1. **Field Selection**: Only select the fields you need in the WIQL query
2. **Result Limitation**: Use the `top` parameter to limit results if you only need a subset
3. **Filters**: Use specific filters to narrow results and improve query performance
4. **Complex Queries**: Break very complex queries into simpler ones for better performance
5. **Batching**: When retrieving the full work items, use `getWorkItems` with batches of IDs (up to 200)

## See Also

- [getWorkItem Method](./get-work-item.md)
- [getWorkItems Method](./get-work-items.md)
- [Work Item Tracking API Reference](../work-item-tracking-api.md)
- [WIQL Query Language Reference](https://learn.microsoft.com/en-us/azure/devops/boards/queries/wiql-syntax) 