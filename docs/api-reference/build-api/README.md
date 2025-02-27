# Build API Documentation

**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > Build API

## Overview

The Azure DevOps Build API provides a comprehensive set of methods for interacting with build pipelines in Azure DevOps. It enables developers to programmatically manage build pipelines, queue builds, monitor build status, and access build artifacts and logs. This documentation provides detailed information about the most commonly used methods in the Build API.

## Purpose

The Build API is designed to facilitate:

### Pipeline Management
- Creating new build pipelines
- Updating existing pipeline configurations
- Querying pipeline information
- Managing pipeline settings and permissions

### Build Execution
- Queuing new builds
- Monitoring build progress
- Canceling running builds
- Handling build triggers and schedules

### Result Analysis
- Accessing build logs
- Retrieving build artifacts
- Analyzing test results
- Evaluating build quality

### Automation
- Integrating builds with other systems
- Creating custom build workflows
- Implementing build approval processes
- Setting up automated notifications

### Reporting and Metrics
- Collecting data on build performance
- Tracking build success rates
- Measuring build times
- Analyzing build trends

## Common Use Cases

The Build API is frequently used for:

### CI/CD Orchestration
Create and manage complex build and deployment pipelines that span multiple projects and repositories.

### Custom Dashboards
Build specialized dashboards that display build status, history, and performance metrics tailored to specific team needs.

### Event-Based Automation
Trigger builds automatically based on external events or system conditions beyond standard Azure DevOps triggers.

### Cross-Project Coordination
Coordinate builds across multiple projects to ensure consistent build processes and dependencies.

### Performance Monitoring
Track and analyze build performance metrics to identify bottlenecks and optimize build processes.

### Notification Systems
Create custom notification systems that alert teams about build status through various channels.

### Third-Party Integration
Connect Azure DevOps builds with external tools and services for enhanced functionality.

### Artifact Management
Automate the handling of build artifacts for deployment, archiving, or distribution.

## What's Included

This documentation covers:

- [Top 5 Build API Methods](./top-5-methods.md) - Detailed documentation of the most commonly used Build API methods
- [Code Examples](./code-examples.md) - Practical examples showing how to use the Build API
- [Common Errors](./common-errors.md) - Troubleshooting information for common Build API errors

## Prerequisites

To use the Build API, you'll need:

- An Azure DevOps account with appropriate permissions
- A Personal Access Token (PAT) or other authentication method with Build (Read, Execute) permissions
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
// Example: Get all build pipelines
const buildDefinitions = await buildApi.getDefinitions("YourProject");
console.log(`Found ${buildDefinitions.length} build pipelines`);
```

## Integration with Other APIs

The Build API is commonly used in conjunction with other Azure DevOps APIs to create comprehensive solutions:

### Build + Git Integration

Builds can be triggered by Git repository events and can operate on specific branches or commits. 

Common integration patterns include:

- Triggering builds on commit or pull request
- Building specific branches
- Reporting build status on pull requests

See [Git + Build Integration](../integration-patterns/git-build-integration.md) for detailed examples.

### Build + Work Item Integration

Builds can be linked to work items to track which requirements are implemented in which builds. 

Common integration patterns include:

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
- [Glossary](../../glossary.md) - Standardized terminology for the Azure DevOps Node API 