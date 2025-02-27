# Connection Options

## Overview

When connecting to Azure DevOps services using the WebApi Core, you can configure various connection options to customize the behavior of the REST client. These options allow you to configure proxies, SSL settings, certificates, and other HTTP client behavior.

## IRequestOptions Interface

The WebApi constructor accepts an optional `options` parameter of type `IRequestOptions` that allows you to configure the underlying HTTP client.

```typescript
import * as azdev from "azure-devops-node-api";

const orgUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";
const authHandler = azdev.getPersonalAccessTokenHandler(token);

const options = {
    // Connection options here
};

const connection = new azdev.WebApi(orgUrl, authHandler, options);
```

## Proxy Configuration

You can configure a proxy server for all requests made through the WebApi instance.

```typescript
const options = {
    proxy: {
        proxyUrl: "http://your-proxy-server:8080",
        proxyUsername: "proxy-username",      // Optional
        proxyPassword: "proxy-password",      // Optional
        proxyBypassHosts: [                   // Optional
            "localhost",
            "127.0.0.1"
        ]
    }
};

const connection = new azdev.WebApi(orgUrl, authHandler, options);
```

### Proxy Options

| Option | Type | Description |
|--------|------|-------------|
| `proxyUrl` | string | The URL of the proxy server |
| `proxyUsername` | string | (Optional) Username for proxy authentication |
| `proxyPassword` | string | (Optional) Password for proxy authentication |
| `proxyBypassHosts` | string[] | (Optional) Array of hostnames that should bypass the proxy |

### Environment Variables

In addition to explicit configuration, the WebApi class also reads proxy settings from environment variables:

- `HTTP_PROXY` or `http_proxy`: URL of the proxy server for HTTP requests
- `HTTPS_PROXY` or `https_proxy`: URL of the proxy server for HTTPS requests
- `NO_PROXY` or `no_proxy`: Comma-separated list of hosts that should bypass the proxy

Environment variables will be used if no explicit proxy configuration is provided.

## SSL Configuration

You can configure SSL options for secure connections.

```typescript
const options = {
    ignoreSslError: false,               // Whether to ignore SSL certificate errors
    cert: {                              // Client certificate settings
        caFile: "/path/to/ca-file.pem",  // Path to CA certificate file
        certFile: "/path/to/cert.pem",   // Path to client certificate file
        keyFile: "/path/to/key.pem",     // Path to client key file
        passphrase: "certificate-passphrase" // Passphrase for the key file
    }
};

const connection = new azdev.WebApi(orgUrl, authHandler, options);
```

### SSL Options

| Option | Type | Description |
|--------|------|-------------|
| `ignoreSslError` | boolean | (Optional) Whether to ignore SSL certificate errors (default: false) |
| `cert.caFile` | string | (Optional) Path to CA certificate file |
| `cert.certFile` | string | (Optional) Path to client certificate file |
| `cert.keyFile` | string | (Optional) Path to client key file |
| `cert.passphrase` | string | (Optional) Passphrase for the key file |

### Environment Variables

SSL settings can also be controlled by environment variables:

- `NODE_TLS_REJECT_UNAUTHORIZED`: Set to "0" to ignore SSL certificate errors (equivalent to `ignoreSslError: true`)
- `AZURE_DEVOPS_SSL_CERT_FILE`: Path to client certificate file
- `AZURE_DEVOPS_SSL_KEY_FILE`: Path to client key file
- `AZURE_DEVOPS_SSL_PASSPHRASE`: Passphrase for the key file

## HTTP Options

You can configure various HTTP client options:

```typescript
const options = {
    allowRedirects: true,       // Whether to follow redirects
    maxRedirects: 5,            // Maximum number of redirects to follow
    maxRetries: 3,              // Maximum number of times to retry a failed request
    socketTimeout: 30000,       // Socket timeout in milliseconds
    keepAlive: true,            // Whether to use HTTP KeepAlive
    allowRetries: true          // Whether to retry failed requests
};

const connection = new azdev.WebApi(orgUrl, authHandler, options);
```

### HTTP Options

| Option | Type | Description |
|--------|------|-------------|
| `allowRedirects` | boolean | (Optional) Whether to follow redirects (default: true) |
| `maxRedirects` | number | (Optional) Maximum number of redirects to follow (default: 50) |
| `maxRetries` | number | (Optional) Maximum number of times to retry a failed request (default: 3) |
| `socketTimeout` | number | (Optional) Socket timeout in milliseconds |
| `keepAlive` | boolean | (Optional) Whether to use HTTP KeepAlive (default: true) |
| `allowRetries` | boolean | (Optional) Whether to retry failed requests (default: true) |

## IWebApiRequestSettings

The WebApi constructor also accepts an optional `requestSettings` parameter of type `IWebApiRequestSettings` that allows you to further configure the API client behavior.

```typescript
const requestSettings = {
    userAgent: "my-custom-user-agent"
};

const connection = new azdev.WebApi(orgUrl, authHandler, options, requestSettings);
```

### WebApi Request Settings

| Option | Type | Description |
|--------|------|-------------|
| `userAgent` | string | (Optional) Custom user agent string to use for all requests |

## Applying Settings to Specific API Clients

When getting a specific API client, you can override the server URL and authentication handlers for that client only:

```typescript
// Get the Git API client with default settings
const gitApi = await connection.getGitApi();

// Get the Work Item Tracking API client with a custom server URL
const customServerUrl = "https://custom-server.dev.azure.com/your-organization";
const workItemTrackingApi = await connection.getWorkItemTrackingApi(customServerUrl);

// Get the Build API client with custom authentication handlers
const customHandlers = [
    customAuthHandler1,
    customAuthHandler2
];
const buildApi = await connection.getBuildApi(undefined, customHandlers);
```

## Common Use Cases

### Corporate Proxy

```typescript
const options = {
    proxy: {
        proxyUrl: "http://corporate-proxy:8080",
        proxyUsername: "domain\\username",
        proxyPassword: "password",
        proxyBypassHosts: ["internal.example.com"]
    }
};

const connection = new azdev.WebApi(orgUrl, authHandler, options);
```

### Self-Signed Certificates

```typescript
const options = {
    ignoreSslError: true  // Use with caution in production
};

const connection = new azdev.WebApi(orgUrl, authHandler, options);
```

### Client Certificates

```typescript
const options = {
    cert: {
        certFile: "/path/to/client-cert.pem",
        keyFile: "/path/to/client-key.pem",
        passphrase: "cert-password"
    }
};

const connection = new azdev.WebApi(orgUrl, authHandler, options);
```

### Timeout Configuration

```typescript
const options = {
    socketTimeout: 60000,  // 60 seconds
    allowRetries: true,
    maxRetries: 2
};

const connection = new azdev.WebApi(orgUrl, authHandler, options);
```

## See Also

- [WebApi Core](./webapi-core.md)
- [Authentication Handlers](./authentication-handlers.md)
- [Authentication Guide](../../getting-started/authentication.md)
- [Glossary](../../glossary.md) - Standardized terminology for the Azure DevOps Node API 