# Interface Documentation Template Guide

This guide explains how to use the interface documentation template to create consistent, comprehensive documentation for interfaces in the Azure DevOps Node API.

## Template Purpose

The interface documentation template provides a standardized format for documenting TypeScript interfaces in the Azure DevOps Node API. It ensures that:

1. All interfaces are documented with consistent structure and detail
2. Developers can quickly understand the purpose and usage of each interface
3. Key information like properties, methods, and type definitions are clearly presented
4. Examples demonstrate practical usage and implementation patterns
5. Related resources are properly cross-referenced

## When to Use This Template

Use this template when:

- Documenting a new or existing interface in the Azure DevOps Node API
- Updating interface documentation to meet new documentation standards
- Creating documentation for model interfaces, client interfaces, or any other interface types in the API

## Template Sections

### Header

```markdown
# Interface: {InterfaceName}
```

- Replace `{InterfaceName}` with the actual name of the interface you're documenting
- Use PascalCase as it appears in the code (e.g., `IWorkItemTrackingApi`, `GitRef`)

### Overview

```markdown
## Overview

{Provide a brief description of the interface and its purpose within the Azure DevOps Node API. Explain what functionality it defines and when a developer would use this interface. Keep this section concise but informative, focusing on the "what" and "why".}

**Package:** {Package name}  
**Extends:** {Parent interface(s) if applicable}  
**Implemented by:** {Class(es) that implement this interface, if applicable}  
```

- The overview should be 2-3 sentences focused on what the interface defines and why it exists
- Include the package name as it appears in the import statement
- For Extends, list parent interfaces. If none, omit this line
- For Implemented by, list known implementing classes. If none or too many, you can omit this line or provide key examples
- Keep metadata items on separate lines with double spaces after each line for proper markdown formatting

### Description

```markdown
## Description

{Provide a more detailed description of the interface. Explain its role within the API architecture, its relationship to other interfaces and classes, and important design patterns or principles it demonstrates. Include any conceptual information that helps developers understand how to use the interface effectively.}

### Key Features

- {Key feature 1}
- {Key feature 2}
- {Key feature 3}
```

- The description should provide deeper context about the interface's role in the API
- Explain any design patterns, architectural considerations, or implementation details
- List 3-5 key features or capabilities that make this interface useful
- Use bullet points that are concise but descriptive

### Properties

```markdown
## Properties

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `{propertyName1}` | `{Type1}` | {Description of propertyName1} | {Yes/No} |
| `{propertyName2}` | `{Type2}` | {Description of propertyName2} | {Yes/No} |
```

- List all properties defined in the interface
- Use backticks around property names and types for consistent formatting
- Indicate if properties are required or optional (based on TypeScript optional property syntax `?:`)
- For complex property types, link to their type definitions when available

### Methods

```markdown
## Methods

{List of methods defined in the interface, each with a link to detailed documentation}

- [{methodName1}](#methodname1)
- [{methodName2}](#methodname2)
- [{methodName3}](#methodname3)

### {methodName1}

```typescript
{methodName1}({parameter1}: {Type1}, {parameter2}: {Type2}): {ReturnType}
```

{Description of what the method does and when to use it.}

#### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `{parameter1}` | `{Type1}` | {Description of parameter1} | {Yes/No} |
| `{parameter2}` | `{Type2}` | {Description of parameter2} | {Yes/No} |

#### Returns

`{ReturnType}`: {Description of the returned data}
```

- First provide a list of all methods with anchor links for navigation
- Then document each method in detail:
  - Include the exact method signature from the interface definition
  - Describe what the method does and when to use it
  - Document all parameters and return values
  - For async methods, indicate if they return Promises
- Focus on the contract defined by the interface, not implementation details
- If the interface has many methods, prioritize the most commonly used ones

### Type Definitions

```markdown
## Type Definitions

{If the interface includes or references complex types, document them here}

### {TypeName1}

```typescript
type {TypeName1} = {
  {property1}: {PropertyType1};
  {property2}: {PropertyType2};
}
```

{Description of the type and its purpose}

| Property | Type | Description | Required |
|----------|------|-------------|----------|
| `{property1}` | `{PropertyType1}` | {Description of property1} | {Yes/No} |
| `{property2}` | `{PropertyType2}` | {Description of property2} | {Yes/No} |
```

- Include this section only if the interface uses complex types that need explanation
- Document each related type with its structure and purpose
- For each type, list its properties in a table similar to the interface properties
- If a type is documented elsewhere, link to it instead of duplicating documentation

### Usage Examples

```markdown
## Usage Examples

### Implementation Example

```typescript
// Example showing how to implement this interface
import * as azdev from "azure-devops-node-api";

class My{InterfaceName}Implementation implements {InterfaceName} {
  // Implement required properties
  {propertyName1}: {Type1} = {defaultValue1};
  {propertyName2}: {Type2} = {defaultValue2};
  
  // Implement required methods
  {methodName1}({parameter1}: {Type1}, {parameter2}: {Type2}): {ReturnType} {
    // Implementation details
    return {returnValue};
  }
  
  {methodName2}({parameter1}: {Type1}): {ReturnType} {
    // Implementation details
    return {returnValue};
  }
}
```

### Usage with API

```typescript
// Example showing how to use this interface with the API
import * as azdev from "azure-devops-node-api";

const organizationUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";

// Initialize the auth handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Initialize the connection to Azure DevOps Services
const connection = new azdev.WebApi(organizationUrl, authHandler);

// Get a client that returns objects implementing this interface
const client = await connection.get{ClientName}();

// Use the interface
const {interfaceInstance}: {InterfaceName} = await client.{methodToGetInterface}();
const result = {interfaceInstance}.{methodName1}({parameter1Value}, {parameter2Value});
console.log(result);
```
```

- Provide at least two examples:
  1. An implementation example showing how to create a class that implements the interface
  2. A usage example showing how to use objects that implement the interface
- Include complete code samples that can be copied and run with minimal modifications
- Include import statements, initialization, and proper error handling
- For client interfaces, show how to obtain an implementing object from the WebApi instance
- Explain any specific implementation requirements or patterns

### Common Scenarios

```markdown
## Common Scenarios

### Scenario 1: {Descriptive name of scenario}

{Description of a common scenario where this interface would be used.}

```typescript
// Code example for this scenario
```

### Scenario 2: {Descriptive name of scenario}

{Description of another common scenario.}

```typescript
// Code example for this scenario
```
```

- Document 2-3 common scenarios where this interface would be used
- Focus on real-world use cases that developers might encounter
- Provide concise code examples for each scenario
- Explain the context and purpose of each scenario

### Interface Extensions

```markdown
## Interface Extensions

{If applicable, describe how this interface can be extended or customized}

```typescript
// Example of extending the interface
interface Extended{InterfaceName} extends {InterfaceName} {
  additionalProperty: string;
  additionalMethod(param: string): void;
}
```
```

- Include this section if the interface is designed to be extended
- Show examples of how to extend the interface with additional properties or methods
- Explain common extension patterns or best practices
- If the interface is not typically extended, this section can be omitted

### Type Guards

```markdown
## Type Guards

{If applicable, provide type guard functions to check if an object implements this interface}

```typescript
// Example of a type guard for this interface
function is{InterfaceName}(obj: any): obj is {InterfaceName} {
  return (
    obj &&
    typeof obj === 'object' &&
    '{propertyName1}' in obj &&
    '{methodName1}' in obj &&
    typeof obj.{methodName1} === 'function'
  );
}
```
```

- Include this section for interfaces that might need runtime type checking
- Provide a type guard function that checks for the presence of key properties and methods
- Explain when and why to use type guards with this interface
- If type guards are not typically needed for this interface, this section can be omitted

### Related Interfaces and Implementing Classes

```markdown
## Related Interfaces

- [{RelatedInterface1}](link-to-related-interface1.md)
- [{RelatedInterface2}](link-to-related-interface2.md)

## Implementing Classes

- [{ImplementingClass1}](link-to-implementing-class1.md)
- [{ImplementingClass2}](link-to-implementing-class2.md)
```

- List related interfaces that are commonly used with this interface
- Include parent interfaces, child interfaces, and interfaces with similar purposes
- List known classes that implement this interface
- Provide links to documentation for each related interface and implementing class
- Include brief descriptions of how they relate to this interface

### See Also

```markdown
## See Also

- [API Reference](../api-reference/index.md)
- [Getting Started](../getting-started/index.md)
- [{Related Guide}](link-to-related-guide.md)
```

- Link to relevant conceptual documentation
- Link to tutorials or guides that use this interface
- Link to the API reference home page
- Ensure all links are correct and follow the linking standards

## Cross-Template Navigation

When documenting interfaces, it's important to maintain proper cross-references with related documentation:

1. **Class-Interface Relationships**: When documenting an interface, link to classes that implement it. Similarly, when documenting a class, link to interfaces it implements.

2. **Interface Hierarchies**: For interfaces that extend other interfaces, link to both parent and child interfaces.

3. **Method References**: When documenting methods in an interface, link to the corresponding method documentation in implementing classes when available.

4. **Type References**: When documenting properties or method parameters, link to the documentation for complex types.

## Versioning Guidance

When documenting interfaces, include version information as follows:

1. **Interface Introduction**: Indicate when the interface was first introduced in the API.

2. **Deprecated Features**: Clearly mark any deprecated properties or methods with a warning notice and information about alternatives.

3. **Version-Specific Behavior**: Document any behavior changes across versions.

4. **Breaking Changes**: Highlight any breaking changes between versions that affect this interface.

Example:
```markdown
> **Version Information**  
> This interface was introduced in version 5.0.  
> The `legacyMethod()` was deprecated in version 5.2 and will be removed in version 6.0. Use `newMethod()` instead.
```

## Template Customization

The template may be customized for specific interface types:

### Model Interfaces

Model interfaces represent data structures and typically have many properties but few methods.

**Customization Guidelines:**
- Focus on the **Properties** section with detailed type information
- Emphasize type definitions and object structure
- Include comprehensive property tables with validation rules
- Provide serialization/deserialization examples
- Add examples of creating and manipulating model objects

**Example:**

```typescript
// Example of a model interface
interface WorkItem {
  id: number;
  rev: number;
  fields: { [key: string]: any };
  relations?: WorkItemRelation[];
  _links?: any;
  url: string;
}

// Usage example
const workItem: WorkItem = {
  id: 42,
  rev: 1,
  fields: {
    "System.Title": "Fix login bug",
    "System.State": "Active"
  },
  url: "https://dev.azure.com/organization/_apis/wit/workItems/42"
};
```

### Client Interfaces

Client interfaces define service clients and typically have many methods but few properties.

**Customization Guidelines:**
- Focus on the **Methods** section with detailed parameter documentation
- Emphasize promise patterns and async/await usage
- Document error handling for each method
- Include authentication and initialization examples
- Group methods by functional area
- Provide examples of common operation sequences

**Example:**

```typescript
// Example of a client interface
interface IGitApi {
  getRepositories(project?: string): Promise<GitRepository[]>;
  getRepository(repositoryId: string, project?: string): Promise<GitRepository>;
  createRepository(gitRepositoryToCreate: GitRepositoryCreateOptions, project?: string): Promise<GitRepository>;
  // ... other methods
}

// Usage example
async function createAndGetRepository(api: IGitApi, project: string) {
  // Create a new repository
  const newRepo = await api.createRepository({
    name: "new-feature-repo",
    project: { id: project }
  }, project);
  
  // Then get a list of all repositories
  const allRepos = await api.getRepositories(project);
  console.log(`Created repository ${newRepo.name}. Project now has ${allRepos.length} repositories.`);
}
```

### Callback Interfaces

Callback interfaces define event handlers and typically have one or more callback methods.

**Customization Guidelines:**
- Focus on the callback method signatures and when they're invoked
- Emphasize event timing and sequencing
- Document the event object structure in detail
- Include examples of registering callbacks
- Provide context on error handling in callbacks
- Document any required return values from callbacks

**Example:**

```typescript
// Example of a callback interface
interface BuildStatusListener {
  onBuildStarted(buildId: number, buildInfo: BuildInfo): void;
  onBuildCompleted(buildId: number, result: BuildResult): void;
  onBuildError(buildId: number, error: Error): boolean; // Return true to retry
}

// Usage example
class MyBuildMonitor implements BuildStatusListener {
  onBuildStarted(buildId: number, buildInfo: BuildInfo): void {
    console.log(`Build ${buildId} started at ${buildInfo.startTime}`);
  }
  
  onBuildCompleted(buildId: number, result: BuildResult): void {
    console.log(`Build ${buildId} completed with result: ${result.status}`);
  }
  
  onBuildError(buildId: number, error: Error): boolean {
    console.error(`Build ${buildId} failed with error: ${error.message}`);
    return buildId % 2 === 0; // Only retry even-numbered builds
  }
}

// Registering the callback
buildService.registerBuildStatusListener(new MyBuildMonitor());
```

### Generic Interfaces

Generic interfaces use type parameters and can be specialized for different types.

**Customization Guidelines:**
- Focus on type parameter documentation and constraints
- Explain common type parameter patterns (T, K, V, etc.)
- Include examples with different type arguments
- Document type inference patterns
- Provide nested generic examples
- Explain when to use generic interfaces vs. specific types

**Example:**

```typescript
// Example of a generic interface
interface Repository<T> {
  getAll(): Promise<T[]>;
  getById(id: string): Promise<T>;
  create(item: T): Promise<T>;
  update(id: string, item: T): Promise<T>;
  delete(id: string): Promise<void>;
}

// Usage with specific type
interface WorkItem {
  id: string;
  title: string;
  state: string;
}

// Type-specialized usage example
class WorkItemRepository implements Repository<WorkItem> {
  async getAll(): Promise<WorkItem[]> {
    // Implementation
    return [];
  }
  
  async getById(id: string): Promise<WorkItem> {
    // Implementation
    return { id, title: "Sample", state: "Active" };
  }
  
  async create(item: WorkItem): Promise<WorkItem> {
    // Implementation
    return item;
  }
  
  async update(id: string, item: WorkItem): Promise<WorkItem> {
    // Implementation
    return { ...item, id };
  }
  
  async delete(id: string): Promise<void> {
    // Implementation
  }
}

// Using the specialized repository
const repo = new WorkItemRepository();
const items = await repo.getAll();
```

### Extension/Plugin Interfaces

Interfaces used for plugins or extensions need special documentation for implementers.

**Customization Guidelines:**
- Focus on implementation requirements and constraints
- Document the lifecycle of plugin instances
- Explain how the host system uses each method
- Include validation and error handling requirements
- Provide examples of minimal and complete implementations
- Document registration and discovery mechanisms

**Example:**

```typescript
// Example of an extension interface
interface BuildTaskPlugin {
  readonly id: string;
  readonly version: string;
  readonly displayName: string;
  execute(context: BuildContext, inputs: TaskInputs): Promise<TaskResult>;
  cleanup?(): Promise<void>;
}

// Implementation example
class DeploymentTask implements BuildTaskPlugin {
  readonly id = "deploy-task";
  readonly version = "1.0.0";
  readonly displayName = "Deployment Task";
  
  async execute(context: BuildContext, inputs: TaskInputs): Promise<TaskResult> {
    try {
      // Implementation of the task
      console.log(`Deploying to ${inputs.environment}`);
      return { status: "succeeded" };
    } catch (error) {
      return { status: "failed", error: error.message };
    }
  }
  
  async cleanup(): Promise<void> {
    // Optional cleanup logic
    console.log("Cleaning up deployment resources");
  }
}

// Registration example
buildSystem.registerTask(new DeploymentTask());
```

## Example Implementation

For a complete example of the template in use, see the [`IWorkItemTrackingApi`](../examples/IWorkItemTrackingApi.md) documentation. 