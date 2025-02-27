# WebApi Core Documentation

**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > WebApi Core

## Overview

This directory contains the comprehensive documentation for the WebApi Core component of the Azure DevOps Node API. The WebApi Core is the foundational layer that handles authentication, connections, and provides access to all other API clients.

## Contents

- [WebApi Core](./webapi-core.md) - Main documentation for the WebApi class and core functionality
- [Authentication Handlers](./authentication-handlers.md) - Documentation for all authentication methods
- [Connection Options](./connection-options.md) - Documentation for connection configuration options

## Related Resources

- [Connect to Azure DevOps Tutorial](../../tutorials/connect-to-azure-devops.md) - Step-by-step guide for connecting to Azure DevOps
- [Troubleshooting Connection Issues](../../troubleshooting/connection-issues.md) - Solutions for common connection problems

## Key Concepts

The WebApi Core documentation covers these essential concepts:

1. **Connection Establishment** - How to create and configure connections to Azure DevOps
2. **Authentication Methods** - Different ways to authenticate with Azure DevOps services
3. **API Client Access** - How to access specific API clients from the WebApi instance
4. **Connection Configuration** - How to customize connection behavior with options

## Target Audience

This documentation is designed for:

- Application developers integrating with Azure DevOps
- DevOps automation engineers
- CI/CD pipeline developers
- Azure DevOps extension developers

## Usage Examples

Quick reference for common WebApi Core tasks:

```typescript
// Basic connection with Personal Access Token (PAT)
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Connect and get an API client
const workItemTrackingApi = await connection.getWorkItemTrackingApi();
const workItem = await workItemTrackingApi.getWorkItem(42);
```

## See Also

- [Git API Documentation](../git-api/README.md) - Documentation for Git API methods
- [Work Item Tracking API Documentation](../work-item-tracking/README.md) - Documentation for Work Item Tracking API methods
- [Build API Documentation](../build-api/README.md) - Documentation for Build API methods
- [Integration Patterns](../integration-patterns/README.md) - Documentation for integration patterns
- [Glossary](../../glossary.md) - Standardized terminology for the Azure DevOps Node API

## Feedback

For issues, questions, or feedback regarding this documentation, please file an issue in the GitHub repository. 