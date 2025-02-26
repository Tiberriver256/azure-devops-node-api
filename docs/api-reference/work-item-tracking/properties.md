**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > [WorkItemTrackingApi](./README.md) > Properties

# WorkItemTrackingApi Properties

This document details the properties available in the WorkItemTrackingApi class.

## Properties Table

| Name | Type | Description | Access |
|------|------|-------------|--------|
| `client` | `RestClient` | The underlying REST client used for API requests | protected |
| `baseUrl` | `string` | The base URL for the Azure DevOps organization | protected |

---

## Property Details

### client

```typescript
protected client: RestClient
```

The underlying REST client that handles HTTP requests to the Azure DevOps API endpoints. This client manages authentication, serialization, and response parsing.

📝 **Note**: 
This property is protected and intended for internal use within the class or its subclasses. Client code should use the public methods of the class instead of accessing this property directly.

### baseUrl

```typescript
protected baseUrl: string
```

The base URL for the Azure DevOps organization (e.g., "https://dev.azure.com/organization"). This URL is used as the foundation for all API requests made by the class.

📝 **Note**: 
This property is protected and intended for internal use within the class or its subclasses.

## Usage Context

These protected properties are used internally by the WorkItemTrackingApi class to manage connections and execute requests against the Azure DevOps REST API. They are not intended to be accessed directly by client code.

```typescript
// Internal example (within class methods)
protected async getWorkItemInternal(id: number): Promise<WorkItem> {
  const url = `${this.baseUrl}/_apis/wit/workitems/${id}`;
  return await this.client.get(url);
}
```

## See Also

- [WorkItemTrackingApi Class](./README.md)
- [Constructor](./constructor.md)
- [Methods](./methods/README.md)