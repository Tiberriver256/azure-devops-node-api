# Troubleshooting Common API Usage Issues

This guide addresses common issues developers encounter when working with the Azure DevOps Node API beyond basic connection and authentication problems.

## API Response Issues

### Issue: Empty Results from API Calls

**Symptoms**:
- API calls return empty arrays or null values
- No error is thrown, but expected data is missing

**Possible Causes and Solutions**:

1. **Incorrect Project or Organization Scope**:
   ```javascript
   // The API call might be targeting the wrong project
   const repositories = await gitApi.getRepositories("WrongProjectName");
   
   // Solution: Verify project name or omit for organization-wide scope
   const repositories = await gitApi.getRepositories("CorrectProjectName");
   // or for all repositories in the organization:
   const allRepositories = await gitApi.getRepositories();
   ```

2. **Permissions Issues**:
   - Verify that the authenticated user or PAT has permissions to access the requested resource
   - For PATs, check if you selected the right scope during creation
   - Solution: Create a new PAT with appropriate scopes or request additional permissions

3. **Nonexistent Resources**:
   - Verify that the resource exists in the project/organization you're querying
   - Check for typos in identifiers or names

### Issue: UnhandledPromiseRejectionWarning

**Symptoms**:
- Console shows `UnhandledPromiseRejectionWarning` 
- Application crashes unexpectedly

**Solutions**:

1. **Always Use try/catch with Async Code**:
   ```javascript
   // Problematic code
   async function getWorkItems() {
       const workItemIds = [1, 2, 3]; // If any of these don't exist...
       const workItems = await witApi.getWorkItems(workItemIds); // ...this will reject
       return workItems;
   }
   
   // Better approach with error handling
   async function getWorkItems() {
       try {
           const workItemIds = [1, 2, 3];
           const workItems = await witApi.getWorkItems(workItemIds);
           return workItems;
       } catch (error) {
           console.error("Error retrieving work items:", error.message);
           // Handle error appropriately, maybe return empty array or default value
           return [];
       }
   }
   ```

2. **Add Unhandled Rejection Handler**:
   ```javascript
   process.on('unhandledRejection', (reason, promise) => {
       console.error('Unhandled Rejection at:', promise, 'reason:', reason);
       // You might want to log to a file or notify monitoring system here
   });
   ```

### Issue: Rate Limiting

**Symptoms**:
- Receiving 429 (Too Many Requests) status codes
- Operations succeed intermittently

**Solutions**:

1. **Implement Backoff and Retry**:
   ```javascript
   async function withRetry(fn, maxRetries = 5) {
       let retries = 0;
       while (true) {
           try {
               return await fn();
           } catch (error) {
               if (error.statusCode !== 429 || retries >= maxRetries) {
                   throw error;
               }
               
               retries++;
               // Exponential backoff: 1s, 2s, 4s, 8s, 16s
               const delay = Math.pow(2, retries - 1) * 1000;
               console.log(`Rate limited. Retry ${retries}/${maxRetries} after ${delay}ms`);
               await new Promise(resolve => setTimeout(resolve, delay));
           }
       }
   }
   
   // Usage
   const workItems = await withRetry(() => witApi.getWorkItems([1, 2, 3, 4, 5]));
   ```

2. **Batch Operations**:
   ```javascript
   // Instead of many small requests
   for (const id of workItemIds) {
       await witApi.getWorkItem(id); // Bad: One request per ID
   }
   
   // Batch them into a single request
   await witApi.getWorkItems(workItemIds); // Good: Single request for all IDs
   ```

3. **Implement Request Throttling**:
   ```javascript
   async function throttledRequests(requests, maxConcurrent = 2, delayMs = 1000) {
       const results = [];
       for (let i = 0; i < requests.length; i += maxConcurrent) {
           const batch = requests.slice(i, i + maxConcurrent);
           const batchResults = await Promise.all(batch.map(req => req()));
           results.push(...batchResults);
           
           if (i + maxConcurrent < requests.length) {
               // Wait before the next batch
               await new Promise(resolve => setTimeout(resolve, delayMs));
           }
       }
       return results;
   }
   
   // Usage
   const requests = projectIds.map(id => () => coreApi.getProject(id));
   const projects = await throttledRequests(requests);
   ```

## Working with Specific APIs

### Work Item Tracking API Issues

#### Issue: Invalid Field Reference Names

**Symptoms**:
- Errors like `TF401232: Field 'name' does not exist or you do not have permissions to access it`
- Work item creation or updates fail

**Solutions**:

1. **Use Correct Field Reference Names**:
   ```javascript
   // Incorrect
   const patchDocument = [
       { op: "add", path: "/title", value: "New Bug" } // Wrong field name
   ];
   
   // Correct
   const patchDocument = [
       { op: "add", path: "/fields/System.Title", value: "New Bug" } // Correct reference name
   ];
   ```

2. **Verify Field Exists for Work Item Type**:
   Some fields are specific to certain work item types. Ensure the field exists for the work item type you're using.

#### Issue: Work Item Parent/Child Relationships

**Symptoms**:
- Unable to create parent-child links between work items
- Relationship links not showing up

**Solution**:
```javascript
// Creating a parent-child link
const patchDocument = [
    { 
        op: "add", 
        path: "/relations/-", 
        value: {
            rel: "System.LinkTypes.Hierarchy-Forward", // Parent-child relationship
            url: `https://dev.azure.com/organization/project/_apis/wit/workItems/${childId}`,
            attributes: {
                comment: "Linking parent to child"
            }
        }
    }
];

await witApi.updateWorkItem([], patchDocument, parentId);
```

### Git API Issues

#### Issue: Large Repository Operations Time Out

**Symptoms**:
- Operations on large repositories time out
- Error messages about operation taking too long

**Solutions**:

1. **Increase Socket Timeout**:
   ```javascript
   const options = {
       socketTimeout: 120000  // Increase to 2 minutes (default is 30 seconds)
   };
   
   const connection = new azdev.WebApi(orgUrl, authHandler, options);
   ```

2. **Use Pagination for Large Result Sets**:
   ```javascript
   async function getAllCommits(repositoryId, projectName) {
       let skip = 0;
       const top = 100; // Items per page
       let allCommits = [];
       
       while (true) {
           const batch = await gitApi.getCommits(
               repositoryId, 
               { 
                   top, 
                   skip,
                   projectName
               }
           );
           
           if (batch.length === 0) break;
           
           allCommits = [...allCommits, ...batch];
           skip += top;
           
           // Optional: Add a small delay between requests
           await new Promise(resolve => setTimeout(resolve, 500));
       }
       
       return allCommits;
   }
   ```

#### Issue: File Content Encoding Problems

**Symptoms**:
- Binary files corrupted when retrieved or uploaded
- Special characters in text files appear incorrectly

**Solution**:
```javascript
// Retrieving binary file content
const item = await gitApi.getItemContent(
    repoId,
    filePath,
    projectName,
    null, // version
    null, // versionOptions
    null, // versionType
    true  // includeContent - set to true to get raw content
);

// Binary data is returned as Buffer
const buffer = Buffer.from(item);

// For text files, specify encoding
const textContent = buffer.toString('utf8');
```

### Build and Release API Issues

#### Issue: Unable to Queue Builds

**Symptoms**:
- Build definition exists but can't be queued
- Receiving permission errors

**Solutions**:

1. **Check Build Definition Status**:
   ```javascript
   const definition = await buildApi.getDefinition(projectName, definitionId);
   if (!definition.queue || definition.quality !== 'definition') {
       console.error("Build definition is not ready to be queued");
   }
   ```

2. **Provide All Required Parameters**:
   ```javascript
   const build = {
       definition: {
           id: definitionId
       },
       sourceBranch: "refs/heads/main",
       reason: 1, // Manual build
       parameters: JSON.stringify({
           // Add any required build parameters
           "param1": "value1"
       })
   };
   
   await buildApi.queueBuild(build, projectName);
   ```

## TypeScript-Specific Issues

### Issue: Type Definition Problems

**Symptoms**:
- TypeScript compiler errors about missing properties
- Invalid type errors despite correct code

**Solutions**:

1. **Use Type Assertions When Necessary**:
   ```typescript
   // If the compiler doesn't recognize a valid property
   const customOptions = {
       myCustomOption: true
   } as azdev.GitQueryCommitsCriteria;
   ```

2. **Check API Version Compatibility**:
   ```typescript
   // Explicitly set API version for version-specific features
   const connection = new azdev.WebApi(orgUrl, authHandler);
   
   // Get connection data to check API version
   const connectionData = await connection.connect();
   console.log(`API Version: ${connectionData.apiVersion}`);
   ```

3. **Update Package to Latest Version**:
   ```bash
   npm update azure-devops-node-api
   ```

## Debugging Tips

### Enable Request Tracing

To see the raw requests and responses:

```javascript
// Enable debug output - before creating any connections
process.env.AZURE_DEVOPS_API_DEBUG = "true";
```

### Use Custom User Agent for Tracking

```javascript
const connection = new azdev.WebApi(
    orgUrl, 
    authHandler, 
    undefined, // options
    { userAgent: "MyApp/1.0 (Custom Application)" }
);
```

### Log API Calls

Create a wrapper to log API operations:

```javascript
class LoggingApiWrapper {
    constructor(api, apiName) {
        this.api = api;
        this.apiName = apiName;
        
        // Proxy all methods
        return new Proxy(this, {
            get: (target, prop) => {
                if (typeof target.api[prop] === 'function') {
                    return async (...args) => {
                        console.log(`[${this.apiName}] Calling ${prop}`, ...args);
                        try {
                            const result = await target.api[prop](...args);
                            console.log(`[${this.apiName}] ${prop} succeeded`);
                            return result;
                        } catch (error) {
                            console.error(`[${this.apiName}] ${prop} failed:`, error);
                            throw error;
                        }
                    };
                } else {
                    return target.api[prop];
                }
            }
        });
    }
}

// Usage
const connection = new azdev.WebApi(orgUrl, authHandler);
const gitApi = await connection.getGitApi();
const loggingGitApi = new LoggingApiWrapper(gitApi, 'GitApi');

// Now all calls are logged
const repos = await loggingGitApi.getRepositories();
```

## Additional Resources

For more detailed troubleshooting:

- [Connection Issues Guide](./connection-issues.md) - For authentication and connection problems
- [Azure DevOps REST API Documentation](https://docs.microsoft.com/en-us/rest/api/azure/devops/?view=azure-devops-rest-6.0) - For API details
- [GitHub Issues](https://github.com/microsoft/azure-devops-node-api/issues) - For known issues and solutions

If you've tried these solutions and still face issues, consider:

1. Checking the Azure DevOps Service Status page
2. Looking for similar issues in GitHub Issues
3. Filing a new issue with detailed reproduction steps 