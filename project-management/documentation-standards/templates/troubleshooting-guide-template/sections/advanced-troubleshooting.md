# Advanced Troubleshooting

> **Navigation**: [Home](../index.md) > Advanced Troubleshooting

This page provides advanced troubleshooting techniques for complex issues with the Azure DevOps Node API that aren't resolved by the standard troubleshooting approaches.

> **TL;DR:** Use these advanced techniques when standard troubleshooting methods don't resolve your issue.

## When to Use Advanced Troubleshooting

Use the techniques on this page when:

1. You've tried the solutions in the [problem categories](../index.md#common-issue-categories) without success
2. You're experiencing intermittent or difficult-to-reproduce issues
3. You need to gather detailed diagnostic information for support
4. You're dealing with complex integration scenarios
5. You suspect issues with network, proxies, or firewalls

## Advanced Diagnostic Techniques

### Network Traffic Analysis

<details>
<summary><b>Using Fiddler to Capture API Traffic</b></summary>

[Fiddler](https://www.telerik.com/fiddler) is a powerful web debugging proxy that can capture HTTP(S) traffic between your application and Azure DevOps.

**Setup:**

1. Download and install Fiddler
2. Configure HTTPS decryption:
   - Go to Tools > Options > HTTPS
   - Enable "Decrypt HTTPS traffic"
   - Install the Fiddler root certificate when prompted

**Capturing Azure DevOps API Traffic:**

```typescript
// Configure your application to use Fiddler as a proxy
process.env.HTTP_PROXY = "http://127.0.0.1:8888";
process.env.HTTPS_PROXY = "http://127.0.0.1:8888";

// Your normal API code
const connection = new azdev.WebApi(orgUrl, authHandler);
const projectApi = await connection.getProjectApi();
```

**Analyzing Results:**

1. Look for failed requests (red entries)
2. Examine request and response headers
3. Check for authentication headers
4. Verify the correct API endpoints are being called
5. Look for error responses and their details

</details>

### Detailed Logging and Tracing

<details>
<summary><b>Advanced Logging Configuration</b></summary>

Create a comprehensive logging system to capture detailed information about API interactions:

```typescript
import * as azdev from "azure-devops-node-api";
import * as fs from "fs";
import * as path from "path";

class AdvancedLogger {
  private logDir: string;
  private requestLogFile: string;
  private errorLogFile: string;
  private perfLogFile: string;
  
  constructor(logDirectory = "./logs") {
    this.logDir = logDirectory;
    
    // Create log directory if it doesn't exist
    if (!fs.existsSync(this.logDir)) {
      fs.mkdirSync(this.logDir, { recursive: true });
    }
    
    // Initialize log files with timestamps
    const timestamp = new Date().toISOString().replace(/:/g, "-");
    this.requestLogFile = path.join(this.logDir, `requests-${timestamp}.log`);
    this.errorLogFile = path.join(this.logDir, `errors-${timestamp}.log`);
    this.perfLogFile = path.join(this.logDir, `performance-${timestamp}.log`);
    
    // Initialize log files with headers
    fs.writeFileSync(this.requestLogFile, "TIMESTAMP,AREA,METHOD,URL,STATUS\n");
    fs.writeFileSync(this.errorLogFile, "TIMESTAMP,ERROR_TYPE,MESSAGE,STACK\n");
    fs.writeFileSync(this.perfLogFile, "TIMESTAMP,OPERATION,DURATION_MS\n");
  }
  
  setupWebApiLogging(webApi) {
    webApi.setLogFunction((area, message) => {
      const timestamp = new Date().toISOString();
      
      // Log to request log
      fs.appendFileSync(this.requestLogFile, `${timestamp},${area},${message}\n`);
      
      // Extract and log performance data if available
      if (message.includes("completed in")) {
        const match = message.match(/completed in (\d+)ms/);
        if (match && match[1]) {
          const duration = match[1];
          const operation = message.split(" ")[0]; // Usually the HTTP method
          fs.appendFileSync(this.perfLogFile, `${timestamp},${operation},${duration}\n`);
        }
      }
      
      // Console output for immediate feedback
      console.log(`[${timestamp}] [${area}] ${message}`);
    });
  }
  
  logError(errorType, error) {
    const timestamp = new Date().toISOString();
    const message = error.message || "No message";
    const stack = error.stack || "No stack trace";
    
    // Log to error log
    fs.appendFileSync(
      this.errorLogFile, 
      `${timestamp},${errorType},${message.replace(/,/g, ";")},${stack.replace(/,/g, ";").replace(/\n/g, " ")}\n`
    );
    
    // Console output for immediate feedback
    console.error(`[${timestamp}] [ERROR:${errorType}] ${message}`);
    console.error(stack);
  }
  
  logPerformance(operation, startTime) {
    const endTime = process.hrtime.bigint();
    const duration = Number(endTime - startTime) / 1000000; // Convert to ms
    const timestamp = new Date().toISOString();
    
    // Log to performance log
    fs.appendFileSync(this.perfLogFile, `${timestamp},${operation},${duration}\n`);
    
    // Console output for immediate feedback
    console.log(`[${timestamp}] [PERF] ${operation} completed in ${duration}ms`);
  }
}

// Usage example
const logger = new AdvancedLogger();

// Setup API with logging
const connection = new azdev.WebApi(orgUrl, authHandler);
logger.setupWebApiLogging(connection);

// Performance tracking
async function trackedApiCall(name, apiCall) {
  const startTime = process.hrtime.bigint();
  try {
    const result = await apiCall();
    logger.logPerformance(name, startTime);
    return result;
  } catch (error) {
    logger.logError(name, error);
    throw error;
  }
}

// Example usage
async function getProjects() {
  return trackedApiCall("getProjects", async () => {
    const projectApi = await connection.getProjectApi();
    return projectApi.getProjects();
  });
}
```

</details>

### Memory and Performance Profiling

<details>
<summary><b>Node.js Memory Profiling</b></summary>

For applications experiencing memory leaks or performance issues:

```typescript
import * as v8 from "v8";
import * as fs from "fs";

// Memory snapshot function
function takeHeapSnapshot(name = "snapshot") {
  const snapshotStream = v8.getHeapSnapshot();
  const fileName = `${name}-${Date.now()}.heapsnapshot`;
  const fileStream = fs.createWriteStream(fileName);
  snapshotStream.pipe(fileStream);
  
  console.log(`Heap snapshot written to ${fileName}`);
  
  return new Promise((resolve, reject) => {
    fileStream.on("finish", resolve);
    fileStream.on("error", reject);
  });
}

// Usage example
async function memoryLeakTest() {
  // Take baseline snapshot
  await takeHeapSnapshot("baseline");
  
  // Run your API operations
  for (let i = 0; i < 100; i++) {
    const projectApi = await connection.getProjectApi();
    await projectApi.getProjects();
    
    // Take intermediate snapshots
    if (i % 25 === 0) {
      await takeHeapSnapshot(`iteration-${i}`);
    }
  }
  
  // Take final snapshot
  await takeHeapSnapshot("final");
  
  console.log("Memory test complete. Analyze snapshots with Chrome DevTools.");
}
```

**Analyzing Snapshots:**

1. Open Chrome DevTools
2. Go to the Memory tab
3. Load the heap snapshots
4. Compare snapshots to identify memory growth
5. Look for retained objects that might indicate leaks

</details>

### Authentication Deep Dive

<details>
<summary><b>OAuth Token Inspection</b></summary>

For OAuth authentication issues, inspect the token contents:

```typescript
function decodeJwt(token) {
  if (!token) {
    console.error("No token provided");
    return null;
  }
  
  try {
    // JWT tokens have three parts: header.payload.signature
    const parts = token.split(".");
    if (parts.length !== 3) {
      console.error("Invalid token format");
      return null;
    }
    
    // Decode the payload (middle part)
    const payload = Buffer.from(parts[1], "base64").toString();
    const parsed = JSON.parse(payload);
    
    // Extract key information
    const now = Math.floor(Date.now() / 1000);
    const expiresIn = parsed.exp ? parsed.exp - now : "unknown";
    
    console.log("Token information:");
    console.log(`- Subject: ${parsed.sub || "unknown"}`);
    console.log(`- Issuer: ${parsed.iss || "unknown"}`);
    console.log(`- Audience: ${parsed.aud || "unknown"}`);
    console.log(`- Expires in: ${expiresIn} seconds`);
    console.log(`- Scopes: ${parsed.scp || parsed.scope || "unknown"}`);
    
    return parsed;
  } catch (error) {
    console.error("Error decoding token:", error.message);
    return null;
  }
}

// Usage
const token = process.env.AZURE_DEVOPS_TOKEN;
const decodedToken = decodeJwt(token);
```

</details>

### Proxy and Network Configuration

<details>
<summary><b>Custom Proxy Configuration</b></summary>

For environments with complex proxy requirements:

```typescript
import * as azdev from "azure-devops-node-api";
import * as https from "https";
import * as tunnel from "tunnel"; // npm install tunnel

// Create a custom agent with proxy configuration
function createProxyAgent(proxyUrl, options = {}) {
  const url = new URL(proxyUrl);
  
  const agentOptions = {
    proxy: {
      host: url.hostname,
      port: parseInt(url.port || "80"),
      proxyAuth: url.username && url.password ? 
        `${url.username}:${url.password}` : undefined
    },
    ...options
  };
  
  return tunnel.httpsOverHttp(agentOptions);
}

// Configure Azure DevOps API with custom agent
function createApiWithProxy(orgUrl, token, proxyUrl, options = {}) {
  const authHandler = azdev.getPersonalAccessTokenHandler(token);
  
  // Create custom agent
  const agent = createProxyAgent(proxyUrl, options);
  
  // Configure client options with the agent
  const clientOptions = {
    allowRetries: true,
    maxRetries: 5,
    socketTimeout: 60000,
    proxy: proxyUrl,
    agent: agent,
    ignoreSslError: options.ignoreSslError || false
  };
  
  return new azdev.WebApi(orgUrl, authHandler, clientOptions);
}

// Usage
const connection = createApiWithProxy(
  "https://dev.azure.com/your-organization",
  process.env.AZURE_DEVOPS_TOKEN,
  "http://your-proxy-server:8080",
  { 
    ignoreSslError: false,
    // Additional HTTPS agent options
    rejectUnauthorized: true
  }
);
```

</details>

## Debugging Complex Scenarios

### Concurrent API Calls

<details>
<summary><b>Diagnosing Race Conditions</b></summary>

For issues with concurrent API calls:

```typescript
import * as azdev from "azure-devops-node-api";

// Sequential vs. Parallel execution tester
async function testConcurrencyIssues(orgUrl, token, concurrentCalls = 10) {
  const authHandler = azdev.getPersonalAccessTokenHandler(token);
  const connection = new azdev.WebApi(orgUrl, authHandler);
  
  console.log("Testing API behavior with different concurrency patterns");
  
  // Test 1: Sequential calls
  console.log("\n--- Sequential Calls Test ---");
  console.time("Sequential");
  
  for (let i = 0; i < concurrentCalls; i++) {
    try {
      const projectApi = await connection.getProjectApi();
      const projects = await projectApi.getProjects();
      console.log(`Sequential call ${i+1}: Retrieved ${projects.length} projects`);
    } catch (error) {
      console.error(`Sequential call ${i+1} failed:`, error.message);
    }
  }
  
  console.timeEnd("Sequential");
  
  // Test 2: Parallel calls with Promise.all
  console.log("\n--- Parallel Calls Test ---");
  console.time("Parallel");
  
  const promises = [];
  for (let i = 0; i < concurrentCalls; i++) {
    promises.push((async () => {
      try {
        const projectApi = await connection.getProjectApi();
        const projects = await projectApi.getProjects();
        console.log(`Parallel call ${i+1}: Retrieved ${projects.length} projects`);
        return { success: true, count: projects.length };
      } catch (error) {
        console.error(`Parallel call ${i+1} failed:`, error.message);
        return { success: false, error: error.message };
      }
    })());
  }
  
  const results = await Promise.all(promises);
  const successCount = results.filter(r => r.success).length;
  
  console.log(`Parallel test complete: ${successCount}/${concurrentCalls} successful`);
  console.timeEnd("Parallel");
  
  // Test 3: Controlled concurrency with batching
  console.log("\n--- Controlled Concurrency Test ---");
  console.time("Controlled");
  
  const batchSize = 3; // Process 3 at a time
  for (let i = 0; i < concurrentCalls; i += batchSize) {
    const batchPromises = [];
    
    for (let j = 0; j < batchSize && i + j < concurrentCalls; j++) {
      batchPromises.push((async () => {
        try {
          const projectApi = await connection.getProjectApi();
          const projects = await projectApi.getProjects();
          console.log(`Batch ${Math.floor(i/batchSize)+1}, call ${j+1}: Retrieved ${projects.length} projects`);
          return { success: true, count: projects.length };
        } catch (error) {
          console.error(`Batch ${Math.floor(i/batchSize)+1}, call ${j+1} failed:`, error.message);
          return { success: false, error: error.message };
        }
      })());
    }
    
    await Promise.all(batchPromises);
  }
  
  console.timeEnd("Controlled");
}
```

</details>

### API Version Compatibility

<details>
<summary><b>Testing Different API Versions</b></summary>

For issues that might be related to API version compatibility:

```typescript
import * as azdev from "azure-devops-node-api";

async function testApiVersions(orgUrl, token) {
  const authHandler = azdev.getPersonalAccessTokenHandler(token);
  
  // Test with explicit API versions
  const apiVersions = ["5.0", "5.1", "6.0", "6.1", "7.0", "7.1"];
  
  for (const version of apiVersions) {
    console.log(`\n--- Testing with API version ${version} ---`);
    
    try {
      // Create connection with specific version
      const connection = new azdev.WebApi(orgUrl, authHandler, {
        allowRetries: true,
        maxRetries: 3,
        apiVersion: version
      });
      
      // Test basic connection
      console.log(`Connecting to ${orgUrl} with API version ${version}...`);
      const connectionData = await connection.connect();
      console.log(`✅ Connection successful with API version ${version}`);
      console.log(`- Authenticated as: ${connectionData.authenticatedUser.providerDisplayName}`);
      console.log(`- Server deployment type: ${connectionData.deploymentType}`);
      
      // Test a basic API call
      const projectApi = await connection.getProjectApi();
      const projects = await projectApi.getProjects();
      console.log(`✅ Retrieved ${projects.length} projects with API version ${version}`);
      
    } catch (error) {
      console.error(`❌ API version ${version} test failed:`, error.message);
      if (error.statusCode) {
        console.error(`  Status code: ${error.statusCode}`);
      }
    }
  }
}
```

</details>

## Submitting Effective Support Requests

When standard and advanced troubleshooting doesn't resolve your issue, you may need to contact support. Follow these guidelines to submit an effective support request:

1. **Gather Diagnostic Information**:
   - API version and client library version
   - Node.js version and OS information
   - Complete error messages and stack traces
   - Network logs if available
   - Steps to reproduce the issue

2. **Create a Minimal Reproduction**:
   - Create a small, standalone project that demonstrates the issue
   - Remove any sensitive information
   - Include clear instructions to reproduce the problem

3. **Document Your Troubleshooting Steps**:
   - List the solutions you've already tried
   - Include relevant code snippets and error messages
   - Note any patterns or conditions that trigger the issue

4. **Submit Your Support Request**:
   - For Azure DevOps service issues: [Azure DevOps Support](https://azure.microsoft.com/support/devops/)
   - For API client issues: [GitHub Issues](https://github.com/microsoft/azure-devops-node-api/issues)
   - For community help: [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-devops-api)

## Next Steps

- Return to the [Issue Identification](./issue-identification.md) page to review symptoms
- Check the [Error Code Reference](./error-code-reference.md) for specific error solutions
- If you need additional help, see the [Getting Help](./getting-help.md) page

---

**Related Pages:**
- [Issue Identification](./issue-identification.md)
- [Decision Tree](./decision-tree.md)
- [Error Code Reference](./error-code-reference.md)
- [Getting Help](./getting-help.md) 