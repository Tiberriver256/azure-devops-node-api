# OAuth Authentication with Azure DevOps

## Overview

OAuth 2.0 authentication provides a secure way for applications to interact with Azure DevOps on behalf of users without exposing their credentials. This authentication method is particularly useful for:

- Web applications where users sign in via Azure AD
- Multi-tenant applications serving multiple organizations
- Applications integrated with the broader Azure ecosystem
- Services that need to perform actions on behalf of users

This guide shows how to implement OAuth 2.0 authentication for Azure DevOps Node API applications using the official `@azure/identity` library, which provides reliable, secure implementations of various authentication flows.

## OAuth 2.0 Concepts

### Key Terminology

| Term | Description |
|------|-------------|
| Resource Owner | The user who authorizes an application to access their data |
| Client | The application requesting access to a resource server |
| Authorization Server | The server issuing access tokens (Azure AD) |
| Resource Server | The server hosting protected resources (Azure DevOps) |
| Access Token | Credential used to access protected resources |
| Refresh Token | Credential used to obtain new access tokens |
| Scope | Permission level granted to the application |

### OAuth 2.0 Flow Types

Azure DevOps supports the following OAuth 2.0 flows:

1. **Authorization Code Flow**: The most common flow for web applications
2. **Device Code Flow**: For devices that cannot display a web interface
3. **Client Credentials Flow**: For server-to-server authentication

## Setting Up OAuth Authentication

### Step 1: Register an Application in Azure AD

Before implementing OAuth, you need to register your application in Azure AD:

1. Sign in to the [Azure Portal](https://portal.azure.com)
2. Navigate to **Azure Active Directory** > **App registrations** > **New registration**
3. Enter a name for your application
4. Set the redirect URI to your application's callback URL
5. Select the supported account types (single tenant or multi-tenant)
6. Click **Register**

Once registered, note the following information:
- Application (client) ID
- Directory (tenant) ID
- Client secret (generate from **Certificates & secrets**)

### Step 2: Configure API Permissions

1. In your application registration, navigate to **API permissions**
2. Click **Add a permission**
3. Select **Azure DevOps** under APIs my organization uses
4. Choose the required permissions based on your application's needs:
   - `user_impersonation` for acting on behalf of users
   - Specific scopes like `vso.work`, `vso.code`, etc.
5. Click **Add permissions**
6. For access to all users, click **Grant admin consent for [Tenant]**

### Step 3: Install Required Packages

The examples in this guide use the official Microsoft libraries for authentication:

```bash
# Install required packages
npm install @azure/identity azure-devops-node-api
```

## Implementing OAuth Authentication Flows

### Authorization Code Flow with PKCE

This flow is suitable for web applications where users sign in through a browser:

#### Backend Code (Node.js with Express)

```typescript
import express from 'express';
import * as azdev from 'azure-devops-node-api';
import { AuthorizationCodeCredential, TokenCredential } from '@azure/identity';
import { SecretClient } from '@azure/keyvault-secrets';
import * as crypto from 'crypto';
import * as url from 'url';

const app = express();
const PORT = 3000;

// OAuth configuration for Azure DevOps
const config = {
    clientId: 'your-client-id',
    clientSecret: 'your-client-secret',
    tenantId: 'your-tenant-id',
    redirectUri: 'http://localhost:3000/callback',
    // Azure DevOps API scope
    scopes: ['499b84ac-1321-427f-aa17-267ca6975798/user_impersonation']
};

// Setup session middleware
app.use(express.session({
    secret: crypto.randomBytes(32).toString('hex'),
    resave: false,
    saveUninitialized: true
}));

// Login route
app.get('/login', (req, res) => {
    // Generate and store PKCE code verifier
    const codeVerifier = crypto.randomBytes(32).toString('base64url');
    
    // Create code challenge (S256 method)
    const codeChallenge = crypto
        .createHash('sha256')
        .update(codeVerifier)
        .digest('base64url');
    
    // Save code verifier in session for later use
    req.session.codeVerifier = codeVerifier;
    
    // Generate state parameter to prevent CSRF
    const state = crypto.randomBytes(16).toString('hex');
    req.session.authState = state;
    
    // Construct authorization URL
    const authorizationUrl = new URL('https://login.microsoftonline.com/' + config.tenantId + '/oauth2/v2.0/authorize');
    authorizationUrl.searchParams.append('client_id', config.clientId);
    authorizationUrl.searchParams.append('response_type', 'code');
    authorizationUrl.searchParams.append('redirect_uri', config.redirectUri);
    authorizationUrl.searchParams.append('scope', config.scopes.join(' '));
    authorizationUrl.searchParams.append('state', state);
    authorizationUrl.searchParams.append('code_challenge', codeChallenge);
    authorizationUrl.searchParams.append('code_challenge_method', 'S256');
    
    // Redirect user to login
    res.redirect(authorizationUrl.toString());
});

// Callback route after user authentication
app.get('/callback', async (req, res) => {
    const { code, state } = req.query;
    
    // Verify state to prevent CSRF attacks
    if (state !== req.session.authState) {
        return res.status(400).send('State validation failed');
    }
    
    try {
        // Create AuthorizationCodeCredential with PKCE
        const credential = new AuthorizationCodeCredential(
            config.tenantId,
            config.clientId,
            config.clientSecret,
            code.toString(),
            config.redirectUri,
            { 
                tokenCachePersistenceOptions: { enabled: true },
                codeVerifier: req.session.codeVerifier  // Use stored code verifier
            }
        );
        
        // Store credential in session for reuse
        req.session.credential = credential;
        
        // Redirect to dashboard
        res.redirect('/dashboard');
        
    } catch (error) {
        console.error('Authentication error:', error.message);
        res.status(500).send('Authentication failed: ' + error.message);
    }
});

// Dashboard route that uses the credential
app.get('/dashboard', async (req, res) => {
    if (!req.session.credential) {
        return res.redirect('/login');
    }
    
    try {
        // Get access token for Azure DevOps API
        const credential = req.session.credential;
        const token = await credential.getToken(config.scopes);
        
        // Use the token with Azure DevOps API
        const authHandler = azdev.getBearerHandler(token.token);
        const connection = new azdev.WebApi('https://dev.azure.com/your-organization', authHandler);
        
        // Test the connection
        const connectionData = await connection.connect();
        
        // Get Git API client
        const gitApi = await connection.getGitApi();
        
        // List repositories
        const repos = await gitApi.getRepositories();
        
        // Render dashboard
        res.send(`
            <h1>Dashboard</h1>
            <p>Connected to: ${connectionData.authenticatedUser.customDisplayName}</p>
            <h2>Your Repositories:</h2>
            <ul>
                ${repos.map(repo => `<li>${repo.name}</li>`).join('')}
            </ul>
            <a href="/logout">Logout</a>
        `);
    } catch (error) {
        console.error('API error:', error);
        res.status(500).send('Error accessing Azure DevOps API: ' + error.message);
    }
});

// Logout route
app.get('/logout', (req, res) => {
    req.session.destroy();
    res.redirect('/');
});

app.listen(PORT, () => {
    console.log(`OAuth demo server running on port ${PORT}`);
});
```

### Device Code Flow

The Device Code flow is useful for applications running on devices that don't have a web browser or have limited input capabilities:

```typescript
import * as azdev from 'azure-devops-node-api';
import { DeviceCodeCredential } from '@azure/identity';

async function authenticateWithDeviceCode() {
    try {
        // Configuration
        const clientId = 'your-client-id';
        const tenantId = 'your-tenant-id';
        const scopes = ['499b84ac-1321-427f-aa17-267ca6975798/user_impersonation'];
        
        console.log('Initializing device code authentication...');
        
        // Create a DeviceCodeCredential
        const credential = new DeviceCodeCredential({
            tenantId: tenantId,
            clientId: clientId,
            userPromptCallback: info => {
                // This callback is called when the device code is ready
                console.log(info.message);
                // The message contains instructions like:
                // "To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code ABCDEF123 to authenticate."
            }
        });
        
        // Get a token - this will trigger the device code flow
        console.log('Please follow the instructions to authenticate:');
        const token = await credential.getToken(scopes);
        
        // Use the token with Azure DevOps API
        const authHandler = azdev.getBearerHandler(token.token);
        const connection = new azdev.WebApi('https://dev.azure.com/your-organization', authHandler);
        
        // Test the connection
        const connectionData = await connection.connect();
        console.log(`Connected to ${connectionData.authenticatedUser.customDisplayName}'s organization`);
        
        return connection;
    } catch (error) {
        console.error('Authentication error:', error.message);
        throw error;
    }
}

// Usage
(async () => {
    try {
        const connection = await authenticateWithDeviceCode();
        
        // Use the connection
        const projectApi = await connection.getCoreApi();
        const projects = await projectApi.getProjects();
        
        console.log('Your projects:');
        projects.forEach(project => {
            console.log(`- ${project.name}`);
        });
    } catch (error) {
        console.error('Error:', error);
    }
})();
```

### Client Credentials Flow (Service-to-Service)

For server applications that need to access Azure DevOps without user interaction:

```typescript
import * as azdev from 'azure-devops-node-api';
import { ClientSecretCredential } from '@azure/identity';

async function authenticateWithClientCredentials() {
    try {
        // Configuration
        const clientId = 'your-client-id';
        const clientSecret = 'your-client-secret';
        const tenantId = 'your-tenant-id';
        const scope = 'https://app.vssps.visualstudio.com/.default'; // Use .default scope
        
        // Create a ClientSecretCredential
        const credential = new ClientSecretCredential(
            tenantId,
            clientId,
            clientSecret
        );
        
        // Get a token for Azure DevOps
        const token = await credential.getToken(scope);
        
        // Use the token with Azure DevOps API
        const authHandler = azdev.getBearerHandler(token.token);
        const connection = new azdev.WebApi('https://dev.azure.com/your-organization', authHandler);
        
        // Test the connection
        const connectionData = await connection.connect();
        console.log('Connected successfully as service principal');
        
        return connection;
    } catch (error) {
        console.error('Authentication error:', error.message);
        throw error;
    }
}

// Usage
(async () => {
    try {
        const connection = await authenticateWithClientCredentials();
        
        // Use the connection
        const buildApi = await connection.getBuildApi();
        const definitions = await buildApi.getDefinitions('your-project');
        
        console.log('Build definitions:');
        definitions.forEach(def => {
            console.log(`- ${def.name}`);
        });
    } catch (error) {
        console.error('Error:', error);
    }
})();
```

## Token Management with Azure Identity

Using `@azure/identity` provides several benefits for token management:

1. **Automatic token caching** - Tokens are cached in memory by default
2. **Transparent token renewal** - Refresh tokens are automatically used to get new access tokens
3. **Persistence options** - Optional token cache persistence for longer sessions
4. **Error handling** - Standard error types and retry policies

### Example: Creating a Reusable Azure DevOps Client

```typescript
import * as azdev from 'azure-devops-node-api';
import { 
    TokenCredential, 
    ChainedTokenCredential, 
    EnvironmentCredential, 
    ManagedIdentityCredential,
    ClientSecretCredential 
} from '@azure/identity';

class AzureDevOpsClient {
    private credential: TokenCredential;
    private connection: azdev.WebApi | null = null;
    private organizationUrl: string;
    private scopes: string[];
    
    constructor(
        organizationUrl: string,
        credential?: TokenCredential
    ) {
        this.organizationUrl = organizationUrl;
        this.scopes = ['499b84ac-1321-427f-aa17-267ca6975798/user_impersonation'];
        
        // If no credential is provided, create a credential chain
        if (!credential) {
            this.credential = new ChainedTokenCredential(
                new EnvironmentCredential(),
                new ManagedIdentityCredential()
                // Add more credential types to the chain as needed
            );
        } else {
            this.credential = credential;
        }
    }
    
    async getConnection(): Promise<azdev.WebApi> {
        if (!this.connection) {
            // Get token
            const token = await this.credential.getToken(this.scopes);
            
            // Create connection
            const authHandler = azdev.getBearerHandler(token.token);
            this.connection = new azdev.WebApi(this.organizationUrl, authHandler);
            
            // Test connection
            await this.connection.connect();
        }
        
        return this.connection;
    }
    
    async getGitApi() {
        const connection = await this.getConnection();
        return connection.getGitApi();
    }
    
    async getWorkItemTrackingApi() {
        const connection = await this.getConnection();
        return connection.getWorkItemTrackingApi();
    }
    
    async getBuildApi() {
        const connection = await this.getConnection();
        return connection.getBuildApi();
    }
    
    static createWithClientSecret(
        organizationUrl: string,
        tenantId: string,
        clientId: string,
        clientSecret: string
    ): AzureDevOpsClient {
        const credential = new ClientSecretCredential(
            tenantId,
            clientId,
            clientSecret
        );
        
        return new AzureDevOpsClient(organizationUrl, credential);
    }
}

// Usage
async function main() {
    try {
        // Option 1: Use environment variables and managed identity
        const client1 = new AzureDevOpsClient('https://dev.azure.com/your-organization');
        
        // Option 2: Use client credentials
        const client2 = AzureDevOpsClient.createWithClientSecret(
            'https://dev.azure.com/your-organization',
            'your-tenant-id',
            'your-client-id',
            'your-client-secret'
        );
        
        // Get repositories
        const gitApi = await client1.getGitApi();
        const repositories = await gitApi.getRepositories();
        
        console.log(`Found ${repositories.length} repositories`);
    } catch (error) {
        console.error('Error:', error);
    }
}

main();
```

## Troubleshooting OAuth Issues

### Common OAuth Errors

| Error Code | Description | Solution |
|------------|-------------|----------|
| `invalid_client` | Client authentication failed | Verify client ID and secret |
| `invalid_grant` | Authorization grant is invalid | Refresh token may be expired or revoked |
| `invalid_scope` | Requested scope is invalid | Verify scope format and permissions |
| `unauthorized_client` | Client is not authorized for this grant type | Check app registration settings |
| `interaction_required` | User interaction is required | For silent auth, redirect to interactive auth |

### Debugging OAuth Authentication

`@azure/identity` provides built-in logging capabilities:

```typescript
import { setLogLevel } from '@azure/logger';

// Enable debug logging
setLogLevel('info');
// For even more detail:
// setLogLevel('verbose');

// Log output will go to the console
```

You can also examine tokens to understand their content:

```typescript
import { decodeJwt } from 'jose';

// Example function to inspect a token
function inspectToken(token: string) {
    try {
        const decodedToken = decodeJwt(token);
        console.log('Token payload:', decodedToken);
        console.log('Expires at:', new Date(decodedToken.exp! * 1000).toISOString());
        console.log('Issued at:', new Date(decodedToken.iat! * 1000).toISOString());
        console.log('Scopes:', decodedToken.scp || decodedToken.scope);
        return decodedToken;
    } catch (error) {
        console.error('Error decoding token:', error);
        return null;
    }
}
```

## Security Best Practices

1. **Store client secrets securely**: Use environment variables, Azure Key Vault, or other secret management solutions
2. **Implement PKCE**: Always use PKCE with authorization code flow for public clients
3. **Utilize Managed Identities**: In Azure, use managed identities when possible to avoid storing credentials
4. **Use token cache encryption**: Enable encrypted persistence for token caches
5. **Set appropriate scopes**: Request only the permissions your application needs
6. **Implement proper logout**: Clear token caches and sessions when logging out
7. **Validate all input**: Sanitize and validate auth-related parameters
8. **Audit authentication activity**: Monitor for suspicious patterns

## See Also

- [Authentication Overview](./authentication.md)
- [@azure/identity Documentation](https://learn.microsoft.com/en-us/javascript/api/overview/azure/identity-readme?view=azure-node-latest)
- [Microsoft Identity Platform](https://docs.microsoft.com/en-us/azure/active-directory/develop/)
- [WebApi Core Documentation](../api-reference/webapi-core/webapi-core.md)
- [Connection Options](../api-reference/webapi-core/connection-options.md)
- [Troubleshooting Connection Issues](../troubleshooting/connection-issues.md) 