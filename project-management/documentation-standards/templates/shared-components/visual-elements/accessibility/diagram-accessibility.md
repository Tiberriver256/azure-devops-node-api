# Diagram Accessibility Guidelines

Accessible diagrams ensure that all users, including those with visual impairments or cognitive disabilities, can understand the information presented. These guidelines ensure our diagrams meet accessibility standards while maintaining their visual effectiveness.

## Core Principles

1. **Alternative Text**: Every diagram must have comprehensive alternative text
2. **Logical Structure**: Information should flow in a logical, understandable sequence
3. **Color Independence**: Meaning should not rely solely on color
4. **Contrast**: Sufficient contrast between elements is essential
5. **Simplicity**: Avoid unnecessary complexity that can overwhelm users

## Alternative Text Requirements

### Basic Requirements

Every diagram must include alternative text that:

1. Describes the purpose of the diagram
2. Explains key components and their relationships
3. Conveys the main insight or conclusion a viewer should take away
4. Follows a logical structure (typically top-to-bottom, left-to-right)
5. Is sufficiently detailed without being excessively lengthy

### Alt Text Formula

Use this formula for creating effective alt text:

```
[Diagram Type]: [Brief description of what the diagram shows] + [Key components and relationships] + [Main insight or conclusion]
```

### Examples

#### Poor Alt Text

```
"Diagram showing authentication flow."
```

#### Good Alt Text

```
"Flow diagram: Authentication process for Azure DevOps API showing three main steps: 1) Application creates credential handler with authentication information, 2) Handler adds authentication headers to HTTP requests, 3) Azure DevOps validates credentials and returns responses. Key insight: Authentication happens at the HTTP request level through the credential handler."
```

## Visual Design Guidelines

### Color and Contrast

1. **Minimum Contrast Ratio**: Maintain a 4.5:1 contrast ratio between text and background
2. **Color Redundancy**: Use multiple visual cues (shape, pattern, label, position) in addition to color
3. **Color Blindness Compatibility**: Test diagrams with color blindness simulation tools
4. **Limited Color Palette**: Use a maximum of 5-7 colors in a single diagram

### Text and Typography

1. **Minimum Text Size**: 12pt (16px) for body text in diagrams
2. **Sans-serif Fonts**: Use clear, sans-serif fonts like Arial, Calibri, or Segoe UI
3. **Bold for Emphasis**: Use bold for important labels instead of italics or color alone
4. **Text Contrast**: Ensure text has sufficient contrast against its background

### Layout and Structure

1. **Logical Flow**: Information should flow in a consistent direction (typically left-to-right, top-to-bottom)
2. **Grouping**: Related elements should be visually grouped together
3. **Whitespace**: Include sufficient whitespace to separate distinct elements
4. **Simplified Connections**: Minimize crossing lines and complex connections

## Types of Accessible Diagrams

### Flowcharts and Sequence Diagrams

For process flows and sequence diagrams:

1. Use clear directional arrows
2. Number steps sequentially
3. Use consistent shapes for similar actions
4. Include start and end points
5. Keep the flow in one direction when possible

### Component/Relationship Diagrams

For architecture or component diagrams:

1. Use distinct shapes for different component types
2. Label all connections to describe relationships
3. Group related components visually
4. Include a legend explaining symbols
5. Limit the number of components to avoid complexity

### Data Visualizations

For charts and data visualizations:

1. Include clear axis labels and units
2. Use patterns or textures in addition to colors
3. Directly label data points when possible instead of relying on legends
4. Include a title that explains the visualization's purpose
5. Avoid 3D effects that can distort data perception

## Implementation Techniques

### Creating Accessible Diagrams

1. Use Draw.io templates provided in this repository
2. Follow the visual style guidelines for consistency
3. Use the built-in accessibility checker in Draw.io
4. Export as SVG when possible for better scaling
5. Include the diagram source file (.drawio) for future editing

### Adding Alt Text in Documentation

When including diagrams in Markdown documentation:

```markdown
![Flow diagram showing the authentication process for Azure DevOps API. The flow starts with the application creating a credential handler, which then adds authentication headers to HTTP requests. These requests are sent to Azure DevOps, which validates the credentials and returns responses.](./images/authentication-flow.png)
```

## Testing Accessibility

Before finalizing a diagram, test its accessibility:

1. **Color Blindness**: Use tools like [Coblis](https://www.color-blindness.com/coblis-color-blindness-simulator/) to simulate various types of color blindness
2. **Screen Reader Test**: Have the alt text read by a screen reader to verify clarity
3. **Zoom Test**: Ensure the diagram remains clear when zoomed to 200%
4. **Text-Only Test**: Review if the alt text conveys the complete message without seeing the visual
5. **Contrast Checker**: Use tools like [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/) for text elements

## Common Pitfalls to Avoid

1. **Overcomplication**: Including too many elements or connections
2. **Relying on Color**: Using only color to distinguish between elements
3. **Poor Contrast**: Using similar colors for adjacent elements
4. **Tiny Text**: Using text too small to be read clearly
5. **Missing Context**: Not providing sufficient context in alt text
6. **Unexplained Symbols**: Using symbols or icons without explanation
7. **Complex Paths**: Creating elaborate, crossing connection paths

## Example: Before and After

### Before (Inaccessible)

A complex diagram with:
- Poor color contrast
- Meaning conveyed only through colors
- No clear flow direction
- Tiny, hard-to-read text
- Missing alt text

### After (Accessible)

An improved diagram with:
- High contrast between elements
- Multiple visual cues (color, shape, labels)
- Clear left-to-right flow
- Legible text size
- Comprehensive alt text
- Simplified connections

## Related Resources

- [Color Contrast Requirements](./color-contrast.md)
- [Screen Reader Compatibility](./screen-reader-compatibility.md)
- [Diagram Templates](../diagrams/)
- [Web Content Accessibility Guidelines (WCAG)](https://www.w3.org/WAI/standards-guidelines/wcag/)

---

[◀ Back to Visual Elements Library](../README.md) 