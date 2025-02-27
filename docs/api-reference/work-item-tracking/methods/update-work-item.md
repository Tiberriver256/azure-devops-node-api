# updateWorkItem Method

**Navigation**: [Home](../../../index.md) > [API Reference](../../index.md) > [Work Item Tracking API](../README.md) > updateWorkItem Method

## Overview

The `updateWorkItem` method modifies an existing work item in Azure DevOps. It uses JSON Patch documents to specify the changes to make to the work item, allowing for flexible updates to any field or property of the work item.

## Signature

```typescript
updateWorkItem(
  customHeaders: any,
  document: JsonPatchDocument,
  id: number,
  project?: string,
  validateOnly?: boolean,
  bypassRules?: boolean,
  suppressNotifications?: boolean,
  expand?: WorkItemExpand
): Promise<WorkItem>
```

## Parameters

| Name | Type | Description | Required |
|------|------|-------------|:--------:|
| `customHeaders` | `any` | Custom headers for the request (can be empty `{}`) | Yes |
| `document` | `JsonPatchDocument` | Array of JSON Patch operations defining the changes | Yes |
| `id` | `number` | The ID of the work item to update | Yes |
| `project` | `string` | The project containing the work item (recommended for performance) | No |
| `validateOnly` | `boolean` | If true, validates the update without applying it | No |
| `bypassRules` | `boolean` | If true, bypasses the work item type rules | No |
| `suppressNotifications` | `boolean` | If true, suppresses notifications for this update | No |
| `expand` | `WorkItemExpand` | The expand options for the returned work item | No |

## Returns

`Promise<WorkItem>`: A promise that resolves to the updated work item.

The updated `WorkItem` object includes:

- `id`: The work item ID
- `rev`: The new revision number (incremented after update)
- `fields`: Dictionary of field names and values (including updates)
- `relations`: Array of related work items (if expand includes Relations)
- `_links`: Dictionary of related resources

## JSON Patch Document

The JSON Patch document is an array of operations that define the changes to make. Each operation has:

- `op`: The operation type ("add", "replace", "remove", "test", "move", "copy")
- `path`: The path to the field (e.g., "/fields/System.Title")
- `value`: The new value for the field (required for "add", "replace", "test")
- `from`: The source path (required for "move", "copy")

Common operations:

- `add`: Add a value to a field that doesn't exist or update an existing field
- `replace`: Replace the value of an existing field
- `remove`: Remove a field or value

## Examples

### Basic Update

```typescript
// Update the state of a work item
const patchDocument = [
  {
    op: "replace",
    path: "/fields/System.State",
    value: "Active"
  }
];

// Update work item with ID 42
const updatedWorkItem = await witApi.updateWorkItem(
  {}, // Custom headers (can be empty)
  patchDocument,
  42,
  "MyProject"
);

console.log(`Updated work item #${updatedWorkItem.id} to state: ${updatedWorkItem.fields["System.State"]}`);
```

### Multiple Field Updates

```typescript
// Update multiple fields in a single request
const patchDocument = [
  {
    op: "replace",
    path: "/fields/System.Title",
    value: "Updated title for this bug"
  },
  {
    op: "replace",
    path: "/fields/System.AssignedTo",
    value: "user@example.com"
  },
  {
    op: "add",
    path: "/fields/System.Tags",
    value: "API; Updated; Critical"
  },
  {
    op: "replace",
    path: "/fields/Microsoft.VSTS.Common.Priority",
    value: 1
  }
];

const updatedWorkItem = await witApi.updateWorkItem(
  {}, 
  patchDocument,
  42
);

console.log(`Updated work item #${updatedWorkItem.id}`);
console.log(`New title: ${updatedWorkItem.fields["System.Title"]}`);
console.log(`Assigned to: ${updatedWorkItem.fields["System.AssignedTo"]}`);
```

### Safe Concurrent Updates with Test Operations

When multiple users or systems might update the same work item simultaneously, use the "test" operation to ensure you're updating the expected version of the work item. This approach prevents accidental overwrites and is essential for maintaining data integrity:

```typescript
import * as azdev from "azure-devops-node-api";

async function safeUpdateWorkItem(workItemId: number, newTitle: string, expectedRevision: number) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/your-organization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get Work Item Tracking API client
    const witApi = await connection.getWorkItemTrackingApi();
    const projectName = "MyProject";
    
    try {
        // Create patch document with test operation for revision
        const patchDocument = [
            // Test operation verifies the current revision matches what we expect
            {
                op: "test",
                path: "/rev",
                value: expectedRevision
            },
            // If test passes, update the title
            {
                op: "replace",
                path: "/fields/System.Title",
                value: newTitle
            }
        ];
        
        // Update the work item
        const updatedWorkItem = await witApi.updateWorkItem(
            {}, 
            patchDocument,
            workItemId,
            projectName
        );
        
        console.log(`Work item #${updatedWorkItem.id} updated successfully`);
        console.log(`New revision: ${updatedWorkItem.rev}`);
        return updatedWorkItem;
    } catch (error) {
        // Handle specific test operation failure
        if (error.statusCode === 412) { // Precondition Failed
            console.error("Update failed: The work item has been modified by someone else since you loaded it.");
            
            // Get the current version
            const currentWorkItem = await witApi.getWorkItem(workItemId, projectName);
            console.log(`Current revision is ${currentWorkItem.rev} (expected ${expectedRevision})`);
            console.log(`Current title: "${currentWorkItem.fields["System.Title"]}"`);
            
            // You could implement merge logic or prompt the user here
        } else {
            console.error(`Error updating work item: ${error.message}`);
        }
        throw error;
    }
}

// Example of using the safe update function
async function updateWithRetry() {
    try {
        // First, get the current work item to know its revision
        const workItem = await witApi.getWorkItem(42, "MyProject");
        const currentRev = workItem.rev;
        
        // Try to update with the current revision
        await safeUpdateWorkItem(42, "Updated title with conflict protection", currentRev);
    } catch (error) {
        console.error("Failed to update work item safely:", error.message);
    }
}
```

You can also use test operations on specific fields to verify their current values:

```typescript
// Test that a field has an expected value before updating
const patchDocument = [
    // Test the current state is "Active"
    {
        op: "test",
        path: "/fields/System.State",
        value: "Active"
    },
    // If test passes, update to "Resolved"
    {
        op: "replace",
        path: "/fields/System.State",
        value: "Resolved"
    }
];
```

### Validation Only

```typescript
// Validate an update without applying it
const patchDocument = [
  {
    op: "replace",
    path: "/fields/System.State",
    value: "Done"
  }
];

try {
  const validationResult = await witApi.updateWorkItem(
    {}, 
    patchDocument,
    42,
    "MyProject",
    true // validateOnly = true
  );
  
  console.log("Update is valid and can be applied");
} catch (error) {
  console.error("Update validation failed:", error.message);
}
```

### Conditional Update

```typescript
// Only update if current value matches expected value
const patchDocument = [
  // Test operation ensures the current title matches expected value
  {
    op: "test",
    path: "/fields/System.Title",
    value: "Expected current title"
  },
  // If test passes, replace the title
  {
    op: "replace",
    path: "/fields/System.Title",
    value: "New title after verification"
  }
];

try {
  const updatedWorkItem = await witApi.updateWorkItem(
    {}, 
    patchDocument,
    42
  );
  console.log("Work item updated successfully");
} catch (error) {
  console.error("Conditional update failed - current value did not match expected:", error.message);
}
```

## Common Errors

| Error | Description | Solution |
|-------|-------------|----------|
| 400 Bad Request | Invalid field values or operations | Check field names, values, and operation types |
| 404 Not Found | Work item doesn't exist | Verify the work item ID exists |
| 401 Unauthorized | Authentication token is invalid | Refresh your authentication token |
| 403 Forbidden | User doesn't have permission to update the work item | Request appropriate permissions |
| 412 Precondition Failed | "test" operation in patch document failed | The current value doesn't match the expected value in a test operation |

## Field Value Formats

Different fields require different value formats:

- **Text fields** (Title, Description): String values, some support HTML
- **Person fields** (AssignedTo): Email address or display name
- **Date fields**: ISO date strings (YYYY-MM-DD) or date-time strings
- **Numeric fields**: Numbers without quotes
- **Tags**: Semicolon-separated values as a single string

## Handling HTML Content in Fields

Several fields in Azure DevOps work items support HTML content, most notably `System.Description`. When updating these fields, there are specific considerations to ensure the HTML is properly formatted and secure:

### 1. HTML Formatting Rules

When providing HTML content:

```typescript
const patchDocument = [
  {
    op: "replace",
    path: "/fields/System.Description",
    value: "<div>This is a <strong>formatted</strong> description with:</div><ul><li>Bullet points</li><li>And <a href='https://example.com'>links</a></li></ul>"
  }
];
```

Guidelines for HTML content:

- Use valid, well-formed HTML
- Azure DevOps supports a subset of HTML tags (most common text formatting elements)
- Supported tags include: `<div>`, `<p>`, `<ul>`, `<ol>`, `<li>`, `<b>`, `<strong>`, `<i>`, `<em>`, `<u>`, `<a>`, `<br>`, `<hr>`, `<table>`, `<tr>`, `<td>`, `<code>`, `<pre>`
- Images can be included with the `<img>` tag but must reference images stored within Azure DevOps as attachments
- Styles are limited to basic formatting; complex CSS is not fully supported

### 2. Security Considerations

To prevent HTML injection vulnerabilities when displaying user-provided content:

```typescript
// Always sanitize HTML content before including it in the API call
function sanitizeHtml(html: string): string {
    // This is a SIMPLIFIED example and NOT suitable for production
    // Replace potentially dangerous characters
    return html
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#39;')
        .replace(/`/g, '&#96;')
        .replace(/\//g, '&#47;');
}

// Example: Creating safe HTML from user input
const userProvidedTitle = "User's <script>alert('xss')</script> input";
const userProvidedDetails = "Details with <script>malicious code</script>";

const sanitizedDetails = sanitizeHtml(userProvidedDetails);

const patchDocument = [
    {
        op: "replace",
        path: "/fields/System.Title",
        value: sanitizeHtml(userProvidedTitle)  // Safe: "User's &lt;script&gt;alert('xss')&lt;/script&gt; input"
    },
    {
        op: "replace",
        path: "/fields/System.Description",
        value: `<div>Issue description:</div><div>${sanitizedDetails}</div>`  // Safe HTML
    }
];
```

### 3. Handling Rich Text Editors

When integrating with rich text editors in web applications:

```typescript
// Example integration with a hypothetical rich text editor
function updateWorkItemFromEditor(workItemId, editorContent) {
    // Most rich text editors output HTML
    const DOMPurify = require('dompurify'); // Proper sanitization library
    const sanitizedHtml = DOMPurify.sanitize(editorContent, {
        ALLOWED_TAGS: ['p', 'div', 'span', 'br', 'ul', 'ol', 'li', 'b', 'i', 'strong', 'em', 'a', 'code', 'pre', 'img'],
        ALLOWED_ATTR: ['href', 'target', 'rel', 'src', 'alt', 'class']
    });
    
    const patchDocument = [
        {
            op: "replace",
            path: "/fields/System.Description",
            value: sanitizedHtml
        }
    ];
    
    return witApi.updateWorkItem({}, patchDocument, workItemId);
}
```

For production applications, always use a proper HTML sanitization library like DOMPurify, sanitize-html, or xss. Simple regex replacements are insufficient for proper security.

## Best Practices

1. **Field Validation**: Use `validateOnly` to check if your update is valid before applying it
2. **Concurrency**: Use "test" operations to ensure the field hasn't changed since you retrieved it
3. **Batching**: Group related field changes in a single update operation for better performance
4. **HTML Content**: For Description and other HTML fields, ensure proper HTML formatting
5. **Notifications**: Use `suppressNotifications` for bulk operations to avoid notification spam
6. **Permissions**: Ensure the authenticating user has permissions to update the fields you're changing
7. **Rules**: Be aware of work item rules that may prevent certain field combinations

## See Also

- [createWorkItem Method](./create-work-item.md)
- [getWorkItem Method](./get-work-item.md)
- [Work Item Tracking API Reference](../work-item-tracking-api.md)
- [JSON Patch Specification](http://jsonpatch.com/) 