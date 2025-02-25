# Tutorial: {User Goal - Start with Action Verb}

![Tutorial difficulty: {Beginner/Intermediate/Advanced}](https://img.shields.io/badge/Level-{Beginner}-green)
![Estimated time: {X} minutes](https://img.shields.io/badge/Time-{15}%20minutes-blue)

{Brief introduction that clearly states the user goal this tutorial will help accomplish. Focus on the outcome rather than the API features used. Keep this to 2-3 sentences.}

## Overview

{Provide a slightly more detailed explanation of what the user will accomplish and why it's valuable. Include:
- The problem this solves
- When to use this approach
- Key benefits
- A brief mention of the main Azure DevOps Node API components that will be used}

![Overview diagram showing the main components and flow](path/to/overview-diagram.png)
*Fig 1: Overview of the {process/workflow} covered in this tutorial*

## Prerequisites

Before you begin, ensure you have:

- Node.js version 14 or higher installed
- Azure DevOps account with appropriate permissions
- {Any other specific requirements for this tutorial}

You should also be familiar with:
- Basic JavaScript/TypeScript
- {Other relevant knowledge areas}

## Basic Implementation

In this section, you'll create a minimal working example to {accomplish the basic goal}.

### Step 1: Set Up Your Project

```bash
# Create a new directory for your project
mkdir my-azuredevops-project
cd my-azuredevops-project

# Initialize npm project
npm init -y

# Install the Azure DevOps Node API package
npm install azure-devops-node-api

# Create a typescript configuration file (if using TypeScript)
npm install typescript @types/node --save-dev
npx tsc --init
```

### Step 2: Authenticate with Azure DevOps

Create a file called `index.js` (or `index.ts` for TypeScript) and add the following code:

```typescript
// Import the Azure DevOps Node API package
import * as azdev from "azure-devops-node-api";

// Replace these values with your own
const organizationUrl = "https://dev.azure.com/your-organization";
const personalAccessToken = "your-personal-access-token";

// Function to initialize the connection to Azure DevOps
async function connectToAzureDevOps() {
  try {
    // Create authentication handler
    const authHandler = azdev.getPersonalAccessTokenHandler(personalAccessToken);
    
    // Create a connection to the Azure DevOps organization
    console.log("Connecting to Azure DevOps...");
    const connection = new azdev.WebApi(organizationUrl, authHandler);
    
    // Test the connection by getting a client
    const coreApi = await connection.getCoreApi();
    const projects = await coreApi.getProjects();
    console.log(`Successfully connected! Found ${projects.length} projects.`);
    
    return connection;
  } catch (error) {
    console.error("Error connecting to Azure DevOps:", error.message);
    throw error;
  }
}

// Main function
async function main() {
  try {
    const connection = await connectToAzureDevOps();
    // We'll add more code here in the next steps
  } catch (error) {
    console.error("Error in main function:", error.message);
  }
}

// Execute the main function
main();
```

💡 **Tip**: Store your personal access token in an environment variable rather than hardcoding it in your script.

### Step 3: {Implement Core Functionality}

Now, add the following code to your `main()` function to {accomplish the core user goal}:

```typescript
async function main() {
  try {
    const connection = await connectToAzureDevOps();
    
    // Get the specific API client needed for this tutorial
    const {apiClient} = await connection.get{ApiClient}Api();
    
    // Implement the core functionality
    const result = await apiClient.{mainMethod}({
      // Required parameters
      param1: "value1",
      param2: "value2"
    });
    
    // Display the result
    console.log("Success! Here's the result:");
    console.log(result);
  } catch (error) {
    console.error("Error in main function:", error.message);
  }
}
```

### Step 4: Run Your Application

Execute your application using the following command:

```bash
# For JavaScript
node index.js

# For TypeScript
npx ts-node index.ts
```

You should see output similar to:

```
Connecting to Azure DevOps...
Successfully connected! Found 3 projects.
Success! Here's the result:
{ /* result object properties */ }
```

**Congratulations!** You've successfully created a basic application that {accomplishes the user goal}.

## Adding Advanced Features

Now that you have a working basic implementation, let's enhance it with additional functionality.

### Feature 1: {Additional Functionality Name}

{Brief explanation of what this feature adds and why it's useful}

![Diagram showing how this feature fits in](path/to/feature1-diagram.png)
*Fig 2: How {Feature 1} extends the basic functionality*

Add the following code to your application:

```typescript
// Add this function to your file
async function implementFeature1(connection, param1) {
  // Get the necessary API client
  const featureApi = await connection.get{FeatureApi}Api();
  
  // Implement the feature
  const featureResult = await featureApi.{featureMethod}({
    // Parameters
    param1: param1,
    options: {
      // Options for this feature
    }
  });
  
  return featureResult;
}

// Modify your main function to use this feature
async function main() {
  try {
    const connection = await connectToAzureDevOps();
    
    // Existing code...
    
    // Add Feature 1
    const featureResult = await implementFeature1(connection, "value");
    console.log("Feature 1 result:", featureResult);
    
  } catch (error) {
    // Error handling
    console.error("Error in main function:", error.message);
  }
}
```

### Feature 2: {Error Handling and Retries}

{Explanation of why proper error handling is important for this functionality}

Enhance your application with robust error handling:

```typescript
// Add this utility function for retries
async function withRetry(operation, maxRetries = 3, delay = 1000) {
  let lastError;
  
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await operation();
    } catch (error) {
      console.warn(`Attempt ${attempt} failed: ${error.message}`);
      lastError = error;
      
      if (attempt < maxRetries) {
        console.log(`Retrying in ${delay/1000} seconds...`);
        await new Promise(resolve => setTimeout(resolve, delay));
        // Exponential backoff
        delay *= 2;
      }
    }
  }
  
  throw new Error(`All ${maxRetries} attempts failed. Last error: ${lastError.message}`);
}

// Modify your main function to use retry logic
async function main() {
  try {
    const connection = await connectToAzureDevOps();
    
    // Use the retry function for operations that might fail
    const result = await withRetry(() => 
      apiClient.{mainMethod}({
        param1: "value1",
        param2: "value2"
      })
    );
    
    console.log("Success with retry handling:", result);
  } catch (error) {
    console.error("Fatal error:", error.message);
  }
}
```

⚠️ **Warning**: Be careful with retry logic on methods that modify data to avoid unintended duplicate operations.

## Common Scenarios

### Scenario 1: {Common Use Case}

{Description of a common scenario where this tutorial would be applied}

```typescript
// Example code for this scenario
async function scenario1Example(connection, projectName) {
  const api = await connection.get{Api}Api();
  
  // Scenario-specific code
  const result = await api.{method}({
    // Parameters for this scenario
    project: projectName,
    // Other parameters...
  });
  
  return result;
}
```

### Scenario 2: {Another Common Use Case}

{Description of another scenario}

```typescript
// Example code for scenario 2
```

## Complete Example

Here's a complete example that combines all the concepts from this tutorial:

```typescript
// Complete example code that can be copied and pasted
import * as azdev from "azure-devops-node-api";

// Configuration
const config = {
  organizationUrl: "https://dev.azure.com/your-organization",
  personalAccessToken: process.env.AZURE_DEVOPS_PAT || "your-personal-access-token",
  project: "YourProject"
};

// Authentication and connection
async function connectToAzureDevOps() {
  const authHandler = azdev.getPersonalAccessTokenHandler(config.personalAccessToken);
  return new azdev.WebApi(config.organizationUrl, authHandler);
}

// Retry utility
async function withRetry(operation, maxRetries = 3, delay = 1000) {
  let lastError;
  
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await operation();
    } catch (error) {
      console.warn(`Attempt ${attempt} failed: ${error.message}`);
      lastError = error;
      
      if (attempt < maxRetries) {
        console.log(`Retrying in ${delay/1000} seconds...`);
        await new Promise(resolve => setTimeout(resolve, delay));
        delay *= 2;
      }
    }
  }
  
  throw new Error(`All ${maxRetries} attempts failed. Last error: ${lastError.message}`);
}

// Core functionality
async function implementCoreFeature(connection) {
  const api = await connection.get{Api}Api();
  
  return await api.{method}({
    // Parameters
  });
}

// Feature 1 implementation
async function implementFeature1(connection, param) {
  const api = await connection.get{FeatureApi}Api();
  
  return await api.{featureMethod}({
    // Parameters
  });
}

// Main function
async function main() {
  try {
    console.log("Starting application...");
    const connection = await connectToAzureDevOps();
    
    // Core functionality with retry
    const result = await withRetry(() => implementCoreFeature(connection));
    console.log("Core functionality result:", result);
    
    // Additional feature
    const featureResult = await implementFeature1(connection, "value");
    console.log("Feature 1 result:", featureResult);
    
    console.log("Application completed successfully!");
  } catch (error) {
    console.error("Application failed:", error.message);
    process.exit(1);
  }
}

// Run the application
main();
```

## Troubleshooting

### Common Issues

#### Problem: 401 Unauthorized Error When Connecting
**Solution:** Your personal access token may have expired or doesn't have sufficient permissions. Generate a new token with the appropriate scopes (typically "{required scopes}").

#### Problem: Cannot Find Module Error
**Solution:** Ensure you have installed all the required packages using npm:
```bash
npm install azure-devops-node-api @types/node typescript
```

#### Problem: {Common Error Message}
**Solution:** {Step-by-step solution}

## Next Steps

Now that you've learned how to {accomplish this user goal}, you might want to explore:

- [Tutorial: {Related Tutorial}](link-to-related-tutorial.md) - Learn how to extend this functionality
- [API Reference: {Related API}](link-to-api-reference.md) - Explore the full capabilities of the API used in this tutorial
- [Conceptual Guide: {Related Concept}](link-to-conceptual-guide.md) - Understand the underlying concepts in more depth

## Related Resources

- [Azure DevOps REST API Documentation](https://docs.microsoft.com/en-us/rest/api/azure/devops/?view=azure-devops-rest-6.0)
- [Azure DevOps Node API GitHub Repository](https://github.com/Microsoft/azure-devops-node-api)
- [Stack Overflow: azure-devops-node-api Tag](https://stackoverflow.com/questions/tagged/azure-devops-node-api)

---

*Last updated: {Current date}* 