# Expandable View Mockup: WorkItemTrackingApi

This document provides a visual mockup of how the expandable view would appear for the WorkItemTrackingApi class documentation.

## Default View (Single Page)

```
+-----------------------------------------------------------------------+
| Azure DevOps Node API Documentation                                    |
+-----------------------------------------------------------------------+
| Home > API Reference > Work Item Tracking > WorkItemTrackingApi        |
+-----------------------------------------------------------------------+
|                                                                       |
| [View as: (•) Single Page | ( ) Expanded Sections]  [?]               |
|                                                                       |
| # WorkItemTrackingApi                                                 |
|                                                                       |
| The WorkItemTrackingApi class provides access to the Azure DevOps     |
| work item tracking functionality.                                     |
|                                                                       |
| ## Contents                                                           |
| - [Overview](#overview)                                               |
| - [Constructor](#constructor)                                         |
| - [Properties](#properties)                                           |
| - [Methods](#methods)                                                 |
| - [Examples](#examples)                                               |
| - [Common Scenarios](#common-scenarios)                               |
| - [Error Handling](#error-handling)                                   |
| - [Related Resources](#related-resources)                             |
|                                                                       |
| ## Overview                                                           |
|                                                                       |
| The WorkItemTrackingApi class is the entry point for all work item    |
| tracking operations in the Azure DevOps Node API. It provides methods |
| for creating, updating, and querying work items, as well as managing  |
| work item types, fields, and queries.                                 |
|                                                                       |
| ...                                                                   |
|                                                                       |
| [Continues with all sections in a single page]                        |
+-----------------------------------------------------------------------+
```

## Expanded View (After Toggle)

```
+-----------------------------------------------------------------------+
| Azure DevOps Node API Documentation                                    |
+-----------------------------------------------------------------------+
| Home > API Reference > Work Item Tracking > WorkItemTrackingApi        |
+-----------------------------------------------------------------------+
|                                                                       |
| [View as: ( ) Single Page | (•) Expanded Sections]  [?]               |
|                                                                       |
| # WorkItemTrackingApi                                                 |
|                                                                       |
+-----------------------------------------------------------------------+
|                          |                                            |
| TABLE OF CONTENTS        | ## Overview                                |
| ----------------------   |                                            |
| → Overview               | The WorkItemTrackingApi class is the entry |
| • Constructor            | point for all work item tracking operations|
| • Properties             | in the Azure DevOps Node API. It provides  |
| • Methods                | methods for creating, updating, and        |
| • Examples               | querying work items, as well as managing   |
| • Common Scenarios       | work item types, fields, and queries.      |
| • Error Handling         |                                            |
| • Related Resources      | ### Implements                             |
|                          |                                            |
| SECTION DETAILS          | - [IWorkItemTrackingApi](./interfaces/     |
| ----------------------   |   IWorkItemTrackingApi.html)               |
| Overview: 2 min read     |                                            |
| Methods: 20+ methods     | ### Version Information                    |
|                          |                                            |
|                          | - Introduced: v1.0                         |
|                          | - Last Updated: v7.2                       |
|                          |                                            |
|                          | ### Package                                |
|                          |                                            |
|                          | - @azure/devops-node-api                   |
|                          |                                            |
|                          |                                            |
|                          |                                            |
|                          |                                            |
|                          |                                            |
|                          |                                            |
+-----------------------------------------------------------------------+
|                                                                       |
| ← Index | ← Previous: IWorkItemTrackingApi | Next: Constructor →      |
+-----------------------------------------------------------------------+
```

## Method Section in Expanded View

```
+-----------------------------------------------------------------------+
| Azure DevOps Node API Documentation                                    |
+-----------------------------------------------------------------------+
| Home > API Reference > Work Item Tracking > WorkItemTrackingApi        |
+-----------------------------------------------------------------------+
|                                                                       |
| [View as: ( ) Single Page | (•) Expanded Sections]  [?]               |
|                                                                       |
| # WorkItemTrackingApi                                                 |
|                                                                       |
+-----------------------------------------------------------------------+
|                          |                                            |
| TABLE OF CONTENTS        | ## Methods                                 |
| ----------------------   |                                            |
| • Overview               | The WorkItemTrackingApi class provides the |
| • Constructor            | following methods:                         |
| • Properties             |                                            |
| → Methods                | - [createWorkItem](#createworkitem)        |
| • Examples               | - [getWorkItem](#getworkitem)              |
| • Common Scenarios       | - [updateWorkItem](#updateworkitem)        |
| • Error Handling         | - [deleteWorkItem](#deleteworkitem)        |
| • Related Resources      | - [queryWorkItems](#queryworkitems)        |
|                          | - [getWorkItemTypes](#getworkitemtypes)    |
| METHOD QUICK LINKS       | - [getFields](#getfields)                  |
| ----------------------   | - [getQueries](#getqueries)                |
| • createWorkItem         | - [createQuery](#createquery)              |
| • getWorkItem            | - [updateQuery](#updatequery)              |
| • updateWorkItem         | - [deleteQuery](#deletequery)              |
| • deleteWorkItem         | - [getAttachments](#getattachments)        |
| • queryWorkItems         | - [createAttachment](#createattachment)    |
| • getWorkItemTypes       | - [getComments](#getcomments)              |
| • getFields              | - [addComment](#addcomment)                |
| • getQueries             | - [getWorkItemRevisions](#getworkitemrevisions) |
| • createQuery            | - [getWorkItemHistory](#getworkitemhistory)|
| • updateQuery            | - [getWorkItemRelations](#getworkitemrelations) |
| • deleteQuery            | - [addWorkItemRelation](#addworkitemrelation) |
| • getAttachments         | - [removeWorkItemRelation](#removeworkitemrelation) |
|                          |                                            |
+-----------------------------------------------------------------------+
|                                                                       |
| ← Previous: Properties | Next: Examples →                             |
+-----------------------------------------------------------------------+
```

## Individual Method in Expanded View

```
+-----------------------------------------------------------------------+
| Azure DevOps Node API Documentation                                    |
+-----------------------------------------------------------------------+
| Home > API Reference > Work Item Tracking > WorkItemTrackingApi > Methods > getWorkItem |
+-----------------------------------------------------------------------+
|                                                                       |
| [View as: ( ) Single Page | (•) Expanded Sections]  [?]               |
|                                                                       |
| # WorkItemTrackingApi.getWorkItem                                     |
|                                                                       |
+-----------------------------------------------------------------------+
|                          |                                            |
| TABLE OF CONTENTS        | ### getWorkItem                            |
| ----------------------   |                                            |
| • Overview               | Retrieves a work item by ID.               |
| • Constructor            |                                            |
| • Properties             | #### Syntax                                |
| → Methods                |                                            |
|   → getWorkItem          | ```typescript                              |
| • Examples               | public async getWorkItem(                  |
| • Common Scenarios       |     id: number,                            |
| • Error Handling         |     project?: string,                      |
| • Related Resources      |     expand?: WorkItemExpand,               |
|                          |     asOf?: Date                            |
| OTHER METHODS            | ): Promise<WorkItem>                       |
| ----------------------   | ```                                        |
| • createWorkItem         |                                            |
| • updateWorkItem         | #### Parameters                            |
| • deleteWorkItem         |                                            |
| • queryWorkItems         | | Name | Type | Description |              |
|                          | |------|------|-------------|              |
|                          | | id | number | The ID of the work item to retrieve |
|                          | | project | string | (Optional) Project name or ID |
|                          | | expand | WorkItemExpand | (Optional) The expand options |
|                          | | asOf | Date | (Optional) Date version to retrieve |
|                          |                                            |
|                          | #### Returns                               |
|                          |                                            |
|                          | Promise<WorkItem>: A promise that resolves to the work item |
|                          |                                            |
+-----------------------------------------------------------------------+
|                                                                       |
| ← Back to Methods | Next Method: updateWorkItem →                     |
+-----------------------------------------------------------------------+
```

## Mobile View (Expanded Sections)

```
+---------------------------------------+
| Azure DevOps Node API                 |
+---------------------------------------+
| [≡] WorkItemTrackingApi              |
+---------------------------------------+
| [View as: Single | Expanded] [?]      |
+---------------------------------------+
| # WorkItemTrackingApi                 |
+---------------------------------------+
| [☰ TABLE OF CONTENTS]                |
+---------------------------------------+
|                                       |
| ## Methods                            |
|                                       |
| The WorkItemTrackingApi class         |
| provides the following methods:       |
|                                       |
| - [createWorkItem](#createworkitem)   |
| - [getWorkItem](#getworkitem)         |
| - [updateWorkItem](#updateworkitem)   |
| - [deleteWorkItem](#deleteworkitem)   |
| - [queryWorkItems](#queryworkitems)   |
| - [getWorkItemTypes](#getworkitemtypes)|
| - [getFields](#getfields)             |
| - [getQueries](#getqueries)           |
| - [createQuery](#createquery)         |
| - [updateQuery](#updatequery)         |
| - [deleteQuery](#deletequery)         |
| - [getAttachments](#getattachments)   |
| - [createAttachment](#createattachment)|
| - [getComments](#getcomments)         |
| - [addComment](#addcomment)           |
|                                       |
+---------------------------------------+
|                                       |
| ← Properties | Examples →             |
+---------------------------------------+
```

## Implementation Notes

1. **Toggle Behavior**:
   - The toggle should be prominently displayed at the top of the page
   - When switching from single page to expanded view, the user should be taken to the first section
   - When switching from expanded to single page, the user should be positioned at the equivalent section in the single page view

2. **Navigation**:
   - The table of contents in the expanded view should be sticky and remain visible when scrolling
   - The current section should be highlighted in the table of contents
   - Navigation links at the bottom should provide context about where the user is in the documentation

3. **Responsive Design**:
   - On smaller screens, the table of contents should be collapsible
   - Navigation controls should be optimized for touch on mobile devices
   - Content should reflow appropriately for different screen sizes

4. **Accessibility**:
   - The toggle should be keyboard accessible with appropriate ARIA labels
   - Navigation should work with screen readers and keyboard navigation
   - Focus management should be maintained when switching views

5. **User Preferences**:
   - The user's view preference should be stored in local storage
   - The system should remember which section the user was viewing when switching views
   - Analytics should track which view users prefer and which sections they spend the most time on 