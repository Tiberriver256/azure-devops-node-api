**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > [WorkItemTrackingApi](./README.md) > Constructor

# WorkItemTrackingApi Constructor

## Signature

```typescript
constructor(baseUrl: string, handlers: VsoClientRequestHandler, options?: IRequestOptions)
```

Creates a new instance of the `WorkItemTrackingApi` class.

## Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `baseUrl` | `string` | The base URL for the Azure DevOps organization | Yes |
| `handlers` | `VsoClientRequestHandler` | The request handlers for authentication and request processing | Yes |
| `options` | `IRequestOptions` | Optional settings for requests | No |

## Returns

A new instance of `WorkItemTrackingApi`.

## Example

```typescript
// Example of creating an instance of the class
// Note: Typically, you would use the WebApi.getWorkItemTrackingApi() method
// instead of creating this directly
const baseUrl = "https://dev.azure.com/organization";
const handlers = new VsoClientRequestHandler(authHandler);
const workItemTrackingApi = new WorkItemTrackingApi(baseUrl, handlers);
```

⚠️ **Caution**:
In most cases, you should not create this class directly. Instead, use the `WebApi.getWorkItemTrackingApi()` method, which handles authentication and instance creation for you.

## See Also

- [WorkItemTrackingApi Class](./README.md)
- [WebApi Class](../web-api/README.md)
- [Authentication Guide](../../guides/authentication.md)