# Linking and Cross-Referencing Guidelines for Azure DevOps Node API Documentation

## Overview

This document establishes the standards for links and cross-references within the Azure DevOps Node API documentation. Consistent linking and cross-referencing improves navigation, helps users discover related content, and creates a cohesive documentation experience.

## Types of Links

The documentation uses several types of links, each with specific formatting and usage guidelines.

### Internal Links

Internal links connect to other pages within the Azure DevOps Node API documentation.

#### Basic Link Format

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

External links connect to resources outside the Azure DevOps Node API documentation.

#### Basic External Link Format

Use Markdown links with full URLs:

```markdown
[TypeScript documentation](https://www.typescriptlang.org/docs/)
```

#### External Link Indicators

Indicate when a link leads to an external site:

```markdown
[Azure DevOps REST API reference](https://docs.microsoft.com/rest/api/azure/devops/) (external)
```

#### External Link Best Practices

- Include the hostname in the link text when helpful
- Consider adding hover text for additional context
- Be selective about external links to reduce maintenance burden
- Verify external links regularly

Example with hover text:
```markdown
[Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md "Microsoft's guidelines for REST API design")
```

### Code Links

Code links connect to source code or code examples.

#### Repository Links

When linking to code in the repository:

```markdown
[WebApi class source code](https://github.com/microsoft/azure-devops-node-api/blob/master/api/WebApi.ts)
```

#### Specific Line Links

When linking to specific lines of code:

```markdown
[Authentication handler implementation](https://github.com/microsoft/azure-devops-node-api/blob/master/api/handlers/basiccreds.ts#L17-L29)
```

## Cross-Reference Patterns

Cross-references help users navigate between related concepts and API elements.

### API Element Cross-References

#### Class-to-Method References

When referencing methods from a class overview:

```markdown
## Available Methods

The GitClient class provides the following methods:

- [getRepositories](./git-client-methods.md#getrepositories): Retrieves all Git repositories
- [getRepository](./git-client-methods.md#getrepository): Retrieves a specific Git repository
- [createRepository](./git-client-methods.md#createrepository): Creates a new Git repository
```

#### Method-to-Class References

When referencing a class from a method documentation:

```markdown
### getWorkItems

This method is part of the [WorkItemTrackingClient](./work-item-tracking-client.md) class.
```

#### Parameter Type References

When referencing parameter types:

```markdown
| Parameter | Type | Description |
|:----------|:-----|:------------|
| `options` | [GitQueryOptions](./git-query-options.md) | Query options for filtering repositories |
```

#### Return Type References

When referencing return types:

```markdown
**Returns**

Promise<[GitRepository[]](./git-repository.md)>: A promise that resolves to an array of GitRepository objects.
```

### Concept Cross-References

#### Concept-to-API References

When referencing APIs from a concept page:

```markdown
## API Implementation

The following classes implement the authentication concepts described above:

- [BasicCredentialHandler](../api-reference/handlers/basic-credential-handler.md): For basic authentication
- [PersonalAccessTokenCredentialHandler](../api-reference/handlers/personal-access-token-handler.md): For PAT authentication
- [BearerTokenCredentialHandler](../api-reference/handlers/bearer-token-handler.md): For OAuth token authentication
```

#### API-to-Concept References

When referencing concepts from an API page:

```markdown
## Related Concepts

- [Authentication Overview](../concepts/authentication.md): Learn about authentication types and best practices
- [Resource Areas](../concepts/resource-areas.md): Understand how API resources are organized
```

### Tutorial Cross-References

#### Tutorial Steps

When referencing steps within a tutorial:

```markdown
Before continuing, make sure you've completed [Step 2: Setting up authentication](./tutorial-step-2.md).
```

#### Prerequisites

When referencing prerequisites:

```markdown
## Prerequisites

This tutorial requires knowledge of:

- [Basic authentication concepts](../concepts/authentication.md)
- [Node.js async/await patterns](../concepts/async-patterns.md)
```

## See Also Sections

The "See Also" section provides structured related links at the end of pages.

### Format

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

### Categorization

Group See Also links by category:

- **Related API References**: Links to related API documentation
- **Conceptual Guides**: Links to related conceptual documentation
- **Tutorials and Examples**: Links to tutorials and examples
- **External Resources**: Links to resources outside the documentation

### Scope

Include 3-7 links in the See Also section, prioritizing the most relevant content.

## Cross-Reference Table Format

For complex relationships between API elements, use reference tables:

### Method Reference Table

```markdown
| Method | Client | Related Methods | Related Types |
|:-------|:-------|:----------------|:--------------|
| getRepositories | GitClient | [getRepository](#getrepository) | [GitRepository](../types/git-repository.md) |
| createRepository | GitClient | [deleteRepository](#deleterepository) | [GitRepositoryCreateOptions](../types/git-repository-create-options.md) |
```

### Type Reference Table

```markdown
| Type | Used By | Extends | Related Types |
|:-----|:--------|:--------|:--------------|
| GitRepository | GitClient | Resource | [GitRef](../types/git-ref.md) |
| GitCommit | GitClient | - | [GitChange](../types/git-change.md) |
```

## Implementation Details

### Link Management

Manage links effectively:

- Use relative paths for internal links
- Use link checking tools to verify links
- Track external links for periodic validation
- Consider using link shorthand for frequently referenced pages

### Anchor Generation

Generate anchors consistently:

- Use lowercase for all anchors
- Replace spaces with hyphens
- Remove special characters
- Keep anchors stable across documentation revisions

For TypeScript/JavaScript method names that use camelCase, preserve the casing in anchors:

```markdown
## getWorkItemTypes {#getWorkItemTypes}
```

### Contextual Linking

Implement contextual links:

- Show related links based on the current page context
- Highlight the current page in navigation structures
- Use breadcrumbs to show the current location in the documentation hierarchy

## Linking Between Documentation Versions

Handle linking between documentation versions:

### Version-Specific Links

When linking to version-specific content:

```markdown
For version 6.x documentation, see [Authentication in v6](../v6/authentication.md).
```

### Version Selector

Implement a version selector that maintains the current page when switching versions.

## Special Link Types

### API Explorer Links

Link to the API Explorer for interactive examples:

```markdown
Try this API in the [interactive API Explorer](https://dev.azure.com/{organization}/_apis/explore?api=git/repositories/get).
```

### Sample Links

Link to code samples with specific formatting:

```markdown
See the [complete Git repository example](../samples/git-repositories.ts) for a working implementation.
```

### Downloadable Resource Links

Link to downloadable resources with file type and size indicators:

```markdown
Download the [API Client Cheat Sheet](../downloads/api-client-cheat-sheet.pdf) (PDF, 125KB)
```

## Best Practices

### Link Maintenance

Maintain links effectively:

- Review links during content updates
- Use link checking tools to find broken links
- Keep a centralized list of commonly linked resources
- Consider the impact of restructuring on existing links

### Link Density

Balance link density for readability:

- Avoid overloading paragraphs with too many links
- Generally limit to 1-2 links per paragraph
- Use bulleted lists for multiple related links
- Consider grouping related links in a dedicated section

### Progressive Disclosure

Use links for progressive disclosure:

- Link to detailed information rather than including everything in one page
- Use links to offer optional learning paths
- Link to prerequisite information rather than repeating it

## Cross-Reference Implementation

### Link Database

Maintain a link database for common references:

```json
{
  "gitClient": {
    "path": "../api-reference/git-client.md",
    "title": "GitClient reference"
  },
  "authentication": {
    "path": "../concepts/authentication.md",
    "title": "Authentication overview"
  }
}
```

### Automatic Cross-References

Implement automatic cross-references for API types and methods:

```markdown
When using the `GitClient` to retrieve repositories, you'll receive <ApiType name="GitRepository" /> objects in the response.
```

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

## Link Context and Relevance

### Providing Context

Include relevant context with links:

```markdown
See [Authentication handlers](../concepts/authentication-handlers.md) for details on how credential handlers are implemented.
```

### Link Annotations

Use annotations to add clarity to links:

```markdown
[Git REST API (v6.0)](https://docs.microsoft.com/rest/api/azure/devops/git/repositories/list?view=azure-devops-rest-6.0)
```

### Purpose Indicators

Indicate the purpose of links when helpful:

```markdown
[Personal Access Tokens (how to create)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)
```

## Cross-Referencing Checklist

Use this checklist when reviewing documentation for linking:

- [ ] Links have descriptive text that makes sense out of context
- [ ] Internal links use relative paths
- [ ] External links use full URLs and indicate they are external
- [ ] Links to API elements follow consistent formatting
- [ ] Section anchors follow the anchor generation rules
- [ ] "See Also" sections are categorized and relevant
- [ ] Links open in the appropriate context (same window for internal, new window option for external)
- [ ] All links have been verified to work
- [ ] Cross-references between related API elements are implemented

## Examples

### Example: Class Documentation with Cross-References

```markdown
# GitClient

Client for interacting with Git repositories in Azure DevOps.

## Overview

The GitClient provides methods for creating, retrieving, and managing Git repositories within Azure DevOps projects.

## Initialization

```typescript
// Get a GitClient instance
const gitClient = await connection.getGitApi();
```

## Methods

| Method | Description | Returns |
|:-------|:------------|:--------|
| [getRepositories](#getrepositories) | Returns all repositories in a project | Promise<[GitRepository[]](./git-repository.md)> |
| [getRepository](#getrepository) | Returns a specific repository | Promise<[GitRepository](./git-repository.md)> |
| [createRepository](#createrepository) | Creates a new repository | Promise<[GitRepository](./git-repository.md)> |

### getRepositories

▸ **getRepositories**(`project?`: string): *Promise‹[GitRepository[]](./git-repository.md)›*

Returns all Git repositories in a project or across the entire organization.

#### Related
- [getRepository](#getrepository): Get a single repository
- [GitRepository](./git-repository.md): Repository interface reference

## See Also

### Related Classes
- [WebApi Class](./web-api.md): Parent class for obtaining a GitClient instance

### Conceptual Guides
- [Working with Git repositories](../guides/git-repositories.md)
- [Git permissions and security](../concepts/git-permissions.md)

### Tutorials
- [Creating and managing repositories](../tutorials/repository-management.md)
```

### Example: Conceptual Guide with Cross-References

```markdown
# Authentication in Azure DevOps Node API

This guide explains the authentication mechanisms available in the Azure DevOps Node API.

## Overview

Before making API calls to Azure DevOps, you must authenticate using one of the supported methods.

## Authentication Methods

### Personal Access Token (PAT)

Personal Access Tokens are the simplest authentication method for personal or script usage.

```typescript
const authHandler = azdev.getPersonalAccessTokenHandler(token);
```

See [Creating a Personal Access Token](../tutorials/create-pat.md) for step-by-step instructions.

### Basic Authentication

Basic authentication uses a username and password.

```typescript
const authHandler = azdev.getBasicHandler(username, password);
```

> [!WARNING]
> Basic authentication with password is deprecated. Use PAT or OAuth instead.

### OAuth Authentication

OAuth is recommended for multi-user applications.

```typescript
const authHandler = azdev.getBearerHandler(oauthToken);
```

See [OAuth integration](../tutorials/oauth-integration.md) for implementation details.

## Implementing Authentication

All authentication methods create a credential handler that is passed to the WebApi constructor:

```typescript
const connection = new azdev.WebApi(orgUrl, authHandler);
```

## API References

The following classes implement authentication handling:

- [BasicCredentialHandler](../api-reference/handlers/basic-credential-handler.md)
- [PersonalAccessTokenCredentialHandler](../api-reference/handlers/personal-access-token-handler.md)
- [BearerTokenCredentialHandler](../api-reference/handlers/bearer-token-handler.md)

## See Also

### API References
- [WebApi class](../api-reference/web-api.md)
- [Credential handlers](../api-reference/handlers/index.md)

### Tutorials
- [Authentication sample code](../samples/authentication.md)
- [Setting up OAuth](../tutorials/oauth-setup.md)

### External Resources
- [Azure DevOps authentication documentation](https://docs.microsoft.com/azure/devops/integrate/get-started/authentication/authentication-guidance)
- [OAuth 2.0 specification](https://oauth.net/2/)
``` 