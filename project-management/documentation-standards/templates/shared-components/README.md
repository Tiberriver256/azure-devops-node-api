# Shared Template Components

This directory contains standardized sections that can be reused across different template types. These shared components ensure consistency in documentation structure, formatting, and navigation patterns.

## Purpose

The shared components serve several purposes:
1. **Consistency**: Ensure that common sections look and behave the same way across all documentation
2. **Maintenance**: Simplify updates by centralizing common components
3. **Efficiency**: Reduce duplication of effort when creating new documentation
4. **Quality**: Standardize best practices for documentation sections

## Available Components

| Component | Description | Used In |
|-----------|-------------|---------|
| [parameters-section.md](./parameters-section.md) | Standard format for documenting method parameters | API Method, Class, Interface templates |
| [return-type-section.md](./return-type-section.md) | Standard format for documenting return types | API Method template |
| [navigation-patterns.md](./navigation-patterns.md) | Standard navigation patterns and breadcrumbs | All templates |
| [error-handling-section.md](./error-handling-section.md) | Standard format for documenting errors | API Method, API Reference templates |
| [example-section.md](./example-section.md) | Standard format for code examples | All templates |

## How to Use

When creating or updating templates, include these shared components using the following approach:

1. **Reference the component**: Include a reference to the shared component in your template guide
2. **Follow the format**: Ensure your documentation follows the format specified in the shared component
3. **Maintain consistency**: Use the same terminology and structure as defined in the shared component

## Component Structure

Each shared component includes:
1. **Format**: The standard format for the section
2. **Required elements**: Elements that must be included
3. **Optional elements**: Elements that can be included based on context
4. **Example**: A complete example of the component in use

## Customization

While consistency is important, some customization may be necessary based on the specific documentation context. When customizing:

1. Maintain the core structure of the component
2. Document any significant deviations
3. Consider whether your customization should become a new standard

## Maintenance

The shared components are maintained by the documentation team. If you have suggestions for improvements or encounter issues, please contact the documentation team.

## See Also

- [Template Selection Guide](../template-selection-guide.md)
- [Documentation Standards](../../README.md)
- [Style Guide](../../style-guide/README.md) 