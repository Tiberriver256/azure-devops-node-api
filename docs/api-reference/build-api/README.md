# Build API Documentation

**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > Build API

## Overview

The Azure DevOps Build API provides a comprehensive set of methods for interacting with build pipelines in Azure DevOps. It enables developers to programmatically manage build definitions, queue builds, monitor build status, and access build artifacts and logs. This documentation provides detailed information about the most commonly used methods in the Build API.

## Purpose

The Build API is designed to facilitate:
- Build definition management (creating, updating, and querying build definitions)
- Build execution (queuing builds and monitoring their progress)
- Build result analysis (accessing logs, artifacts, and test results)
- Build pipeline automation (integrating builds with other systems)
- Build reporting and metrics (collecting data on build performance and outcomes)

## Common Use Cases

The Build API is frequently used for:
- CI/CD pipeline orchestration
- Custom build dashboards and reporting tools
- Automated build triggering based on external events
- Cross-project build coordination
- Build analytics and performance monitoring
- Build notification systems
- Integration with third-party tools and services
- Build artifact management and deployment

## What's Included

This documentation covers:
- [Top 5 Build API Methods](./top-5-methods.md) - Detailed documentation of the most commonly used Build API methods
- [Code Examples](./code-examples.md) - Practical examples showing how to use the Build API
- [Common Errors](./common-errors.md) - Troubleshooting information for common Build API errors

## Prerequisites

To use the Build API, you'll need:
- An Azure DevOps account with appropriate permissions
- A personal access token (PAT) or other authentication method with Build (Read, Execute) permissions
- The Azure DevOps Node.js API client library

## Getting Started

To start using the Build API, first establish a connection to your Azure DevOps organization:

```typescript
import * as azdev from "azure-devops-node-api";

// Connection setup
const orgUrl = "https://dev.azure.com/yourorganization";
const token = "your-personal-access-token";

const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get Build API client
const buildApi = await connection.getBuildApi();

// Now you can use the Build API methods
// Example: Get all build definitions
const buildDefinitions = await buildApi.getDefinitions("YourProject");
console.log(`Found ${buildDefinitions.length} build definitions`);
```

## Integration with Other APIs

The Build API is commonly used in conjunction with other Azure DevOps APIs to create comprehensive solutions:

### Build + Git Integration

Builds can be triggered by Git repository events and can operate on specific branches or commits. Common integration patterns include:

- Triggering builds on commit or pull request
- Building specific branches
- Reporting build status on pull requests

See [Git + Build Integration](../integration-patterns/git-build-integration.md) for detailed examples.

### Build + Work Item Integration

Builds can be linked to work items to track which requirements are implemented in which builds. Common integration patterns include:

- Linking builds to work items
- Updating work item status based on build results
- Generating release notes from work items

See [Work Item + Build Integration](../integration-patterns/work-item-build-integration.md) for detailed examples.

### Cross-API Examples

For complex scenarios involving Build, Git, and Work Item Tracking APIs together, see [Cross-API Examples](../integration-patterns/cross-api-examples.md).

## Related API Methods

For a comprehensive view of how Build API methods relate to other APIs, see the [API Cross-Reference Table](../integration-patterns/api-cross-reference-table.md).

## See Also

- [WebApi Core Documentation](../webapi-core/README.md) - Essential information about the WebApi class
- [Authentication Handlers](../webapi-core/authentication-handlers.md) - Authentication options for connecting to Azure DevOps
- [Git API Documentation](../git-api/README.md) - Documentation for Git API methods
- [Work Item Tracking API Documentation](../work-item-tracking/README.md) - Documentation for Work Item Tracking API methods
- [Integration Patterns](../integration-patterns/README.md) - Documentation for integration patterns
- [API Priority Matrix](../priority-matrix/README.md) - Understand the most important APIs and their use cases 