# Error Code Reference

> **Navigation**: [Home](../index.md) > Error Code Reference

This page provides a comprehensive reference of error codes you might encounter when using the Azure DevOps Node API, along with their explanations and solutions.

> **TL;DR:** Find your error code in the tables below for quick solutions.

## How to Use This Reference

1. Locate your error code in the appropriate section below
2. Review the explanation and solution
3. Follow the links to detailed troubleshooting guides for specific issues
4. If your error code is not listed, see the [Getting Help](./getting-help.md) page

## HTTP Status Code Overview

| Status Code | General Meaning | Common Causes |
|-------------|----------------|---------------|
| 400 | Bad Request | Malformed request, invalid parameters |
| 401 | Unauthorized | Authentication failure, invalid/expired token |
| 403 | Forbidden | Insufficient permissions, IP restrictions |
| 404 | Not Found | Resource doesn't exist, incorrect URL |
| 409 | Conflict | Version conflict, concurrent modification |
| 429 | Too Many Requests | Rate limiting, throttling |
| 500 | Internal Server Error | Server-side issue, unexpected error |
| 503 | Service Unavailable | Maintenance, service outage |

## Team Foundation (TF) Error Codes

> These error codes are prefixed with "TF" and are specific to Azure DevOps/Team Foundation Server.

<details>
<summary><b>Authentication & Authorization Errors (TF4xxxxx)</b></summary>

| Error Code | Message | Explanation | Solution |
|------------|---------|-------------|----------|
| TF400813 | The user '[user]' is not authorized to access this resource | The authenticated user lacks permission to access the requested resource | [Check token permissions](./problem-categories/authentication-issues.md#insufficient-permissions) |
| TF400819 | The security token provided is invalid or has expired | The PAT or OAuth token is no longer valid | [Renew your token](./problem-categories/authentication-issues.md#token-expiration) |
| TF401349 | The user '[user]' does not have permission to access this field | The user has access to the resource but not to a specific field | [Review field-level permissions](./problem-categories/authentication-issues.md#field-level-permissions) |

</details>

<details>
<summary><b>Connection & Request Errors (TF2xxxxx)</b></summary>

| Error Code | Message | Explanation | Solution |
|------------|---------|-------------|----------|
| TF200016 | The operation has timed out | The request took too long to complete | [Connection troubleshooting](./problem-categories/connection-problems.md#timeouts) |
| TF200019 | The service is temporarily unavailable | The service is under heavy load or maintenance | [Service availability](./problem-categories/connection-problems.md#service-availability) |
| TF237082 | The request has been terminated | The connection was closed before the request completed | [Network stability](./problem-categories/connection-problems.md#network-stability) |

</details>

<details>
<summary><b>Work Item Errors (TF3xxxxx)</b></summary>

| Error Code | Message | Explanation | Solution |
|------------|---------|-------------|----------|
| TF301013 | The work item does not exist | The requested work item ID doesn't exist | [Data retrieval issues](./problem-categories/data-retrieval-issues.md#non-existent-resources) |
| TF301019 | The work item is currently locked for changes | Another process is modifying the work item | [Handling locked resources](./problem-categories/data-modification-issues.md#locked-resources) |
| TF301022 | The field '[field]' cannot be empty | A required field is missing a value | [Required fields](./problem-categories/data-modification-issues.md#required-fields) |

</details>

<details>
<summary><b>Version Control Errors (TF4xxxxx)</b></summary>

| Error Code | Message | Explanation | Solution |
|------------|---------|-------------|----------|
| TF401019 | The Git repository with name or identifier '[name]' does not exist | The repository doesn't exist or you don't have access | [Repository access](./problem-categories/data-retrieval-issues.md#repository-access) |
| TF401027 | You need the Git 'GenericRead' permission to perform this action | Missing read permission for Git operations | [Git permissions](./problem-categories/authentication-issues.md#git-permissions) |
| TF401393 | The pull request '[id]' does not exist | The pull request ID is invalid | [Pull request access](./problem-categories/data-retrieval-issues.md#pull-request-access) |

</details>

## Visual Studio (VS) Error Codes

> These error codes are prefixed with "VS" and relate to Visual Studio services.

<details>
<summary><b>Authentication Errors (VS4xxxxx)</b></summary>

| Error Code | Message | Explanation | Solution |
|------------|---------|-------------|----------|
| VS402965 | The token has expired | The OAuth or PAT token has reached its expiration date | [Token renewal](./problem-categories/authentication-issues.md#token-expiration) |
| VS403442 | The token does not have the required scope | The token lacks the necessary permission scopes | [Token scopes](./problem-categories/authentication-issues.md#token-scopes) |

</details>

## Azure Active Directory (AADSTS) Error Codes

> These error codes are prefixed with "AADSTS" and relate to Azure Active Directory authentication.

<details>
<summary><b>Common AAD Errors</b></summary>

| Error Code | Message | Explanation | Solution |
|------------|---------|-------------|----------|
| AADSTS50126 | Invalid username or password | The credentials provided are incorrect | [AAD authentication](./problem-categories/authentication-issues.md#azure-active-directory-issues) |
| AADSTS50076 | Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access | MFA is required but not provided | [MFA setup](./problem-categories/authentication-issues.md#multi-factor-authentication) |
| AADSTS65001 | The user or administrator has not consented to use the application | The application hasn't been granted permission | [Application consent](./problem-categories/authentication-issues.md#application-consent) |

</details>

## Rate Limiting and Throttling Errors

<details>
<summary><b>Rate Limiting Errors</b></summary>

| Error Code | Message | Explanation | Solution |
|------------|---------|-------------|----------|
| N/A (429) | Too many requests | You've exceeded the API rate limits | [Rate limiting](./problem-categories/performance-issues.md#rate-limiting) |
| VS403454 | Application is over its request limit | Your application has exceeded its quota | [Request quotas](./problem-categories/performance-issues.md#request-quotas) |

</details>

## Custom Error Handling

When working with the Azure DevOps Node API, you can implement custom error handling to better identify and respond to specific error codes:

```typescript
import * as azdev from "azure-devops-node-api";

async function apiCallWithErrorHandling() {
  try {
    // Your API call here
    const connection = new azdev.WebApi(orgUrl, authHandler);
    const projectApi = await connection.getProjectApi();
    const projects = await projectApi.getProjects();
    return projects;
  } catch (error) {
    // Extract error information
    const statusCode = error.statusCode || "unknown";
    const errorMessage = error.message || "No error message";
    
    // Check for specific error codes
    if (errorMessage.includes("TF400813")) {
      console.error("Permission error: You don't have access to this resource");
      // Handle permission error
    } else if (statusCode === 401 || errorMessage.includes("authorized")) {
      console.error("Authentication error: Your credentials are invalid or expired");
      // Handle authentication error
    } else if (statusCode === 429 || errorMessage.includes("Too many requests")) {
      console.error("Rate limiting: You've exceeded the API rate limits");
      // Handle rate limiting
    } else if (statusCode >= 500) {
      console.error("Server error: The Azure DevOps service is experiencing issues");
      // Handle server error
    } else {
      console.error(`Unhandled error (${statusCode}): ${errorMessage}`);
    }
    
    throw error; // Re-throw or handle as needed
  }
}
```

## Adding Custom Error Codes

If you encounter error codes not listed in this reference, please contribute by:

1. Documenting the error code, message, and context
2. Identifying the solution if known
3. Submitting a pull request to update this reference
4. Sharing the information in the [community forums](https://developercommunity.visualstudio.com/VisualStudio)

## Next Steps

- For authentication-specific errors, see [Authentication Issues](./problem-categories/authentication-issues.md)
- For connection problems, see [Connection Problems](./problem-categories/connection-problems.md)
- For data-related errors, see [Data Retrieval Issues](./problem-categories/data-retrieval-issues.md) or [Data Modification Issues](./problem-categories/data-modification-issues.md)
- If you can't find your error code, see [Getting Help](./getting-help.md)

---

**Related Pages:**
- [Issue Identification](./issue-identification.md)
- [Decision Tree](./decision-tree.md)
- [Advanced Troubleshooting](./advanced-troubleshooting.md) 