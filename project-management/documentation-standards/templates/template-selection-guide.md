# Template Selection Guide

This guide helps you select the appropriate template for your documentation needs. Choosing the right template ensures your documentation is structured appropriately for its content and complexity.

## Selection Flowchart

```
Start
  |
  +--> Is it a tutorial or step-by-step guide?
  |     |
  |     +--> Yes --> Use Tutorial Template
  |     |
  |     +--> No
  |           |
  +--> Is it explaining a concept rather than an API?
  |     |
  |     +--> Yes --> Use Conceptual Guide Template
  |     |
  |     +--> No
  |           |
  +--> Is it documenting common issues and solutions?
  |     |
  |     +--> Yes --> Use Troubleshooting Guide Template
  |     |
  |     +--> No
  |           |
  +--> Is it documenting an API component?
        |
        +--> Yes
              |
              +--> Is it a complex component (15+ methods, complex structure)?
              |     |
              |     +--> Yes --> Use API Reference Template
              |     |
              |     +--> No
              |           |
              +--> Is it a single method?
              |     |
              |     +--> Yes --> Use API Method Template
              |     |
              |     +--> No
              |           |
              +--> Is it a class?
              |     |
              |     +--> Yes --> Use Class Template
              |     |
              |     +--> No
              |           |
              +--> Is it an interface?
                    |
                    +--> Yes --> Use Interface Template
                    |
                    +--> No --> Consult documentation team
```

## Template Complexity Ratings

To help you understand the complexity of each template and the effort required to use it, we've created the following ratings:

| Template | Complexity | Writer Effort | Reader Navigation Complexity |
|----------|------------|--------------|------------------------------|
| API Method Template | ⭐ | Low | Low |
| Class Template | ⭐⭐ | Medium | Low |
| Interface Template | ⭐⭐ | Medium | Low |
| Conceptual Guide Template | ⭐⭐ | Medium | Low |
| Troubleshooting Guide Template | ⭐⭐ | Medium | Low |
| Tutorial Template | ⭐⭐⭐ | High | Medium |
| API Reference Template | ⭐⭐⭐ | High | Medium |

## Detailed Selection Criteria

### API Reference Template

**When to use:**
- API component has 15+ methods
- API component has complex data structures
- API component is frequently used by developers
- API component generates many support inquiries
- Documentation requires splitting across multiple files for maintainability

**Examples:**
- WorkItemTrackingApi
- GitApi
- BuildApi

### API Method Template

**When to use:**
- Documenting a single API method
- Method exists independently or as part of a smaller API
- Method requires detailed documentation but doesn't belong to a complex component

**Examples:**
- getWorkItem
- createBuildDefinition
- updatePullRequest

### Class Template

**When to use:**
- Documenting a class with fewer than 15 methods
- Class has a straightforward structure
- Class doesn't require the split view approach
- Documentation fits comfortably in a single file

**Examples:**
- WebApi
- ConnectionData
- TeamProject

### Interface Template

**When to use:**
- Documenting a TypeScript interface
- Interface doesn't belong to a complex component
- Interface requires detailed documentation about implementation

**Examples:**
- IWorkItemTrackingApi
- IGitApi
- IBuildApi

### Tutorial Template

**When to use:**
- Creating step-by-step guides
- Teaching users how to accomplish specific tasks
- Documentation benefits from progressive disclosure
- Content includes multiple sequential steps

**Examples:**
- Work Item Automation Tutorial
- Setting Up Authentication
- Creating Custom Build Definitions

### Conceptual Guide Template

**When to use:**
- Explaining concepts rather than specific API features
- Providing architectural overviews
- Explaining design principles and patterns
- Helping users understand "why" rather than just "how"

**Examples:**
- Azure DevOps Architecture Overview
- Authentication Concepts
- REST vs. Node.js API Comparison

### Troubleshooting Guide Template

**When to use:**
- Documenting common issues and their solutions
- Creating Q&A or FAQ content
- Providing diagnostic information
- Helping users resolve specific problems

**Examples:**
- Authentication Troubleshooting
- Common Errors and Solutions
- Performance Optimization Guide

## Special Considerations

### Mixed Content Types

Some documentation may include elements of multiple content types. In these cases:

1. Identify the primary purpose of the documentation
2. Select the template that best matches that purpose
3. Adapt the template as needed to incorporate secondary elements
4. Document any significant deviations from the standard template

### Collaborative Decision Making

If you're unsure which template to use:

1. Consult with the documentation team
2. Consider getting input from UI/UX specialists
3. Create a small prototype using each potential template
4. Evaluate based on:
   - Reader experience
   - Maintenance requirements
   - Content organization
   - Future scalability

### Template Customization

While consistency is important, templates can be customized to better serve specific documentation needs:

1. Maintain consistent navigation patterns
2. Preserve the overall structure
3. Document any significant deviations
4. Consider whether your customization should become a standard

## See Also

- [Documentation Templates](./README.md)
- [Shared Components](./shared-components/README.md)
- [Documentation Standards](../README.md) 