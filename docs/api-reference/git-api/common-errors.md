# Common Git API Errors and Troubleshooting

This document provides a comprehensive guide to common errors encountered when using the Azure DevOps Git API, including their causes and recommended solutions.

## Authentication Errors

### 401 Unauthorized

| Error Message | Cause | Solution |
|---------------|-------|----------|
| "TF400813: The user 'email@example.com' is not authorized to access this resource." | Invalid or expired Personal Access Token (PAT) | Generate a new PAT with the appropriate scopes (at minimum, "Code (Read)"). |
| "Authentication failed." | Invalid credentials or token format | Ensure the token is properly formatted and included in the request header. |
| "The OAuth token is invalid for this resource." | Token doesn't have the required scopes | Generate a new PAT with the necessary scopes for the operation (e.g., "Code (Read)" for read operations, "Code (Read & Write)" for write operations). |

**Example Error Response:**
```json
{
  "message": "TF400813: The user 'email@example.com' is not authorized to access this resource.",
  "typeKey": "UnauthorizedRequestException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
// Correct way to set up authentication
const authHandler = azdev.getPersonalAccessTokenHandler("your-valid-token");
const connection = new azdev.WebApi(orgUrl, authHandler);
```

## Access Errors

### 403 Forbidden

| Error Message | Cause | Solution |
|---------------|-------|----------|
| "TF401019: The Git repository does not exist or you do not have permissions to access it." | User has insufficient permissions | Request appropriate permissions from a project administrator. |
| "You are not authorized to access this resource." | User has access to the organization but not the specific project or repository | Verify you have been granted access to the specific resource. |
| "The current user is not authorized to access the requested resource." | User doesn't have the required permission level | For operations that modify data, ensure the user has Contributor or higher access level. |

**Example Error Response:**
```json
{
  "message": "TF401019: The Git repository does not exist or you do not have permissions to access it.",
  "typeKey": "GitRepositoryNotFoundException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution:**
- Verify the user has appropriate permissions for the repository.
- For repository creation or modification, ensure the user has "Contribute" permissions or higher.

## Resource Not Found Errors

### 404 Not Found

| Error Message | Cause | Solution |
|---------------|-------|----------|
| "TF401019: The Git repository does not exist or you do not have permissions to access it." | Repository doesn't exist | Verify the repository name or ID is correct. |
| "Project 'XYZ' does not exist." | Project doesn't exist or incorrect name | Check the project name or ID for typos. |
| "The ref 'refs/heads/branch-name' does not exist." | Branch doesn't exist | Verify the branch name exists, including case sensitivity. |
| "Pull request with ID 123 does not exist." | Pull request doesn't exist | Confirm the pull request ID is correct and the pull request hasn't been deleted. |
| "Could not retrieve commit abcdef1234567890..." | Commit doesn't exist | Verify the commit ID is correct and exists in the repository. |

**Example Error Response:**
```json
{
  "message": "TF401019: The Git repository does not exist or you do not have permissions to access it.",
  "typeKey": "GitRepositoryNotFoundException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
// Safely get a repository with error handling
async function safeGetRepository(projectName, repositoryName) {
    try {
        const repository = await gitApi.getRepository(repositoryName, projectName);
        return repository;
    } catch (error) {
        if (error.statusCode === 404) {
            console.error(`Repository '${repositoryName}' not found in project '${projectName}'.`);
            // Handle the not found case gracefully
            return null;
        }
        throw error;
    }
}
```

## Validation Errors

### 400 Bad Request

| Error Message | Cause | Solution |
|---------------|-------|----------|
| "RepositoryId cannot be null." | Missing required parameter | Ensure all required parameters are provided and not null. |
| "You must provide a valid object ID." | Invalid Git object ID format | Ensure commit IDs and other Git object IDs are valid 40-character hexadecimal strings. |
| "The specified branch already exists." | Attempting to create a branch that already exists | Check if the branch exists before attempting to create it. |
| "Pull request source and target branch cannot be the same." | Source and target branches are identical | Specify different branches for source and target. |
| "A pull request already exists for this branch." | Duplicate pull request | Check for existing pull requests before creating a new one, or use a different branch. |

**Example Error Response:**
```json
{
  "message": "You must provide a valid object ID.",
  "typeKey": "InvalidArgumentValueException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
// Validating parameters before making the API call
function validateRefName(refName) {
    if (!refName) {
        throw new Error("Branch name cannot be null or empty");
    }
    
    // Add proper refs/heads/ prefix if not present
    if (!refName.startsWith("refs/")) {
        return `refs/heads/${refName}`;
    }
    
    return refName;
}

// Usage
const sourceBranch = validateRefName("feature/my-feature");
const targetBranch = validateRefName("main");
```

## Conflict Errors

### 409 Conflict

| Error Message | Cause | Solution |
|---------------|-------|----------|
| "The ref 'refs/heads/branch-name' already exists." | Branch already exists | Use updateRefs with an appropriate oldObjectId if you want to update an existing branch. |
| "The item 'path/to/file.txt' already exists." | File already exists | Use a different filename or update the existing file instead. |
| "TF401179: The pull request creation failed because of a conflict." | Merge conflict between source and target branches | Resolve the conflicts locally and push the changes before creating the pull request. |
| "The repository with name 'repoName' already exists." | Repository name already in use | Choose a different repository name or update the existing repository. |

**Example Error Response:**
```json
{
  "message": "TF401179: The pull request creation failed because of a conflict.",
  "typeKey": "GitPullRequestConflictException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
// Check if a branch exists before creating it
async function createBranchIfNotExists(repoId, projectName, branchName, sourceCommitId) {
    try {
        // Check if branch exists
        const refs = await gitApi.getRefs(
            repoId,
            projectName,
            `heads/${branchName}`
        );
        
        if (refs && refs.length > 0) {
            console.log(`Branch ${branchName} already exists.`);
            return refs[0]; // Return existing branch
        }
        
        // Create the branch
        const refUpdate = [{
            name: `refs/heads/${branchName}`,
            oldObjectId: "0000000000000000000000000000000000000000",
            newObjectId: sourceCommitId
        }];
        
        const result = await gitApi.updateRefs(refUpdate, repoId, projectName);
        return result[0];
    } catch (error) {
        console.error(`Error in createBranchIfNotExists: ${error.message}`);
        throw error;
    }
}
```

## Rate Limiting Errors

### 429 Too Many Requests

| Error Message | Cause | Solution |
|---------------|-------|----------|
| "Too many requests." | Exceeded API rate limits | Implement retry logic with exponential backoff. |
| "You have exceeded the throttle limit for this operation." | Too many requests in a short time | Reduce concurrent requests and add delays between requests. |

**Example Error Response:**
```json
{
  "message": "You have exceeded the throttle limit for this operation.",
  "typeKey": "RequestThrottledException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
/**
 * Retries an async function with exponential backoff
 * @param operation Function to retry
 * @param maxRetries Maximum number of retry attempts
 * @param baseDelay Base delay in milliseconds
 */
async function retryWithBackoff(operation, maxRetries = 5, baseDelay = 1000) {
    let lastError;
    
    for (let attempt = 0; attempt < maxRetries; attempt++) {
        try {
            return await operation();
        } catch (error) {
            lastError = error;
            
            // If it's not a throttling error, or we've used all retries, throw
            if (error.statusCode !== 429 && error.statusCode !== 503) {
                throw error;
            }
            
            if (attempt === maxRetries - 1) {
                throw error;
            }
            
            // Calculate delay with exponential backoff
            const delay = baseDelay * Math.pow(2, attempt);
            console.log(`Rate limited. Retrying in ${delay}ms (attempt ${attempt + 1}/${maxRetries})...`);
            
            // Wait for the calculated delay
            await new Promise(resolve => setTimeout(resolve, delay));
        }
    }
    
    throw lastError;
}

// Usage example
async function getRepositoriesWithRetry() {
    return retryWithBackoff(() => gitApi.getRepositories("MyProject"));
}
```

## Server Errors

### 500 Internal Server Error

| Error Message | Cause | Solution |
|---------------|-------|----------|
| "TF400898: An Internal Error Occurred." | Server-side issue with Azure DevOps | Wait and retry the request. If persistent, contact Azure DevOps support. |
| "The service is temporarily unavailable." | Temporary server-side issue | Implement retry logic with appropriate backoff. |

**Example Error Response:**
```json
{
  "message": "TF400898: An Internal Error Occurred.",
  "typeKey": "InternalServerErrorException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution:**
- Implement retry logic for transient failures
- If the issue persists, contact Azure DevOps support with the event ID from the error response

## Service Unavailable Errors

### 503 Service Unavailable

| Error Message | Cause | Solution |
|---------------|-------|----------|
| "The service is temporarily unavailable or overloaded." | Azure DevOps service under high load | Implement retry logic with exponential backoff. |
| "Service temporarily unavailable." | Maintenance or temporary outage | Wait and retry after some time. Check Azure DevOps service status page. |

**Example Error Response:**
```json
{
  "message": "The service is temporarily unavailable or overloaded.",
  "typeKey": "ServiceUnavailableException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution:**
- Implement retry logic similar to rate limiting errors
- Check the [Azure DevOps Service Status](https://status.dev.azure.com/) page for any ongoing incidents

## Git-Specific Errors

| Error Message | Cause | Solution |
|---------------|-------|----------|
| "The pull request has conflicts that prevent automatic merging." | Merge conflicts between source and target branches | Resolve the conflicts locally and push the changes. |
| "The ref update operation failed on the server." | Various issues with updating Git references | Check for concurrent updates, permissions, or branch policies. |
| "You need to resolve the conflicts before completing the pull request." | Attempting to complete a PR with conflicts | Resolve conflicts first or use a different merge strategy. |
| "The object referenced by 'commitId' does not exist." | Commit not found or has been garbage collected | Ensure the commit exists and hasn't been removed by history rewriting. |

**Solution Code Example:**
```typescript
// Check for merge conflicts before creating a pull request
async function checkForMergeConflicts(repoId, projectName, targetBranch, sourceBranch) {
    try {
        const conflicts = await gitApi.getConflicts(
            repoId,
            projectName,
            // Parameters to identify potential conflicts
        );
        
        if (conflicts && conflicts.length > 0) {
            console.log(`Detected ${conflicts.length} conflicts between ${sourceBranch} and ${targetBranch}`);
            return conflicts;
        }
        
        console.log("No conflicts detected. Safe to merge.");
        return [];
    } catch (error) {
        console.error(`Error checking for conflicts: ${error.message}`);
        throw error;
    }
}
```

## Handling Errors Gracefully

Here are some general best practices for handling Git API errors:

1. **Always use try/catch blocks** when making API calls
2. **Implement retry logic with exponential backoff** for transient errors (429, 503)
3. **Check for existing resources** before attempting to create them to avoid 409 conflicts
4. **Validate input parameters** before making API calls to catch issues early
5. **Provide clear error messages** to users, including next steps or resolution paths
6. **Log detailed error information** for debugging purposes
7. **Use status code checking** to handle different error scenarios appropriately

**Error Handling Example:**
```typescript
async function safeGitOperation(operation, errorMessage) {
    try {
        return await operation();
    } catch (error) {
        // Extract useful information from the error
        const statusCode = error.statusCode || 'unknown';
        const message = error.message || 'Unknown error';
        const serverMessage = error.serverError?.message || message;
        
        // Log detailed info for troubleshooting
        console.error(`Git API Error (${statusCode}): ${serverMessage}`);
        
        // Handle specific status codes
        switch (statusCode) {
            case 401:
            case 403:
                throw new Error(`Authorization error: ${errorMessage}. Check your permissions and credentials.`);
            case 404:
                throw new Error(`Resource not found: ${errorMessage}. Verify the resource exists.`);
            case 409:
                throw new Error(`Conflict error: ${errorMessage}. The resource may already exist.`);
            case 429:
                throw new Error(`Rate limit exceeded: ${errorMessage}. Try again later.`);
            default:
                throw new Error(`${errorMessage}: ${serverMessage}`);
        }
    }
}

// Usage example
async function getRepositorySafely(repoName, projectName) {
    return safeGitOperation(
        () => gitApi.getRepository(repoName, projectName),
        `Failed to retrieve repository '${repoName}' in project '${projectName}'`
    );
}
```

## Resources

If you encounter errors not covered in this document, consider checking these resources:

1. [Azure DevOps REST API Reference](https://docs.microsoft.com/en-us/rest/api/azure/devops/?view=azure-devops-rest-7.1)
2. [Azure DevOps Service Status](https://status.dev.azure.com/)
3. [Azure DevOps Developer Community](https://developercommunity.visualstudio.com/AzureDevOps)
4. [Azure DevOps Node.js API GitHub Repository](https://github.com/microsoft/azure-devops-node-api) 