# Azure DevOps Node API Reference

Welcome to the Azure DevOps Node API reference documentation. This documentation provides detailed information about the classes, methods, and interfaces available in the Azure DevOps Node API.

## API Classes

| Class | Description |
|-------|-------------|
| [WorkItemTrackingApi](./work-item-tracking/README.md) | Work with work items, queries, and work item types |
| BuildApi | Manage builds and build definitions |
| GitApi | Work with Git repositories, pull requests, and commits |
| CoreApi | Access projects, teams, and process templates |
| ReleaseApi | Manage release definitions and deployments |
| TaskAgentApi | Work with agent pools and task groups |
| TestApi | Manage test plans, test suites, and test cases |
| WikiApi | Work with wikis and wiki pages |

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