# Documentation Technology Stack

## Overview

This document defines the technical stack for our Azure DevOps Node API documentation. Due to budget constraints, our documentation is hosted **exclusively as markdown files in GitHub** without additional hosting solutions.

## Core Technology Stack

- **Format**: GitHub Flavored Markdown (GFM)
- **Hosting**: GitHub Repository
- **Viewing Context**: GitHub UI (web interface)
- **Images**: Static PNG images stored in the repository
- **Diagrams**: Created externally, exported as PNG, and stored in the repository

## GitHub Markdown Capabilities and Limitations

### What We CAN Use

- **Standard Markdown Syntax**:
  - Headers (# for h1, ## for h2, etc.)
  - Emphasis (bold, italic)
  - Lists (ordered and unordered)
  - Links
  - Images
  - Code blocks (inline and fenced with syntax highlighting)
  - Blockquotes
  - Horizontal rules
  - Tables

- **GitHub-Specific Features**:
  - Task lists with checkboxes
  - Auto-linking of URLs
  - Strikethrough
  - Emoji shortcodes
  - Collapsible sections using `<details>` and `<summary>` HTML tags (the only HTML we generally allow)
  - Anchored headings for in-page navigation
  - Table of contents via links to heading anchors

### What We CANNOT Use

- **Custom HTML/CSS Styling**: GitHub sanitizes most HTML and strips CSS
- **JavaScript**: No interactive elements or scripting
- **Custom Background Colors**: GitHub does not support custom background colors in markdown
- **Complex Layout Controls**: No multi-column layouts or precise positioning
- **Custom Fonts or Typography**: Limited to GitHub's styling
- **Embedded Interactive Diagrams**: No interactive SVGs or diagram tools
- **Server-Side Processing**: No templates, variables, or dynamic content generation

## Best Practices for Our Constraints

1. **Use Native Markdown**: Rely on standard markdown features rather than HTML when possible
2. **Avoid HTML**: Generally avoid HTML in our markdown except for `<details>` and `<summary>` tags for collapsible sections
3. **Visual Distinction**: Use markdown's native formatting (bold, italics, blockquotes) for visual distinction
4. **Accessible Images**: Ensure all images have descriptive alt text
5. **Diagrams as Images**: Create diagrams in external tools and import as static images
6. **Progressive Disclosure**: Use GitHub's collapsible sections for progressive disclosure
7. **Consistent Structure**: Maintain consistent document structure for predictable navigation
8. **Link-Based Navigation**: Create clear navigation patterns using links between documents

## Example Implementations

### TL;DR Summary Boxes
Instead of styled boxes, use blockquotes with bold text:

```markdown
> **TL;DR:** This is a summary of the section.
```

### Collapsible Sections
Use GitHub's supported HTML tags for collapsible sections:

```markdown
<details>
<summary>Click to expand section</summary>

Content inside the collapsible section goes here.
Content can include standard markdown formatting.

</details>
```

### Tables for Structured Information
Use markdown tables for structured information:

```markdown
| Header 1 | Header 2 |
|----------|----------|
| Content 1 | Content 2 |
| Content 3 | Content 4 |
```

## Conclusion

While our technical constraints limit some visual and interactive features, we can still create effective documentation using GitHub's markdown capabilities. By focusing on clear structure, consistent formatting, and well-organized content, we can deliver high-quality documentation within these constraints.

All documentation standards and templates should adhere to these constraints. HTML examples or styling references that cannot be implemented in GitHub markdown should be removed or adapted to use GitHub-compatible alternatives. 