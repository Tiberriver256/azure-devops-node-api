# Azure DevOps Node API Authentication

## Overview

This directory contains comprehensive documentation for authenticating with the Azure DevOps Node API. Authentication is the first step in using the API and is essential for accessing Azure DevOps resources securely.

## Contents

- [Authentication Guide](authentication.md) - Complete guide to authentication methods and best practices
- [OAuth Authentication](oauth-authentication.md) - Detailed implementation guide for OAuth 2.0 flows
- [Security Best Practices](security-best-practices.md) - Guide to secure credential management
- [Authentication Cheat Sheet](authentication-cheat-sheet.md) - Quick reference for common authentication patterns

## Getting Started

If you're new to the Azure DevOps Node API, start with the [Authentication Guide](authentication.md), which provides a comprehensive overview of all authentication methods and helps you choose the best option for your scenario.

For a quick reference with code snippets, see the [Authentication Cheat Sheet](authentication-cheat-sheet.md).

## Authentication Methods

The Azure DevOps Node API supports several authentication methods:

1. **Personal Access Tokens (PATs)** - Recommended for most scenarios
2. **Basic Authentication** - Simple username/password authentication
3. **OAuth 2.0** - Ideal for web applications and multi-tenant scenarios
4. **NTLM Authentication** - For on-premises Azure DevOps Server deployments

## Code Examples

Each authentication guide includes practical code examples that you can adapt for your applications. Here's a simple example of PAT authentication:

```typescript
import * as azdev from "azure-devops-node-api";

// Get authentication credentials from environment variables
const orgUrl = process.env.AZURE_DEVOPS_ORG_URL;
const token = process.env.AZURE_DEVOPS_PAT;

// Create a PAT authentication handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Create the WebApi connection
const connection = new azdev.WebApi(orgUrl, authHandler);

// Use the connection
async function useConnection() {
    try {
        // Test the connection
        const connData = await connection.connect();
        console.log("Connected successfully!");
    } catch (error) {
        console.error("Error connecting:", error);
    }
}

useConnection();
```

## Security Considerations

Authentication credentials provide access to your Azure DevOps resources, so it's essential to handle them securely:

- Never hardcode credentials in your source code
- Use environment variables or secret management services
- Set appropriate token expirations
- Implement proper token rotation policies

For detailed security guidance, see the [Security Best Practices](security-best-practices.md) guide.

## Related Resources

- [WebApi Core Documentation](../api-reference/webapi-core/webapi-core.md)
- [Authentication Handlers Reference](../api-reference/webapi-core/authentication-handlers.md)
- [Connection Options](../api-reference/webapi-core/connection-options.md)
- [Connecting to Azure DevOps Tutorial](../tutorials/connect-to-azure-devops.md)
- [Troubleshooting Connection Issues](../troubleshooting/connection-issues.md)

## Need Help?

If you encounter issues with authentication, consult the [Troubleshooting Connection Issues](../troubleshooting/connection-issues.md) guide, which covers common authentication problems and their solutions. 