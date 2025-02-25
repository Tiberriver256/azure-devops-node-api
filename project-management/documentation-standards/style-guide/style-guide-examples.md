# Style Guide Application Examples

This document demonstrates how applying our style guide transforms documentation content across different documentation types. These examples will be used during the style guide review to illustrate the practical impact of our standards.

## API Reference Documentation

### Before

```typescript
/**
 * getRepositories - Gets a list of Git repositories.
 * 
 * @param project - Project ID or project name
 * @param includeLinks - True to include reference links. Default is false.
 * @param includeAllUrls - True to include all remote URLs. Default is false.
 * @returns Promise<GitRepository[]>
 */
public async getRepositories(
    project?: string,
    includeLinks?: boolean,
    includeAllUrls?: boolean
): Promise<GitRepository[]> {
    // Implementation
}
```

#### Associated Documentation:

The getRepositories function will return Git repositories from the project specified. If no project is specified it will return all repositories for the organization. The function returns a Promise that resolves to an array of GitRepository objects. The function can optionally include links and URLs. The function might throw exceptions if the connection fails or if the user doesn't have sufficient permissions.

### After

```typescript
/**
 * Gets a list of Git repositories in a project or organization.
 * 
 * @param project - Project ID or project name. If not specified, repositories from all projects are returned.
 * @param includeLinks - Include reference links in the response. Default: false.
 * @param includeAllUrls - Include all remote URLs for the repository. Default: false.
 * @returns A Promise that resolves to an array of GitRepository objects.
 * @throws UnauthorizedError when the user lacks sufficient permissions.
 * @throws ConnectionFailedError when the service is unavailable.
 * @example
 * // Get all repositories in a project
 * const repos = await gitClient.getRepositories("MyProject");
 * 
 * // Get all repositories in the organization with additional information
 * const allRepos = await gitClient.getRepositories(undefined, true, true);
 */
public async getRepositories(
    project?: string,
    includeLinks?: boolean,
    includeAllUrls?: boolean
): Promise<GitRepository[]> {
    // Implementation
}
```

#### Associated Documentation:

## Getting Started Guide

### Before

When you want to use the Azure DevOps Node API, you need to first set up authentication. There are multiple authentication methods available and you need to choose one. After that you can create a connection and then you can access the API clients to do operations against your Azure DevOps instance. Error handling is important or your application might crash when something goes wrong.

### After

## Connecting to Azure DevOps

Before you can use the Azure DevOps Node API, you need to authenticate and establish a connection.

### 1. Choose an authentication method

Azure DevOps supports several authentication methods:

- Personal Access Token (recommended)
- Basic authentication
- OAuth 2.0
- Azure Active Directory

For most scenarios, we recommend using a Personal Access Token (PAT).

### 2. Create a connection

```typescript
import * as azdev from "azure-devops-node-api";

// Personal Access Token authentication
const orgUrl = "https://dev.azure.com/yourorganization";
const token = "your-personal-access-token";

const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Test the connection
const connectionData = await connection.connect();
console.log(`Connected to organization ${connectionData.authenticatedUser.providerDisplayName}`);
```

### 3. Access API clients

Once connected, you can access specific API clients to work with different Azure DevOps services:

```typescript
// Get the Git API client
const gitClient = await connection.getGitApi();

// Get the Work Item Tracking API client
const workItemClient = await connection.getWorkItemTrackingApi();
```

### 4. Implement error handling

Always implement error handling to manage potential issues:

```typescript
try {
  const projects = await coreClient.getProjects();
  console.log(`Found ${projects.length} projects`);
} catch (error) {
  if (error.statusCode === 401) {
    console.error("Authentication failed. Check your token and permissions.");
  } else if (error.statusCode >= 500) {
    console.error("Azure DevOps service is currently unavailable.");
  } else {
    console.error(`Error: ${error.message}`);
  }
}
```

## Conceptual Documentation

### Before

Resource areas are a concept in Azure DevOps that you need to understand. The WebApi client uses them to route requests properly. Each area has a unique identifier and is versioned separately from other areas. When a client object is created, it knows the location it should use for requests based on the resource area ID.

### After

## Understanding Resource Areas

Resource areas are logical groupings of related API resources within Azure DevOps. Each resource area:

- Represents a specific service domain (Git, Work Items, Build, etc.)
- Has a unique identifier (GUID)
- Can be versioned independently from other areas
- Is hosted at a specific location URL

### How Resource Areas Work

When you create an API client through the WebApi class, the following process occurs:

1. The WebApi client identifies the appropriate resource area for the requested client type
2. It retrieves the location URL for that resource area
3. It routes API requests to the correct service endpoint
4. It handles API version negotiation specific to that resource area

This architecture allows Azure DevOps to:

- Scale services independently
- Deploy and version different components separately
- Maintain backward compatibility for specific resource areas

### Resource Areas in Your Code

You typically don't need to work with resource areas directly. The WebApi client handles this automatically:

```typescript
// The WebApi client manages resource area routing behind the scenes
const connection = new azdev.WebApi(orgUrl, authHandler);

// When you request a client, it's configured with the correct resource area
const gitClient = await connection.getGitApi();
const workItemClient = await connection.getWorkItemTrackingApi();

// Each client knows its resource area and makes requests to the appropriate endpoint
const repositories = await gitClient.getRepositories();
```

In advanced scenarios, you can access resource area information directly:

```typescript
// Get information about all resource areas
const resourceAreas = await connection.getResourceAreas();

// Find a specific resource area
const gitResourceArea = resourceAreas.find(area => area.name.toLowerCase() === "git");
console.log(`Git API endpoint: ${gitResourceArea.locationUrl}`);
```

## Error Handling Guide

### Before

The Azure DevOps Node API can throw errors that you should catch and handle. Different types of errors can occur depending on what went wrong. Authentication errors happen when your credentials are wrong. Validation errors happen when your inputs are wrong. Resource errors happen when the resource doesn't exist. Server errors happen when something goes wrong on the server.

### After

# Error Handling in Azure DevOps Node API

Effective error handling improves the reliability of your applications that integrate with Azure DevOps. This guide explains common error patterns and how to handle them.

## Common Error Types

| Error Category | HTTP Status Codes | Typical Causes |
|----------------|-------------------|----------------|
| Authentication | 401, 403 | Invalid credentials, insufficient permissions, expired token |
| Validation | 400, 422 | Invalid request format, malformed parameters, constraint violations |
| Resource | 404 | Resource not found, project doesn't exist, ID not valid |
| Service | 500, 503 | Azure DevOps service issues, maintenance, internal errors |
| Throttling | 429 | Too many requests, rate limit exceeded |

## Implementing Robust Error Handling

Use try/catch blocks with conditional handling based on error characteristics:

```typescript
try {
  // API operations
  const workItem = await workItemClient.getWorkItem(workItemId);
  // Process work item...
} catch (error) {
  // Check status code
  if (error.statusCode === 401 || error.statusCode === 403) {
    console.error("Authentication error. Check your credentials and permissions.");
    // Potentially refresh token or prompt for re-authentication
  } else if (error.statusCode === 404) {
    console.error(`Work item ${workItemId} not found.`);
    // Handle missing resource gracefully
  } else if (error.statusCode === 429) {
    console.error("Rate limit exceeded. Implementing backoff...");
    // Implement exponential backoff and retry
    await delay(calculateBackoff(retryCount));
    retryCount++;
  } else if (error.statusCode >= 500) {
    console.error("Azure DevOps service error. Retry operation later.");
    // Log detailed error for troubleshooting
    logger.error(`Service Error: ${error.message}`, error);
  } else {
    console.error(`Unexpected error: ${error.message}`);
    // Fall back to default error handling
  }
}
```

## Retry Strategies

For transient errors (service unavailability or throttling), implement a retry strategy:

```typescript
async function executeWithRetry(operation, maxRetries = 3) {
  let retryCount = 0;
  
  while (retryCount <= maxRetries) {
    try {
      return await operation();
    } catch (error) {
      if (isRetryable(error) && retryCount < maxRetries) {
        const delayMs = calculateExponentialBackoff(retryCount);
        console.log(`Retrying after ${delayMs}ms (Attempt ${retryCount + 1}/${maxRetries})`);
        await delay(delayMs);
        retryCount++;
      } else {
        throw error; // Non-retryable error or max retries exceeded
      }
    }
  }
}

// Helper function to determine if an error is retryable
function isRetryable(error) {
  return error.statusCode === 429 || error.statusCode >= 500;
}

// Calculate exponential backoff with jitter
function calculateExponentialBackoff(retryCount) {
  const baseDelay = 1000; // 1 second
  const maxDelay = 30000; // 30 seconds
  const backoff = Math.min(maxDelay, baseDelay * Math.pow(2, retryCount));
  
  // Add jitter (±30%) to prevent synchronized retries
  const jitter = backoff * 0.3 * (Math.random() * 2 - 1);
  return backoff + jitter;
}
```

These examples demonstrate how our style guide creates clearer, more user-friendly documentation that helps developers succeed with the Azure DevOps Node API. 