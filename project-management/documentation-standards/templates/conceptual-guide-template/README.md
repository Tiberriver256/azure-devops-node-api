# Conceptual Guide Template

This directory contains the template and guidance for creating conceptual documentation for the Azure DevOps Node API.

## Purpose

Conceptual guides explain abstract concepts, architectures, and design principles related to Azure DevOps and its APIs. These guides help users understand the "why" behind API designs and provide mental models for working with the API effectively.

## Contents

- [conceptual-guide-template.md](./conceptual-guide-template.md) - The template file to use when creating conceptual documentation
- [conceptual-guide-template-guide.md](./conceptual-guide-template-guide.md) - Comprehensive guide on how to use this template
- [examples/](./examples/) - Example implementations of the template

## When to Use This Template

Use this template when:

- Explaining the architecture or design of Azure DevOps systems
- Documenting important concepts that span multiple API components
- Introducing terminology and mental models needed to understand the API
- Providing background information necessary for effective API usage
- Explaining relationships between different system components

For step-by-step instructions, use the [Tutorial Template](../tutorial-template/) instead. For documenting specific API methods or classes, use the appropriate API template.

## Complexity Rating: ⭐⭐ (Intermediate)

This template has an intermediate complexity level:

- **Writer Effort**: Medium - Requires deep understanding of concepts but follows a straightforward structure
- **Reader Navigation**: Low - Single-file format with clear sections makes navigation straightforward

## Key Features

- **Clear Structure**: Organized flow from general concepts to specific details
- **Progressive Disclosure**: Information presented from basic to advanced with collapsible sections
- **Visual Learning Support**: Required diagrams and various visual elements to enhance understanding
- **Relationship Mapping**: Explicit connections between concepts and components
- **Practical Guidance**: Best practices and pitfalls sections for real-world application
- **Prerequisite Knowledge**: Clear indication of what prior knowledge is expected
- **TL;DR Summaries**: Concise summaries at the beginning of major sections
- **Concept Difficulty Indicator**: Visual indicator of concept complexity
- **Categorized Resources**: Related resources organized by purpose (prerequisites, next steps, etc.)
- **Accessibility Focus**: Guidelines for ensuring content is accessible to all users

## How to Use

1. Copy the [conceptual-guide-template.md](./conceptual-guide-template.md) file
2. Follow the guidance in [conceptual-guide-template-guide.md](./conceptual-guide-template-guide.md)
3. Replace all placeholder text with your content
4. Add the appropriate metadata, including difficulty level
5. Create required diagrams for the "How It Works" section
6. Include TL;DR summaries for major sections
7. Replace placeholder content with your specific information
8. Review the example implementations for reference

## Example Implementations

- [Authentication Concepts](./examples/authentication-concepts.md) - Example of authentication concept documentation
- [Resource Areas Concept](./examples/resource-areas-concept.md) - Example of resource areas concept documentation

## Visual Elements

This template encourages the use of various visual elements:

- **Diagrams**: Required for the "How It Works" section (process flows, component relationships)
- **Tables**: For organizing relationships, comparisons, and terminology
- **TL;DR Boxes**: Highlighted summary boxes at the beginning of major sections
- **Difficulty Indicators**: Star rating (⭐ to ⭐⭐⭐) indicating concept complexity

## Accessibility Considerations

All documentation created with this template should follow accessibility best practices:

- Provide alternative text for all diagrams and images
- Ensure sufficient color contrast in visual elements
- Use proper heading hierarchy (don't skip levels)
- Make tables accessible with proper headers
- Use descriptive link text rather than "click here"

## See Also

- [Template Selection Guide](../template-selection-guide.md)
- [Shared Components](../shared-components/README.md)
- [Documentation Standards](../../README.md)
- [Visual Elements Library](../../visual-elements/README.md) 