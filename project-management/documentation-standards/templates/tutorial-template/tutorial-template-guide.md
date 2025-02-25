# Tutorial Template Guide

This guide explains how to use the tutorial template to create user-centered, task-oriented documentation for the Azure DevOps Node API.

## Template Purpose

The tutorial template provides a standardized format for creating step-by-step guides that help users accomplish specific tasks using the Azure DevOps Node API. Unlike reference documentation that focuses on API features, tutorials focus on user goals and practical implementation.

Effective tutorials should:
1. Focus on user goals rather than API features
2. Provide complete, working code examples
3. Follow a progressive disclosure pattern (simple → complex)
4. Include visual aids that enhance understanding
5. Address common problems with troubleshooting guidance
6. Connect to other relevant documentation

## When to Use This Template

Use this template when:

- Creating a step-by-step guide for accomplishing a specific task
- Demonstrating how to solve a common user problem
- Showing how multiple API components work together
- Providing practical implementation examples that users can adapt
- Introducing new users to part of the API through a practical example

## Template Structure

The tutorial template has been organized into a multi-file structure for easier navigation and management. This approach offers several benefits:

1. **Reduced Cognitive Load**: Users can focus on one section at a time
2. **Easier Maintenance**: Writers can update individual sections without navigating a large file
3. **Better Navigation**: Clear navigation between sections improves the user experience
4. **Modular Reuse**: Sections can be reused or referenced across different tutorials

### Directory Structure

```
tutorial-name/
├── index.md                      # Main entry point with navigation
└── sections/
    ├── 01-header-overview.md     # Title, badges, introduction, and overview
    ├── 02-prerequisites.md       # Required software, accounts, and knowledge
    ├── 03-basic-implementation.md # Step-by-step guide for minimal example
    ├── 04-advanced-features.md   # Additional functionality
    ├── 05-common-scenarios.md    # Real-world use cases
    ├── 06-complete-example.md    # Full code example
    ├── 07-troubleshooting.md     # Common issues and solutions
    └── 08-next-steps-resources.md # Related documentation and further learning
```

### Navigation System

Each section file includes a consistent navigation footer:

```markdown
---

**Navigation**:
- [Back to Index](../index.md)
- Previous: [Previous Section](./previous-section.md)
- Next: [Next Section](./next-section.md)
```

This navigation system helps users move through the tutorial in a logical sequence while maintaining context.

## Using the Multi-File Template

To create a new tutorial using this template:

1. Create a new directory for your tutorial (e.g., `work-item-automation/`)
2. Copy the entire `tutorial-template` directory structure to your new directory
3. Edit the `index.md` file to reflect your tutorial's specific content
4. Edit each section file in the `sections/` directory
5. Update navigation links if you add, remove, or rename any sections
6. Ensure all internal links between sections are working correctly

## Template Customization

While the multi-file structure provides a standard organization, you can customize it based on your tutorial's needs:

- **Add New Sections**: Create additional section files for tutorial-specific content
- **Remove Sections**: If a section isn't relevant, remove it and update navigation links
- **Reorder Sections**: Change the order by renaming files and updating navigation links
- **Combine Sections**: For shorter tutorials, you might combine related sections

When customizing, always maintain the consistent navigation pattern to ensure a good user experience.

## Template Sections

### Header

```markdown
# Tutorial: {User Goal - Start with Action Verb}

![Tutorial difficulty: {Beginner/Intermediate/Advanced}](https://img.shields.io/badge/Level-{Beginner}-green)
![Estimated time: {X} minutes](https://img.shields.io/badge/Time-{15}%20minutes-blue)

{Brief introduction that clearly states the user goal this tutorial will help accomplish. Focus on the outcome rather than the API features used. Keep this to 2-3 sentences.}
```

- The title should start with "Tutorial:" followed by the user goal expressed with an action verb
  - Good examples: "Creating Work Items Programmatically" or "Automating Build Pipeline Management"
  - Poor examples: "Using the WorkItemTrackingApi" or "Build Pipeline API Tutorial"
- Include difficulty level and time estimate badges to help users gauge commitment
- The introduction should clearly state what the user will accomplish, not what API features they'll use

### Overview

```markdown
## Overview

{Provide a slightly more detailed explanation of what the user will accomplish and why it's valuable. Include:
- The problem this solves
- When to use this approach
- Key benefits
- A brief mention of the main Azure DevOps Node API components that will be used}

![Overview diagram showing the main components and flow](path/to/overview-diagram.png)
*Fig 1: Overview of the {process/workflow} covered in this tutorial*
```

- The overview should expand on the introduction and explain the value proposition
- Always include a visual diagram showing the workflow or components involved
- Keep this section concise (3-5 paragraphs maximum)
- Mention the main API components that will be used, but focus on what they help accomplish
- Diagrams should follow the [documentation visual style guide](link-to-style-guide)

### Prerequisites

```markdown
## Prerequisites

Before you begin, ensure you have:

- Node.js version 14 or higher installed
- Azure DevOps account with appropriate permissions
- {Any other specific requirements for this tutorial}

You should also be familiar with:
- Basic JavaScript/TypeScript
- {Other relevant knowledge areas}
```

- List all technical requirements (software, accounts, permissions)
- List knowledge prerequisites so users can determine if they're ready for this tutorial
- Be specific about versions and permission levels
- Link to setup guides where appropriate

### Basic Implementation

```markdown
## Basic Implementation

In this section, you'll create a minimal working example to {accomplish the basic goal}.

### Step 1: Set Up Your Project

...

### Step 2: Authenticate with Azure DevOps

...

### Step 3: {Implement Core Functionality}

...

### Step 4: Run Your Application

...
```

- Start with a minimal but complete implementation that accomplishes the core goal
- Break the implementation into clear, numbered steps (typically 3-7 steps)
- Each step should have a clear, action-oriented heading
- Include complete code snippets that can be copied and run with minimal modification
- Use inline comments to explain important parts of the code
- Use emoji icons for tips (💡), warnings (⚠️), and notes (🔍)
- Show expected output when running the code

### Adding Advanced Features

```markdown
## Adding Advanced Features

Now that you have a working basic implementation, let's enhance it with additional functionality.

### Feature 1: {Additional Functionality Name}

...

### Feature 2: {Error Handling and Retries}

...
```

- After the basic implementation, introduce additional features one at a time
- Each feature should build on the previous implementation
- Explain why each feature is valuable before showing the code
- Include diagrams for complex features
- Use a progressive approach: don't overwhelm with all advanced features at once

### Common Scenarios

```markdown
## Common Scenarios

### Scenario 1: {Common Use Case}

...

### Scenario 2: {Another Common Use Case}

...
```

- Provide 2-3 practical scenarios where the tutorial solution would be used
- For each scenario, provide a brief description and code example
- Focus on real-world use cases that users can relate to
- Adapt the basic or advanced implementation to these specific contexts

### Complete Example

```markdown
## Complete Example

Here's a complete example that combines all the concepts from this tutorial:

```typescript
// Complete example code...
```
```

- Provide a single, complete code example that includes all the features covered
- The example should be self-contained and runnable
- Include all imports, error handling, and configuration
- Use realistic variable names and values
- Include comments that explain each section

### Troubleshooting

```markdown
## Troubleshooting

### Common Issues

#### Problem: 401 Unauthorized Error When Connecting
**Solution:** Your personal access token may have expired...

#### Problem: Cannot Find Module Error
**Solution:** Ensure you have installed all the required packages...
```

- Structure as Problem/Solution pairs
- Include the exact error messages users might see
- Provide clear, actionable solutions for each problem
- Focus on issues specific to the tutorial's tasks
- Include 3-5 of the most common issues

### Next Steps

```markdown
## Next Steps

Now that you've learned how to {accomplish this user goal}, you might want to explore:

- [Tutorial: {Related Tutorial}](link-to-related-tutorial.md)
- [API Reference: {Related API}](link-to-api-reference.md)
- [Conceptual Guide: {Related Concept}](link-to-conceptual-guide.md)
```

- Suggest 3-5 logical next steps for users to explore
- Include a mix of tutorials, reference docs, and conceptual guides
- Briefly explain why each resource is relevant as a next step
- Ensure all links are valid and follow the linking standards

### Related Resources

```markdown
## Related Resources

- [Azure DevOps REST API Documentation](link)
- [Azure DevOps Node API GitHub Repository](link)
- [Stack Overflow: azure-devops-node-api Tag](link)
```

- List external resources that provide additional context or help
- Include official documentation, repositories, and community resources
- Ensure all links are valid and up-to-date

## Visual Elements

### Diagrams

Every tutorial should include at least two types of diagrams:

1. **Overview diagram**: Shows the overall workflow or architecture
2. **Feature diagrams**: Illustrate specific features or components

Guidelines for diagrams:
- Use consistent visual style following the [Diagram Style Guide](link-to-style-guide)
- Include clear labels and arrows showing flow direction
- Use color coding to distinguish different types of components
- Keep diagrams focused on the specific concepts being explained
- Include captions that explain what the diagram represents
- Ensure diagrams are accessible with proper alt text

### Code Formatting

Format code examples following these guidelines:

- Use syntax highlighting for all code blocks
- Include language identifier with code fences (\```typescript)
- Use meaningful variable and function names
- Include inline comments explaining "why," not just "what"
- Show expected console output where relevant
- Highlight important lines or sections within code blocks
- Format code according to standard style guidelines for the language

## Accessibility Considerations

Ensure your tutorial meets these accessibility requirements:

- All images and diagrams must have descriptive alt text
- Follow proper heading hierarchy (H1 > H2 > H3)
- Ensure code examples can be navigated via screen readers
- Provide text alternatives to any visual decision trees
- Use sufficient color contrast for any highlighted text
- Avoid conveying information through color alone
- Ensure any interactive elements are keyboard accessible

## Example Implementation

For a complete example of the tutorial template in use, see the ["Automating Work Item Creation from External Systems"](../examples/workitem-automation-tutorial.md) tutorial.

## Cross-Documentation Navigation

When writing tutorials, include proper cross-references to:

1. **API Reference Documentation**: Link to the reference docs for any classes, interfaces, or methods used in the tutorial
2. **Conceptual Documentation**: Link to relevant conceptual guides that explain underlying principles
3. **Related Tutorials**: Link to tutorials that build on or complement this one

This cross-referencing creates a cohesive documentation experience and helps users find related information easily.

## Tutorial Customization

While following the standard structure, you may customize the template based on the tutorial's complexity:

### Short Tutorials (15-30 minutes)
- Focus on a single core task
- Minimize advanced features (1-2 maximum)
- Use simpler diagrams
- Keep code examples concise

### Complex Tutorials (30-60 minutes)
- Include more detailed architecture diagrams
- Add more advanced features (3-5)
- Consider splitting implementation into multiple phases
- Include more extensive error handling
- Add more comprehensive troubleshooting section

### Integration Tutorials
For tutorials showing integration with external systems:
- Include architecture diagrams showing both systems
- Add authentication sections for both platforms
- Include detailed prerequisites for both systems
- Focus on data mapping between the systems 