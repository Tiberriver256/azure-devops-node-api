# Code Example Style Guide

**Navigation**: [Home](./index.md) > Code Example Style Guide

This style guide provides standards for creating consistent, high-quality code examples across the Azure DevOps Node API documentation. Following these guidelines ensures that code examples are easy to understand, follow best practices, and provide a consistent experience for users.

## General Principles

1. **Clarity over cleverness**: Write code that is easy to understand, even if it's slightly more verbose.
2. **Completeness**: Include all necessary imports, setup, and error handling.
3. **Consistency**: Use consistent naming, formatting, and patterns across all examples.
4. **Practicality**: Examples should demonstrate real-world usage scenarios.
5. **Progressive disclosure**: Start with simple examples and build to more complex ones.

## Code Example Structure

### Imports and Setup

Begin each example with the necessary imports and basic setup:

```typescript
// Import the Azure DevOps Node API package
import * as azdev from "azure-devops-node-api";

// Organization URL and authentication information
const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";

// Create authentication handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Create WebApi instance
const connection = new azdev.WebApi(orgUrl, authHandler);
```

### API Client Initialization

Use consistent variable names for API clients:

```typescript
// Get Git API client
const gitApi = await connection.getGitApi();

// Get Work Item Tracking API client
const workItemTrackingApi = await connection.getWorkItemTrackingApi();

// Get Build API client
const buildApi = await connection.getBuildApi();
```

### Function Structure

Wrap example code in async functions with proper error handling:

```typescript
async function getRepositories(projectName: string) {
    try {
        // Get Git API client
        const gitApi = await connection.getGitApi();
        
        // Get repositories from the project
        const repositories = await gitApi.getRepositories(projectName);
        
        console.log(`Found ${repositories.length} repositories in ${projectName}`);
        return repositories;
    } catch (error) {
        // Provide specific error handling
        console.error(`Error getting repositories: ${error.message}`);
        throw error;
    }
}
```

### Self-Executing Functions

For complete examples, use self-executing async functions:

```typescript
(async () => {
    try {
        const projectName = "YourProject";
        const repositories = await getRepositories(projectName);
        
        // Process the results
        repositories.forEach(repo => {
            console.log(`Repository: ${repo.name}`);
        });
    } catch (error) {
        console.error("Error in main process:", error.message);
    }
})();
```

## Naming Conventions

### Variables

Use descriptive, consistent variable names:

| Type | Convention | Example |
|------|------------|---------|
| API Clients | `{serviceName}Api` | `gitApi`, `workItemTrackingApi` |
| Organization URL | `orgUrl` | `const orgUrl = "https://dev.azure.com/your-organization"` |
| Personal Access Token | `token` | `const token = "your-personal-access-token"` |
| Authentication Handler | `authHandler` | `const authHandler = azdev.getPersonalAccessTokenHandler(token)` |
| Connection | `connection` | `const connection = new azdev.WebApi(orgUrl, authHandler)` |
| Project Name | `projectName` | `const projectName = "YourProject"` |
| Repository | `repository` or `repo` | `const repository = await gitApi.getRepository(repoId)` |
| Work Item | `workItem` | `const workItem = await workItemTrackingApi.getWorkItem(id)` |
| Build | `build` | `const build = await buildApi.getBuild(buildId)` |
| Collections | Plural noun | `repositories`, `workItems`, `builds` |

### Functions

Use descriptive function names that indicate the action and the object:

```typescript
async function getWorkItem(id: number) { /* ... */ }
async function createPullRequest(repoId: string, sourceBranch: string, targetBranch: string) { /* ... */ }
async function queueBuild(definitionId: number) { /* ... */ }
```

## Comments

### Comment Style

Use descriptive comments that explain the "why" not just the "what":

```typescript
// Get repositories from the specified project
// This allows us to list all Git repositories the user has access to
const repositories = await gitApi.getRepositories(projectName);

// Filter repositories to only include those with "api" in the name
// This helps narrow down repositories related to API development
const apiRepos = repositories.filter(repo => repo.name.toLowerCase().includes('api'));
```

### Section Comments

Use section comments to divide longer examples into logical sections:

```typescript
// 1. Setup and authentication
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// 2. Get API clients
const gitApi = await connection.getGitApi();
const workItemTrackingApi = await connection.getWorkItemTrackingApi();

// 3. Retrieve data
const repositories = await gitApi.getRepositories(projectName);
const workItems = await workItemTrackingApi.getWorkItems([1, 2, 3]);

// 4. Process and display results
console.log(`Found ${repositories.length} repositories and ${workItems.length} work items`);
```

## Error Handling

### Basic Error Handling

Always include try/catch blocks with specific error handling:

```typescript
try {
    const workItem = await workItemTrackingApi.getWorkItem(id);
    console.log(`Work Item: ${workItem.id} - ${workItem.fields["System.Title"]}`);
} catch (error) {
    if (error.statusCode === 404) {
        console.error(`Work item ${id} not found`);
    } else if (error.statusCode === 401) {
        console.error("Authentication error. Check your credentials or token.");
    } else {
        console.error(`Error getting work item: ${error.message}`);
    }
}
```

### Specific Error Types

For more complex examples, handle specific error types:

```typescript
try {
    // API call here
} catch (error) {
    if (error.statusCode) {
        // Handle REST API errors
        switch (error.statusCode) {
            case 400:
                console.error("Bad request. Check your parameters:", error.message);
                break;
            case 401:
                console.error("Authentication error. Check your credentials or token.");
                break;
            case 403:
                console.error("Authorization error. You don't have permission to perform this action.");
                break;
            case 404:
                console.error("Resource not found. Check your project or repository name.");
                break;
            case 429:
                console.error("Too many requests. You've been rate limited.");
                break;
            default:
                console.error(`Server error (${error.statusCode}): ${error.message}`);
        }
    } else if (error.code === 'ECONNREFUSED') {
        console.error("Connection refused. Check your network or proxy settings.");
    } else if (error.code === 'ETIMEDOUT') {
        console.error("Connection timed out. Check your network or try again later.");
    } else {
        console.error("Unexpected error:", error.message);
    }
}
```

## TypeScript vs JavaScript

### Prefer TypeScript

Prefer TypeScript examples with proper type annotations:

```typescript
import * as azdev from "azure-devops-node-api";
import * as GitInterfaces from "azure-devops-node-api/interfaces/GitInterfaces";

async function getPullRequests(
    repositoryId: string,
    status: GitInterfaces.PullRequestStatus = GitInterfaces.PullRequestStatus.Active
): Promise<GitInterfaces.GitPullRequest[]> {
    try {
        const gitApi = await connection.getGitApi();
        
        const searchCriteria: GitInterfaces.GitPullRequestSearchCriteria = {
            repositoryId,
            status
        };
        
        return await gitApi.getPullRequests(repositoryId, searchCriteria);
    } catch (error) {
        console.error(`Error getting pull requests: ${error.message}`);
        throw error;
    }
}
```

### JavaScript Alternative

When providing JavaScript examples, include comments that explain types:

```javascript
async function getPullRequests(repositoryId, status = 1 /* Active */) {
    try {
        const gitApi = await connection.getGitApi();
        
        // Create search criteria object with repository ID and status
        const searchCriteria = {
            repositoryId: repositoryId,
            status: status // 1 = Active, 2 = Abandoned, 3 = Completed
        };
        
        return await gitApi.getPullRequests(repositoryId, searchCriteria);
    } catch (error) {
        console.error(`Error getting pull requests: ${error.message}`);
        throw error;
    }
}
```

## Code Introduction

Use consistent phrasing to introduce code examples:

### For Basic Examples

"Here's how to [perform action]:"

```typescript
// Example code here
```

### For Complete Examples

"The following example demonstrates how to [perform action]:"

```typescript
// Example code here
```

### For Complex Examples

"This complete example shows how to [perform action], including [additional context]:"

```typescript
// Example code here
```

## Security Best Practices

### Handling Credentials

Never hardcode credentials in examples:

```typescript
// DON'T do this
const token = "actual-token-value";

// DO this instead
const token = process.env.AZURE_DEVOPS_PAT || "your-personal-access-token";
```

Include security notes:

```typescript
// Security Note: In production, always store tokens in environment variables
// or a secure secret management solution, not in your code.
const token = process.env.AZURE_DEVOPS_PAT;
if (!token) {
    throw new Error("Personal Access Token not found. Set the AZURE_DEVOPS_PAT environment variable.");
}
```

## Example Types

### Minimal Examples

For API reference documentation, provide concise examples focused on a single method:

```typescript
// Get a single work item
const workItem = await workItemTrackingApi.getWorkItem(42);
console.log(`Work Item #${workItem.id}: ${workItem.fields["System.Title"]}`);
```

### Complete Examples

For tutorials and guides, provide complete examples with imports, setup, and error handling:

```typescript
import * as azdev from "azure-devops-node-api";

async function getWorkItem(id: number) {
    try {
        // Setup connection
        const orgUrl = "https://dev.azure.com/your-organization";
        const token = process.env.AZURE_DEVOPS_PAT || "your-personal-access-token";
        const authHandler = azdev.getPersonalAccessTokenHandler(token);
        const connection = new azdev.WebApi(orgUrl, authHandler);
        
        // Get Work Item Tracking API client
        const workItemTrackingApi = await connection.getWorkItemTrackingApi();
        
        // Get work item
        const workItem = await workItemTrackingApi.getWorkItem(id);
        console.log(`Work Item #${workItem.id}: ${workItem.fields["System.Title"]}`);
        return workItem;
    } catch (error) {
        console.error(`Error getting work item ${id}:`, error.message);
        throw error;
    }
}

// Usage
(async () => {
    try {
        const workItem = await getWorkItem(42);
        console.log(`State: ${workItem.fields["System.State"]}`);
    } catch (error) {
        console.error("Error:", error.message);
    }
})();
```

### Complex Integration Examples

For integration patterns, provide examples that show how multiple APIs work together:

```typescript
// Example of a complex integration between Work Item Tracking and Git APIs
```

## See Also

- [API Reference](./api-reference/index.md) - Complete API reference documentation
- [Getting Started Guide](./getting-started/getting-started.md) - Guide to getting started with the Azure DevOps Node API
- [Tutorials](./tutorials/index.md) - Step-by-step tutorials for common scenarios
- [Glossary](./glossary.md) - Standardized terminology for the Azure DevOps Node API
``` 