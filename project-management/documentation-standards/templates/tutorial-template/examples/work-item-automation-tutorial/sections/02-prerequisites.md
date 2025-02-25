# Prerequisites

Before you begin this tutorial, ensure you have the following:

## Environment Requirements

- Node.js version 14.x or higher
- npm or yarn package manager
- A code editor (VS Code recommended)
- Git (optional, for cloning example repositories)

## Azure DevOps Requirements

- An Azure DevOps organization
- A project with work item tracking enabled
- Permissions to create and modify work items
- A Personal Access Token (PAT) with the following scopes:
  - `Work Items: Read, Write, & Manage`
  - `Project and Team: Read`

### Creating a Personal Access Token

1. Sign in to your Azure DevOps organization
2. Click on your profile icon in the top right corner
3. Select "Personal access tokens"
4. Click "New Token"
5. Name your token (e.g., "Work Item Automation")
6. Set the organization to your organization
7. Set the expiration date (recommended: 30-90 days)
8. Select the scopes mentioned above
9. Click "Create"
10. **Important**: Copy and securely store your token. You won't be able to see it again.

## Required Packages

This tutorial uses the following npm packages:

```bash
# Core Azure DevOps API package
npm install azure-devops-node-api

# For TypeScript support (recommended)
npm install typescript @types/node --save-dev

# For handling environment variables
npm install dotenv --save-dev
```

## Knowledge Prerequisites

You should be familiar with:

- Basic JavaScript or TypeScript
- Asynchronous programming concepts (Promises, async/await)
- Basic understanding of Azure DevOps work items
- Terminal/command line usage

## Project Setup

Create a new directory for your project and initialize it:

```bash
# Create project directory
mkdir azure-devops-work-item-automation
cd azure-devops-work-item-automation

# Initialize npm project
npm init -y

# Install required packages
npm install azure-devops-node-api dotenv
npm install typescript @types/node --save-dev

# Initialize TypeScript configuration (if using TypeScript)
npx tsc --init
```

Create a `.env` file in your project root to store your Azure DevOps credentials:

```
# .env file
AZURE_DEVOPS_ORG=https://dev.azure.com/your-organization
AZURE_DEVOPS_PAT=your-personal-access-token
AZURE_DEVOPS_PROJECT=YourProjectName
```

**Important**: Add `.env` to your `.gitignore` file to prevent accidentally committing your credentials.

---

**Navigation**:
- [Back to Index](../index.md)
- Previous: [Overview](./01-header-overview.md)
- Next: [Basic Implementation](./03-basic-implementation.md) 