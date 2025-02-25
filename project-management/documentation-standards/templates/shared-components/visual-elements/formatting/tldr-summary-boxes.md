# TL;DR Summary Boxes

TL;DR (Too Long; Didn't Read) summary boxes provide quick, concise summaries of content for readers who need the essential information without reading the full section. These boxes help users quickly grasp key concepts while supporting those who need more detailed explanations.

## Purpose

TL;DR summary boxes serve to:

1. Provide at-a-glance summaries of complex sections
2. Support different reading styles and information needs
3. Improve scanability of documentation
4. Enhance the user experience for both novice and expert users

## Visual Style

TL;DR boxes use a consistent markdown-based format to ensure they're immediately recognizable throughout the documentation:

- **Format**: Blockquote with bold "TL;DR:" prefix
- **Text Format**: Bold "TL;DR:" followed by concise summary text
- **Position**: At the beginning of the relevant section, after the section heading
- **Optional Icon**: Information emoji (ℹ️) can be used at the beginning for additional visual distinction

## Implementation

### GitHub Markdown Implementation

```markdown
> **TL;DR:** Resource Areas help the API client find the correct URLs for services in Azure DevOps, making your code resilient to service location changes and enabling versioned access to API resources.
```

Renders as:

> **TL;DR:** Resource Areas help the API client find the correct URLs for services in Azure DevOps, making your code resilient to service location changes and enabling versioned access to API resources.

### Alternative Implementation (with emoji)

```markdown
> ℹ️ **TL;DR:** Resource Areas help the API client find the correct URLs for services in Azure DevOps, making your code resilient to service location changes and enabling versioned access to API resources.
```

## Usage Guidelines

### When to Use TL;DR Boxes

- At the beginning of major sections in conceptual guides
- For complex technical explanations that benefit from a simplified overview
- In sections longer than 3-4 paragraphs
- When introducing a new concept that builds on previous knowledge

### When Not to Use TL;DR Boxes

- For very short sections (less than 2 paragraphs)
- In procedural step-by-step guides
- For critical security or warning information (use warning boxes instead)
- In summaries or conclusions (which are already condensed)

### Writing Effective TL;DR Summaries

1. **Keep it brief**: 1-2 sentences, maximum 50 words
2. **Focus on the essential**: Include only the most critical information
3. **Use active voice**: Makes the summary more direct and engaging
4. **Avoid jargon**: Use simple language when possible
5. **Answer the "so what?"**: Explain why the concept matters
6. **Don't introduce new concepts**: Only summarize what's in the full section

## Examples

### Good Example

> **TL;DR:** Personal Access Tokens provide secure, scoped authentication for Azure DevOps API access while giving you control over token expiration and permissions.

### Poor Example (Too Detailed)

> **TL;DR:** Personal Access Tokens are security tokens that you can create in your Azure DevOps profile settings under Security. They allow you to authenticate with the API without using your password, and you can configure them with different scopes like read, write, or manage depending on what operations you need to perform. They also have configurable lifetimes so you can set them to expire after a specific period.

### Poor Example (Too Vague)

> **TL;DR:** PATs are tokens you can use with Azure DevOps.

## Section Placement

TL;DR boxes should be used in the following sections of the conceptual guide template:

- After the Introduction section
- At the beginning of the Overview section
- At the beginning of the Key Components section
- At the beginning of the How It Works section
- At the beginning of the Practical Applications section

## Accessibility Considerations

- Use the standard blockquote syntax to ensure proper semantic structure
- Keep TL;DR summaries concise and focused to benefit users with cognitive disabilities
- Ensure any emoji used are relevant and don't replace text content
- Maintain the consistent format to create predictable patterns for all users

## Related Resources

- [Warning and Note Boxes](./warning-note-boxes.md)
- [Conceptual Guide Template](../../conceptual-guide-template/README.md)
- [Documentation Tech Stack](../../../../docs-tech-stack.md)

---

[◀ Back to Visual Elements Library](../README.md) 