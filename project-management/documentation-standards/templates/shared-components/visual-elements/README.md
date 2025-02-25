# Visual Elements Library

This library contains standardized visual elements for use in Azure DevOps Node API documentation. These elements are designed to provide consistency across all documentation types while working within the constraints of GitHub-hosted markdown files.

## Purpose

The Visual Elements Library serves to:

1. Provide standardized diagram templates to ensure consistency in visual representation
2. Define guidelines for creating visual distinction using GitHub-compatible markdown
3. Ensure all documentation meets accessibility standards
4. Reduce the time required to create high-quality documentation visuals

## Contents

### Diagram Templates

- [Component Relationship Diagram](./diagrams/component-relationship-template.drawio)
- [Process Flow Diagram](./diagrams/process-flow-template.drawio)
- [Authentication Flow Diagram](./diagrams/authentication-flow-template.drawio)
- [System Architecture Diagram](./diagrams/system-architecture-template.drawio)
- [Sequence Diagram](./diagrams/sequence-diagram-template.drawio)

### Markdown Formatting Elements

- [TL;DR Summary Boxes](./formatting/tldr-summary-boxes.md)
- [Warning and Note Boxes](./formatting/warning-note-boxes.md)
- [Code Block Formatting](./formatting/code-block-formatting.md)
- [Table Formatting](./formatting/table-formatting.md)
- [Collapsible Section Formatting](./formatting/collapsible-section-formatting.md)

### Accessibility Guidelines

- [Diagram Accessibility Guidelines](./accessibility/diagram-accessibility.md)
- [Alternative Text Best Practices](./accessibility/alt-text-best-practices.md)
- [Documentation Structure Guidelines](./accessibility/documentation-structure.md)

## How to Use

### Diagram Templates

1. Download the appropriate diagram template (`.drawio` file)
2. Open the template using [Draw.io](https://www.draw.io/) or Visual Studio Code with the Draw.io Integration extension
3. Customize the diagram with your specific content while maintaining the visual style
4. Export as PNG with transparent background
5. Add appropriate alt text when including in documentation

### Markdown Formatting Elements

Each formatting element comes with:

- GitHub-compatible markdown templates
- Guidelines for usage
- Examples showing proper implementation

Example TL;DR Box:

```markdown
> **TL;DR:** Resource Areas help the API client find the correct URLs for services in Azure DevOps, making your code resilient to service location changes and enabling versioned access to API resources.
```

Renders as:

> **TL;DR:** Resource Areas help the API client find the correct URLs for services in Azure DevOps, making your code resilient to service location changes and enabling versioned access to API resources.

## Accessibility Standards

All documentation elements must meet the following accessibility standards:

1. **Alternative text** for all diagrams that clearly describes the content and purpose
2. **Semantic structure** that makes sense when read by screen readers
3. **Consistent formatting** for predictable navigation
4. **Text alternatives** for any information conveyed by visual elements

## GitHub Markdown Constraints

All elements in this library are designed to work within GitHub's markdown constraints:

1. **No custom CSS/styling**: We rely on GitHub's default rendering
2. **Limited HTML support**: We use only the HTML tags supported by GitHub (like `<details>` and `<summary>`)
3. **No JavaScript**: We cannot use interactive elements
4. **Static images only**: All diagrams are static PNG images

See [Documentation Tech Stack](../../../../docs-tech-stack.md) for more details on these constraints.

## Contribution

To contribute new visual elements or improve existing ones:

1. Create your visual element following the established style guidelines
2. Add appropriate documentation explaining usage
3. Ensure accessibility compliance
4. Submit a pull request with your contribution

## Maintenance

This library is maintained by the Documentation Standards team. Visual elements are reviewed quarterly to ensure they remain:

1. Technically accurate
2. Visually consistent within GitHub constraints
3. Accessible to all users
4. Compatible with current documentation templates

## Related Resources

- [Conceptual Guide Template](../../conceptual-guide-template/README.md)
- [API Reference Template](../../api-reference-template/README.md)
- [Template Selection Guide](../../template-selection-guide.md)
- [Shared Components Library](../README.md)
- [Documentation Tech Stack](../../../../docs-tech-stack.md)

---

[◀ Back to Shared Components](../README.md) 