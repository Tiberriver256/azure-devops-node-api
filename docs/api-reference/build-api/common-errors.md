# Common Build API Errors

This document provides information about common errors encountered when using the Azure DevOps Build API, along with their causes and solutions.

## Authentication Errors

### 401 Unauthorized

**Error Message:** "TF400813: The user 'email@example.com' is not authorized to access this resource."

**Cause:** This error occurs when the authentication token is invalid, expired, or does not have sufficient permissions to access the requested resource.

**Solution:**
1. Verify that your Personal Access Token (PAT) is valid and has not expired.
2. Ensure that the PAT has the necessary permissions (typically "Build (Read, Execute)" for most Build API operations).
3. Check that the organization and project names are correct.

**Example Error Response:**
```json
{
  "id": "0000000-0000-0000-0000-000000000000",
  "innerException": null,
  "message": "TF400813: The user 'email@example.com' is not authorized to access this resource.",
  "typeName": "Microsoft.TeamFoundation.Framework.Server.UnauthorizedRequestException",
  "typeKey": "UnauthorizedRequestException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
import * as azdev from "azure-devops-node-api";

// Correct way to set up authentication
const orgUrl = "https://dev.azure.com/yourorganization";
const token = process.env.AZURE_DEVOPS_PAT; // Store token in environment variable

// Create a handler with the token
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Verify connection by getting authorized projects
try {
    const coreApi = await connection.getCoreApi();
    const projects = await coreApi.getProjects();
    console.log(`Successfully authenticated. Found ${projects.length} projects.`);
} catch (error) {
    if (error.statusCode === 401) {
        console.error("Authentication failed. Please check your PAT token and permissions.");
    } else {
        console.error(`Error: ${error.message}`);
    }
}
```

## Access Errors

### 403 Forbidden

**Error Message:** "TF400857: The project does not have a valid build definition."

**Cause:** This error occurs when your authentication is valid, but you don't have permission to access a specific resource, such as a build definition or build.

**Solution:**
1. Verify that you have the necessary permissions in the project.
2. Contact your project administrator to grant you the required permissions.
3. Check if the resource (e.g., build definition) exists and is accessible to your account.

**Example Error Response:**
```json
{
  "id": "0000000-0000-0000-0000-000000000000",
  "innerException": null,
  "message": "TF400857: The project does not have a valid build definition.",
  "typeName": "Microsoft.TeamFoundation.Build.WebApi.BuildNotFoundException",
  "typeKey": "BuildNotFoundException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
import * as azdev from "azure-devops-node-api";

async function safelyGetBuildDefinitions(projectName: string) {
    try {
        const buildApi = await connection.getBuildApi();
        const definitions = await buildApi.getDefinitions(projectName);
        console.log(`Successfully retrieved ${definitions.length} build definitions.`);
        return definitions;
    } catch (error) {
        if (error.statusCode === 403) {
            console.error(`Access denied to build definitions in project '${projectName}'. Please check your permissions.`);
            return [];
        } else {
            console.error(`Error: ${error.message}`);
            throw error;
        }
    }
}
```

## Resource Not Found Errors

### 404 Not Found

**Error Message:** "TF400857: The project 'ProjectName' does not have a valid build definition with id '123'."

**Cause:** This error occurs when the requested resource (project, build definition, build, etc.) does not exist or has been deleted.

**Solution:**
1. Verify that the project, build definition, or build ID exists.
2. Check for typos in the project name or ID.
3. Confirm that the resource has not been deleted or moved.

**Example Error Response:**
```json
{
  "id": "0000000-0000-0000-0000-000000000000",
  "innerException": null,
  "message": "TF400857: The project 'ProjectName' does not have a valid build definition with id '123'.",
  "typeName": "Microsoft.TeamFoundation.Build.WebApi.BuildNotFoundException",
  "typeKey": "BuildNotFoundException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
import * as azdev from "azure-devops-node-api";

async function safelyGetBuild(projectName: string, buildId: number) {
    try {
        const buildApi = await connection.getBuildApi();
        const build = await buildApi.getBuild(projectName, buildId);
        console.log(`Successfully retrieved build ${build.buildNumber}.`);
        return build;
    } catch (error) {
        if (error.statusCode === 404) {
            console.error(`Build with ID ${buildId} not found in project '${projectName}'.`);
            return null;
        } else {
            console.error(`Error: ${error.message}`);
            throw error;
        }
    }
}

// Example of checking if a build exists before trying to access it
async function getBuildIfExists(projectName: string, buildId: number) {
    const build = await safelyGetBuild(projectName, buildId);
    if (build) {
        // Process the build
        return build;
    } else {
        console.log(`Build ${buildId} does not exist or is not accessible.`);
        return null;
    }
}
```

## Validation Errors

### 400 Bad Request

**Error Message:** "TF400898: An Internal Error Occurred. Activity Id: ..."

**Cause:** This error occurs when the request contains invalid parameters or malformed data.

**Solution:**
1. Check that all required parameters are provided and have valid values.
2. Verify that the data format is correct (e.g., dates are in the correct format).
3. Ensure that IDs are valid and exist in the system.

**Common Validation Errors:**

1. **Invalid Build Definition ID**
   ```
   "TF400857: The project does not have a valid build definition with id '999'."
   ```

2. **Missing Required Parameters**
   ```
   "Value cannot be null. Parameter name: project"
   ```

3. **Invalid Parameter Format**
   ```
   "TF400898: An Internal Error Occurred. Invalid date format."
   ```

**Solution Code Example:**
```typescript
import * as azdev from "azure-devops-node-api";
import * as BuildInterfaces from "azure-devops-node-api/interfaces/BuildInterfaces";

// Validate parameters before making API calls
async function queueBuildWithValidation(projectName: string, definitionId: number, sourceBranch: string) {
    try {
        // Validate project name
        if (!projectName || typeof projectName !== 'string') {
            throw new Error("Project name must be a non-empty string");
        }
        
        // Validate definition ID
        if (!definitionId || typeof definitionId !== 'number' || definitionId <= 0) {
            throw new Error("Definition ID must be a positive number");
        }
        
        // Validate branch name
        if (!sourceBranch || typeof sourceBranch !== 'string') {
            throw new Error("Source branch must be a non-empty string");
        }
        
        // Check if definition exists before queuing
        const buildApi = await connection.getBuildApi();
        try {
            await buildApi.getDefinition(projectName, definitionId);
        } catch (error) {
            if (error.statusCode === 404) {
                throw new Error(`Build definition with ID ${definitionId} not found in project '${projectName}'`);
            }
            throw error;
        }
        
        // Create build object
        const build: BuildInterfaces.Build = {
            definition: {
                id: definitionId
            },
            sourceBranch: sourceBranch,
            reason: BuildInterfaces.BuildReason.Manual
        };
        
        // Queue the build
        return await buildApi.queueBuild(build, projectName);
    } catch (error) {
        console.error(`Validation error: ${error.message}`);
        throw error;
    }
}
```

## Conflict Errors

### 409 Conflict

**Error Message:** "TF400889: The build definition 'BuildName' already exists."

**Cause:** This error occurs when trying to create a resource that already exists or when there's a conflict with an existing resource.

**Solution:**
1. Check if the resource already exists and use it instead of creating a new one.
2. Use a different name or identifier for the new resource.
3. Update the existing resource instead of creating a new one.

**Example Error Response:**
```json
{
  "id": "0000000-0000-0000-0000-000000000000",
  "innerException": null,
  "message": "TF400889: The build definition 'BuildName' already exists.",
  "typeName": "Microsoft.TeamFoundation.Build.WebApi.BuildDefinitionExistsException",
  "typeKey": "BuildDefinitionExistsException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
import * as azdev from "azure-devops-node-api";
import * as BuildInterfaces from "azure-devops-node-api/interfaces/BuildInterfaces";

async function createOrUpdateBuildDefinition(projectName: string, definitionName: string, definitionData: BuildInterfaces.BuildDefinition) {
    try {
        const buildApi = await connection.getBuildApi();
        
        // Check if definition already exists
        const existingDefinitions = await buildApi.getDefinitions(
            projectName,
            definitionName, // name filter
            undefined,
            undefined,
            undefined,
            1 // top
        );
        
        if (existingDefinitions && existingDefinitions.length > 0) {
            console.log(`Definition '${definitionName}' already exists. Updating instead of creating.`);
            
            // Update existing definition
            const existingDef = existingDefinitions[0];
            definitionData.id = existingDef.id;
            definitionData.revision = existingDef.revision;
            
            return await buildApi.updateDefinition(definitionData, projectName, existingDef.id);
        } else {
            // Create new definition
            return await buildApi.createDefinition(definitionData, projectName);
        }
    } catch (error) {
        console.error(`Error creating/updating build definition: ${error.message}`);
        throw error;
    }
}
```

## Rate Limiting Errors

### 429 Too Many Requests

**Error Message:** "TF400733: Too many requests. Please try again later."

**Cause:** This error occurs when you've exceeded the API rate limits for Azure DevOps.

**Solution:**
1. Implement retry logic with exponential backoff.
2. Reduce the frequency of API calls.
3. Batch operations where possible to reduce the number of API calls.

**Example Error Response:**
```json
{
  "id": "0000000-0000-0000-0000-000000000000",
  "innerException": null,
  "message": "TF400733: Too many requests. Please try again later.",
  "typeName": "Microsoft.TeamFoundation.Framework.Server.RateLimitExceededException",
  "typeKey": "RateLimitExceededException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
import * as azdev from "azure-devops-node-api";

// Retry function with exponential backoff
async function retryWithBackoff(fn, maxRetries = 5, initialDelayMs = 1000) {
    let retries = 0;
    
    while (true) {
        try {
            return await fn();
        } catch (error) {
            if (error.statusCode === 429) {
                retries++;
                if (retries > maxRetries) {
                    console.error(`Exceeded maximum retries (${maxRetries}) due to rate limiting.`);
                    throw error;
                }
                
                // Calculate delay with exponential backoff
                const delayMs = initialDelayMs * Math.pow(2, retries - 1);
                console.log(`Rate limit exceeded. Retrying in ${delayMs}ms (retry ${retries}/${maxRetries})...`);
                
                // Wait before retrying
                await new Promise(resolve => setTimeout(resolve, delayMs));
            } else {
                // Not a rate limit error, rethrow
                throw error;
            }
        }
    }
}

// Example usage
async function getBuildsWithRetry(projectName: string) {
    const buildApi = await connection.getBuildApi();
    
    return await retryWithBackoff(async () => {
        return await buildApi.getBuilds(projectName);
    });
}
```

## Server Errors

### 500 Internal Server Error

**Error Message:** "TF400898: An Internal Error Occurred."

**Cause:** This error occurs when there's an unexpected error on the server side.

**Solution:**
1. Retry the request after a short delay.
2. Check the Azure DevOps Service Health status.
3. Contact Azure DevOps support if the issue persists.

**Example Error Response:**
```json
{
  "id": "0000000-0000-0000-0000-000000000000",
  "innerException": null,
  "message": "TF400898: An Internal Error Occurred. Activity Id: ...",
  "typeName": "Microsoft.TeamFoundation.Framework.Server.TeamFoundationServerInternalException",
  "typeKey": "TeamFoundationServerInternalException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
import * as azdev from "azure-devops-node-api";

// Retry function for server errors
async function retryServerErrors(fn, maxRetries = 3, delayMs = 2000) {
    let retries = 0;
    
    while (true) {
        try {
            return await fn();
        } catch (error) {
            if (error.statusCode === 500) {
                retries++;
                if (retries > maxRetries) {
                    console.error(`Exceeded maximum retries (${maxRetries}) for server error.`);
                    throw error;
                }
                
                console.log(`Server error encountered. Retrying in ${delayMs}ms (retry ${retries}/${maxRetries})...`);
                
                // Wait before retrying
                await new Promise(resolve => setTimeout(resolve, delayMs));
            } else {
                // Not a server error, rethrow
                throw error;
            }
        }
    }
}

// Example usage
async function getBuildWithRetry(projectName: string, buildId: number) {
    const buildApi = await connection.getBuildApi();
    
    return await retryServerErrors(async () => {
        return await buildApi.getBuild(projectName, buildId);
    });
}
```

## Service Unavailable Errors

### 503 Service Unavailable

**Error Message:** "TF400733: Service Unavailable."

**Cause:** This error occurs when the Azure DevOps service is temporarily unavailable or undergoing maintenance.

**Solution:**
1. Wait and retry the request later.
2. Check the Azure DevOps Service Health status.
3. Implement retry logic with increasing delays.

**Example Error Response:**
```json
{
  "id": "0000000-0000-0000-0000-000000000000",
  "innerException": null,
  "message": "TF400733: Service Unavailable.",
  "typeName": "Microsoft.TeamFoundation.Framework.Server.ServiceUnavailableException",
  "typeKey": "ServiceUnavailableException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
import * as azdev from "azure-devops-node-api";

// Comprehensive retry function for various error types
async function retryOperation(fn, options = {}) {
    const {
        maxRetries = 5,
        initialDelayMs = 1000,
        maxDelayMs = 30000,
        retryStatusCodes = [429, 500, 503]
    } = options;
    
    let retries = 0;
    
    while (true) {
        try {
            return await fn();
        } catch (error) {
            if (error.statusCode && retryStatusCodes.includes(error.statusCode)) {
                retries++;
                if (retries > maxRetries) {
                    console.error(`Exceeded maximum retries (${maxRetries}).`);
                    throw error;
                }
                
                // Calculate delay with exponential backoff and jitter
                const baseDelay = Math.min(initialDelayMs * Math.pow(2, retries - 1), maxDelayMs);
                const jitter = Math.random() * 0.3 * baseDelay; // Add up to 30% jitter
                const delayMs = baseDelay + jitter;
                
                console.log(`Error ${error.statusCode} encountered. Retrying in ${Math.round(delayMs)}ms (retry ${retries}/${maxRetries})...`);
                
                // Wait before retrying
                await new Promise(resolve => setTimeout(resolve, delayMs));
            } else {
                // Not a retryable error, rethrow
                throw error;
            }
        }
    }
}

// Example usage
async function getBuildsWithRobustRetry(projectName: string) {
    const buildApi = await connection.getBuildApi();
    
    return await retryOperation(
        async () => await buildApi.getBuilds(projectName),
        {
            maxRetries: 5,
            initialDelayMs: 1000,
            maxDelayMs: 30000,
            retryStatusCodes: [429, 500, 503, 504]
        }
    );
}
```

## Git-Specific Errors

### Build Source Version Errors

**Error Message:** "TF401019: The Git repository with name or identifier ... does not exist or you do not have permissions for the operation you are attempting."

**Cause:** This error occurs when trying to queue a build with an invalid source version or branch.

**Solution:**
1. Verify that the source branch exists in the repository.
2. Check that the source version (commit ID) is valid.
3. Ensure that you have access to the repository.

**Example Error Response:**
```json
{
  "id": "0000000-0000-0000-0000-000000000000",
  "innerException": null,
  "message": "TF401019: The Git repository with name or identifier '12345678-1234-1234-1234-123456789012' does not exist or you do not have permissions for the operation you are attempting.",
  "typeName": "Microsoft.TeamFoundation.Git.Server.GitRepositoryNotFoundException",
  "typeKey": "GitRepositoryNotFoundException",
  "errorCode": 0,
  "eventId": 3000
}
```

**Solution Code Example:**
```typescript
import * as azdev from "azure-devops-node-api";
import * as BuildInterfaces from "azure-devops-node-api/interfaces/BuildInterfaces";
import * as GitInterfaces from "azure-devops-node-api/interfaces/GitInterfaces";

// Validate branch exists before queuing build
async function queueBuildWithBranchValidation(projectName: string, definitionId: number, repositoryId: string, branchName: string) {
    try {
        // Get Git API client
        const gitApi = await connection.getGitApi();
        
        // Normalize branch name (add refs/heads/ if not present)
        let fullBranchName = branchName;
        if (!branchName.startsWith('refs/')) {
            fullBranchName = `refs/heads/${branchName}`;
        }
        
        // Check if branch exists
        try {
            const refs = await gitApi.getRefs(repositoryId, projectName, fullBranchName);
            if (!refs || refs.length === 0) {
                throw new Error(`Branch '${branchName}' does not exist in repository '${repositoryId}'`);
            }
        } catch (error) {
            if (error.statusCode === 404) {
                throw new Error(`Branch '${branchName}' does not exist in repository '${repositoryId}'`);
            }
            throw error;
        }
        
        // Queue build with validated branch
        const buildApi = await connection.getBuildApi();
        const build: BuildInterfaces.Build = {
            definition: {
                id: definitionId
            },
            sourceBranch: fullBranchName,
            reason: BuildInterfaces.BuildReason.Manual
        };
        
        return await buildApi.queueBuild(build, projectName);
    } catch (error) {
        console.error(`Error queuing build: ${error.message}`);
        throw error;
    }
}
```

## Best Practices for Handling Build API Errors

1. **Use try/catch blocks** around all API calls to handle errors gracefully.

2. **Implement retry logic** for transient errors (429, 500, 503) with exponential backoff.

3. **Validate input parameters** before making API calls to avoid 400 Bad Request errors.

4. **Check resource existence** before trying to access or modify resources to avoid 404 Not Found errors.

5. **Use descriptive error messages** that include the context of what you were trying to do.

6. **Log detailed error information** including status codes, error messages, and request details.

7. **Handle specific error types** differently based on their status codes and error messages.

8. **Provide clear feedback to users** about what went wrong and how to fix it.

9. **Monitor API usage** to avoid hitting rate limits.

10. **Keep authentication tokens secure** and refresh them before they expire.

## Example: Comprehensive Error Handling

```typescript
import * as azdev from "azure-devops-node-api";
import * as BuildInterfaces from "azure-devops-node-api/interfaces/BuildInterfaces";

async function getBuildWithComprehensiveErrorHandling(projectName: string, buildId: number) {
    try {
        // Validate input parameters
        if (!projectName || typeof projectName !== 'string') {
            throw new Error("Project name must be a non-empty string");
        }
        
        if (!buildId || typeof buildId !== 'number' || buildId <= 0) {
            throw new Error("Build ID must be a positive number");
        }
        
        // Get Build API client
        const connection = getConnection(); // Your connection setup function
        const buildApi = await connection.getBuildApi();
        
        // Get the build with retry logic for transient errors
        return await retryOperation(
            async () => await buildApi.getBuild(projectName, buildId),
            {
                maxRetries: 3,
                retryStatusCodes: [429, 500, 503, 504]
            }
        );
    } catch (error) {
        // Handle specific error types
        if (error.statusCode) {
            switch (error.statusCode) {
                case 401:
                    console.error(`Authentication error: Please check your credentials and permissions.`);
                    break;
                case 403:
                    console.error(`Access denied: You don't have permission to access build ${buildId} in project '${projectName}'.`);
                    break;
                case 404:
                    console.error(`Build ${buildId} not found in project '${projectName}'.`);
                    break;
                case 429:
                    console.error(`Rate limit exceeded. Please try again later.`);
                    break;
                default:
                    console.error(`API error (${error.statusCode}): ${error.message}`);
            }
        } else {
            console.error(`Error retrieving build: ${error.message}`);
        }
        
        // Log detailed error information
        console.debug('Detailed error:', {
            statusCode: error.statusCode,
            message: error.message,
            typeName: error.typeName,
            projectName,
            buildId
        });
        
        // Rethrow or return null based on your error handling strategy
        return null;
    }
}

// Helper retry function
async function retryOperation(fn, options = {}) {
    // Implementation as shown in previous examples
}

// Helper connection function
function getConnection() {
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    return new azdev.WebApi(orgUrl, authHandler);
}
``` 