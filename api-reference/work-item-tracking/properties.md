# WorkItemTrackingApi Properties

[◀ Back to WorkItemTrackingApi](./README.md)

---

## Overview

The `WorkItemTrackingApi` class has several properties that provide information about the API client and its configuration.

## Properties

| Property | Type | Description |
|----------|------|-------------|
| baseUrl | string | The base URL of the Azure DevOps instance |
| vsoClient | VsoClient | The underlying VSO client used for making requests |
| restClient | RestClient | The REST client used for making HTTP requests |

## Usage

These properties are primarily used internally by the API client. In most cases, you won't need to access these properties directly.

```typescript
// Example of accessing the baseUrl property
console.log(`API Base URL: ${workItemTrackingApi.baseUrl}`);
```

## Inherited Properties

The `WorkItemTrackingApi` class inherits properties from its parent classes:

### From VsoClientBase

| Property | Type | Description |
|----------|------|-------------|
| options | IRequestOptions | Options for making HTTP requests |
| authHandler | IRequestHandler | The authentication handler used for requests |
| userAgent | string | The user agent string sent with requests |

### From RestClientBase

| Property | Type | Description |
|----------|------|-------------|
| client | RestClient | The REST client used for making HTTP requests |
| baseUrl | string | The base URL for API requests |

## See Also

- [VsoClientBase Class](../vso-client-base.md)
- [RestClientBase Class](../rest-client-base.md)
- [IRequestOptions Interface](../interfaces/irequest-options.md)

---

## Navigation

- [WorkItemTrackingApi Overview](./README.md)
- [Constructor](./constructor.md)
- [Methods](./methods/README.md)
- [Examples](./examples.md)
- [Common Scenarios](./common-scenarios.md)
- [Error Handling](./error-handling.md) 