# Prerequisites

Before you begin, ensure you have:

- Node.js version 14 or higher installed
- Azure DevOps account with appropriate permissions
- {Any other specific requirements for this tutorial}

You should also be familiar with:
- Basic JavaScript/TypeScript
- {Other relevant knowledge areas}

## Required Packages

This tutorial uses the following npm packages:

```bash
npm install azure-devops-node-api
npm install typescript @types/node --save-dev  # If using TypeScript
```

## Authentication Setup

You'll need a Personal Access Token (PAT) with the following permissions:
- {List specific scopes required, e.g., "Work Items: Read & Write"}
- {Other required permissions}

To create a PAT:
1. Go to your Azure DevOps organization settings
2. Select "Personal Access Tokens"
3. Click "New Token"
4. Set the appropriate scopes
5. Copy the token value (you won't be able to see it again)

---

**Navigation**:
- [Back to Index](../index.md)
- Previous: [Header and Overview](./01-header-overview.md)
- Next: [Basic Implementation](./03-basic-implementation.md) 