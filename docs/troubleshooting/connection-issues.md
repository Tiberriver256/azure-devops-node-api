# Troubleshooting WebApi Core Connection Issues

This guide provides solutions for common connection problems when using the Azure DevOps Node API's WebApi Core components to connect to Azure DevOps services.

## Authentication Errors

### 401 Unauthorized Errors

**Symptoms:**
- You receive a 401 Unauthorized error when calling `connection.connect()` or any API method
- Error message contains "TF400813: The user 'X' is not authorized to access this resource"

**Possible Causes and Solutions:**

1. **Invalid Personal Access Token (PAT):**
   ```
   Error: TF400813: The user 'xxxxxxxx' is not authorized to access this resource.
   ```
   
   **Solution:** Verify your PAT is valid and has not expired. Generate a new PAT in your Azure DevOps organization settings.

2. **Insufficient PAT Permissions:**
   ```
   Error: The current user does not have permission to access this resource.
   ```
   
   **Solution:** Make sure your PAT has the correct scopes for the APIs you're trying to access:
   - For read operations, you need at least "Read" scope
   - For write operations, you need "Read & Write" scope
   - For specific services, you may need dedicated scopes

3. **Basic Authentication Issues:**
   ```javascript
   // The following code might fail
   const authHandler = azdev.getBasicHandler("username", "password");
   ```
   
   **Solution:** Microsoft is phasing out basic authentication. Use a PAT instead:
   ```javascript
   const authHandler = azdev.getPersonalAccessTokenHandler("your-pat");
   ```

### Bearer Token Errors

**Symptoms:**
- Authentication fails with bearer token
- Token validation errors

**Solutions:**

1. **Check Token Expiry:**
   Bearer tokens typically have a limited lifetime. Ensure your token is still valid.

2. **Use the Correct Factory Method:**
   ```javascript
   // Incorrect way
   const authHandler = new azdev.BearerCredentialHandler(token);
   
   // Correct way
   const authHandler = azdev.getBearerHandler(token);
   // OR
   const connection = azdev.WebApi.createWithBearerToken(orgUrl, token);
   ```

## Connection Configuration Errors

### SSL Certificate Errors

**Symptoms:**
- Error messages like "unable to verify the first certificate"
- "SSL certificate problem: self-signed certificate"
- "certificate has expired"

**Solutions:**

1. **Ignore SSL Errors (for development/testing only):**
   ```javascript
   const options = {
       ignoreSslError: true
   };
   
   const connection = new azdev.WebApi(orgUrl, authHandler, options);
   ```
   > ⚠️ Warning: Using `ignoreSslError: true` in production is a security risk!

2. **Provide CA Certificate:**
   ```javascript
   const options = {
       cert: {
           caFile: "/path/to/ca-certificate.pem"
       }
   };
   
   const connection = new azdev.WebApi(orgUrl, authHandler, options);
   ```

3. **Set NODE_TLS_REJECT_UNAUTHORIZED Environment Variable (for testing only):**
   ```javascript
   // In your code (not recommended for production)
   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
   ```
   > ⚠️ Warning: This disables SSL certificate validation for all HTTPS connections in your Node.js application!

### Proxy Configuration Issues

**Symptoms:**
- Connection timeouts when behind a corporate proxy
- "Unable to connect to the remote server"
- "ECONNREFUSED" errors

**Solutions:**

1. **Configure Proxy Settings:**
   ```javascript
   const options = {
       proxy: {
           proxyUrl: "http://your-proxy-server:port",
           proxyUsername: "username",  // If needed
           proxyPassword: "password"   // If needed
       }
   };
   
   const connection = new azdev.WebApi(orgUrl, authHandler, options);
   ```

2. **Set Environment Variables:**
   The library will automatically pick up proxy settings from standard environment variables:
   ```bash
   # Set these before running your Node.js application
   export HTTP_PROXY=http://proxy-server:port
   export HTTPS_PROXY=http://proxy-server:port
   export NO_PROXY=localhost,127.0.0.1
   ```

3. **Bypass Proxy for Internal Resources:**
   ```javascript
   const options = {
       proxy: {
           proxyUrl: "http://proxy-server:port",
           proxyBypassHosts: [
               "internal-resource.com",
               "*.internal.domain"
           ]
       }
   };
   ```

## Network and Timeout Errors

### Request Timeouts

**Symptoms:**
- "Socket hang up" errors
- "ETIMEDOUT" errors
- Requests taking a long time and then failing

**Solutions:**

1. **Configure Socket Timeout:**
   ```javascript
   const options = {
       socketTimeout: 60000  // 60 seconds
   };
   
   const connection = new azdev.WebApi(orgUrl, authHandler, options);
   ```

2. **Enable Request Retries:**
   ```javascript
   const options = {
       allowRetries: true,
       maxRetries: 3
   };
   
   const connection = new azdev.WebApi(orgUrl, authHandler, options);
   ```

### Connection Refused Errors

**Symptoms:**
- "ECONNREFUSED" errors
- Unable to establish connection with the server

**Solutions:**

1. **Check Server URL:**
   Ensure the organization URL is correct. The URL should be in the format:
   ```
   https://dev.azure.com/your-organization
   ```
   or for older Azure DevOps accounts:
   ```
   https://your-organization.visualstudio.com
   ```

2. **Check Firewall Settings:**
   Ensure your firewall allows outbound connections to Azure DevOps services:
   - For Azure DevOps Services: ports 80 and 443
   - For specific IP ranges, see Microsoft's documentation on Azure DevOps IP ranges

## API-Specific Issues

### 404 Not Found Errors

**Symptoms:**
- Error messages like "TF404098: Resource not found"
- API methods return 404 status code

**Solutions:**

1. **Check Resource Existence:**
   Ensure the resource you're trying to access exists:
   ```javascript
   try {
       const workItem = await witApi.getWorkItem(999999);
   } catch (error) {
       if (error.statusCode === 404) {
           console.log("Work item does not exist");
       }
   }
   ```

2. **Check API Version Compatibility:**
   Some API methods may require specific API versions. The library should handle this automatically, but check for any API version-specific issues.

### Rate Limiting

**Symptoms:**
- "TF429012: Too many requests" errors
- "You have exceeded the rate limit" messages
- HTTP 429 status codes

**Solutions:**

1. **Implement Exponential Backoff:**
   ```javascript
   async function executeWithBackoff(fn, maxRetries = 5) {
       let retries = 0;
       while (true) {
           try {
               return await fn();
           } catch (error) {
               if (error.statusCode !== 429 || retries >= maxRetries) {
                   throw error;
               }
               const delay = Math.pow(2, retries) * 1000;
               console.log(`Rate limited. Retrying in ${delay}ms...`);
               await new Promise(resolve => setTimeout(resolve, delay));
               retries++;
           }
       }
   }
   
   // Usage
   const workItem = await executeWithBackoff(() => witApi.getWorkItem(42));
   ```

2. **Reduce Request Frequency:**
   Batch requests where possible or implement throttling in your application.

## Debugging Connection Issues

### Enable Verbose Logging

To diagnose connection issues, enable request logging:

**Node.js Environment:**
```javascript
// Before creating the WebApi instance
const verbose = true;

process.env.AZURE_DEVOPS_NODE_API_LOG_LEVEL = verbose ? "debug" : "info";
process.env.AZURE_DEVOPS_NODE_API_LOG_OUTPUT = "console";
```

### Check Connection Status Explicitly

Always verify your connection is established before making API calls:

```javascript
let connectionData;
try {
    // Connect and explicitly check connection status
    connectionData = await connection.connect();
    console.log(`Connected to ${connectionData.instanceId}`);
    console.log(`API version: ${connectionData.apiVersion}`);
    
    // Now it's safe to proceed with API calls
    const witApi = await connection.getWorkItemTrackingApi();
} catch (error) {
    console.error("Connection failed:", error.message);
    // Handle error appropriately
}
```

### Test Network Connectivity

Test basic connectivity to Azure DevOps services before debugging API-specific issues:

```bash
# Simple connectivity test using curl
curl -s -o /dev/null -w "%{http_code}" https://dev.azure.com/your-organization
```

## Common Error Codes

| Error Code | Description | Solution |
|------------|-------------|----------|
| TF400813 | User not authorized | Check authentication credentials and permissions |
| TF400898 | Authentication failed | Verify PAT or credentials are valid |
| TF400904 | PAT has expired | Generate a new PAT |
| TF401013 | Invalid OAuth token | Refresh or obtain a new token |
| TF30063 | Account doesn't exist | Check organization URL |
| TF400897 | JWT token expired | Refresh OAuth token |
| TF429012 | Too many requests | Implement rate limiting and backoff |

## See Also

- [WebApi Core Documentation](../api-reference/webapi-core/webapi-core.md)
- [Authentication Handlers](../api-reference/webapi-core/authentication-handlers.md)
- [Connection Options](../api-reference/webapi-core/connection-options.md)
- [Connect to Azure DevOps Tutorial](../tutorials/connect-to-azure-devops.md) 