# Split View Approach for Complex API Documentation (GitHub-Compatible)

**Author**: Taylor  
**Date**: March 25, 2023  
**Status**: Draft  

## Executive Summary

Due to budget constraints eliminating the possibility of hosting documentation on a dedicated website, we need to adapt our documentation strategy to work within the constraints of GitHub-hosted markdown files. This proposal outlines a "split view" approach for complex API components that maintains the benefits of our previously proposed expandable view while working within GitHub's limitations.

## Background

Our previous expandable view proposal relied on JavaScript and client-side interactions that are not supported in GitHub-rendered markdown. However, the core problem remains: complex API components like `WorkItemTrackingApi` with numerous methods and properties create cognitive load issues for users when presented in a single file.

## Proposal: Split View Approach

The split view approach divides complex API documentation into multiple markdown files with consistent navigation patterns, creating a more manageable experience while working within GitHub's constraints.

### Key Features

1. **Directory-Based Structure**: Organize complex component documentation into a directory with multiple markdown files.
2. **Main Overview File**: Create a central index file that provides component overview and links to all sub-files.
3. **Specialized Sub-Files**: Separate methods, properties, examples, and scenarios into dedicated files.
4. **Consistent Navigation**: Implement standardized navigation sections at the top and bottom of each file.
5. **Visual Hierarchy**: Use markdown formatting to create clear visual hierarchies within each file.

### Selection Criteria

Apply the split view approach to components that meet at least one of the following criteria:

- Classes/interfaces with more than 15 methods or properties
- Methods with more than 10 parameters
- Components requiring extensive examples or scenarios
- Components with complex inheritance hierarchies

### Implementation Approach

#### Directory Structure

```
api-reference/
├── work-item-tracking/
│   ├── README.md                     # Main overview file (index)
│   ├── constructor.md                # Constructor details
│   ├── properties.md                 # All properties
│   ├── methods/                      # Directory for method documentation
│   │   ├── README.md                 # Methods overview with links
│   │   ├── get-work-item.md          # Individual method documentation
│   │   ├── create-work-item.md       # Individual method documentation
│   │   └── ...                       # Other method files
│   ├── examples.md                   # Usage examples
│   ├── common-scenarios.md           # Common scenarios
│   └── error-handling.md             # Error handling guidance
```

#### Navigation Pattern

Each file will include:

1. **Header Navigation**:
   ```markdown
   # WorkItemTrackingApi.getWorkItem Method
   
   [◀ Back to WorkItemTrackingApi](../README.md) | [◀ Back to Methods](./README.md)
   
   ---
   ```

2. **Footer Navigation**:
   ```markdown
   ---
   
   ## Navigation
   
   - [WorkItemTrackingApi Overview](../README.md)
   - [Constructor](../constructor.md)
   - [Properties](../properties.md)
   - [Methods](./README.md)
   - [Examples](../examples.md)
   - [Common Scenarios](../common-scenarios.md)
   - [Error Handling](../error-handling.md)
   ```

### Main Overview File (README.md)

The main overview file will serve as the entry point and include:

1. Component description and purpose
2. Version information
3. Import/usage syntax
4. Links to all sub-sections
5. Quick reference tables for methods and properties
6. Basic usage example

### Method Documentation Structure

Each method file will follow a consistent structure:

1. Method signature and return type
2. Parameter descriptions with types
3. Return value description
4. Code examples
5. Common errors and troubleshooting
6. Related methods

## Pilot Implementation: WorkItemTrackingApi

We will implement this approach first for the `WorkItemTrackingApi` class, which is an ideal candidate due to its complexity:

- 25+ methods for interacting with work items
- Complex parameter objects
- Multiple usage scenarios
- Frequently accessed by developers

### Implementation Timeline

1. **Week 1**: Create directory structure and main overview file
2. **Week 2**: Document constructor, properties, and 5 core methods
3. **Week 3**: Complete remaining method documentation
4. **Week 4**: Add examples, common scenarios, and error handling files
5. **Week 5**: Review, refine, and finalize

## Benefits

1. **Reduced Cognitive Load**: Users only need to focus on the specific section they're interested in.
2. **Improved Navigation**: Clear navigation patterns help users maintain context.
3. **Better Maintainability**: Smaller files are easier to update and maintain.
4. **GitHub Compatibility**: Works within the constraints of GitHub-hosted markdown.
5. **Progressive Disclosure**: Information is presented in a logical, progressive manner.
6. **Searchability**: GitHub's search functionality works well with this approach.

## Considerations and Mitigations

| Consideration | Mitigation |
|---------------|------------|
| Navigation Complexity | Consistent navigation patterns and breadcrumbs in each file |
| Potential for Duplication | Create reusable content snippets for common elements |
| Maintenance Overhead | Develop clear guidelines and templates for consistency |
| GitHub Limitations | Leverage GitHub's native features (anchors, relative links) |
| User Experience Consistency | Standardize formatting and structure across all files |

## Resource Requirements

- **Personnel**: 1 technical writer (Taylor), 1 UI/UX reviewer (Jamie)
- **Time**: 5 weeks for pilot implementation
- **Tools**: Standard markdown editors, GitHub

## Success Metrics

1. **User Feedback**: Collect feedback from developers using the documentation
2. **GitHub Analytics**: Track page views and time spent on documentation
3. **Support Tickets**: Monitor reduction in support tickets related to API usage
4. **Contribution Activity**: Track community contributions to documentation

## Next Steps

1. Update the expandable view proposal to reflect GitHub constraints
2. Create detailed templates for each file type
3. Implement the pilot for WorkItemTrackingApi
4. Collect feedback and refine the approach
5. Develop guidelines for applying this approach to other complex components

## Appendix: Example Implementation

### Main Overview File (README.md)

```markdown
# WorkItemTrackingApi

[◀ Back to API Reference](../../README.md)

---

## Overview

The `WorkItemTrackingApi` class provides methods for creating, retrieving, updating, and deleting work items in Azure DevOps. It also supports querying work items and managing work item types.

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

---

## Navigation

- [API Reference Home](../../README.md)
- [Constructor](./constructor.md)
- [Properties](./properties.md)
- [Methods](./methods/README.md)
- [Examples](./examples.md)
- [Common Scenarios](./common-scenarios.md)
- [Error Handling](./error-handling.md)
```

### Method Documentation Example (get-work-item.md)

```markdown
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

---

## Navigation

- [WorkItemTrackingApi Overview](../README.md)
- [Constructor](../constructor.md)
- [Properties](../properties.md)
- [Methods](./README.md)
- [Examples](../examples.md)
- [Common Scenarios](../common-scenarios.md)
- [Error Handling](../error-handling.md)
``` 