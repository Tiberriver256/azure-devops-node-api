# Diagnostic Tools

> **Navigation**: [Home](../index.md) > Diagnostic Tools

This page provides diagnostic tools and utilities to help you gather more information about issues you're experiencing with the Azure DevOps Node API. These tools can help identify the root cause of problems and provide data that will be useful when seeking support.

## Connection Diagnostics

<details>
<summary><b>Network Connectivity Test</b> | ⏱️ 2 minutes | 🟢 Low Severity</summary>

```typescript
// Basic connectivity test to Azure DevOps
const https = require('https');

function testConnectivity(url = 'https://dev.azure.com') {
  console.log(`Testing connectivity to ${url}...`);
  
  return new Promise((resolve, reject) => {
    const startTime = Date.now();
    
    https.get(url, (res) => {
      const endTime = Date.now();
      const responseTime = endTime - startTime;
      
      console.log(`Status code: ${res.statusCode}`);
      console.log(`Response time: ${responseTime}ms`);
      
      if (res.statusCode >= 200 && res.statusCode < 300) {
        console.log('✅ Connection successful');
        resolve({
          success: true,
          statusCode: res.statusCode,
          responseTime
        });
      } else {
        console.log('❌ Connection failed with status code:', res.statusCode);
        resolve({
          success: false,
          statusCode: res.statusCode,
          responseTime
        });
      }
    }).on('error', (err) => {
      console.error('❌ Connection error:', err.message);
      reject(err);
    });
  });
}

// Usage
// testConnectivity('https://dev.azure.com/your-organization');
```

</details>

<details>
<summary><b>Proxy Configuration Checker</b> | ⏱️ 5 minutes | 🟡 Medium Severity</summary>

```typescript
// Check proxy configuration
function checkProxyConfiguration() {
  console.log('Checking proxy configuration...');
  
  const proxyEnvVars = [
    'HTTP_PROXY',
    'HTTPS_PROXY',
    'NO_PROXY',
    'http_proxy',
    'https_proxy',
    'no_proxy'
  ];
  
  let proxyConfigured = false;
  
  proxyEnvVars.forEach(varName => {
    if (process.env[varName]) {
      console.log(`✅ ${varName} is set to: ${process.env[varName]}`);
      proxyConfigured = true;
    }
  });
  
  if (!proxyConfigured) {
    console.log('❌ No proxy environment variables detected');
  }
  
  // Check Node.js proxy agent if using request or axios
  try {
    const httpProxyAgent = require('http-proxy-agent');
    const httpsProxyAgent = require('https-proxy-agent');
    console.log('✅ Proxy agent libraries are available');
  } catch (err) {
    console.log('❌ Proxy agent libraries not found. If you need proxy support, install them:');
    console.log('npm install http-proxy-agent https-proxy-agent');
  }
  
  return proxyConfigured;
}

// Usage
// checkProxyConfiguration();
```

</details>

## Authentication Diagnostics

<details>
<summary><b>Token Validator</b> | ⏱️ 3 minutes | 🔴 High Severity</summary>

```typescript
// Validate PAT token format and test authentication
import * as azdev from 'azure-devops-node-api';

async function validateToken(token: string, orgUrl: string): Promise<void> {
  console.log('Validating token...');
  
  // Check token format
  if (!token || token.length === 0) {
    console.error('❌ Token is empty or undefined');
    return;
  }
  
  // Check for common issues
  if (token.includes('\n') || token.includes('\r')) {
    console.warn('⚠️ Token contains newline characters that may cause issues');
    token = token.trim();
  }
  
  if (token.length < 30) {
    console.warn('⚠️ Token appears to be too short for a typical PAT');
  }
  
  console.log('✅ Basic token format check passed');
  
  // Test authentication
  try {
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    console.log('Attempting to connect to Azure DevOps...');
    const connData = await connection.connect();
    
    console.log('✅ Authentication successful!');
    console.log(`Authenticated as: ${connData.authenticatedUser.providerDisplayName}`);
    console.log(`User ID: ${connData.authenticatedUser.id}`);
    
    // Test a simple API call
    try {
      const coreApi = await connection.getCoreApi();
      const projects = await coreApi.getProjects();
      console.log(`✅ Successfully retrieved ${projects.length} projects`);
    } catch (err) {
      console.error('❌ API call failed despite successful authentication');
      console.error(`Error: ${err.message}`);
      console.log('This suggests a permissions issue rather than an authentication problem');
    }
  } catch (err) {
    console.error('❌ Authentication failed');
    console.error(`Error: ${err.message}`);
    
    if (err.message.includes('TF400813')) {
      console.log('This error indicates an invalid or revoked token');
    } else if (err.message.includes('unable to get local issuer certificate')) {
      console.log('This error indicates a network or SSL certificate issue');
    }
  }
}

// Usage
// validateToken(process.env.AZURE_DEVOPS_TOKEN, 'https://dev.azure.com/your-organization');
```

</details>

<details>
<summary><b>Token Scope Analyzer</b> | ⏱️ 5 minutes | 🟡 Medium Severity</summary>

```typescript
// This is a conceptual tool - Azure DevOps API doesn't provide a direct way to check token scopes
// You'll need to manually check the scopes in the Azure DevOps portal

function analyzeTokenScopes() {
  console.log('Token Scope Analyzer');
  console.log('=====================');
  console.log('To check your token scopes:');
  console.log('1. Go to https://dev.azure.com/{your-organization}/_usersSettings/tokens');
  console.log('2. Find your token in the list');
  console.log('3. Check the scopes that were assigned when it was created');
  console.log('\nCommon required scopes for different operations:');
  console.log('- Code (read): For accessing repositories');
  console.log('- Code (write): For creating commits, PRs');
  console.log('- Build (read): For accessing build definitions and results');
  console.log('- Work Items (read/write): For accessing or modifying work items');
  console.log('- Project and Team (read): For accessing project information');
  console.log('\nIf you get 403 Forbidden errors, you likely need additional scopes');
}

// Usage
// analyzeTokenScopes();
```

</details>

## API Usage Diagnostics

<details>
<summary><b>API Request Logger</b> | ⏱️ 10 minutes | 🟡 Medium Severity</summary>

```typescript
// Log API requests for debugging
import * as azdev from 'azure-devops-node-api';

function createLoggingHandler(token: string) {
  const handler = azdev.getPersonalAccessTokenHandler(token);
  const originalFetch = handler.prepareRequest.bind(handler);
  
  handler.prepareRequest = (options) => {
    console.log(`🔍 API Request: ${options.method} ${options.url}`);
    
    const start = Date.now();
    const originalCallback = options.callback;
    
    options.callback = (err, response, body) => {
      const duration = Date.now() - start;
      
      if (err) {
        console.error(`❌ API Error (${duration}ms):`, err.message);
      } else {
        console.log(`✅ API Response (${duration}ms): Status ${response.statusCode}`);
        
        if (response.statusCode >= 400) {
          console.error(`Response body: ${body}`);
        }
      }
      
      if (originalCallback) {
        originalCallback(err, response, body);
      }
    };
    
    return originalFetch(options);
  };
  
  return handler;
}

// Usage
// const token = process.env.AZURE_DEVOPS_TOKEN;
// const orgUrl = 'https://dev.azure.com/your-organization';
// const loggingHandler = createLoggingHandler(token);
// const connection = new azdev.WebApi(orgUrl, loggingHandler);
```

</details>

<details>
<summary><b>Rate Limit Monitor</b> | ⏱️ 5 minutes | 🟡 Medium Severity</summary>

```typescript
// Monitor rate limits in API responses
import * as azdev from 'azure-devops-node-api';

function createRateLimitMonitor(token: string) {
  const handler = azdev.getPersonalAccessTokenHandler(token);
  const originalFetch = handler.prepareRequest.bind(handler);
  
  handler.prepareRequest = (options) => {
    const originalCallback = options.callback;
    
    options.callback = (err, response, body) => {
      if (response) {
        // Check for rate limit headers
        const remaining = response.headers['x-ratelimit-remaining'];
        const limit = response.headers['x-ratelimit-limit'];
        const reset = response.headers['x-ratelimit-reset'];
        
        if (remaining && limit) {
          console.log(`Rate limit: ${remaining}/${limit} requests remaining`);
          
          // Warn if getting close to limit
          if (parseInt(remaining) < parseInt(limit) * 0.1) {
            console.warn('⚠️ Approaching rate limit! Consider throttling requests.');
          }
        }
        
        // Check for 429 Too Many Requests
        if (response.statusCode === 429) {
          console.error('❌ Rate limit exceeded! Need to wait before making more requests.');
          
          if (reset) {
            const resetTime = new Date(parseInt(reset) * 1000);
            console.log(`Rate limit resets at: ${resetTime.toLocaleString()}`);
          }
        }
      }
      
      if (originalCallback) {
        originalCallback(err, response, body);
      }
    };
    
    return originalFetch(options);
  };
  
  return handler;
}

// Usage
// const token = process.env.AZURE_DEVOPS_TOKEN;
// const orgUrl = 'https://dev.azure.com/your-organization';
// const rateLimitHandler = createRateLimitMonitor(token);
// const connection = new azdev.WebApi(orgUrl, rateLimitHandler);
```

</details>

## Environment Diagnostics

<details>
<summary><b>Environment Information Collector</b> | ⏱️ 2 minutes | 🟢 Low Severity</summary>

```typescript
// Collect environment information for troubleshooting
function collectEnvironmentInfo() {
  const info = {
    nodeVersion: process.version,
    platform: process.platform,
    architecture: process.arch,
    env: {
      NODE_ENV: process.env.NODE_ENV || 'not set',
      NODE_TLS_REJECT_UNAUTHORIZED: process.env.NODE_TLS_REJECT_UNAUTHORIZED || 'not set',
      HTTP_PROXY: process.env.HTTP_PROXY || process.env.http_proxy || 'not set',
      HTTPS_PROXY: process.env.HTTPS_PROXY || process.env.https_proxy || 'not set'
    }
  };
  
  // Get package versions
  try {
    const packageJson = require('./package.json');
    info.dependencies = packageJson.dependencies || {};
    info.devDependencies = packageJson.devDependencies || {};
  } catch (err) {
    info.dependencies = 'Could not read package.json';
  }
  
  console.log('Environment Information:');
  console.log(JSON.stringify(info, null, 2));
  
  return info;
}

// Usage
// collectEnvironmentInfo();
```

</details>

<details>
<summary><b>SSL/TLS Configuration Checker</b> | ⏱️ 5 minutes | 🟡 Medium Severity</summary>

```typescript
// Check SSL/TLS configuration
function checkSslConfiguration() {
  console.log('Checking SSL/TLS configuration...');
  
  // Check if certificate validation is disabled (not recommended for production)
  const rejectUnauthorized = process.env.NODE_TLS_REJECT_UNAUTHORIZED;
  if (rejectUnauthorized === '0') {
    console.warn('⚠️ WARNING: SSL certificate validation is disabled (NODE_TLS_REJECT_UNAUTHORIZED=0)');
    console.warn('This is a security risk and should not be used in production environments');
  } else {
    console.log('✅ SSL certificate validation is enabled');
  }
  
  // Check OpenSSL version
  const crypto = require('crypto');
  console.log(`OpenSSL version: ${crypto.constants.OPENSSL_VERSION_TEXT}`);
  
  // Test HTTPS connection with detailed SSL info
  const https = require('https');
  const url = 'https://dev.azure.com';
  
  console.log(`Testing HTTPS connection to ${url}...`);
  const req = https.get(url, (res) => {
    console.log(`✅ HTTPS connection successful (status ${res.statusCode})`);
    console.log('TLS information:');
    console.log(`- Protocol: ${res.socket.getProtocol()}`);
    console.log(`- Cipher: ${res.socket.getCipher().name}`);
    
    res.on('data', () => {});
    res.on('end', () => {});
  });
  
  req.on('error', (err) => {
    console.error(`❌ HTTPS connection failed: ${err.message}`);
    
    if (err.message.includes('certificate')) {
      console.log('This appears to be a certificate validation issue.');
      console.log('Possible solutions:');
      console.log('1. Update your CA certificates');
      console.log('2. Use a custom certificate authority');
      console.log('3. Only in development: set NODE_TLS_REJECT_UNAUTHORIZED=0');
    }
  });
  
  req.end();
}

// Usage
// checkSslConfiguration();
```

</details>

## Comprehensive Diagnostics

<details>
<summary><b>Full Diagnostic Suite</b> | ⏱️ 15 minutes | 🟡 Medium Severity</summary>

```typescript
// Run all diagnostics in sequence
import * as azdev from 'azure-devops-node-api';

async function runFullDiagnostics(token: string, orgUrl: string) {
  console.log('🔍 Running full diagnostic suite...');
  console.log('==================================');
  
  // 1. Environment information
  console.log('\n📊 ENVIRONMENT INFORMATION:');
  collectEnvironmentInfo();
  
  // 2. Network connectivity
  console.log('\n🌐 NETWORK CONNECTIVITY:');
  try {
    await testConnectivity(orgUrl);
  } catch (err) {
    console.error('Network connectivity test failed:', err.message);
  }
  
  // 3. Proxy configuration
  console.log('\n🔌 PROXY CONFIGURATION:');
  checkProxyConfiguration();
  
  // 4. SSL/TLS configuration
  console.log('\n🔒 SSL/TLS CONFIGURATION:');
  checkSslConfiguration();
  
  // 5. Token validation
  console.log('\n🔑 TOKEN VALIDATION:');
  try {
    await validateToken(token, orgUrl);
  } catch (err) {
    console.error('Token validation failed:', err.message);
  }
  
  console.log('\n✅ Diagnostic suite completed');
  console.log('If you need to share these results with support, please remove any sensitive information first.');
}

// Usage
// const token = process.env.AZURE_DEVOPS_TOKEN;
// const orgUrl = 'https://dev.azure.com/your-organization';
// runFullDiagnostics(token, orgUrl);
```

</details>

## Using the Diagnostic Tools

1. Copy the diagnostic tool code you need into a new TypeScript file (e.g., `diagnostics.ts`)
2. Install the required dependencies:
   ```bash
   npm install azure-devops-node-api
   ```
3. Set your Azure DevOps token as an environment variable:
   ```bash
   export AZURE_DEVOPS_TOKEN=your-pat-token
   ```
4. Run the diagnostic tool:
   ```bash
   ts-node diagnostics.ts
   ```
5. Review the output for insights into your issue

## Next Steps

After running diagnostics:

1. Review the results to identify potential issues
2. Check the specific [problem category](../index.md#common-issue-categories) related to your findings
3. If you still need help, see [Getting Help](./getting-help.md)

---

<div align="left">
  <a href="../index.md">← Back to Home</a>
</div>

<div align="right">
  <a href="./getting-help.md">Next: Getting Help →</a>
</div>

<div align="right">
<i>Last updated: February 26, 2024</i>
</div> 