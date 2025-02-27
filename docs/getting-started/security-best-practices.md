# Security Best Practices for Authentication

## Overview

Securing your authentication credentials is critical when working with the Azure DevOps Node API. This guide outlines best practices for managing and protecting your authentication tokens and credentials to prevent unauthorized access to your Azure DevOps resources.

> **⚠️ WARNING**: Failing to properly secure authentication credentials can lead to unauthorized access, data breaches, and potentially significant security incidents.

## Types of Credentials

The Azure DevOps Node API supports several types of authentication credentials:

| Credential Type | Security Level | Usage | Storage Sensitivity |
|-----------------|----------------|-------|---------------------|
| Personal Access Tokens (PATs) | High | Most scenarios | Highly sensitive |
| Basic Auth Credentials | Medium | Development, testing | Highly sensitive |
| OAuth Tokens | High | User-centric apps | Highly sensitive |
| Service Principal Secrets | High | Server-to-server | Highly sensitive |

## General Security Principles

### 1. Never Hardcode Credentials

❌ **Bad practice**:
```typescript
// Credentials hardcoded directly in source code
const pat = "abcdefghijklmnopqrstuvwxyz1234567890";
const authHandler = azdev.getPersonalAccessTokenHandler(pat);
```

✅ **Good practice**:
```typescript
// Credentials from environment variables
const pat = process.env.AZURE_DEVOPS_PAT;
if (!pat) {
    throw new Error("Missing AZURE_DEVOPS_PAT environment variable");
}
const authHandler = azdev.getPersonalAccessTokenHandler(pat);
```

### 2. Use Environment Variables

Environment variables are a safer way to store credentials than hardcoding them in your source code:

```bash
# Set environment variables in bash/zsh
export AZURE_DEVOPS_PAT="your-pat-token"
export AZURE_DEVOPS_ORG="https://dev.azure.com/your-organization"

# Windows Command Prompt
set AZURE_DEVOPS_PAT=your-pat-token
set AZURE_DEVOPS_ORG=https://dev.azure.com/your-organization

# PowerShell
$env:AZURE_DEVOPS_PAT="your-pat-token"
$env:AZURE_DEVOPS_ORG="https://dev.azure.com/your-organization"
```

### 3. Use Secret Management Services

For production applications, consider using a dedicated secret management service:

#### Azure Key Vault

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { SecretClient } from "@azure/keyvault-secrets";
import * as azdev from "azure-devops-node-api";

async function getAzureDevOpsConnection() {
    // Create a secret client using managed identity or service principal
    const credential = new DefaultAzureCredential();
    const vaultUrl = "https://your-vault.vault.azure.net/";
    const secretClient = new SecretClient(vaultUrl, credential);
    
    // Retrieve the PAT from Key Vault
    const patSecret = await secretClient.getSecret("azure-devops-pat");
    const orgUrlSecret = await secretClient.getSecret("azure-devops-org-url");
    
    // Create the connection using the retrieved secrets
    const authHandler = azdev.getPersonalAccessTokenHandler(patSecret.value);
    return new azdev.WebApi(orgUrlSecret.value, authHandler);
}
```

#### AWS Secrets Manager

```typescript
import { SecretsManagerClient, GetSecretValueCommand } from "@aws-sdk/client-secrets-manager";
import * as azdev from "azure-devops-node-api";

async function getAzureDevOpsConnection() {
    // Create a Secrets Manager client
    const client = new SecretsManagerClient({ region: "us-east-1" });
    
    // Retrieve the PAT from Secrets Manager
    const patCommand = new GetSecretValueCommand({ SecretId: "azure-devops-pat" });
    const orgUrlCommand = new GetSecretValueCommand({ SecretId: "azure-devops-org-url" });
    
    const patResponse = await client.send(patCommand);
    const orgUrlResponse = await client.send(orgUrlCommand);
    
    // Create the connection using the retrieved secrets
    const authHandler = azdev.getPersonalAccessTokenHandler(patResponse.SecretString);
    return new azdev.WebApi(orgUrlResponse.SecretString, authHandler);
}
```

### 4. Use Configuration Files Securely

If you must use configuration files, ensure they are:
- Not committed to source control
- Protected with appropriate file permissions
- Encrypted when possible

```typescript
import * as fs from "fs";
import * as path from "path";
import * as azdev from "azure-devops-node-api";

// Load configuration from a file not tracked in git
function loadConfig() {
    try {
        const configPath = path.join(process.cwd(), '.config', 'credentials.json');
        const configData = fs.readFileSync(configPath, 'utf8');
        return JSON.parse(configData);
    } catch (error) {
        throw new Error(`Failed to load config: ${error.message}`);
    }
}

async function getConnection() {
    const config = loadConfig();
    const authHandler = azdev.getPersonalAccessTokenHandler(config.pat);
    return new azdev.WebApi(config.orgUrl, authHandler);
}
```

Make sure to add the configuration file to `.gitignore`:

```
# .gitignore
.config/credentials.json
```

## Token Management Best Practices

### 1. Use the Principle of Least Privilege

When creating a PAT or configuring OAuth permissions, request only the scopes your application actually needs:

| If you need to... | Request only these scopes |
|-------------------|---------------------------|
| Read work items | `vso.work_read` |
| Read and write work items | `vso.work` |
| Read code | `vso.code_read` |
| Read and write code | `vso.code` |
| Read build definitions | `vso.build_read` |
| Read and write build definitions | `vso.build` |

### 2. Implement Token Rotation

Regularly rotate your credentials to minimize the impact of potential leaks:

```typescript
import * as azdev from "azure-devops-node-api";
import * as fs from "fs";
import * as path from "path";

// Function to check if token needs rotation
function checkTokenRotation() {
    const configPath = path.join(process.cwd(), '.config', 'token-metadata.json');
    let metadata;
    
    try {
        metadata = JSON.parse(fs.readFileSync(configPath, 'utf8'));
    } catch (error) {
        // If file doesn't exist or is invalid, create new metadata
        metadata = { 
            created: Date.now(),
            rotated: Date.now()
        };
    }
    
    const daysSinceRotation = (Date.now() - metadata.rotated) / (1000 * 60 * 60 * 24);
    
    // If token is older than 90 days, it's time to rotate
    if (daysSinceRotation > 90) {
        console.warn("⚠️ Your PAT is over 90 days old. Consider rotating it for security.");
        // Log or notify administrator to rotate token
    }
    
    return metadata;
}
```

### 3. Set Appropriate Token Expirations

Always set expiration dates on your tokens:

- For development: 30 days or less
- For CI/CD pipelines: 90 days with rotation reminders
- For critical systems: As short as practical with automated rotation

### 4. Monitor Token Usage

Implement monitoring to detect unusual patterns in token usage:

```typescript
import * as azdev from "azure-devops-node-api";

// Wrapper class with usage tracking
class AzureDevOpsClient {
    private connection: azdev.WebApi;
    private usageMetrics: {
        lastUsed: Date;
        requestCount: number;
        apiCalls: Record<string, number>;
    };
    
    constructor(orgUrl: string, authHandler: azdev.IRequestHandler) {
        this.connection = new azdev.WebApi(orgUrl, authHandler);
        this.usageMetrics = {
            lastUsed: new Date(),
            requestCount: 0,
            apiCalls: {}
        };
    }
    
    async getGitApi() {
        this.trackUsage('git');
        return await this.connection.getGitApi();
    }
    
    async getBuildApi() {
        this.trackUsage('build');
        return await this.connection.getBuildApi();
    }
    
    async getWorkItemTrackingApi() {
        this.trackUsage('workItemTracking');
        return await this.connection.getWorkItemTrackingApi();
    }
    
    private trackUsage(apiName: string) {
        this.usageMetrics.lastUsed = new Date();
        this.usageMetrics.requestCount++;
        this.usageMetrics.apiCalls[apiName] = (this.usageMetrics.apiCalls[apiName] || 0) + 1;
        
        // You could send these metrics to a logging system
        console.log(`API usage: ${apiName}, total requests: ${this.usageMetrics.requestCount}`);
        
        // Check for unusual patterns
        this.detectAnomalies();
    }
    
    private detectAnomalies() {
        // Example: detect unusually high request rates
        const recentRequests = this.usageMetrics.requestCount;
        if (recentRequests > 1000) {
            console.warn("⚠️ Unusually high number of API requests detected");
            // Alert or take action
        }
    }
}
```

## Securing Different Authentication Methods

### Personal Access Tokens (PATs)

1. **Create dedicated PATs** for each application or use case
2. **Use meaningful names** that identify the purpose when creating PATs
3. **Store PATs securely** using methods described above
4. **Revoke PATs immediately** when no longer needed
5. **Check PAT expirations** regularly, especially for CI/CD systems

### Basic Authentication

1. **Avoid basic authentication** in production environments when possible
2. **Use strong, unique passwords** if basic auth is required
3. **Consider using a PAT as the password** instead of an actual user password
4. **Transmit only over HTTPS** to protect credentials in transit

### OAuth Authentication

1. **Implement PKCE** (Proof Key for Code Exchange) for public clients
2. **Use state parameters** to prevent CSRF attacks
3. **Validate redirect URIs** to prevent token hijacking
4. **Store refresh tokens securely** with the same precautions as other credentials
5. **Implement proper token refresh logic** to handle expiration

```typescript
// OAuth best practices example (abbreviated)
app.get('/login', (req, res) => {
    // Generate and store PKCE code verifier
    const codeVerifier = generateRandomString(64);
    // Create code challenge from verifier
    const codeChallenge = base64UrlEncode(
        createHash('sha256').update(codeVerifier).digest()
    );
    // Store in session
    req.session.codeVerifier = codeVerifier;
    
    // Generate state parameter
    const state = generateRandomString(32);
    req.session.oauthState = state;
    
    // Build authorization URL with PKCE and state
    const authUrl = new URL(authorizationEndpoint);
    authUrl.searchParams.append('client_id', clientId);
    authUrl.searchParams.append('response_type', 'code');
    authUrl.searchParams.append('redirect_uri', redirectUri);
    authUrl.searchParams.append('scope', 'offline_access user.read');
    authUrl.searchParams.append('code_challenge', codeChallenge);
    authUrl.searchParams.append('code_challenge_method', 'S256');
    authUrl.searchParams.append('state', state);
    
    res.redirect(authUrl.toString());
});
```

## Handling Credentials in Different Environments

### Development Environment

1. **Use local environment variables** set in your shell or IDE
2. **Consider using tools like `dotenv`** for local environment configuration
3. **Use shorter-lived tokens** to encourage regular rotation

```typescript
// Using dotenv for local development
import dotenv from 'dotenv';
import * as azdev from 'azure-devops-node-api';

// Load environment variables from .env file (in dev only)
if (process.env.NODE_ENV === 'development') {
    dotenv.config();
}

const token = process.env.AZURE_DEVOPS_PAT;
const orgUrl = process.env.AZURE_DEVOPS_ORG;

const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

### CI/CD Pipelines

1. **Use pipeline secrets or variables** to store credentials
2. **Never output secrets in logs** or build artifacts
3. **Scope tokens to specific pipelines** when possible

#### Azure Pipelines
```yaml
# azure-pipelines.yml
variables:
  - group: azure-devops-api-secrets  # Variable group containing secrets

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '16.x'
    
- script: |
    npm install
    npm run test
  env:
    AZURE_DEVOPS_PAT: $(azureDevOpsPat)  # Reference to secret variable
```

#### GitHub Actions
```yaml
# .github/workflows/ci.yml
name: CI

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '16.x'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run tests
      run: npm test
      env:
        AZURE_DEVOPS_PAT: ${{ secrets.AZURE_DEVOPS_PAT }}
```

### Production Environment

1. **Use managed identity** when running in Azure
2. **Use secret management services** (Azure Key Vault, AWS Secrets Manager, etc.)
3. **Implement emergency token revocation** procedures
4. **Set up alerts** for credential expiration and unusual usage

## Responding to Credential Leaks

If you suspect that your credentials have been compromised:

1. **Revoke the token immediately** through the Azure DevOps portal
2. **Audit access logs** for unauthorized activity
3. **Issue new credentials** with different scopes if needed
4. **Investigate the root cause** of the leak
5. **Implement additional safeguards** based on findings

```typescript
// Example emergency revocation script
import * as azdev from 'azure-devops-node-api';
import { IRequestHandler } from 'typed-rest-client/Interfaces';

/**
 * Emergency function to revoke a PAT 
 * Requires a separate admin-level PAT to revoke the compromised one
 */
async function emergencyRevoke(orgUrl: string, adminPat: string, compromisedPatDescriptor: string) {
    try {
        // Create connection with admin token
        const authHandler = azdev.getPersonalAccessTokenHandler(adminPat);
        const connection = new azdev.WebApi(orgUrl, authHandler);
        
        // Get token administration API (Note: This is a simplified example)
        const tokenApi = await connection.getTokenAdministrationApi();
        
        // Revoke the compromised token
        await tokenApi.revokeToken(compromisedPatDescriptor);
        
        console.log('Token successfully revoked');
        
        // Audit recent activity with the token
        const auditApi = await connection.getAuditApi();
        const auditEvents = await auditApi.queryLog({
            startTime: new Date(Date.now() - 7 * 24 * 60 * 60 * 1000), // Last 7 days
            skipToken: null
        });
        
        console.log('Recent audit events to review:', auditEvents);
        
    } catch (error) {
        console.error('Failed to revoke token:', error);
        throw error;
    }
}
```

## See Also

- [Authentication Overview](./authentication.md)
- [OAuth Authentication with Azure DevOps](./oauth-authentication.md)
- [Connection Options](../api-reference/webapi-core/connection-options.md)
- [Troubleshooting Connection Issues](../troubleshooting/connection-issues.md)
- [Azure DevOps Security Documentation](https://docs.microsoft.com/en-us/azure/devops/organizations/security/) 