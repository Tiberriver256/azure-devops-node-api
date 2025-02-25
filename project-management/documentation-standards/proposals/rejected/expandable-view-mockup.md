# Expandable View Mockup: WorkItemTrackingApi

**Author**: Taylor  
**Date**: March 24, 2023  
**Status**: REJECTED (March 29, 2023)  

This document provides a visual mockup of how the expandable view would appear for the WorkItemTrackingApi class documentation.

## Rejection Reason

This mockup was rejected due to budget constraints that eliminated the possibility of hosting documentation on a dedicated website with JavaScript support. The interactive elements and dynamic content display shown in this mockup cannot be implemented in GitHub-hosted markdown files.

Instead, we have adopted a GitHub-compatible "Split View" approach that achieves similar goals through a directory-based structure with multiple markdown files. See the [Split View GitHub Approach](../accepted/split-view-github-approach.md) for details.

## Original Mockup (For Reference Only)

### Default View (Single Page)

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
|   - [getWorkItem](#getworkitem)                                       |
|   - [createWorkItem](#createworkitem)                                 |
|   - [updateWorkItem](#updateworkitem)                                 |
|   - [deleteWorkItem](#deleteworkitem)                                 |
|   - [queryByWiql](#querybywiql)                                       |
|   - [... 20+ more methods](#all-methods)                              |
| - [Examples](#examples)                                               |
| - [Common Scenarios](#common-scenarios)                               |
| - [Error Handling](#error-handling)                                   |
| - [Related Resources](#related-resources)                             |
|                                                                       |
| ## Overview                                                           |
|                                                                       |
| The WorkItemTrackingApi class provides methods for creating,          |
| retrieving, updating, and deleting work items in Azure DevOps. It     |
| also supports querying work items and managing work item types,       |
| fields, and queries.                                                  |
|                                                                       |
| ... [content continues with all sections in a single page] ...        |
+-----------------------------------------------------------------------+
```

### Expanded View (After Toggle)

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
| The WorkItemTrackingApi class provides access to the Azure DevOps     |
| work item tracking functionality.                                     |
|                                                                       |
| ## Sections                                                           |
|                                                                       |
| +-------------------+  +-------------------+  +-------------------+   |
| | Overview          |  | Constructor       |  | Properties        |   |
| | [Current Section] |  |                   |  |                   |   |
| +-------------------+  +-------------------+  +-------------------+   |
|                                                                       |
| +-------------------+  +-------------------+  +-------------------+   |
| | Methods           |  | Examples          |  | Common Scenarios  |   |
| |                   |  |                   |  |                   |   |
| +-------------------+  +-------------------+  +-------------------+   |
|                                                                       |
| +-------------------+  +-------------------+                          |
| | Error Handling    |  | Related Resources |                          |
| |                   |  |                   |                          |
| +-------------------+  +-------------------+                          |
|                                                                       |
| ## Overview                                                           |
|                                                                       |
| The WorkItemTrackingApi class provides methods for creating,          |
| retrieving, updating, and deleting work items in Azure DevOps. It     |
| also supports querying work items and managing work item types,       |
| fields, and queries.                                                  |
|                                                                       |
| ... [content for the Overview section only] ...                       |
|                                                                       |
| [Previous: Related Resources] | [Next: Constructor]                   |
+-----------------------------------------------------------------------+
```

### Method Section (Expanded View)

```
+-----------------------------------------------------------------------+
| Azure DevOps Node API Documentation                                    |
+-----------------------------------------------------------------------+
| Home > API Reference > Work Item Tracking > WorkItemTrackingApi > Methods |
+-----------------------------------------------------------------------+
|                                                                       |
| [View as: ( ) Single Page | (•) Expanded Sections]  [?]               |
|                                                                       |
| # WorkItemTrackingApi Methods                                         |
|                                                                       |
| ## Sections                                                           |
|                                                                       |
| +-------------------+  +-------------------+  +-------------------+   |
| | Overview          |  | Constructor       |  | Properties        |   |
| |                   |  |                   |  |                   |   |
| +-------------------+  +-------------------+  +-------------------+   |
|                                                                       |
| +-------------------+  +-------------------+  +-------------------+   |
| | Methods           |  | Examples          |  | Common Scenarios  |   |
| | [Current Section] |  |                   |  |                   |   |
| +-------------------+  +-------------------+  +-------------------+   |
|                                                                       |
| +-------------------+  +-------------------+                          |
| | Error Handling    |  | Related Resources |                          |
| |                   |  |                   |                          |
| +-------------------+  +-------------------+                          |
|                                                                       |
| ## Available Methods                                                  |
|                                                                       |
| ### Work Item Methods                                                 |
| - [getWorkItem](#getworkitem)                                         |
| - [createWorkItem](#createworkitem)                                   |
| - [updateWorkItem](#updateworkitem)                                   |
| - [deleteWorkItem](#deleteworkitem)                                   |
|                                                                       |
| ### Query Methods                                                     |
| - [queryByWiql](#querybywiql)                                         |
| - [queryById](#querybyid)                                             |
|                                                                       |
| ... [list of all methods by category] ...                             |
|                                                                       |
| ## getWorkItem                                                        |
|                                                                       |
| ```typescript                                                         |
| getWorkItem(                                                          |
|     id: number,                                                       |
|     project?: string,                                                 |
|     fields?: string[],                                                |
|     asOf?: Date,                                                      |
|     expand?: WorkItemExpand                                           |
| ): Promise<WorkItem>                                                  |
| ```                                                                   |
|                                                                       |
| ... [detailed documentation for getWorkItem method] ...               |
|                                                                       |
| [Previous: Properties] | [Next: Examples]                             |
+-----------------------------------------------------------------------+
```

### Individual Method View (Expanded View)

```
+-----------------------------------------------------------------------+
| Azure DevOps Node API Documentation                                    |
+-----------------------------------------------------------------------+
| Home > API Reference > Work Item Tracking > WorkItemTrackingApi > Methods > getWorkItem |
+-----------------------------------------------------------------------+
|                                                                       |
| [View as: ( ) Single Page | (•) Expanded Sections]  [?]               |
|                                                                       |
| # WorkItemTrackingApi.getWorkItem Method                              |
|                                                                       |
| [◀ Back to Methods]                                                   |
|                                                                       |
| ## Syntax                                                             |
|                                                                       |
| ```typescript                                                         |
| getWorkItem(                                                          |
|     id: number,                                                       |
|     project?: string,                                                 |
|     fields?: string[],                                                |
|     asOf?: Date,                                                      |
|     expand?: WorkItemExpand                                           |
| ): Promise<WorkItem>                                                  |
| ```                                                                   |
|                                                                       |
| ## Parameters                                                         |
|                                                                       |
| | Parameter | Type | Required | Description |                         |
| |-----------|------|----------|-------------|                         |
| | id | number | Yes | The ID of the work item to retrieve |           |
| | project | string | No | Project ID or name |                        |
| | fields | string[] | No | Array of field names to include |          |
| | asOf | Date | No | Date to retrieve the work item as it existed |   |
| | expand | WorkItemExpand | No | Expansion options |                  |
|                                                                       |
| ... [detailed documentation for getWorkItem method] ...               |
|                                                                       |
| [Previous: Methods Overview] | [Next: createWorkItem]                 |
+-----------------------------------------------------------------------+
```

### Mobile View (Expanded Sections)

```
+-----------------------------------+
| Azure DevOps Node API             |
+-----------------------------------+
| ☰ Menu | [Single Page | Expanded] |
+-----------------------------------+
| Home > ... > WorkItemTrackingApi  |
+-----------------------------------+
|                                   |
| # WorkItemTrackingApi             |
|                                   |
| [Overview] [Constructor] [Props]  |
| [Methods] [Examples] [Scenarios]  |
| [Errors] [Resources]              |
|                                   |
| ## Overview                       |
|                                   |
| The WorkItemTrackingApi class     |
| provides access to the Azure      |
| DevOps work item tracking         |
| functionality.                    |
|                                   |
| ... [content for Overview] ...    |
|                                   |
| [◀ Prev] | [Next ▶]               |
+-----------------------------------+
```

## Implementation Notes

These mockups were designed to demonstrate:

1. **Toggle Behavior**: How users would switch between single-page and expanded views
2. **Navigation**: How users would navigate between sections in the expanded view
3. **Visual Hierarchy**: How content would be organized and presented
4. **Responsive Design**: How the interface would adapt to different screen sizes
5. **Accessibility**: How navigation would work for keyboard and screen reader users

The implementation would have required:
- JavaScript for the toggle functionality and dynamic content display
- CSS for styling the navigation and content sections
- Local storage for remembering user preferences
- Custom HTML templates for the documentation site

Due to budget constraints, we are unable to implement this approach and have instead adopted a GitHub-compatible solution. 