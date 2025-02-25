# Headings and Structure Guidelines for Azure DevOps Node API Documentation

## Overview

This document provides guidance on creating clear, consistent, and logical document structures across the Azure DevOps Node API documentation. Well-structured documentation improves user comprehension, supports efficient information retrieval, and creates a cohesive documentation experience.

## Document Types and Their Structures

Different documentation types require tailored structures. Below are the standard structures for our main document types.

### API Reference Pages

API reference pages document specific classes, methods, and interfaces.

#### Structure for Class/Client Documentation

```markdown
# [ClassName] Client

Brief description of the client's purpose and functionality.

## Overview

Detailed explanation of the client's role, when to use it, and key concepts.

## Installation

How to install/import the client (if applicable).

## Initialization

How to initialize and configure the client.

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

#### Structure for Interface/Type Documentation

```markdown
# [InterfaceName] Interface

Brief description of the interface's purpose.

## Properties

For each property, include:
- Name
- Type
- Description
- Default value (if applicable)
- Example usage

## Usage Examples

Examples showing how to use the interface.

## Related Types

Links to related interfaces or classes.
```

### Conceptual Guides

Conceptual guides explain key concepts and provide in-depth understanding.

```markdown
# [Concept Name]

Brief introduction to the concept.

## Overview

Comprehensive explanation of the concept.

## Key Components

Breakdown of important sub-concepts or components.

### [Component 1]

Detailed explanation with examples.

### [Component 2]

Detailed explanation with examples.

## Best Practices

Guidance on effectively working with this concept.

## Common Scenarios

Real-world usage scenarios.

## Advanced Topics

Deeper exploration of complex aspects.

## Related Concepts

Links to related documentation.
```

### Tutorial/How-To Guides

Tutorials and how-to guides provide step-by-step instructions.

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

## Step N: [Final Task]

Detailed instructions with code examples.

## Verification

How to verify successful completion.

## Troubleshooting

Common issues and their solutions.

## Next Steps

Suggestions for what to learn or try next.

## Complete Example

Full working example combining all steps.
```

### Troubleshooting Pages

Troubleshooting pages help users solve common problems.

```markdown
# Troubleshooting [Feature/Area]

Brief overview of common issues in this area.

## Symptoms and Solutions

### Issue: [Problem Description]

- **Symptom**: What the user observes
- **Cause**: Why it happens
- **Solution**: Step-by-step resolution
- **Example**: Illustrative example

### Issue: [Another Problem]

...

## Preventative Measures

How to avoid common problems.

## Diagnostic Tools

Tools that help identify issues.

## Getting Additional Help

How to get assistance for unresolved issues.
```

## Heading Guidelines

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

### Section IDs and Linking

For documentation platforms that support section IDs:

- Use lowercase, hyphenated IDs that match the heading text
- Keep IDs stable across document revisions
- Include IDs for all H2 and H3 headings

Example:
```markdown
## Working with repositories {#working-with-repositories}
```

## Document Organization Principles

### Progression Patterns

Structure content in a logical progression using one of these patterns:

1. **Simple to Complex**: Start with basic concepts and progress to advanced topics
2. **General to Specific**: Begin with overview information and move to detailed specifics
3. **Chronological**: Present steps or processes in the order they occur
4. **Problem/Solution**: Present a problem followed by its solution
5. **Known to Unknown**: Start with familiar concepts as a foundation for new information

### Content Chunking

Organize content into digestible chunks:

- Group related information into distinct sections
- Aim for sections that cover one clear concept or task
- Use visual separation between content chunks
- Consider information hierarchy when organizing chunks

### Progressive Disclosure

Implement progressive disclosure to manage complex information:

- Present the most important information first
- Provide links to more detailed or advanced content
- Use expandable sections for optional details
- Ensure core content is accessible without requiring clicks

Example:
```markdown
## Basic authentication

[Essential information about basic authentication]

<details>
<summary>Advanced authentication scenarios</summary>

[Detailed content about edge cases and advanced options]
</details>
```

## Navigation and Wayfinding

### Table of Contents

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

### Orientation Cues

Include orientation cues to help readers understand where they are:

- Section numbering for sequential content
- Progress indicators for tutorials
- Breadcrumb references for hierarchical content
- "You are here" indicators within a larger process

Example:
```markdown
# Step 3 of 5: Configuring the client

[Previous: Installing dependencies](./step-2.md) | [Next: Making your first API call](./step-4.md)
```

### Visual Hierarchy

Reinforce structure through visual hierarchy:

- Use consistent heading styles
- Incorporate whitespace to group related content
- Use indentation to show relationships
- Apply visual treatments (such as background colors) to distinguish content types

## Document Templates

To ensure consistency, we provide templates for each document type:

- [API Client Template](./templates/api-client-template.md)
- [Interface Template](./templates/interface-template.md)
- [Conceptual Guide Template](./templates/conceptual-guide-template.md)
- [Tutorial Template](./templates/tutorial-template.md)
- [Troubleshooting Template](./templates/troubleshooting-template.md)

## Content Design Patterns

### Common Design Patterns

Use these established content design patterns:

#### Summary Boxes

Start complex articles with a summary:

```markdown
> **Summary**  
> This article explains how to authenticate with Azure DevOps using personal access tokens,
> OAuth, or Azure Active Directory. You'll learn how to create authentication handlers
> and initialize API clients securely.
```

#### Key Point Highlights

Highlight important information:

```markdown
> [!IMPORTANT]  
> Always store credentials securely. Never commit tokens or credentials to source control.
```

#### Step-by-Step Sequences

For procedures, use numbered lists with clear steps:

```markdown
1. Install the package:
   ```bash
   npm install azure-devops-node-api
   ```

2. Import the module:
   ```typescript
   import * as azdev from "azure-devops-node-api";
   ```

3. Create an authentication handler:
   ```typescript
   const authHandler = azdev.getPersonalAccessTokenHandler(token);
   ```
```

#### Comparison Tables

For comparing options or approaches:

```markdown
| Authentication Method | Use Case | Complexity | Security |
|:----------------------|:---------|:-----------|:---------|
| Personal Access Token | Simple scripts, personal use | Low | Medium |
| OAuth 2.0 | Multi-user applications | High | High |
| Azure AD | Enterprise applications | Medium | High |
```

## Document Length Guidelines

### Optimal Lengths

Follow these guidelines for document length:

- **API Method Documentation**: 300-800 words
- **Conceptual Guides**: 1000-3000 words
- **Tutorials**: 1500-4000 words
- **Troubleshooting Articles**: 500-2000 words

### Managing Long Content

For longer documents:

- Break into multiple pages when exceeding the upper length guidelines
- Use clear navigation between pages
- Consider creating "hub pages" that link to related content
- Use progressive disclosure techniques

## Special Section Types

### Introduction Sections

Start documents with a clear introduction:

- State the document's purpose
- Identify the target audience
- Outline prerequisites or assumptions
- Preview key takeaways
- Set expectations for what will be covered

### Prerequisite Sections

For tutorials and guides, clearly list prerequisites:

```markdown
## Prerequisites

Before beginning this tutorial, ensure you have:

- Node.js v12 or higher installed
- An Azure DevOps organization
- A personal access token with appropriate permissions
- Basic familiarity with TypeScript
```

### Version Information

Include version information when content is version-specific:

```markdown
> **Version note**  
> This documentation applies to version 7.0 and later of the Azure DevOps Node API.
> For earlier versions, see [legacy documentation](./legacy/index.md).
```

### Next Steps

End documents with clear guidance on what to do next:

```markdown
## Next steps

Now that you've configured authentication, you can:

- [Set up your first Git client](./git-client.md)
- [Learn about work item tracking](./work-item-tracking.md)
- [Explore build and release management](./build-release.md)
```

## Implementation Guidelines

### Document Creation Process

Follow this process when creating new documents:

1. Select the appropriate template for your document type
2. Fill in the document skeleton with required sections
3. Write content using the heading hierarchy guidelines
4. Review for structural consistency and completeness
5. Verify all links and references
6. Submit for peer review with focus on structure

### Document Review Checklist

When reviewing document structure, verify that:

- [ ] The document uses the correct template for its type
- [ ] Heading hierarchy is logical and follows guidelines
- [ ] All required sections are present
- [ ] Information flows in a logical progression
- [ ] Content chunking is appropriate
- [ ] Navigation aids are implemented where needed
- [ ] Special section types follow guidelines

## Examples

### Example: Well-Structured API Reference

```markdown
# GitClient

Client for interacting with Git repositories in Azure DevOps.

## Overview

The GitClient provides methods for creating, retrieving, and managing
Git repositories within Azure DevOps projects.

## Initialization

```typescript
// Get a GitClient instance
const gitClient = await connection.getGitApi();
```

## Methods

| Method | Description |
|:-------|:------------|
| getRepositories | Returns all repositories in a project |
| getRepository | Returns a specific repository |
| createRepository | Creates a new repository |

### getRepositories

▸ **getRepositories**(`project?`: string): *Promise‹GitRepository[]›*

Returns all Git repositories in a project or across the entire organization.

#### Parameters:

| Name | Type | Description |
|:-----|:-----|:------------|
| `project` | string | Optional. The project name or ID. If not specified, returns repositories across the entire organization. |

#### Returns:

*Promise‹GitRepository[]›*

A promise that resolves to an array of GitRepository objects.

#### Example:

```typescript
// Get all repositories in a project
const repositories = await gitClient.getRepositories("MyProject");

// Display repository names
repositories.forEach(repo => {
  console.log(`Repository: ${repo.name}`);
});
```

## Related Resources

- [GitRepository Interface](./git-repository.md)
- [Working with Git Repositories](../guides/git-repositories.md)
```

### Example: Well-Structured Tutorial

```markdown
# How to authenticate with Personal Access Tokens

Learn how to create and use Personal Access Tokens (PATs) to authenticate with the Azure DevOps Node API.

## Prerequisites

Before you begin, make sure you have:

- Node.js v12 or higher installed
- An Azure DevOps organization
- Permissions to create personal access tokens

## Step 1: Create a Personal Access Token

1. Sign in to your Azure DevOps organization
2. Click on your profile picture in the top right
3. Select "Personal access tokens"
4. Click "New Token"
5. Configure the token:
   - Name: Give your token a descriptive name
   - Organization: Select your organization
   - Expiration: Set an expiration date
   - Scopes: Select the required permissions

## Step 2: Install the Azure DevOps Node API

Install the package using npm:

```bash
npm install azure-devops-node-api
```

## Step 3: Create an authentication handler

Create a personal access token handler:

```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";

// Create a personal access token handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);
```

## Step 4: Initialize the API client

Use the authentication handler to initialize the API client:

```typescript
// Initialize the client
const connection = new azdev.WebApi(orgUrl, authHandler);

// Test the connection
const coreApi = await connection.getCoreApi();
const projects = await coreApi.getProjects();
console.log(projects.map(p => p.name));
```

## Verification

If your connection is successful, you should see a list of your projects in the console output.

## Troubleshooting

### Token is rejected

- Verify the token is active and hasn't expired
- Check that the token has the necessary permissions
- Ensure you're using the correct organization URL

## Next steps

Now that you've set up authentication with a Personal Access Token, you can:

- [Work with Git repositories](./git-repositories.md)
- [Manage work items](./work-items.md)
- [Set up build pipelines](./build-pipelines.md)

## Complete Example

Here's a complete example that puts everything together:

```typescript
import * as azdev from "azure-devops-node-api";

async function main() {
  try {
    const orgUrl = "https://dev.azure.com/your-organization";
    const token = "your-personal-access-token";
    
    // Create authentication handler
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    
    // Initialize the client
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Test the connection
    const coreApi = await connection.getCoreApi();
    const projects = await coreApi.getProjects();
    
    console.log("Connected successfully! Projects:");
    projects.forEach(project => {
      console.log(` - ${project.name}`);
    });
  } catch (error) {
    console.error("Connection failed:", error.message);
  }
}

main();
```