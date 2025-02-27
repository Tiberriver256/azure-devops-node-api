# Getting Started with Azure DevOps Node API

Welcome to the Azure DevOps Node API documentation! This section will help you get up and running quickly with the API, whether you're building DevOps integrations, automated tools, or custom extensions.

## Introduction to Azure DevOps Node API

The Azure DevOps Node API is a JavaScript/TypeScript client library that allows you to interact with Azure DevOps Services and Azure DevOps Server. It provides a convenient interface to work with:

- Projects and Teams
- Work Items and Queries
- Git Repositories and Pull Requests
- Build Pipelines
- Release Management
- Test Management
- And many other Azure DevOps services

## Quick Start Guides

| Guide | Description |
|-------|-------------|
| [Getting Started Guide](./getting-started.md) | A step-by-step introduction to using the Azure DevOps Node API |
| [Authentication Guide](./authentication.md) | Comprehensive guide to authentication methods |
| [Authentication Cheat Sheet](./authentication-cheat-sheet.md) | Quick reference for authentication methods |
| [OAuth Authentication](./oauth-authentication.md) | Detailed guide for implementing OAuth authentication |
| [Security Best Practices](./security-best-practices.md) | Best practices for securely using the API |

## Installation

The Azure DevOps Node API package can be installed via npm:

```bash
npm install azure-devops-node-api
```

For TypeScript users, type definitions are included in the package.

## Basic Usage Example

Here's a quick example to get you started:

```javascript
const azdev = require("azure-devops-node-api");

// Your organization URL and Personal Access Token
const orgUrl = "https://dev.azure.com/your-organization";
const token = process.env.AZURE_DEVOPS_PAT;

async function example() {
    try {
        // Create authentication handler
        const authHandler = azdev.getPersonalAccessTokenHandler(token);
        
        // Create a connection to Azure DevOps
        const connection = new azdev.WebApi(orgUrl, authHandler);
        
        // Get the Core API
        const coreApi = await connection.getCoreApi();
        
        // Get all projects
        const projects = await coreApi.getProjects();
        console.log(`Found ${projects.length} projects`);
        
        // Get the Git API
        const gitApi = await connection.getGitApi();
        
        // Get all repositories
        const repositories = await gitApi.getRepositories();
        console.log(`Found ${repositories.length} repositories`);
    } catch (error) {
        console.error("Error:", error.message);
    }
}

example();
```

## Learning Path

If you're new to the Azure DevOps Node API, we recommend following this learning path:

1. Start with the [Getting Started Guide](./getting-started.md) to set up your environment and create your first connection
2. Learn about authentication options in the [Authentication Guide](./authentication.md)
3. Follow the [Connect to Azure DevOps Tutorial](../tutorials/connect-to-azure-devops.md) for a detailed connection walkthrough
4. Explore specific API clients based on your needs (Work Items, Git, Build, etc.)
5. Refer to the [WebApi Core documentation](../api-reference/webapi-core/webapi-core.md) for advanced configuration options

## Related Resources

- [Azure DevOps Services REST API Reference](https://docs.microsoft.com/en-us/rest/api/azure/devops/?view=azure-devops-rest-6.0)
- [Azure DevOps Node API GitHub Repository](https://github.com/microsoft/azure-devops-node-api)
- [Azure DevOps Services](https://azure.microsoft.com/en-us/services/devops/)

## Need Help?

If you encounter issues with the Azure DevOps Node API, check the [Troubleshooting Connection Issues](../troubleshooting/connection-issues.md) guide or refer to the specific API client documentation. 