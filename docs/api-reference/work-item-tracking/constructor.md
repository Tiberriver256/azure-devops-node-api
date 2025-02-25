# WorkItemTrackingApi Constructor

[◀ Back to WorkItemTrackingApi](./README.md)

---

## Overview

The `WorkItemTrackingApi` class is not typically instantiated directly. Instead, it is obtained through the `getWorkItemTrackingApi()` method of the `WebApi` class.

## Syntax

```typescript
// Not recommended - internal use only
constructor(baseUrl: string, handlers: VsoClientRequestHandler[], options?: IRequestOptions);

// Recommended approach
const connection = new azdev.WebApi(orgUrl, authHandler);
const workItemTrackingApi: WorkItemTrackingApi = await connection.getWorkItemTrackingApi();
```

## Parameters

The constructor is intended for internal use. When using the Azure DevOps Node API, you should obtain an instance of the `WorkItemTrackingApi` class through the `WebApi` class.

## Example

```typescript
import * as azdev from "azure-devops-node-api";

// Create a connection to Azure DevOps
const orgUrl = "https://dev.azure.com/myorganization";
const token = "YOUR_PERSONAL_ACCESS_TOKEN"; // https://dev.azure.com/YOUR_ORG/_usersSettings/tokens
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get the WorkItemTrackingApi instance
const workItemTrackingApi = await connection.getWorkItemTrackingApi();

// Now you can use workItemTrackingApi to interact with work items
const workItem = await workItemTrackingApi.getWorkItem(1);
console.log(`Work Item ${workItem.id}: ${workItem.fields["System.Title"]}`);
```

## Authentication Options

The `WorkItemTrackingApi` class supports various authentication methods through the `WebApi` class:

### Personal Access Token (PAT)

```typescript
const token = "YOUR_PERSONAL_ACCESS_TOKEN";
const authHandler = azdev.getPersonalAccessTokenHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

### Basic Authentication

```typescript
const username = "username";
const password = "password";
const authHandler = azdev.getBasicHandler(username, password);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

### Bearer Token

```typescript
const token = "YOUR_BEARER_TOKEN";
const authHandler = azdev.getBearerHandler(token);
const connection = new azdev.WebApi(orgUrl, authHandler);
```

## See Also

- [WebApi Class](../web-api.md)
- [Authentication Overview](../../authentication.md)
- [Common Scenarios: Authentication](../common-scenarios.md#authentication)

---

## Navigation

- [WorkItemTrackingApi Overview](./README.md)
- [Properties](./properties.md)
- [Methods](./methods/README.md)
- [Examples](./examples.md)
- [Common Scenarios](./common-scenarios.md)
- [Error Handling](./error-handling.md) 