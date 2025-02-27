# Azure DevOps Node API Reference

**Navigation**: [Home](../index.md) > API Reference

Welcome to the Azure DevOps Node API reference documentation. This documentation provides detailed information about the classes, methods, and interfaces available in the Azure DevOps Node API.

## API Classes

| Class | Description |
|-------|-------------|
| [WorkItemTrackingApi](./work-item-tracking/README.md) | Work with work items, queries, and work item types |
| [BuildApi](./build-api/README.md) | Manage builds and build definitions |
| [GitApi](./git-api/README.md) | Work with Git repositories, pull requests, and commits |
| [CoreApi](./core-api/README.md) | Access projects, teams, and process templates |
| [ReleaseApi](./release-api/README.md) | Manage release definitions and deployments |
| [TaskAgentApi](./task-agent-api/README.md) | Work with agent pools and task groups |
| [TestApi](./test-api/README.md) | Manage test plans, test suites, and test cases |
| [WikiApi](./wiki-api/README.md) | Work with wikis and wiki pages |

## Getting Started

To use the Azure DevOps Node API, you need to:

1. Install the package:
   ```
   npm install azure-devops-node-api
   ```

2. Create a connection to Azure DevOps:
   ```typescript
   import * as azdev from "azure-devops-node-api";

   // Create a connection to Azure DevOps
   const orgUrl = "https://dev.azure.com/myorganization";
   const token = "YOUR_PERSONAL_ACCESS_TOKEN";
   const authHandler = azdev.getPersonalAccessTokenHandler(token);
   const connection = new azdev.WebApi(orgUrl, authHandler);
   ```

3. Get the API client you need:
   ```typescript
   // Get the WorkItemTrackingApi instance
   const workItemTrackingApi = await connection.getWorkItemTrackingApi();
   ```

4. Use the API client to interact with Azure DevOps:
   ```typescript
   // Get a work item
   const workItem = await workItemTrackingApi.getWorkItem(42);
   console.log(`Work Item ${workItem.id}: ${workItem.fields["System.Title"]}`);
   ```

## Authentication

The Azure DevOps Node API supports several authentication methods:

### Personal Access Token (Recommended)

```typescript
const token = "YOUR_PERSONAL_ACCESS_TOKEN";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

### Basic Authentication

```typescript
const username = "username";
const password = "password";
const authHandler = azdev.getBasicHandler(username, password);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

### Bearer Token

```typescript
const token = "YOUR_BEARER_TOKEN";
const authHandler = azdev.getBearerHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

## Common Patterns

- [Error Handling](./common/error-handling.md)
- [Pagination](./common/pagination.md)
- [Batch Operations](./common/batch-operations.md)
- [Authentication](./common/authentication.md)

## API Structure

The Azure DevOps Node API is organized into several API clients, each focused on a specific area of Azure DevOps:

- **WorkItemTrackingApi**: Work with work items, queries, and work item types
- **BuildApi**: Manage builds and build definitions
- **GitApi**: Work with Git repositories, pull requests, and commits
- **CoreApi**: Access projects, teams, and process templates
- **ReleaseApi**: Manage release definitions and deployments
- **TaskAgentApi**: Work with agent pools and task groups
- **TestApi**: Manage test plans, test suites, and test cases
- **WikiApi**: Work with wikis and wiki pages

Each API client provides methods for interacting with the corresponding Azure DevOps service.

## Integration Patterns

While each API client can be used independently, many real-world scenarios require combining multiple APIs. The [Integration Patterns](./integration-patterns/README.md) documentation provides guidance on how to effectively combine multiple Azure DevOps APIs to solve common real-world scenarios.

Key integration patterns include:

- [Work Item + Git Integration](./integration-patterns/work-item-git-integration.md) - Patterns for integrating work item tracking with source control
- [Work Item + Build Integration](./integration-patterns/work-item-build-integration.md) - Patterns for connecting work items with build pipelines
- [Git + Build Integration](./integration-patterns/git-build-integration.md) - Patterns for automating builds based on repository events
- [Cross-API Examples](./integration-patterns/cross-api-examples.md) - Complex scenarios involving three or more APIs

## API Relationships

To understand how different API clients and methods relate to each other, refer to the [API Cross-Reference Table](./integration-patterns/api-cross-reference-table.md). This table shows which methods are commonly used together and which integration patterns they support.

## API Priority Matrix

The [API Priority Matrix](./priority-matrix/README.md) provides a strategic view of the most important methods within each API client, along with common use cases and complexity ratings. Use this matrix to focus your learning and implementation efforts on the most impactful functionality.

## See Also

- [Getting Started Guide](../getting-started/README.md) - Step-by-step guide to getting started with the Azure DevOps Node API
- [Tutorials](../tutorials/README.md) - Practical tutorials for common scenarios
- [Troubleshooting Guide](../troubleshooting/README.md) - Solutions for common issues
- [Azure DevOps REST API Reference](https://learn.microsoft.com/en-us/rest/api/azure/devops/?view=azure-devops-rest-7.1) - Official Microsoft documentation for the underlying REST API 