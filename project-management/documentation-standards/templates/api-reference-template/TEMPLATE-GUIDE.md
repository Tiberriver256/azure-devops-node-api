# API Reference Template Guide

This guide explains how to use the API reference template to document complex API components in the Azure DevOps Node API.

## Overview

The API reference template follows a "split view" approach, which divides complex API documentation into multiple markdown files with consistent navigation patterns. This approach reduces cognitive load, improves navigation, and enhances maintainability while ensuring compatibility with GitHub-hosted markdown files.

## Directory Structure

The template includes the following files and directories:

```
api-reference-template/
├── README.md                 # Main overview file
├── constructor.md            # Constructor documentation
├── properties.md             # Properties documentation
├── examples.md               # Examples documentation
├── common-scenarios.md       # Common scenarios documentation
├── error-handling.md         # Error handling documentation
├── methods/                  # Methods directory
│   ├── README.md             # Methods overview
│   └── method-template.md    # Template for individual method documentation
├── enums/                    # Enums directory (for enum documentation)
├── interfaces/               # Interfaces directory (for interface documentation)
└── TEMPLATE-GUIDE.md         # This guide
```

## How to Use the Template

### Step 1: Create the Directory Structure

Create a directory for your API component with the following structure:

```
api-reference/
└── your-api-component/
    ├── README.md
    ├── constructor.md
    ├── properties.md
    ├── examples.md
    ├── common-scenarios.md
    ├── error-handling.md
    └── methods/
        ├── README.md
        └── [method-files].md
```

### Step 2: Copy and Customize Template Files

1. Copy each template file to the corresponding location in your API component directory.
2. Replace placeholder values in each file with actual values for your API component.

#### Placeholder Replacement Guide

| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{ApiClassName}` | The name of the API class | `WorkItemTrackingApi` |
| `{apiInstanceName}` | The variable name for an instance of the API | `workItemTrackingApi` |
| `{brief description}` | A brief description of what the API does | `creating, retrieving, updating, and deleting work items` |
| `{methodName}` | The name of a method | `getWorkItem` |
| `{method-filename}` | The filename for a method (kebab-case) | `get-work-item.md` |
| `{param1}`, `{param2}`, etc. | Parameter names | `id`, `project` |
| `{type1}`, `{type2}`, etc. | Parameter types | `number`, `string` |
| `{returnType}` | The return type of a method | `WorkItem` |
| `{ResourceType}` | The main resource type the API works with | `WorkItem` |
| `{resourceType}` | Lowercase version of the resource type | `workItem` |

### Step 3: Organize Methods

1. In the methods/README.md file, organize methods into logical categories.
2. Create a separate markdown file for each method in the methods directory.
3. Ensure consistent navigation between method files.

### Step 4: Add Examples and Common Scenarios

1. Add realistic examples to the examples.md file.
2. Document common usage scenarios in the common-scenarios.md file.
3. Include error handling patterns in the error-handling.md file.

### Step 5: Review and Refine

1. Review all documentation for accuracy and completeness.
2. Ensure consistent navigation between files.
3. Verify that all links work correctly.

## Selection Criteria

Apply this template to API components that meet one or more of the following criteria:

1. **Method Count**: The API has more than 15 methods.
2. **Complexity**: The API works with complex data structures or has complex workflows.
3. **Usage Frequency**: The API is frequently used and requires detailed documentation.
4. **Support Inquiries**: The API generates a high number of support inquiries.

## Navigation Pattern

Each file should include consistent navigation links:

1. **Top Navigation**: Links to parent pages at the top of each file.
2. **Bottom Navigation**: Links to all main sections at the bottom of each file.

Example:
```markdown
[◀ Back to ApiClassName](../README.md) | [◀ Back to Methods](./README.md)

---

<!-- Content here -->

---

## Navigation

- [ApiClassName Overview](../README.md)
- [Constructor](../constructor.md)
- [Properties](../properties.md)
- [Methods](./README.md)
- [Examples](../examples.md)
- [Common Scenarios](../common-scenarios.md)
- [Error Handling](../error-handling.md)
```

## Enhanced Navigation Features

Based on feedback from our UI/UX team, we've enhanced the navigation with these additional features:

1. **Breadcrumb Navigation**: Include breadcrumbs at the top of each file to show context:
   ```markdown
   [API Reference](../../README.md) > [ApiClassName](../README.md) > [Methods](./README.md) > methodName
   ```

2. **Section Indicators**: Add visual indicators of section complexity or length:
   ```markdown
   ## Methods (25 total) {#methods}
   ```

3. **Related Content Links**: Include "See Also" sections that link to related content:
   ```markdown
   ## See Also
   - [Related Method](./related-method.md) - Brief description
   - [Related Concept](../../concepts/related-concept.md) - Brief description
   ```

4. **Anchor Links**: Use anchor links for easy navigation within long pages:
   ```markdown
   ## Table of Contents
   - [Overview](#overview)
   - [Parameters](#parameters)
   - [Returns](#returns)
   ```

## Accessibility Considerations

To ensure our documentation is accessible to all users:

1. **Alternative Text**: Include descriptive alt text for all images and diagrams.
2. **Heading Hierarchy**: Maintain proper heading levels (H1 > H2 > H3) without skipping levels.
3. **Descriptive Link Text**: Use descriptive link text instead of "click here" or "read more".
4. **Color Contrast**: Ensure sufficient contrast for text and visual elements.
5. **Code Examples**: Provide text explanations alongside code examples.
6. **Table Headers**: Use proper table headers and structure for data tables.

## Best Practices

1. **Consistency**: Maintain consistent formatting and navigation across all files.
2. **Completeness**: Include all relevant information for each component.
3. **Examples**: Provide realistic, working examples for all methods.
4. **Error Handling**: Document common errors and how to handle them.
5. **Cross-References**: Include links to related methods and resources.
6. **Progressive Disclosure**: Start with basic information and progressively reveal more complex details.
7. **User-Centric**: Focus on user goals rather than API features.
8. **Visual Separation**: Use visual separation (horizontal rules, headers) to delineate content sections.
9. **Mobile Responsiveness**: Ensure content is readable on various screen sizes.
10. **Print Optimization**: Format content to be printer-friendly.

## Example Implementation

For a complete example of this template in action, see the [WorkItemTrackingApi documentation](../../api-reference/work-item-tracking/).

## Need Help?

If you have questions or need assistance with using this template, please contact the documentation team. 