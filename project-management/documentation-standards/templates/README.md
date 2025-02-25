# Documentation Templates

This directory contains standardized templates for creating consistent documentation for the Azure DevOps Node API.

## Available Templates

| Template | Description | When to Use | Complexity |
|----------|-------------|------------|------------|
| [API Reference Template](./api-reference-template/) | Template for complex API component documentation using a split view approach | Use for API components with many methods (15+), complex data structures, high usage frequency, or that generate many support inquiries | ⭐⭐⭐ |
| [API Method Template](./api-method-template/) | Template for individual API method documentation | Use for documenting individual API methods with parameters, return types, and examples | ⭐ |
| [Class Template](./class-template/) | Template for class documentation | Use for documenting classes with fewer than 15 methods, straightforward structure, and documentation that fits comfortably in a single file | ⭐⭐ |
| [Interface Template](./interface-template/) | Template for interface documentation | Use for documenting TypeScript interfaces with properties, methods, and implementation examples | ⭐⭐ |
| [Tutorial Template](./tutorial-template/) | Template for step-by-step tutorials using a multi-file structure | Use for creating guided, task-oriented documentation that walks users through completing specific tasks | ⭐⭐⭐ |
| [Conceptual Guide Template](./conceptual-guide-template/) | Template for conceptual documentation | Use for explaining concepts, architecture, and design principles | ⭐⭐ |
| [Troubleshooting Guide Template](./troubleshooting-guide-template/) | Template for troubleshooting documentation | Use for documenting common issues and their solutions | ⭐⭐ |

*Complexity: ⭐ Basic, ⭐⭐ Intermediate, ⭐⭐⭐ Advanced*

For detailed guidance on selecting the appropriate template, see the [Template Selection Guide](./template-selection-guide.md).

## Template Structure

Each template directory follows a consistent structure:

```
template-name/
├── template-name.md                 # The main template file
├── template-name-guide.md           # Detailed guide on how to use the template
├── examples/                        # Example implementations of the template
│   └── example-implementation.md    # Specific example files
├── sections/                        # (If applicable) Template sections for multi-file templates
└── README.md                        # Overview of the template
```

## Shared Components

To ensure consistency across different template types, we've created standardized sections that can be reused:

| Component | Description | Used In |
|-----------|-------------|---------|
| [Parameters Section](./shared-components/parameters-section.md) | Standard format for documenting method parameters | API Method, Class, Interface templates |
| [Return Type Section](./shared-components/return-type-section.md) | Standard format for documenting return types | API Method template |
| [Navigation Patterns](./shared-components/navigation-patterns.md) | Standard navigation patterns and breadcrumbs | All templates |
| [Error Handling Section](./shared-components/error-handling-section.md) | Standard format for documenting errors | API Method, API Reference templates |
| [Example Section](./shared-components/example-section.md) | Standard format for code examples | All templates |

See the [Shared Components](./shared-components/README.md) directory for more information.

## When to Use Each Template

### For API Documentation

1. **Complex API Components**:
   - Use the [API Reference Template](./api-reference-template/) when documenting complex API components that meet one or more of these criteria:
     - 15+ methods
     - Complex data structures
     - High usage frequency
     - Generates many support inquiries
   - Example: WorkItemTrackingApi, GitApi, BuildApi

2. **Simple API Components**:
   - For simpler API components, use the [Class Template](./class-template/)
   - Example: WebApi, ConnectionData, TeamProject

3. **Individual Methods**:
   - Use the [API Method Template](./api-method-template/) for documenting individual methods
   - Example: getWorkItem, createBuildDefinition, updatePullRequest

4. **Interfaces**:
   - Use the [Interface Template](./interface-template/) for documenting TypeScript interfaces
   - Example: IWorkItemTrackingApi, IGitApi, IBuildApi

### For Conceptual & Educational Documentation

1. **Step-by-Step Guides**:
   - Use the [Tutorial Template](./tutorial-template/) for step-by-step guides
   - Example: Work Item Automation Tutorial, Setting Up Authentication

2. **Conceptual Documentation**:
   - Use the [Conceptual Guide Template](./conceptual-guide-template/) for explaining concepts and architectures
   - Example: Azure DevOps Architecture Overview, Authentication Concepts

3. **Troubleshooting Documentation**:
   - Use the [Troubleshooting Guide Template](./troubleshooting-guide-template/) for documenting common issues and solutions
   - Example: Authentication Troubleshooting, Common Errors and Solutions

## Template Usage

Each template directory includes a TEMPLATE-GUIDE.md file with detailed instructions on how to use the template. Please refer to these guides for specific usage instructions.

## Template Customization

While these templates provide a standardized structure, they can be customized to meet specific documentation needs. When customizing templates:

1. Maintain consistent navigation patterns
2. Preserve the overall structure
3. Document any significant deviations from the standard template
4. Consider whether your customization should become a standard

## Navigation Consistency

All templates should follow consistent navigation patterns to provide a seamless user experience:

1. **Breadcrumb Navigation**: Include breadcrumbs at the top of each file
2. **Table of Contents**: Include a table of contents for long documents
3. **Related Content Links**: Include "See Also" sections with related content
4. **Visual Indicators**: Use visual indicators to show section complexity or length

## Template Maintenance

These templates are maintained by the documentation team. If you have suggestions for improvements or encounter issues with the templates, please contact the documentation team.

## See Also

- [Template Selection Guide](./template-selection-guide.md)
- [Shared Components](./shared-components/README.md)
- [Documentation Standards](../README.md)
- [Style Guide](../style-guide/README.md)
- [Documentation Process](../implementation/process.md) 