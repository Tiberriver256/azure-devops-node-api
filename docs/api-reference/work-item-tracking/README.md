# Work Item Tracking API

**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > Work Item Tracking API

## Overview

The Work Item Tracking API enables programmatic access to work items in Azure DevOps. It provides capabilities to create, read, update, and delete work items, run queries, and interact with work item types and fields. This API is essential for building integrations, automating work item management, and implementing custom workflows for Azure DevOps projects.

## Key Features

- Retrieve individual or multiple work items by ID
- Create new work items with customized fields
- Update existing work items with field changes
- Query work items using Work Item Query Language (WIQL)
- Access work item type definitions and field metadata
- Manage work item links and attachments

## API Client

To use the Work Item Tracking API, you'll need to obtain an instance of the Work Item Tracking API client through the WebApi Core:

```typescript
import * as azdev from "azure-devops-node-api";

// Initialize authentication
const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Create a connection to Azure DevOps
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get the Work Item Tracking API client
const witApi = await connection.getWorkItemTrackingApi();
```

## Documentation Contents

- [Work Item Tracking API Reference](./work-item-tracking-api.md) - Full API documentation
- Methods:
  - [getWorkItem](./methods/get-work-item.md) - Retrieve a single work item
  - [getWorkItems](./methods/get-work-items.md) - Retrieve multiple work items
  - [createWorkItem](./methods/create-work-item.md) - Create a new work item
  - [updateWorkItem](./methods/update-work-item.md) - Update an existing work item
  - [queryByWiql](./methods/query-work-items.md) - Execute WIQL queries

## Common Usage Scenarios

- **Integration with External Systems**: Sync work items with third-party tools
- **Automation**: Automate creation and updates of work items based on external events
- **Reporting**: Generate custom reports on work item status and progress
- **Custom Dashboards**: Build custom dashboards with work item data
- **Bulk Operations**: Perform batch operations on multiple work items

## Integration with Other APIs

The Work Item Tracking API is commonly used in conjunction with other Azure DevOps APIs to create comprehensive solutions:

### Work Item + Git Integration

Work items can be linked to Git commits, branches, and pull requests to provide traceability between requirements and code changes. Common integration patterns include:

- Linking work items to pull requests
- Creating branches based on work item information
- Updating work item status based on code changes

See [Work Item + Git Integration](../integration-patterns/work-item-git-integration.md) for detailed examples.

### Work Item + Build Integration

Work items can be linked to builds to track which requirements are implemented in which builds. Common integration patterns include:

- Linking work items to builds
- Updating work item status based on build results
- Generating release notes from work items

See [Work Item + Build Integration](../integration-patterns/work-item-build-integration.md) for detailed examples.

### Cross-API Examples

For complex scenarios involving Work Item Tracking, Git, and Build APIs together, see [Cross-API Examples](../integration-patterns/cross-api-examples.md).

## Related API Methods

For a comprehensive view of how Work Item Tracking API methods relate to other APIs, see the [API Cross-Reference Table](../integration-patterns/api-cross-reference-table.md).

## See Also

- [WebApi Core Documentation](../webapi-core/webapi-core.md)
- [Authentication Handlers](../webapi-core/authentication-handlers.md)
- [Git API Documentation](../git-api/README.md)
- [Build API Documentation](../build-api/README.md)
- [Integration Patterns](../integration-patterns/README.md)
- [Work Items REST API](https://learn.microsoft.com/en-us/rest/api/azure/devops/wit/?view=azure-devops-rest-7.1)
- [Glossary](../../glossary.md) - Standardized terminology for the Azure DevOps Node API