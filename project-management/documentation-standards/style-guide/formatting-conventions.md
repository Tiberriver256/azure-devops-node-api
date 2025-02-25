# Formatting Conventions for Azure DevOps Node API Documentation

## Overview

This document establishes the formatting standards for all Azure DevOps Node API documentation. Consistent formatting improves readability, accessibility, and maintainability while creating a professional and unified look across all documentation assets.

## Document Structure

### Page Organization

Every documentation page should follow this general structure:

1. **Title** (H1): Clear, concise description of the content
2. **Introduction**: Brief overview of the page content
3. **Main Content**: Organized into logical sections with consistent heading hierarchy
4. **Related Information**: Links to related content (where applicable)

Example:
```markdown
# Work Item Tracking Client

A client for interacting with work items in Azure DevOps.

## Overview

The WorkItemTrackingClient provides methods to create, retrieve, update, and delete work items in Azure DevOps projects.

## Initialization

...

## Common Operations

...

## Related Information

- [Work Item Types Reference](./work-item-types.md)
- [Query Examples](./work-item-query-examples.md)
```

### Heading Hierarchy

Maintain a logical heading hierarchy using the following guidelines:

- Use only one H1 (`#`) per page as the page title
- Start subsections with H2 (`##`)
- Use H3 (`###`) for topics within subsections
- Use H4 (`####`) for details within topics
- Rarely use H5 or H6, as deep nesting indicates content that may need restructuring
- Do not skip heading levels (e.g., don't follow an H2 with an H4)

Example:
```markdown
# API Client Reference (H1)

## GitClient (H2)

### Methods (H3)

#### getRepositories (H4)
```

### Section Length

Keep sections readable and focused:

- Aim for 300-500 words per section
- If a section exceeds 500 words, consider dividing it into subsections
- Ensure each section covers a single concept or topic
- Use visual breaks (headings, lists, code blocks) to improve scanability

## Text Formatting

### Emphasis and Highlighting

Use formatting consistently to emphasize important content:

- **Bold** (`**text**`): Use for UI elements, important terms on first use, and strong emphasis
- *Italic* (`*text*`): Use for slight emphasis, book titles, and non-UI element names
- `Code Font` (`` `code` ``): Use for code, file names, parameters, properties, and methods
- ~~Strikethrough~~ (`~~text~~`): Use sparingly, only for deprecated features with replacement noted

Example:
```markdown
When you use the **Create Work Item** dialog, you must provide a `title` parameter. The *Azure DevOps Service* will then generate a unique identifier.
```

### Links

Format links consistently:

- Use descriptive link text that makes sense out of context
- For internal links, use relative paths
- For external links, use the full URL
- Add hover text for additional context when helpful

Examples:
```markdown
[GitClient initialization](../guides/authentication.md)

[Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md "Microsoft REST API Guidelines on GitHub")
```

### Lists

Format lists consistently:

#### Bulleted Lists
- Use for unordered collections of items
- Use a hyphen (`-`) as the bullet character
- Maintain consistent capitalization and punctuation
- Nest lists with indentation (2 spaces)

Example:
```markdown
- Personal Access Token (PAT)
- OAuth 2.0
  - Authorization Code flow
  - Device Code flow
- Azure Active Directory
```

#### Numbered Lists
1. Use for sequential steps, prioritized items, or items referenced by number
2. Always start with 1
3. Markdown will handle the numbering automatically
4. Use consistent punctuation and capitalization

Example:
```markdown
1. Initialize the connection.
2. Create the client.
3. Call the API method.
4. Process the response.
```

#### Definition Lists
Use HTML definition lists for term/definition pairs:

```html
<dl>
  <dt>Resource</dt>
  <dd>An addressable unit of information or service within the API.</dd>
  
  <dt>Collection</dt>
  <dd>A set of resources, often returned by API operations that return multiple items.</dd>
</dl>
```

### Tables

Format tables consistently:

- Include a header row
- Use alignment indicators for readability (`:---` for left, `:---:` for center, `---:` for right)
- Keep tables simple and focused
- Consider alternative formats if a table exceeds 5 columns

Example:
```markdown
| Method | Return Type | Description | Authentication |
|:-------|:------------|:------------|:---------------|
| getRepositories | Promise<GitRepository[]> | Returns all repositories | Required |
| getRepository | Promise<GitRepository> | Returns a specific repository | Required |
| createRepository | Promise<GitRepository> | Creates a new repository | Required |
```

### Code Examples

Format code examples consistently:

#### Inline Code
Use backticks for inline code references:
```markdown
Use the `getWorkItem()` method to retrieve a specific work item.
```

#### Code Blocks
Use fenced code blocks with language specification:

````markdown
```typescript
const workItem = await client.getWorkItem(123);
console.log(workItem.fields["System.Title"]);
```
````

#### Code Block Titles
For titled code blocks, use HTML comments before the code block:

````markdown
<!-- filename: src/samples/get-work-item.ts -->
```typescript
import * as azdev from "azure-devops-node-api";

async function getWorkItem(id: number): Promise<WorkItem> {
    // Code implementation
}
```
````

## Callouts and Notes

Use consistent styling for callouts:

### Note Callouts
For general information and tips:

```markdown
> [!NOTE]
> This method requires 'Read' permissions for the project.
```

### Warning Callouts
For potential problems or important considerations:

```markdown
> [!WARNING]
> This operation permanently deletes data and cannot be undone.
```

### Important Callouts
For critical information:

```markdown
> [!IMPORTANT]
> You must initialize the client before calling any methods.
```

### Tip Callouts
For best practices and suggestions:

```markdown
> [!TIP]
> Use batch operations to create multiple work items efficiently.
```

## Images and Diagrams

Format images consistently:

- Include alt text for all images
- Use descriptive file names
- Specify width for larger images
- Include captions for complex images

Example:
```markdown
![API Client Hierarchy Diagram](../images/client-hierarchy.png "Hierarchy of Azure DevOps Node API clients")
*Figure 1: Relationship between WebApi client and service clients*
```

## Code Samples

Format code samples consistently:

- Use syntax highlighting by specifying the language
- Include comments for complex logic
- Follow TypeScript/JavaScript conventions:
  - 2-space indentation
  - semicolons at the end of statements
  - single quotes for strings
  - camelCase for variables and functions
  - PascalCase for classes and interfaces
  - Descriptive variable names

Example:
````markdown
```typescript
// Initialize the API client
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get the Git client
const gitClient = await connection.getGitApi();

// Get repositories
try {
  const repositories = await gitClient.getRepositories(project);
  
  // Process repositories
  repositories.forEach(repo => {
    console.log(`Repository: ${repo.name}`);
  });
} catch (error) {
  console.error('Failed to retrieve repositories:', error.message);
}
```
````

## Versioning Information

Format versioning information consistently:

### Version Badges
Use badges to indicate version support:

```markdown
![Supported in v6.0+](../images/badges/supported-v6.0+.svg)
```

### Version Tables
Use tables for version compatibility:

```markdown
| Feature | v5.0 | v5.1 | v6.0 |
|:--------|:----:|:----:|:----:|
| OAuth Support | ✅ | ✅ | ✅ |
| Work Item Batching | ❌ | ✅ | ✅ |
| Custom Fields | ❌ | ❌ | ✅ |
```

### Deprecation Notices
Format deprecation notices consistently:

```markdown
> [!WARNING]
> **Deprecated in v6.0**
> 
> The `getProjects` method is deprecated and will be removed in v7.0.
> Use `getProjectsWithOptions` instead.
```

## API Reference Formatting

### Method Signatures

Format method signatures consistently:

```markdown
### getWorkItem

▸ **getWorkItem**(`id`: number, `options`?: WorkItemGetOptions): *Promise‹WorkItem›*

Retrieves a work item by its ID.

#### Parameters:

| Name | Type | Description |
|:-----|:-----|:------------|
| `id` | number | The ID of the work item to retrieve |
| `options` | WorkItemGetOptions | Optional parameters to customize the request |

#### Returns:

*Promise‹WorkItem›*

A promise that resolves to the work item
```

### Type Definitions

Format type definitions consistently:

```markdown
### WorkItemGetOptions

Options for retrieving work items.

#### Properties:

| Name | Type | Description |
|:-----|:-----|:------------|
| `includeDeleted` | boolean | Whether to include deleted work items. Default: false |
| `expand` | WorkItemExpand | Expansion options for the work item. Default: None |
| `asOf` | Date | Historical version of the work item at the given date |
```

### Examples Section

Format examples consistently:

```markdown
#### Example

```typescript
// Get a work item by ID
const workItem = await workClient.getWorkItem(123, {
  expand: WorkItemExpand.All
});
console.log(workItem.fields["System.Title"]);
```
```

## Common Elements

### File Paths

Format file paths using code font and consistent separators:

```markdown
The configuration file is located at `./config/settings.json`.
```

### Command Line Examples

Format command line examples consistently:

````markdown
```bash
# Install the Azure DevOps Node API package
npm install azure-devops-node-api

# Configure the environment variable
export AZURE_DEVOPS_PAT=your_personal_access_token
```
````

### Keyboard Shortcuts

Format keyboard shortcuts consistently:

```markdown
Use <kbd>Ctrl</kbd>+<kbd>C</kbd> to copy the selected text.
```

## Accessibility Considerations

Ensure formatting meets accessibility standards:

- Maintain heading hierarchy for screen readers
- Use descriptive alt text for all images
- Avoid using color alone to convey meaning
- Ensure sufficient color contrast in diagrams and images
- Use table headers properly
- Provide text alternatives for complex visuals

## Implementation Guidelines

Apply these formatting conventions using:

1. GitHub Flavored Markdown (GFM) for most documentation
2. HTML only when necessary for complex formatting
3. Consistent document templates for each content type
4. Automated linting tools to check formatting compliance
5. Regular formatting review as part of documentation workflow

---

## Formatting Checklist

Use this checklist when reviewing documentation for formatting consistency:

- [ ] Page follows standard structure with proper heading hierarchy
- [ ] Text formatting (bold, italic, code) is used consistently
- [ ] Lists are formatted properly with consistent punctuation
- [ ] Tables include headers and proper alignment
- [ ] Code examples include language specification
- [ ] Callouts follow standard format
- [ ] Images include alt text and captions where needed
- [ ] Version information is clearly indicated
- [ ] API references follow standard format
- [ ] Accessibility requirements are met 