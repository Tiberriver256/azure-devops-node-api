# Documentation Templates

This directory contains standardized templates for creating consistent documentation for the Azure DevOps Node API.

## Available Templates

| Template | Description | When to Use |
|----------|-------------|------------|
| [API Reference Template](./api-reference-template/) | Template for complex API component documentation using a split view approach | Use for API components with many methods (15+), complex data structures, high usage frequency, or that generate many support inquiries |
| [Tutorial Template](./tutorial-template/) | Template for step-by-step tutorials using a multi-file structure | Use for creating guided, task-oriented documentation that walks users through completing specific tasks |
| [API Method Template](./api-method-template/) | Template for individual API method documentation | Use for documenting individual API methods with parameters, return types, and examples |
| [Class Template](./class-template/) | Template for class documentation | Use for documenting classes with properties, methods, and usage examples |
| [Interface Template](./interface-template/) | Template for interface documentation | Use for documenting TypeScript interfaces with properties, methods, and implementation examples |
| [Conceptual Guide Template](./conceptual-guide-template/) | Template for conceptual documentation | Use for explaining concepts, architecture, and design principles |
| [Troubleshooting Guide Template](./troubleshooting-guide-template/) | Template for troubleshooting documentation | Use for documenting common issues and their solutions |

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

## Template Selection Guide

When creating new documentation, follow these guidelines to select the appropriate template:

1. **API Reference Documentation**:
   - Use the [API Reference Template](./api-reference-template/) for complex API components
   - For simpler API components, use a single markdown file
   - For individual methods, use the [API Method Template](./api-method-template/)

2. **Task-Oriented Documentation**:
   - Use the [Tutorial Template](./tutorial-template/) for step-by-step guides
   - Include clear prerequisites, steps, and examples

3. **Conceptual Documentation**:
   - Use the [Conceptual Guide Template](./conceptual-guide-template/) for explaining concepts
   - Focus on helping users understand "why" and "how" rather than just "what"

4. **Troubleshooting Documentation**:
   - Use the [Troubleshooting Guide Template](./troubleshooting-guide-template/) for documenting common issues
   - Organize by symptoms and include clear solutions

## Template Usage

Each template directory includes a TEMPLATE-GUIDE.md file with detailed instructions on how to use the template. Please refer to these guides for specific usage instructions.

## Template Customization

While these templates provide a standardized structure, they can be customized to meet specific documentation needs. When customizing templates:

1. Maintain consistent navigation patterns
2. Preserve the overall structure
3. Document any significant deviations from the standard template

## Template Maintenance

These templates are maintained by the documentation team. If you have suggestions for improvements or encounter issues with the templates, please contact the documentation team.

## See Also

- [Documentation Standards](../README.md)
- [Style Guide](../style-guide/README.md)
- [Documentation Process](../implementation/process.md) 