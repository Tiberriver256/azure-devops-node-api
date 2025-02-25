# Conceptual Guide Template Guide

This document provides guidance on how to use the Conceptual Guide Template effectively. Conceptual guides explain abstract concepts, architectures, and design principles without focusing on specific code implementations.

## Purpose

The Conceptual Guide Template serves several important purposes:

1. **Explain abstract concepts** related to Azure DevOps and its APIs
2. **Provide context and background** for developers working with the API
3. **Help users understand "why"** rather than just "how"
4. **Establish foundations** for more specific documentation
5. **Bridge knowledge gaps** between different API features

## When to Use

Use the Conceptual Guide Template when:

- Explaining the architecture or design of Azure DevOps systems
- Documenting important concepts that span multiple API components
- Introducing terminology and mental models needed to understand the API
- Providing background information necessary for effective API usage
- Explaining relationships between different system components

For step-by-step instructions, use the [Tutorial Template](../tutorial-template/) instead. For documenting specific API methods or classes, use the appropriate API template.

## Template Structure

The Conceptual Guide Template includes the following sections:

1. **Title**: Clear, concise name of the concept
2. **Metadata**: Hidden structured information about the document
3. **Difficulty Level**: Visual indicator of concept complexity
4. **Introduction**: Brief introduction to the concept
5. **TL;DR Summary**: Ultra-concise summary of the concept
6. **What You Need to Know**: Prerequisites and background knowledge
7. **Overview**: Comprehensive explanation of the concept and its importance
8. **Key Terminology**: Definitions of important terms used in the document
9. **Key Components**: Breakdown of major elements or components
10. **How It Works**: Detailed explanation of the concept's functioning
11. **Practical Applications**: Real-world usage scenarios
12. **Relationship to Other Components**: How this concept connects to other parts of the system
13. **Version Considerations**: Version-specific information (if applicable)
14. **Best Practices**: Guidance on effective usage
15. **Common Pitfalls**: Issues to avoid when working with this concept
16. **Code Representations**: (Optional) Code examples that illustrate the concept
17. **Advanced Topics**: (Collapsible) In-depth technical details
18. **Related Resources**: Categorized links to related documentation
19. **Feedback**: Information on how to provide feedback

## How to Fill Out the Template

### Metadata and Difficulty Level

Start with metadata and difficulty indicators:

```markdown
<!-- 
Metadata:
- Difficulty: Intermediate ⭐⭐
- Related API Components: WebApi, WorkItemTracking
- Key Use Cases: Authentication, Custom Clients
- API Version Applicability: v6.0+
-->

**Difficulty Level: Intermediate ⭐⭐**
```

The difficulty levels are:
- **Basic ⭐**: Foundational concepts requiring minimal prior knowledge
- **Intermediate ⭐⭐**: Concepts that build on basic knowledge
- **Advanced ⭐⭐⭐**: Complex concepts requiring understanding of multiple related areas

### Title and Introduction

Follow with a clear title and concise introduction:

```markdown
# Authentication Concepts

Azure DevOps authentication mechanisms provide secure access to resources while supporting multiple identity providers and access methods.

> **TL;DR:** Authentication verifies user identity before allowing access to Azure DevOps resources, with multiple methods available based on your scenario.
```

### What You Need to Know

Specify prerequisites clearly:

```markdown
## What You Need to Know

Before diving into this concept, you should be familiar with:

- **Azure DevOps Basics**: Understanding of Azure DevOps organization structure
- **HTTP Communication**: Familiarity with HTTP requests and responses
- **Node.js**: Basic knowledge of Node.js programming
```

### Overview and Key Terminology

Provide a comprehensive explanation and define key terms:

```markdown
## Overview

> **TL;DR:** Authentication is critical for securing Azure DevOps API access and supports various methods appropriate for different scenarios.

Authentication in Azure DevOps is the process of verifying the identity of users and services before granting access to resources. The Azure DevOps Node API supports multiple authentication methods, allowing developers to choose the approach that best fits their security requirements and operational constraints.

Understanding authentication is critical for developing secure applications that interact with Azure DevOps APIs, as different authentication methods provide different security guarantees and are suitable for different usage scenarios.

## Key Terminology

| Term | Definition |
|------|------------|
| Authentication | The process of verifying identity |
| Authorization | The process of determining what actions an authenticated user can perform |
| PAT | Personal Access Token, a type of authentication credential |
| OAuth | An open standard for access delegation |
| Bearer Token | A type of token used in OAuth authentication |
```

### Key Components

Break down the concept into elements:

```markdown
## Key Components

> **TL;DR:** Authentication involves identity providers, tokens, scopes, handlers, and connections working together to secure API access.

- **Identity Providers**: Authentication systems that verify user identities (Microsoft Account, Azure AD, etc.)
- **Tokens**: Secure credentials used to authenticate API requests
- **Scopes**: Permission sets that define what actions can be performed
- **Handlers**: Objects in the Node API that encapsulate authentication logic
- **Connection**: The authenticated session established with Azure DevOps services
```

### How It Works

Explain mechanisms and processes in detail, including a required diagram:

```markdown
## How It Works

> **TL;DR:** Authentication follows a flow where credentials from an identity provider are passed through a handler to establish an authenticated connection.

Authentication with the Azure DevOps API typically follows these steps:

1. The client obtains credentials from an identity provider
2. The client includes these credentials with API requests
3. The Azure DevOps service validates the credentials
4. If valid, the service processes the request and returns a response

```
┌──────────────┐     ┌───────────────┐     ┌─────────────┐     ┌──────────────┐
│              │     │               │     │             │     │              │
│    Client    │────▶│  Auth Handler │────▶│ WebApi Conn │────▶│ Azure DevOps │
│ Application  │     │               │     │             │     │   Services   │
│              │     │               │     │             │     │              │
└──────────────┘     └───────────────┘     └─────────────┘     └──────────────┘
      │                                                               │
      │                                                               │
      │                        Response                               │
      └───────────────────────────────────────────────────────────────┘
```

### Practical Applications and Relationships

Describe real-world scenarios and relationships:

```markdown
## Practical Applications

> **TL;DR:** Authentication methods vary by scenario - PATs for scripts, OAuth for web apps, and service principals for server-to-server communication.

### Common Use Cases

- **Automated Build Scripts**: Use service principal authentication for CI/CD pipelines
- **Web Applications**: Use OAuth to allow users to authorize actions in their projects
- **Command-Line Tools**: Use Personal Access Tokens for simple scripting scenarios

## Relationship to Other Components

| Related Component | Relationship |
|-------------------|--------------|
| Authorization | Authentication establishes identity; authorization determines access rights |
| WebApi Client | The client must be configured with appropriate authentication credentials |
| Resource Areas | Different resource areas may have different authentication requirements |
```

### Version Considerations

Document version-specific information when relevant:

```markdown
## Version Considerations

This concept applies to:
- Azure DevOps Node API v5.0+
- Azure DevOps REST API v5.0+

In versions prior to 5.0, bearer token authentication required different initialization patterns. Version 6.0 added support for managed identity authentication.

### Version-Specific Examples

```typescript
// Example for version 4.x
const authHandler4 = azdev.getPersonalAccessTokenHandler("your-pat-token");
const connection4 = new azdev.WebApi("https://dev.azure.com/organization", authHandler4);

// Example for version 6.x
const authHandler6 = azdev.getManagedIdentityHandler();
const connection6 = new azdev.WebApi("https://dev.azure.com/organization", authHandler6);
```
```

### Best Practices and Common Pitfalls

Provide guidance and warnings:

```markdown
## Best Practices

- **Use the appropriate authentication method** for your scenario
- **Store credentials securely** using environment variables or secure storage
- **Implement token refresh logic** for OAuth tokens
- **Request minimal scopes** needed for your application

## Common Pitfalls

| Pitfall | Cause | Solution |
|---------|-------|----------|
| Expired tokens | Tokens have a limited lifetime | Implement refresh logic or token renewal |
| Insufficient permissions | Requesting incorrect scopes | Request appropriate scopes for your needs |
| Credentials in source code | Hardcoding secrets | Use environment variables or secret management |
```

### Code Representations

Include code examples when helpful:

```markdown
## Code Representations

```typescript
// Authenticate using Personal Access Token
const authHandler = azdev.getPersonalAccessTokenHandler("your-pat-token");
const connection = new azdev.WebApi("https://dev.azure.com/organization", authHandler);

// Authenticate using OAuth token
const authHandler = azdev.getBearerHandler("your-oauth-token");
const connection = new azdev.WebApi("https://dev.azure.com/organization", authHandler);
```
```

### Advanced Topics

Use collapsible sections for advanced information:

```markdown
## Advanced Topics

<details>
<summary>Click to expand advanced information</summary>

### Token Lifetimes and Renewal

Personal Access Tokens can be configured with different lifetimes...

### Service Principal Authentication

For server-to-server scenarios, service principals provide...

</details>
```

### Related Resources and Feedback

Categorize related documentation and include feedback mechanism:

```markdown
## Related Resources

### Prerequisites
- [Authentication Overview](../authentication/overview.md)
- [Azure DevOps Basics](../getting-started/basics.md)

### Next Steps
- [OAuth Implementation Guide](../authentication/oauth-guide.md)
- [Using the WebApi Client](../core-concepts/webapi-client.md)

### Alternative Approaches
- [REST API Authentication](../advanced/rest-authentication.md)
- [Command Line Authentication](../tools/command-line-auth.md)

### External Documentation
- [Microsoft Identity Platform](https://docs.microsoft.com/en-us/azure/active-directory/develop/)
- [OAuth 2.0 Documentation](https://oauth.net/2/)

## Feedback

Have feedback on this documentation? Please submit an issue on our [GitHub repository](https://github.com/your-org/azure-devops-node-api).
```

## Visual Elements

### Diagrams

Diagrams are required for the "How It Works" section and should illustrate:
- System architecture
- Process flows
- Component relationships
- Decision trees

Use tools like Draw.io, Mermaid, or PlantUML to create diagrams that:
1. Are visually clear with appropriate sizing
2. Use consistent visual language across documentation
3. Include text labels for all components
4. Provide alternative text descriptions for accessibility

### TL;DR Summaries

Include highlighted TL;DR boxes at the beginning of major sections:

```markdown
> **TL;DR:** Authentication verifies user identity before allowing access to Azure DevOps resources, with multiple methods available based on your scenario.
```

Format TL;DR summaries as blockquotes with bold "TL;DR:" prefix for visual distinction.

### Tables

Use tables to organize:
- Comparisons between related concepts
- Feature matrices
- Terminology definitions
- Relationship definitions

Ensure tables have clear headers and consistent formatting:

```markdown
| Term | Definition |
|------|------------|
| Authentication | The process of verifying identity |
| Authorization | The process of determining what actions an authenticated user can perform |
```

### Collapsible Sections

Use GitHub's supported HTML tags for collapsible sections:

```markdown
<details>
<summary>Click to expand advanced information</summary>

Content that might be too detailed for beginners but valuable for advanced users.

</details>
```

## Accessibility Guidelines

Ensure your documentation is accessible to all users:

1. **Alternative Text**: Provide descriptive alternative text for all diagrams and images
2. **Legible Content**: Focus on clear, readable text with appropriate formatting
3. **Semantic Structure**: Use proper heading hierarchy (don't skip levels)
4. **Descriptive Links**: Use meaningful text for links rather than "click here"
5. **Table Headers**: Always include headers in tables

Example of providing alternative text for a diagram:

```markdown
![Authentication flow diagram showing the process of client application connecting to Azure DevOps through authentication handlers](./images/auth-flow.png)
```

## Style Guidelines

### Language

- Use clear, straightforward language
- Define technical terms when first introduced
- Maintain a professional, instructional tone
- Use present tense and active voice
- Focus on explaining "why" in addition to "what"

### Organization

- Present information from general to specific
- Group related concepts together
- Use headings to create a clear information hierarchy
- Begin with foundational information before advanced topics
- Use the "progressive disclosure" principle

## Content Balance

Maintain an appropriate balance between different types of content:

- **Conceptual explanation**: 60-70% of content
- **Code examples**: 15-20% of content
- **Visual elements**: 10-15% of content
- **Related resources**: 5-10% of content

This balance ensures the document thoroughly explains concepts while providing practical implementation guidance.

## Example

See the [Authentication Concepts](./examples/authentication-concepts.md) example for a complete implementation of this template.

## Additional Resources

- [Shared Components](../shared-components/README.md)
- [Template Selection Guide](../template-selection-guide.md)
- [Documentation Standards](../../README.md)
- [Visual Elements Library](../../visual-elements/README.md)

---

[◀ Back to Documentation Templates](../README.md) | [▲ Back to Top](#conceptual-guide-template-guide) 