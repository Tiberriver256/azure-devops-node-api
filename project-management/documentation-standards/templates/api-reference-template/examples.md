# {ApiClassName} Examples

[◀ Back to {ApiClassName}](./README.md)

---

## Basic Examples

### Example 1: {Brief description of example 1}

```typescript
// Example 1 code
import * as azdev from "azure-devops-node-api";
import { {ApiClassName} } from "azure-devops-node-api/{ApiClassName}";

async function example1() {
    // Create a connection to Azure DevOps
    const orgUrl = "https://dev.azure.com/your-organization";
    const token = "your-personal-access-token";
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get the {ApiClassName} instance
    const {apiInstanceName}: {ApiClassName} = await connection.get{ApiClassName}();
    
    // Use the {ApiClassName} to {perform an action}
    const {result} = await {apiInstanceName}.{method1}({param1}, {param2});
    
    console.log({result});
}

example1().catch(error => {
    console.error("Error:", error);
});
```

### Example 2: {Brief description of example 2}

```typescript
// Example 2 code
import * as azdev from "azure-devops-node-api";
import { {ApiClassName} } from "azure-devops-node-api/{ApiClassName}";

async function example2() {
    // Create a connection to Azure DevOps
    const orgUrl = "https://dev.azure.com/your-organization";
    const token = "your-personal-access-token";
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get the {ApiClassName} instance
    const {apiInstanceName}: {ApiClassName} = await connection.get{ApiClassName}();
    
    // Use the {ApiClassName} to {perform an action}
    const {result} = await {apiInstanceName}.{method2}({param1}, {param2});
    
    console.log({result});
}

example2().catch(error => {
    console.error("Error:", error);
});
```

## Advanced Examples

### Example 3: {Brief description of example 3}

```typescript
// Example 3 code
import * as azdev from "azure-devops-node-api";
import { {ApiClassName} } from "azure-devops-node-api/{ApiClassName}";

async function example3() {
    // Create a connection to Azure DevOps
    const orgUrl = "https://dev.azure.com/your-organization";
    const token = "your-personal-access-token";
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get the {ApiClassName} instance
    const {apiInstanceName}: {ApiClassName} = await connection.get{ApiClassName}();
    
    // Use the {ApiClassName} to {perform a complex action}
    // Step 1: {description of step 1}
    const {result1} = await {apiInstanceName}.{method3}({param1}, {param2});
    
    // Step 2: {description of step 2}
    const {result2} = await {apiInstanceName}.{method4}({param3}, {result1}.{property});
    
    // Step 3: {description of step 3}
    const {finalResult} = await {apiInstanceName}.{method5}({result2}.{property});
    
    console.log({finalResult});
}

example3().catch(error => {
    console.error("Error:", error);
});
```

### Example 4: {Brief description of example 4}

```typescript
// Example 4 code
import * as azdev from "azure-devops-node-api";
import { {ApiClassName} } from "azure-devops-node-api/{ApiClassName}";

async function example4() {
    // Create a connection to Azure DevOps
    const orgUrl = "https://dev.azure.com/your-organization";
    const token = "your-personal-access-token";
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get the {ApiClassName} instance
    const {apiInstanceName}: {ApiClassName} = await connection.get{ApiClassName}();
    
    // Use the {ApiClassName} to {perform a complex action with error handling}
    try {
        // Step 1: {description of step 1}
        const {result1} = await {apiInstanceName}.{method6}({param1}, {param2});
        
        // Step 2: {description of step 2}
        const {result2} = await {apiInstanceName}.{method7}({param3}, {result1}.{property});
        
        // Step 3: {description of step 3}
        const {finalResult} = await {apiInstanceName}.{method8}({result2}.{property});
        
        console.log({finalResult});
    } catch (error) {
        // Handle specific errors
        if (error.statusCode === 404) {
            console.error("Resource not found:", error.message);
        } else if (error.statusCode === 401) {
            console.error("Authentication error:", error.message);
        } else {
            console.error("Unexpected error:", error);
        }
    }
}

example4().catch(error => {
    console.error("Error:", error);
});
```

## See Also

- [Common Scenarios](./common-scenarios.md)
- [Error Handling](./error-handling.md)
- [Methods Reference](./methods/README.md)

---

## Navigation

- [{ApiClassName} Overview](./README.md)
- [Constructor](./constructor.md)
- [Properties](./properties.md)
- [Methods](./methods/README.md)
- [Common Scenarios](./common-scenarios.md)
- [Error Handling](./error-handling.md) 