# Git API Documentation

## Overview

The Azure DevOps Git API provides a comprehensive set of methods for interacting with Git repositories in Azure DevOps. It enables developers to programmatically manage repositories, branches, commits, pull requests, and more. This documentation provides detailed information about the most commonly used methods in the Git API.

## Purpose

The Git API is designed to facilitate:
- Repository management (creating, updating, and querying repositories)
- Branch and reference operations (managing branches, tags, and other references)
- Pull request workflows (creating, reviewing, and completing pull requests)
- Commit history retrieval and analysis
- File and content operations (retrieving, analyzing, and modifying code)

## Common Use Cases

The Git API is frequently used for:
- CI/CD pipeline integrations
- Custom dashboards and reporting tools
- Automated code reviews and quality checks
- Branch policy enforcement
- Repository analytics and statistics
- Automated pull request creation
- Code search and navigation tools
- Cross-repository operations and migrations

## What's Included

This documentation covers:
- [Top 5 Git API Methods](./top-5-methods.md) - Detailed documentation of the most commonly used Git API methods
- [Code Examples](./code-examples.md) - Practical examples showing how to use the Git API
- [Common Errors](./common-errors.md) - Troubleshooting information for common Git API errors

## Prerequisites

To use the Git API, you'll need:
- An Azure DevOps account with appropriate permissions
- A personal access token (PAT) or other authentication method
- The Azure DevOps Node.js API client library

## Getting Started

To start using the Git API, first establish a connection to your Azure DevOps organization:

```typescript
import * as azdev from "azure-devops-node-api";

// Connection setup
const orgUrl = "https://dev.azure.com/yourorganization";
const token = "your-personal-access-token";

const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get Git API client
const gitApi = await connection.getGitApi();

// Now you can use the Git API methods
// Example: Get all repositories
const repositories = await gitApi.getRepositories();
console.log(`Found ${repositories.length} repositories`);
```

## Related Resources

- [WebApi Core Documentation](../webapi-core/README.md) - Essential information about the WebApi class
- [Authentication Handlers](../webapi-core/authentication-handlers.md) - Authentication options for connecting to Azure DevOps
- [API Priority Matrix](../priority-matrix/README.md) - Understand the most important APIs and their use cases 