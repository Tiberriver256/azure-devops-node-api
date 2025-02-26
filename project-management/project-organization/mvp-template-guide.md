# MVP Template Implementation Guide

## Overview

This guide outlines the simplified template approach for the MVP documentation of the Azure DevOps Node API. It leverages our existing templates from the documentation standards but focuses on essential sections to accelerate delivery while maintaining quality.

## Template Simplification Principles

1. **Focus on Essential Information**
   - Include only information critical for developer understanding
   - Prioritize practical usage over comprehensive coverage
   - Ensure technical accuracy of included content

2. **Consistent Structure**
   - Maintain consistent headings and organization across all documents
   - Use standardized formatting for code examples, notes, and warnings
   - Follow established style guide for terminology and tone

3. **Practical Examples**
   - Include minimal but complete code examples
   - Focus on common use cases
   - Ensure all examples are tested and functional

4. **Progressive Disclosure**
   - Present most important information first
   - Use collapsible sections for additional details
   - Link to future documentation for advanced scenarios

## Simplified Template Structure

### API Client Documentation Template

```markdown
# [API Client Name] Client

## Overview

Brief description of the API client's purpose and functionality (2-3 sentences).

## Installation

```bash
npm install azure-devops-node-api
```

## Authentication

Brief explanation of how to authenticate with this API client, with a simple example.

```typescript
// Authentication example
```

## Common Methods

### Method 1: [Method Name]

**Purpose**: Brief description of what the method does.

**Signature**:
```typescript
function methodName(param1: Type, param2: Type): ReturnType;
```

**Parameters**:
- `param1`: Description of parameter
- `param2`: Description of parameter

**Returns**: Description of return value

**Example**:
```typescript
// Code example
```

### Method 2: [Method Name]

[Follow same structure as Method 1]

## Common Errors

Brief description of common errors and how to handle them.

```typescript
// Error handling example
```

## See Also

- Link to related documentation
- Link to sample code
```

### Method Documentation Template

```markdown
# [Method Name]

**Client**: [API Client Name]

## Purpose

Brief description of what the method does and when to use it.

## Signature

```typescript
function methodName(param1: Type, param2: Type): ReturnType;
```

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| param1    | Type | Yes/No   | Description |
| param2    | Type | Yes/No   | Description |

## Returns

Description of return value and structure.

## Example

```typescript
// Simple example showing basic usage
```

## Common Errors

Brief description of common errors and how to handle them.

```typescript
// Error handling example
```

## See Also

- Link to related methods
- Link to sample code
```

## Template Implementation Guidelines

### 1. API Client Selection

For the MVP, focus on documenting these API clients in order of priority:

1. Work Item Tracking API
2. Git API
3. Build API
4. WebApi Core

### 2. Method Prioritization

For each API client, document only the top 5-7 most commonly used methods, based on:

- Developer feedback
- Usage patterns in sample code
- Essential functionality for basic operations

### 3. Example Creation

For each method, create:

- One basic example showing standard usage
- One error handling example for common errors
- Minimal but complete code that can be copied and used directly

### 4. Cross-Referencing

Implement basic cross-references:

- Link between related API clients
- Link between related methods
- Link to future documentation for advanced scenarios

## Template Sections to Defer for Post-MVP

The following sections from our comprehensive templates can be deferred for post-MVP documentation:

1. **Advanced Scenarios**: Complex usage patterns and edge cases
2. **Performance Considerations**: Optimization techniques and benchmarks
3. **Versioning Information**: Version-specific behaviors and changes
4. **Extensive Type Documentation**: Detailed documentation of all types and interfaces
5. **Interactive Examples**: Playground-style interactive examples
6. **Diagrams and Visual Aids**: Complex workflow visualizations

## Quality Assurance Checklist

Despite the simplified approach, all MVP documentation must pass this basic quality check:

- [ ] All code examples compile and run correctly
- [ ] Method signatures are accurate and complete
- [ ] Parameter descriptions are clear and accurate
- [ ] Return value descriptions are clear and accurate
- [ ] Common errors are documented with handling strategies
- [ ] Links to related documentation are functional
- [ ] Content follows style guide for terminology and tone
- [ ] No critical information is missing for basic usage

## Implementation Process

1. **Template Selection**: Choose the appropriate template based on content type
2. **Content Creation**: Fill in the template with essential information
3. **Technical Review**: Have an API specialist verify technical accuracy
4. **Editorial Review**: Ensure clarity, consistency, and completeness
5. **Publication**: Deploy to documentation site

## Example Implementation

### Work Item Tracking API Client Example

```markdown
# Work Item Tracking API Client

## Overview

The Work Item Tracking API client provides access to Azure DevOps work items, queries, and work item types. It allows you to create, update, and manage work items programmatically.

## Installation

```bash
npm install azure-devops-node-api
```

## Authentication

Authenticate using a Personal Access Token (PAT) to access the Work Item Tracking API.

```typescript
import * as azdev from "azure-devops-node-api";

// Your organization URL and personal access token
const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";

// Connect to Azure DevOps
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get Work Item Tracking API client
const witApi = await connection.getWorkItemTrackingApi();
```

## Common Methods

### Method 1: getWorkItem

**Purpose**: Retrieves a single work item by ID.

**Signature**:
```typescript
getWorkItem(id: number, project?: string, fields?: string[], asOf?: Date, expand?: WorkItemExpand): Promise<WorkItem>;
```

**Parameters**:
- `id`: The work item ID
- `project`: Optional project name or GUID
- `fields`: Optional list of fields to return
- `asOf`: Optional date to retrieve work item as of a specific date
- `expand`: Optional expand options

**Returns**: Promise that resolves to a WorkItem object

**Example**:
```typescript
// Get work item with ID 42
const workItem = await witApi.getWorkItem(42);
console.log(`Work Item Title: ${workItem.fields["System.Title"]}`);
```

### Method 2: createWorkItem

[Follow same structure as Method 1]

## Common Errors

Handle authentication and permission errors when accessing work items.

```typescript
try {
  const workItem = await witApi.getWorkItem(42);
  console.log(`Work Item Title: ${workItem.fields["System.Title"]}`);
} catch (error) {
  if (error.statusCode === 401) {
    console.error("Authentication failed. Check your PAT token.");
  } else if (error.statusCode === 403) {
    console.error("You don't have permission to access this work item.");
  } else {
    console.error(`Error: ${error.message}`);
  }
}
```

## See Also

- [Work Item Field Reference](future-link)
- [Work Item Query Examples](future-link)
```

This guide ensures we maintain quality and consistency while accelerating documentation delivery for the MVP phase. 