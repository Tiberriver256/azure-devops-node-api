# WorkItemTrackingApi

[◀ Back to API Reference](../README.md)

---

## Overview

The `WorkItemTrackingApi` class provides methods for creating, retrieving, updating, and deleting work items in Azure DevOps. It also supports querying work items and managing work item types, fields, and queries.

## Installation

```typescript
import * as azdev from "azure-devops-node-api";
import { WorkItemTrackingApi } from "azure-devops-node-api/WorkItemTrackingApi";

// Get WorkItemTrackingApi instance
const connection = new azdev.WebApi(orgUrl, authHandler);
const workItemTrackingApi: WorkItemTrackingApi = await connection.getWorkItemTrackingApi();
```

## Sections

- [Constructor](./constructor.md)
- [Properties](./properties.md)
- [Methods](./methods/README.md)
- [Examples](./examples.md)
- [Common Scenarios](./common-scenarios.md)
- [Error Handling](./error-handling.md)

## Quick Reference

### Key Methods

| Method | Description |
|--------|-------------|
| [getWorkItem](./methods/get-work-item.md) | Retrieves a single work item |
| [createWorkItem](./methods/create-work-item.md) | Creates a new work item |
| [updateWorkItem](./methods/update-work-item.md) | Updates an existing work item |
| [deleteWorkItem](./methods/delete-work-item.md) | Deletes a work item |
| [queryByWiql](./methods/query-by-wiql.md) | Queries work items using WIQL |

See [all methods](./methods/README.md) for the complete list.

## Basic Example

```typescript
// Get a work item
const workItem = await workItemTrackingApi.getWorkItem(1);
console.log(`Work Item ${workItem.id}: ${workItem.fields["System.Title"]}`);
```

## Version Information

- **Package**: azure-devops-node-api
- **Introduced**: v1.0
- **Last Updated**: v7.2

## Implements

- [IWorkItemTrackingApi](../interfaces/IWorkItemTrackingApi.md)

---

## Navigation

- [API Reference Home](../README.md)
- [Constructor](./constructor.md)
- [Properties](./properties.md)
- [Methods](./methods/README.md)
- [Examples](./examples.md)
- [Common Scenarios](./common-scenarios.md)
- [Error Handling](./error-handling.md) 