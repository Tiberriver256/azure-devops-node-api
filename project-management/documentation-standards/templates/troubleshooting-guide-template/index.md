# Troubleshooting Guide: [Component Name]

> This guide helps you identify and resolve common issues with the [Component Name] in the Azure DevOps Node API.

## 🔎 How to Use This Guide

1. If you know your specific issue, use the navigation below to go directly to a solution
2. For quick fixes to common problems, check the [Quick Solutions](#quick-solutions) section below
3. If you're unsure what's wrong, use the [Decision Tree](#decision-tree) or browse the issue categories
4. Look for severity indicators (🔴 High, 🟡 Medium, 🟢 Low) and time estimates (⏱️) to prioritize your troubleshooting

## 📋 Common Issue Categories

| Category | Description | Common Issues |
|----------|-------------|---------------|
| 🔒 [Authentication Issues](./sections/problem-categories/authentication-issues.md) | Problems with tokens, credentials, and identity | [Invalid Token](./sections/problem-categories/authentication-issues.md#invalid-token) (🔴), [Token Expiration](./sections/problem-categories/authentication-issues.md#token-expiration) (🟡) |
| 🌐 [Connection Problems](./sections/problem-categories/connection-problems.md) | Network, proxy, and connectivity issues | [Network Connectivity](./sections/problem-categories/connection-problems.md#network-connectivity) (🔴), [Proxy Issues](./sections/problem-categories/connection-problems.md#proxy-issues) (🟡) |
| 🔑 [Permission Errors](./sections/problem-categories/permission-errors.md) | Access denied and authorization problems | [Insufficient Permissions](./sections/problem-categories/permission-errors.md#insufficient-permissions) (🔴), [Scope Issues](./sections/problem-categories/permission-errors.md#scope-issues) (🟡) |
| 📊 [Data Retrieval Issues](./sections/problem-categories/data-retrieval-issues.md) | Problems getting data from the API | [Query Errors](./sections/problem-categories/data-retrieval-issues.md#query-errors) (🟡), [Missing Data](./sections/problem-categories/data-retrieval-issues.md#missing-data) (🟢) |
| ✏️ [Data Modification Errors](./sections/problem-categories/data-modification-errors.md) | Issues creating, updating, or deleting data | [Validation Failures](./sections/problem-categories/data-modification-errors.md#validation-failures) (🟡), [Concurrency Issues](./sections/problem-categories/data-modification-errors.md#concurrency-issues) (🔴) |
| ⚡ [Performance Issues](./sections/problem-categories/performance-issues.md) | Rate limiting, timeout, and performance problems | [Rate Limiting](./sections/problem-categories/performance-issues.md#rate-limiting) (🟡), [Timeouts](./sections/problem-categories/performance-issues.md#timeouts) (🔴) |
| ⚙️ [Configuration Problems](./sections/problem-categories/configuration-problems.md) | Setup and environment configuration issues | [Environment Setup](./sections/problem-categories/configuration-problems.md#environment-setup) (🟢), [Version Compatibility](./sections/problem-categories/configuration-problems.md#version-compatibility) (🟡) |
| 📚 [Advanced Troubleshooting](./sections/advanced-troubleshooting.md) | Complex diagnostic techniques and tools | [Debugging Techniques](./sections/advanced-troubleshooting.md#debugging-techniques), [Logging](./sections/advanced-troubleshooting.md#logging) |

## 🔍 Decision Tree

Not sure where to start? Use our [Troubleshooting Decision Tree](./sections/decision-tree.md) to navigate to the right solution:

<details>
<summary><b>Quick Decision Tree Preview</b> (click to expand)</summary>

**Is the issue related to:**

* **Making a connection?**
  * Can't establish connection → [Check network settings](./sections/problem-categories/connection-problems.md#network-connectivity) | ⏱️ 10 min | 🔴 High Severity
  * Connection timeout → [Check API endpoint and throttling](./sections/problem-categories/connection-problems.md#connection-timeout) | ⏱️ 5 min | 🟡 Medium Severity
* **Authentication?**
  * Invalid credentials → [Verify token/credentials](./sections/problem-categories/authentication-issues.md#invalid-token) | ⏱️ 5 min | 🔴 High Severity
  * Token expired → [Refresh authentication](./sections/problem-categories/authentication-issues.md#token-expiration) | ⏱️ 5 min | 🟡 Medium Severity
* **Using the API?**
  * Can't access resource → [Permission Errors](./sections/problem-categories/permission-errors.md) | ⏱️ 15 min | 🔴 High Severity
  * Can't retrieve data → [Data Retrieval Issues](./sections/problem-categories/data-retrieval-issues.md) | ⏱️ 10 min | 🟡 Medium Severity
  * Can't modify data → [Data Modification Errors](./sections/problem-categories/data-modification-errors.md) | ⏱️ 15 min | 🟡 Medium Severity

</details>

## 🛠️ Quick Solutions

<details>
<summary><b>Authentication Quick Fixes</b></summary>

1. Generate a new PAT token | ⏱️ 2 minutes | 🔴 High Severity
   ```typescript
   // Example: Properly format and use a PAT token
   const token = process.env.AZURE_DEVOPS_PAT.trim(); // Remove any whitespace
   const authHandler = azdev.getPersonalAccessTokenHandler(token);
   ```

2. Check token format and remove whitespace | ⏱️ 1 minute | 🟡 Medium Severity
   ```typescript
   // Clean token before using
   const token = String(process.env.AZURE_DEVOPS_TOKEN).trim();
   ```

3. Verify token scopes in Azure DevOps | ⏱️ 3 minutes | 🟡 Medium Severity
</details>

<details>
<summary><b>Connection Quick Fixes</b></summary>

1. Check network connectivity | ⏱️ 2 minutes | 🔴 High Severity
   ```typescript
   // Test basic connectivity
   const https = require('https');
   https.get('https://dev.azure.com', (res) => {
     console.log('Connection test status:', res.statusCode);
   }).on('error', (e) => {
     console.error('Connection test failed:', e.message);
   });
   ```

2. Configure proxy settings | ⏱️ 5 minutes | 🟡 Medium Severity
   ```typescript
   // Set proxy for Node.js
   process.env.HTTPS_PROXY = 'http://your-proxy:port';
   ```

3. Increase timeout settings | ⏱️ 1 minute | 🟢 Low Severity
</details>

<details>
<summary><b>Permission Quick Fixes</b></summary>

1. Verify API permissions | ⏱️ 3 minutes | 🔴 High Severity
2. Check organization access | ⏱️ 2 minutes | 🟡 Medium Severity
3. Review project permissions | ⏱️ 5 minutes | 🟡 Medium Severity
</details>

See our [full Quick Solutions](./sections/quick-solutions.md) page for more immediate fixes to common issues.

## 🆘 Need More Help?

If you can't find a solution in this guide:

- Check our [Getting Help](./sections/getting-help.md) page
- Review the [API Documentation](https://learn.microsoft.com/en-us/rest/api/azure/devops/)
- Submit an issue on our [GitHub repository](https://github.com/microsoft/azure-devops-node-api/issues)
- Run our [Diagnostic Tools](./sections/diagnostic-tools.md) to gather more information

---

<details>
<summary><b>About This Template</b></summary>

When implementing this template, replace all placeholders and section content with specific information relevant to your component. The multi-file structure allows users to quickly navigate to the information they need.

**Important sections to customize:**
- Component name and description
- Issue categories relevant to your component
- Error codes specific to your component
- Example code snippets that match your component's usage patterns
- Severity indicators and time estimates for each issue

</details>

<div align="right">
<i>Last updated: February 26, 2024</i>
</div> 