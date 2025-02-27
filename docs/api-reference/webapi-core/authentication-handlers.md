# Authentication Handlers

## Overview

Authentication handlers are responsible for adding the appropriate authentication information to requests sent to Azure DevOps services. The Azure DevOps Node API provides several factory methods to create authentication handlers for different authentication methods.

## Personal Access Token Handler

The Personal Access Token (PAT) handler is the recommended authentication method for most scenarios. It's secure, easy to use, and supports both user and application scenarios.

### getPersonalAccessTokenHandler

**Purpose**: Creates an authentication handler that uses a Personal Access Token (PAT) for authentication.

**Signature**:
```typescript
function getPersonalAccessTokenHandler(token: string, allowCrossOriginAuthentication?: boolean): IRequestHandler
```

**Parameters**:
- `token`: The Personal Access Token to use for authentication
- `allowCrossOriginAuthentication`: (Optional) Whether to allow cross-origin authentication, defaults to false

**Returns**: An authentication handler that can be used with the WebApi constructor.

**Example**:
```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";

// Get PAT handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Create connection with the PAT handler
const connection = new azdev.WebApi(orgUrl, authHandler);
```

**Usage Notes**:
- PATs can be created in your Azure DevOps profile settings
- Set appropriate scopes for your PAT based on the APIs you need to access
- Store your PATs securely and don't commit them to version control

## Basic Authentication Handler

The Basic Authentication handler uses username and password for authentication. While it works for standard Azure DevOps authentication, Personal Access Tokens are generally preferred for security reasons.

### getBasicHandler

**Purpose**: Creates an authentication handler that uses basic authentication (username/password).

**Signature**:
```typescript
function getBasicHandler(username: string, password: string, allowCrossOriginAuthentication?: boolean): IRequestHandler
```

**Parameters**:
- `username`: The username to use for authentication
- `password`: The password to use for authentication
- `allowCrossOriginAuthentication`: (Optional) Whether to allow cross-origin authentication, defaults to false

**Returns**: An authentication handler that can be used with the WebApi constructor.

**Example**:
```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const username = "your-username";
const password = "your-password";

// Get Basic Authentication handler
const authHandler = azdev.getBasicHandler(username, password);

// Create connection with the Basic Authentication handler
const connection = new azdev.WebApi(orgUrl, authHandler);
```

**Usage Notes**:
- Basic authentication is less secure than PATs and may not work in all scenarios
- Consider using PATs instead of passwords when possible

## Bearer Token Handler

The Bearer Token handler allows authentication using OAuth access tokens or other bearer tokens.

### getBearerHandler

**Purpose**: Creates an authentication handler that uses a bearer token for authentication.

**Signature**:
```typescript
function getBearerHandler(token: string, allowCrossOriginAuthentication?: boolean): IRequestHandler
```

**Parameters**:
- `token`: The bearer token to use for authentication
- `allowCrossOriginAuthentication`: (Optional) Whether to allow cross-origin authentication, defaults to false

**Returns**: An authentication handler that can be used with the WebApi constructor.

**Example**:
```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-bearer-token";

// Get Bearer Token handler
const authHandler = azdev.getBearerHandler(token);

// Create connection with the Bearer Token handler
const connection = new azdev.WebApi(orgUrl, authHandler);

// Alternative: Use the factory method
const connection2 = azdev.WebApi.createWithBearerToken(orgUrl, token);
```

**Usage Notes**:
- Bearer tokens are typically obtained through OAuth flows
- Tokens have a limited lifetime and may need to be refreshed

## NTLM Authentication Handler

The NTLM handler provides Windows-based authentication for environments that require it.

### getNtlmHandler

**Purpose**: Creates an authentication handler that uses NTLM authentication.

**Signature**:
```typescript
function getNtlmHandler(username: string, password: string, workstation?: string, domain?: string): IRequestHandler
```

**Parameters**:
- `username`: The username to use for authentication
- `password`: The password to use for authentication
- `workstation`: (Optional) The workstation name
- `domain`: (Optional) The domain name

**Returns**: An authentication handler that can be used with the WebApi constructor.

**Example**:
```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const username = "your-username";
const password = "your-password";
const domain = "your-domain";

// Get NTLM Authentication handler
const authHandler = azdev.getNtlmHandler(username, password, undefined, domain);

// Create connection with the NTLM Authentication handler
const connection = new azdev.WebApi(orgUrl, authHandler);
```

**Usage Notes**:
- NTLM authentication is primarily used in on-premises deployments
- Not all Azure DevOps scenarios support NTLM authentication

## Custom Authentication Handlers

In addition to the built-in handlers, you can create custom authentication handlers by implementing the `IRequestHandler` interface from the `typed-rest-client` library.

```typescript
import * as azdev from "azure-devops-node-api";
import { IRequestHandler, IRequestOptions } from "typed-rest-client/Interfaces";

class CustomAuthHandler implements IRequestHandler {
    constructor(private customToken: string) {}

    // Prepare request by adding auth headers
    prepareRequest(options: IRequestOptions): void {
        options.headers = options.headers || {};
        options.headers["Authorization"] = `Custom ${this.customToken}`;
    }

    // Handle response (usually just returns response)
    handleResponse(response: Response): Promise<Response> {
        return Promise.resolve(response);
    }
}

// Use custom handler
const orgUrl = "https://dev.azure.com/your-organization";
const customToken = "your-custom-token";
const authHandler = new CustomAuthHandler(customToken);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

## See Also

- [WebApi Core](./webapi-core.md)
- [Authentication Guide](../../getting-started/authentication.md)
- [Connection Options](./connection-options.md) 