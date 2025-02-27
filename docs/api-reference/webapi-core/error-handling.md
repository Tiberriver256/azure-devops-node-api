## Common Error Patterns

When calling Azure DevOps API methods, errors can occur for various reasons. Here's how to handle them properly:

```typescript
import * as azdev from "azure-devops-node-api";

async function getRepository(repoName: string) {
    try {
        const orgUrl = "https://dev.azure.com/your-organization";
        const token = "your-personal-access-token";
        const authHandler = azdev.getPersonalAccessTokenHandler(token);
        const connection = new azdev.WebApi(orgUrl, authHandler);
        
        const gitApi = await connection.getGitApi();
        const repo = await gitApi.getRepository(repoName);
        return repo;
    } catch (error) {
        // Check the error status code to handle specific cases
        if (error.statusCode === 404) {
            console.log(`Repository '${repoName}' not found`);
            return null;
        } else if (error.statusCode === 401) {
            console.log("Authentication failed. Check your personal access token.");
            throw error;
        } else if (error.statusCode === 403) {
            console.log("Permission denied. You don't have access to this repository.");
            throw error;
        } else {
            console.log(`Unexpected error: ${error.message}`);
            throw error;
        }
    }
}
```

### Handling Network Errors

In addition to API errors, network issues can also occur:

```typescript
try {
    // API call
    const result = await gitApi.getRepositories();
    return result;
} catch (error) {
    if (error.code === 'ECONNREFUSED' || error.code === 'ETIMEDOUT') {
        console.log('Network connection error. Check your internet connection.');
    } else if (error.statusCode >= 500) {
        console.log('Azure DevOps server error. Try again later.');
    } else {
        console.log(`Error: ${error.message}`);
    }
    throw error;
}
```

## Timeout Configuration

By default, the Azure DevOps API client uses a timeout of 100 seconds for requests. You can configure this timeout when creating the WebApi instance:

```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Configure a custom timeout (in milliseconds)
const options = {
    socketTimeout: 30000  // 30 seconds timeout
};

// Pass the options when creating the WebApi instance
const connection = new azdev.WebApi(orgUrl, authHandler, options);
```

## Implementing Retry Logic

For reliable applications, especially in environments with unstable network connections, implementing retry logic is recommended:

```typescript
import * as azdev from "azure-devops-node-api";

async function getRepositoriesWithRetry(maxRetries = 3, retryDelay = 1000) {
    const orgUrl = "https://dev.azure.com/your-organization";
    const token = "your-personal-access-token";
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    let attempt = 0;
    
    while (attempt < maxRetries) {
        try {
            const gitApi = await connection.getGitApi();
            return await gitApi.getRepositories();
        } catch (error) {
            attempt++;
            
            // Determine if the error is transient and can be retried
            const isTransient = error.statusCode >= 500 || 
                               error.code === 'ETIMEDOUT' || 
                               error.code === 'ECONNRESET' ||
                               error.code === 'ECONNREFUSED';
            
            if (!isTransient || attempt >= maxRetries) {
                console.log(`Error after ${attempt} attempts: ${error.message}`);
                throw error;
            }
            
            // Implement exponential backoff
            const delay = retryDelay * Math.pow(2, attempt - 1);
            console.log(`Attempt ${attempt} failed. Retrying in ${delay}ms...`);
            await new Promise(resolve => setTimeout(resolve, delay));
        }
    }
}
```

**Note**: The retry logic above uses exponential backoff, which increases the wait time between retries. This is considered a best practice to avoid overwhelming servers during outages.
