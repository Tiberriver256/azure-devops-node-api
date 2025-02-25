# Azure DevOps Node API Documentation Style Guide

## Introduction

This comprehensive style guide establishes the standards and conventions for all Azure DevOps Node API documentation. By following these guidelines, we ensure consistency, clarity, and usability across our documentation.

The primary audiences for this style guide are:
- Technical writers creating documentation
- API specialists contributing code examples and technical content
- External contributors submitting documentation changes
- Reviewers evaluating documentation quality

This guide covers voice and tone, formatting conventions, document structure, linking standards, terminology usage, and code example guidelines. These standards apply to all documentation types, including API references, conceptual guides, tutorials, and troubleshooting content.

## Table of Contents

1. [Voice and Tone](#voice-and-tone)
2. [Formatting Conventions](#formatting-conventions)
3. [Document Structure and Headings](#document-structure-and-headings)
4. [Linking and Cross-Referencing](#linking-and-cross-referencing)
5. [Terminology and Glossary](#terminology-and-glossary)
6. [Code Examples](#code-examples)
7. [Accessibility Guidelines](#accessibility-guidelines)
8. [Review Process](#review-process)

## Voice and Tone

### General Voice Principles

Our documentation speaks with the voice of a helpful, knowledgeable colleague who understands both the Azure DevOps ecosystem and the challenges of Node.js development.

#### Professional but Approachable

✅ **Do**:
- Use clear, direct language that respects the reader's intelligence
- Balance technical accuracy with accessibility
- Provide context and explanations that help developers understand not just how, but why

❌ **Don't**:
- Use overly casual language, slang, or cultural references
- Adopt an authoritative or condescending tone
- Oversimplify to the point of imprecision

#### Present Tense

Use present tense to describe how the API works, as this creates immediacy and clarity.

✅ **Do**:
- "The API returns a Promise that resolves to a WorkItem object."
- "When you create a new BuildDefinition, the system validates your inputs."

❌ **Don't**:
- "The API will return a Promise that will resolve to a WorkItem object."
- "When you have created a new BuildDefinition, the system will validate your inputs."

#### Active Voice

Active voice makes documentation clearer and more direct. It emphasizes who or what is performing the action.

✅ **Do**:
- "The GitClient retrieves repository information."
- "You must provide authentication credentials."

❌ **Don't**:
- "Repository information is retrieved by the GitClient."
- "Authentication credentials must be provided."

#### Direct Address

Address the reader directly as "you" to create a more engaging and personal experience.

✅ **Do**:
- "You can configure the client to use different authentication methods."
- "When you implement error handling, consider using try/catch blocks."

❌ **Don't**:
- "Users can configure the client to use different authentication methods."
- "When error handling is implemented, try/catch blocks should be considered."

### Technical Voice Guidelines

#### Balance Precision and Clarity

Technical documentation must be precise without becoming unnecessarily complex.

✅ **Do**:
- Use precise technical terms when they are the most accurate way to describe a concept
- Explain complex concepts with analogies or examples when appropriate
- Break down complex processes into manageable steps

❌ **Don't**:
- Use three technical terms where one would suffice
- Oversimplify technical concepts to the point of inaccuracy
- Introduce unnecessary jargon

#### Define Terms on First Use

When introducing specialized terminology, provide a clear definition on first use.

✅ **Do**:
- "Resource Areas are logical groupings of API resources that share routing and versioning."
- "The Promise pattern (an asynchronous programming model where operations return a Promise object representing a future result) is used throughout the API."

❌ **Don't**:
- Use specialized terms without definition
- Define the same term repeatedly throughout a document
- Provide overly technical definitions that require additional explanation

### Content-Specific Tone Adjustments

Different documentation types require slight adjustments in tone:

#### API Reference Documentation

- **Tone**: Precise, technical, comprehensive
- **Focus**: Accuracy, completeness, and clarity
- **Example**: "The `getRepositories` method accepts an optional `project` parameter of type `string` that filters results to a specific project. When omitted, repositories from all projects are returned."

#### Getting Started Guides

- **Tone**: Helpful, encouraging, step-by-step
- **Focus**: Successful first experiences and quick wins
- **Example**: "Let's create your first connection to Azure DevOps. This simple example will retrieve your profile information to confirm everything is working correctly."

#### Conceptual Guides

- **Tone**: Explanatory, educational, contextual
- **Focus**: Understanding core concepts and patterns
- **Example**: "Authentication in the Azure DevOps Node API follows a credential provider pattern. This flexible approach allows you to implement various authentication mechanisms while maintaining a consistent client interface."

#### Troubleshooting Content

- **Tone**: Empathetic, solution-oriented, practical
- **Focus**: Resolving common issues quickly and effectively
- **Example**: "If you're seeing 401 Unauthorized errors, first check that your Personal Access Token includes the appropriate scopes. The most common issue is missing 'read' permissions for the resource you're trying to access."

### Inclusive Language Guidelines

#### Gender-Neutral Language

Use gender-neutral language throughout all documentation.

✅ **Do**:
- "The developer can access their token from the profile page."
- Use role titles: "system administrator" not "system admin guy"

❌ **Don't**:
- Use "he," "she," "his," "her" when referring to unspecified individuals
- Use gendered terms like "guys," "mankind," or "manpower"

#### Accessibility in Language

Choose terms that are inclusive of people with disabilities.

✅ **Do**:
- "Select the option" rather than "Click the option"
- "Review the output" rather than "See the output"

❌ **Don't**:
- Use terms that assume specific physical abilities
- Use metaphors related to disability (e.g., "blind to the issue")

### Writing Style Elements

#### Contractions

Use contractions naturally to create a more conversational tone.

✅ **Do**:
- "You'll need to initialize a connection before making any API calls."
- "Don't forget to handle promise rejections."

❌ **Don't**:
- Avoid contractions entirely, which can make the tone overly formal
- Overuse contractions to the point of seeming unprofessional

#### Technical Abbreviations

Use standard technical abbreviations where appropriate, but define non-standard ones.

✅ **Do**:
- Use common abbreviations like API, SDK, UI without definition
- Define less common abbreviations on first use: "Project Collection Token (PCT)"
- Be consistent with abbreviation use throughout documentation

## Formatting Conventions

### Document Structure

#### Page Organization

Every documentation page should follow this general structure:

1. **Title** (H1): Clear, concise description of the content
2. **Introduction**: Brief overview of the page content
3. **Main Content**: Organized into logical sections with consistent heading hierarchy
4. **Related Information**: Links to related content (where applicable)

Example:
```markdown
# Work Item Tracking Client

A client for interacting with work items in Azure DevOps.

## Overview

The WorkItemTrackingClient provides methods to create, retrieve, update, and delete work items in Azure DevOps projects.

## Initialization

...

## Common Operations

...

## Related Information

- [Work Item Types Reference](./work-item-types.md)
- [Query Examples](./work-item-query-examples.md)
```

#### Section Length

Keep sections readable and focused:

- Aim for 300-500 words per section
- If a section exceeds 500 words, consider dividing it into subsections
- Ensure each section covers a single concept or topic
- Use visual breaks (headings, lists, code blocks) to improve scanability

### Text Formatting

#### Emphasis and Highlighting

Use formatting consistently to emphasize important content:

- **Bold** (`**text**`): Use for UI elements, important terms on first use, and strong emphasis
- *Italic* (`*text*`): Use for slight emphasis, book titles, and non-UI element names
- `Code Font` (`` `code` ``): Use for code, file names, parameters, properties, and methods
- ~~Strikethrough~~ (`~~text~~`): Use sparingly, only for deprecated features with replacement noted

Example:
```markdown
When you use the **Create Work Item** dialog, you must provide a `title` parameter. The *Azure DevOps Service* will then generate a unique identifier.
```

### Lists

Format lists consistently:

#### Bulleted Lists
- Use for unordered collections of items
- Use a hyphen (`-`) as the bullet character
- Maintain consistent capitalization and punctuation
- Nest lists with indentation (2 spaces)

Example:
```markdown
- Personal Access Token (PAT)
- OAuth 2.0
  - Authorization Code flow
  - Device Code flow
- Azure Active Directory
```

#### Numbered Lists
1. Use for sequential steps, prioritized items, or items referenced by number
2. Always start with 1
3. Markdown will handle the numbering automatically
4. Use consistent punctuation and capitalization

Example:
```markdown
1. Initialize the connection.
2. Create the client.
3. Call the API method.
4. Process the response.
```

### Tables

Format tables consistently:

- Include a header row
- Use alignment indicators for readability (`:---` for left, `:---:` for center, `---:` for right)
- Keep tables simple and focused
- Consider alternative formats if a table exceeds 5 columns

Example:
```markdown
| Method | Return Type | Description | Authentication |
|:-------|:------------|:------------|:---------------|
| getRepositories | Promise<GitRepository[]> | Returns all repositories | Required |
| getRepository | Promise<GitRepository> | Returns a specific repository | Required |
| createRepository | Promise<GitRepository> | Creates a new repository | Required |
```

### Code Examples

Format code examples consistently:

#### Inline Code
Use backticks for inline code references:
```markdown
Use the `getWorkItem()` method to retrieve a specific work item.
```

#### Code Blocks
Use fenced code blocks with language specification:

````markdown
```typescript
const workItem = await client.getWorkItem(123);
console.log(workItem.fields["System.Title"]);
```
````

### Callouts and Notes

Use consistent styling for callouts:

#### Note Callouts
For general information and tips:

```markdown
> [!NOTE]
> This method requires 'Read' permissions for the project.
```

#### Warning Callouts
For potential problems or important considerations:

```markdown
> [!WARNING]
> This operation permanently deletes data and cannot be undone.
```

#### Important Callouts
For critical information:

```markdown
> [!IMPORTANT]
> You must initialize the client before calling any methods.
```

#### Tip Callouts
For best practices and suggestions:

```markdown
> [!TIP]
> Use batch operations to create multiple work items efficiently.
```

### Images and Diagrams

Format images consistently:

- Include alt text for all images
- Use descriptive file names
- Specify width for larger images
- Include captions for complex images

Example:
```markdown
![API Client Hierarchy Diagram](../images/client-hierarchy.png "Hierarchy of Azure DevOps Node API clients")
*Figure 1: Relationship between WebApi client and service clients*
```

### TypeScript/Node.js Code Sample Formatting

For TypeScript and Node.js specific code examples:

- Use 2-space indentation
- Include semicolons at the end of statements
- Use single quotes for strings
- Use camelCase for variables and functions
- Use PascalCase for classes and interfaces
- Include proper type annotations
- Follow async/await patterns for Promise-based operations
- Include error handling for all asynchronous code

Example:
```typescript
async function getWorkItems(
  client: WorkItemTrackingApi, 
  ids: number[]
): Promise<WorkItem[]> {
  try {
    const workItems = await client.getWorkItems(
      ids, 
      undefined, 
      undefined, 
      WorkItemExpand.All
    );
    return workItems;
  } catch (error) {
    console.error('Failed to retrieve work items:', error.message);
    throw error;
  }
}
```

## Document Structure and Headings

### Heading Hierarchy

Maintain a logical heading structure:

1. **H1** (`#`): Document title - only one per page
2. **H2** (`##`): Major sections
3. **H3** (`###`): Subsections
4. **H4** (`####`): Topic details
5. **H5** (`#####`): Rarely used, only for deep hierarchies when necessary
6. **H6** (`######`): Almost never used

### Heading Formatting

Follow these conventions for heading text:

- Use sentence case (capitalize only the first word and proper nouns)
- Keep headings concise (ideally under 60 characters)
- Avoid punctuation at the end of headings
- Be descriptive and specific
- Maintain parallel structure within the same level of headings

#### Examples

✅ **Good headings**:
```markdown
# Git client reference
## Initializing the client
## Working with repositories
### Creating a repository
### Retrieving repository information
```

❌ **Poor headings**:
```markdown
# GIT CLIENT REFERENCE.
## How You Can Initialize the Client
## Repositories
### Create
### Getting Info About the Repository...
```

### Document Types and Their Structures

Different documentation types require tailored structures:

#### API Reference Pages

```markdown
# [ClassName] Client

Brief description of the client's purpose and functionality.

## Overview

Detailed explanation of the client's role, when to use it, and key concepts.

## Methods

Summary table of available methods.

### [MethodName]

For each method, include:
- Description
- Syntax/signature
- Parameters
- Return value
- Examples
- Exceptions/errors
- Notes/remarks

## Interfaces and Types

Documentation for related interfaces and types.

## Examples

Complete examples showing common usage patterns.

## Related Resources

Links to related documentation.
```

#### Tutorial/How-To Guides

```markdown
# How to [Accomplish Task]

Brief description of what the reader will accomplish.

## Prerequisites

What the reader needs before starting.

## Step 1: [First Task]

Detailed instructions with code examples.

## Step 2: [Second Task]

Detailed instructions with code examples.

...

## Verification

How to verify successful completion.

## Troubleshooting

Common issues and their solutions.

## Next Steps

Suggestions for what to learn or try next.

## Complete Example

Full working example combining all steps.
```

### Document Organization Principles

#### Progression Patterns

Structure content in a logical progression using one of these patterns:

1. **Simple to Complex**: Start with basic concepts and progress to advanced topics
2. **General to Specific**: Begin with overview information and move to detailed specifics
3. **Chronological**: Present steps or processes in the order they occur
4. **Problem/Solution**: Present a problem followed by its solution
5. **Known to Unknown**: Start with familiar concepts as a foundation for new information

#### Progressive Disclosure

Implement progressive disclosure to manage complex information:

- Present the most important information first
- Provide links to more detailed or advanced content
- Use expandable sections for optional details
- Ensure core content is accessible without requiring clicks

### Navigation and Wayfinding

#### Table of Contents

For longer documents (over 1000 words), include a table of contents:

```markdown
## Contents

- [Overview](#overview)
- [Installation](#installation)
- [Basic usage](#basic-usage)
  - [Authentication](#authentication)
  - [Making requests](#making-requests)
- [Advanced topics](#advanced-topics)
```

#### Orientation Cues

Include orientation cues to help readers understand where they are:

- Section numbering for sequential content
- Progress indicators for tutorials
- Breadcrumb references for hierarchical content
- "You are here" indicators within a larger process

## Linking and Cross-Referencing

### Types of Links

#### Internal Links

Use Markdown links with relative paths:

```markdown
[GitClient methods](../api-reference/git-client.md)
```

#### Section Links

When linking to a specific section within a document, use section anchors:

```markdown
[Authentication methods](./authentication.md#authentication-methods)
```

#### Link Text Best Practices

- Make link text descriptive and meaningful
- Avoid generic phrases like "click here" or "read more"
- Use the title of the target page when appropriate
- Keep link text concise (generally 2-5 words)
- Ensure link text makes sense out of context

✅ **Good link text**:
```markdown
For more information, see the [WorkItemTrackingClient reference documentation](./work-item-tracking-client.md).
```

❌ **Poor link text**:
```markdown
For more information, [click here](./work-item-tracking-client.md).
```

### External Links

Use Markdown links with full URLs and indicate when a link leads to an external site:

```markdown
[Azure DevOps REST API reference](https://docs.microsoft.com/rest/api/azure/devops/) (external)
```

### Cross-Reference Patterns

#### API Element Cross-References

When referencing methods from a class overview:

```markdown
## Available Methods

The GitClient class provides the following methods:

- [getRepositories](./git-client-methods.md#getrepositories): Retrieves all Git repositories
- [getRepository](./git-client-methods.md#getrepository): Retrieves a specific Git repository
- [createRepository](./git-client-methods.md#createrepository): Creates a new Git repository
```

#### Parameter Type References

When referencing parameter types:

```markdown
| Parameter | Type | Description |
|:----------|:-----|:------------|
| `options` | [GitQueryOptions](./git-query-options.md) | Query options for filtering repositories |
```

### See Also Sections

Place See Also sections at the end of documents, before any appendices:

```markdown
## See Also

### Related Documentation
- [Working with Git repositories](../guides/git-repositories.md)
- [Git client examples](../examples/git-examples.md)

### External Resources
- [Azure DevOps Git REST API](https://docs.microsoft.com/rest/api/azure/devops/git/)
- [Git concepts](https://git-scm.com/book/en/v2)
```

Group See Also links by category:

- **Related API References**: Links to related API documentation
- **Conceptual Guides**: Links to related conceptual documentation
- **Tutorials and Examples**: Links to tutorials and examples
- **External Resources**: Links to resources outside the documentation

### API Link Format

Standardize API link formats:

- **Classes**: `ClassName` class
- **Methods**: `ClassName.methodName()` method
- **Properties**: `ClassName.propertyName` property
- **Interfaces**: `InterfaceName` interface
- **Types**: `TypeName` type

Example:
```markdown
The `WebApi.getGitApi()` method returns a `GitClient` instance that you can use to access Git repositories.
```

## Terminology and Glossary

*Note: This section is a placeholder for the terminology guidelines that will be developed in the coming sprint.*

### Consistency Principles

- Use the same term for the same concept throughout the documentation
- Follow Microsoft and Azure DevOps terminology where applicable
- Use industry-standard terms for Node.js and TypeScript concepts
- Define terms on first use in each major document

### API-Specific Terms

*A comprehensive glossary of API-specific terms will be developed.*

### Standard Verbs

Use consistent verbs for actions:

- Use "create" (not "add" or "build") for creation operations
- Use "retrieve" or "get" for read operations
- Use "update" for modification operations
- Use "delete" or "remove" for deletion operations

## Code Examples

*Note: This section is a placeholder for the code example guidelines that will be developed in the coming sprint.*

### General Principles

- Ensure all code examples are accurate and tested
- Use TypeScript for all examples
- Include error handling for all asynchronous operations
- Show complete, working examples where possible

### Example Types

- **Snippets**: Short, focused examples showing a specific operation
- **Complete Examples**: End-to-end examples showing a complete workflow
- **Sample Applications**: More complex examples demonstrating real-world usage

## Accessibility Guidelines

### Content Accessibility

- Use proper heading hierarchy for screen readers
- Provide descriptive alt text for all images
- Ensure sufficient color contrast in diagrams and images
- Use descriptive link text (not "click here" or "more info")
- Structure tables with proper headers

### Technical Accessibility

- Format code for readability, including by screen readers
- Use appropriate semantic HTML when needed
- Avoid using color alone to convey meaning
- Provide text alternatives for all non-text content

## Review Process

### Documentation Review Levels

All documentation undergoes multiple levels of review:

1. **Technical Accuracy Review**: Performed by API specialists
2. **Editorial Review**: Ensures style and formatting compliance
3. **User Experience Review**: Ensures usability and completeness
4. **Final Review**: Overall quality assurance

### Review Checklist

When reviewing documentation, verify that:

- The content follows all style guide principles
- Technical information is accurate and complete
- Examples are working and follow best practices
- Structure follows the templates for the content type
- Links are functional and descriptive
- Accessibility requirements are met

## Implementation

This style guide will be implemented through:

1. Document templates for each content type
2. Automated linting tools for formatting checks
3. Review checklists for manual reviews
4. Regular style guide training for contributors
5. Periodic reviews and updates to the guide itself

## Appendix

### Style Guide Change Process

This style guide is a living document. To propose changes:

1. Submit a pull request with the proposed changes
2. Provide rationale for the changes
3. Include examples showing the improvement
4. The documentation team will review and approve/reject changes

### References

- [Microsoft Writing Style Guide](https://docs.microsoft.com/style-guide/welcome/)
- [Google Developer Documentation Style Guide](https://developers.google.com/style)
- [TypeScript Documentation Guidelines](https://github.com/microsoft/TypeScript-Website/blob/v2/style-guide.md)
- [Azure DevOps Documentation](https://docs.microsoft.com/azure/devops/) 