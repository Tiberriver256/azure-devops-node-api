**Navigation**: [Home](../../../index.md) > [API Reference](../../index.md) > [WorkItemTrackingApi](../README.md) > Methods

# WorkItemTrackingApi Methods

This page provides an overview of all methods available in the WorkItemTrackingApi class, organized by category.

## Contents

- [Work Item Methods](#work-item-methods)
- [Query Methods](#query-methods)
- [Type Methods](#type-methods)
- [Link Methods](#link-methods)
- [Attachment Methods](#attachment-methods)

---

## Work Item Methods

These methods allow you to create, retrieve, update, and delete work items.

| Method | Description | Common Use |
|--------|-------------|------------|
| ⭐ [getWorkItem](./getWorkItem.md) | Retrieves a single work item by ID | Very common |
| ⭐ [getWorkItems](./getWorkItems.md) | Retrieves multiple work items by ID | Very common |
| 🆕 [createWorkItem](./createWorkItem.md) | Creates a new work item | Common |
| 🔄 [updateWorkItem](./updateWorkItem.md) | Updates an existing work item | Common |
| ⚠️ [deleteWorkItem](./deleteWorkItem.md) | Permanently deletes a work item | Uncommon |

---

## Query Methods

These methods allow you to execute and manage work item queries.

| Method | Description | Common Use |
|--------|-------------|------------|
| 🔍 [queryByWiql](./queryByWiql.md) | Executes a work item query using WIQL | Common |
| 🔍 [queryById](./queryById.md) | Executes a saved query by ID | Common |
| [getQueries](./getQueries.md) | Retrieves all queries for a project | Uncommon |
| [getQuery](./getQuery.md) | Retrieves a specific query by ID or path | Uncommon |

---

## Type Methods

These methods allow you to work with work item type definitions.

| Method | Description | Common Use |
|--------|-------------|------------|
| [getWorkItemType](./getWorkItemType.md) | Retrieves a specific work item type | Common |
| [getWorkItemTypes](./getWorkItemTypes.md) | Retrieves all work item types for a project | Common |
| [getFields](./getFields.md) | Retrieves all fields for a project | Uncommon |
| [getField](./getField.md) | Retrieves a specific field | Uncommon |

---

## Link Methods

These methods allow you to work with work item links.

| Method | Description | Common Use |
|--------|-------------|------------|
| [getRelationTypes](./getRelationTypes.md) | Retrieves all relation types | Uncommon |
| [getRelationType](./getRelationType.md) | Retrieves a specific relation type | Uncommon |

---

## Attachment Methods

These methods allow you to work with work item attachments.

| Method | Description | Common Use |
|--------|-------------|------------|
| [createAttachment](./createAttachment.md) | Creates a new attachment | Uncommon |
| [getAttachment](./getAttachment.md) | Retrieves an attachment | Uncommon |
| [deleteAttachment](./deleteAttachment.md) | Deletes an attachment | Uncommon |

## See Also

- [WorkItemTrackingApi Class](../README.md)
- [Constructor](../constructor.md)
- [Properties](../properties.md)
- [Common Scenarios](../common-scenarios.md)
- [Error Handling](../error-handling.md)