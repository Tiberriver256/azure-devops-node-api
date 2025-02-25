# Class Documentation Template Guide

This guide explains how to use the class documentation template to create consistent, comprehensive documentation for classes in the Azure DevOps Node API.

## Template Purpose

The class documentation template provides a standardized format for documenting classes in the Azure DevOps Node API. It ensures that:

1. All classes are documented with consistent structure and detail
2. Developers can quickly understand the purpose and usage of each class
3. Key information like constructors, properties, and methods are clearly presented
4. Examples demonstrate practical usage in common scenarios
5. Related resources are properly cross-referenced

## When to Use This Template

Use this template when:

- Documenting a new or existing class in the Azure DevOps Node API
- Updating class documentation to meet new documentation standards
- Creating documentation for client classes, model classes, utility classes, or any other class types in the API

## Template Sections

### Header

```markdown
# Class: {ClassName}
```

- Replace `{ClassName}` with the actual name of the class you're documenting
- Use PascalCase as it appears in the code (e.g., `WorkItemTrackingApi`, `GitRepositoryRef`)

### Overview

```markdown
## Overview

{Provide a brief description of the class and its purpose within the Azure DevOps Node API. Explain what functionality it encapsulates and when a developer would use this class. Keep this section concise but informative, focusing on the "what" and "why".}

**Package:** {Package name}  
**Extends:** {Parent class(es) if applicable}  
**Implements:** {Interface(s) if applicable}  
```

- The overview should be 2-3 sentences focused on what the class does and why it exists
- Include the package name as it appears in the import statement
- For Extends/Implements, list parent classes or interfaces. If none, omit these lines
- Keep metadata items on separate lines with double spaces after each line for proper markdown formatting

### Description

```markdown
## Description

{Provide a more detailed description of the class. Explain its role within the API architecture, its relationship to other classes, and important design patterns or principles it demonstrates. Include any conceptual information that helps developers understand how to use the class effectively.}

### Key Features

- {Key feature 1}
- {Key feature 2}
- {Key feature 3}
```

- The description should provide deeper context about the class's role in the API
- Explain any design patterns, architectural considerations, or implementation details
- List 3-5 key features or capabilities that make this class useful
- Use bullet points that are concise but descriptive

### Constructor

```markdown
## Constructor

```typescript
constructor({parameter1}: {Type1}, {parameter2}: {Type2}, ...): {ClassName}
```

Creates a new instance of the `{ClassName}` class.

### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `{parameter1}` | `{Type1}` | {Description of parameter1} | {Yes/No} |
| `{parameter2}` | `{Type2}` | {Description of parameter2} | {Yes/No} |

### Returns

A new instance of `{ClassName}`.

### Example

```typescript
// Example of creating an instance of the class
const {instanceName} = new {ClassName}({
  {parameter1}: {value1},
  {parameter2}: {value2},
});
```
```

- Include the constructor signature exactly as it appears in the code
- For the parameter table:
  - Include all parameters, even optional ones
  - Use backticks around parameter names and types for consistent formatting
  - Indicate if parameters are required or optional
- Provide a simple example of creating an instance of the class
- If the class has multiple constructors, document each one separately

### Properties

```markdown
## Properties

| Name | Type | Description | Access |
|------|------|-------------|--------|
| `{propertyName1}` | `{Type1}` | {Description of propertyName1} | {public/private/protected} |
| `{propertyName2}` | `{Type2}` | {Description of propertyName2} | {public/private/protected} |
```

- List all public properties of the class
- Optionally include protected properties if they're important for subclasses
- Generally omit private properties unless they are exposed through getters/setters
- In the access column, indicate the visibility (public, private, protected)

### Methods

```markdown
## Methods

{List of primary methods in the class, each with a link to detailed documentation}

- [{methodName1}](#methodname1)
- [{methodName2}](#methodname2)
- [{methodName3}](#methodname3)

### {methodName1}

```typescript
{methodName1}({parameter1}: {Type1}, {parameter2}: {Type2}): Promise<{ReturnType}>
```

{Description of what the method does and when to use it.}

#### Parameters

| Name | Type | Description | Required |
|------|------|-------------|----------|
| `{parameter1}` | `{Type1}` | {Description of parameter1} | {Yes/No} |
| `{parameter2}` | `{Type2}` | {Description of parameter2} | {Yes/No} |

#### Returns

`Promise<{ReturnType}>`: {Description of the returned data}

#### Example

```typescript
const {instanceName} = new {ClassName}({
  {parameter1}: {value1},
  {parameter2}: {value2},
});

const result = await {instanceName}.{methodName1}({
  {parameter1}: {value1},
  {parameter2}: {value2},
});

console.log(result);
// Output: {sample output}
```
```

- First provide a list of all methods with anchor links for navigation
- Then document each method in detail:
  - Include the exact method signature from the code
  - Describe what the method does and when to use it
  - Document all parameters and return values
  - Provide a practical example of using the method
  - Use `await` for async methods that return Promises
- For methods with complex logic, focus on the usage rather than internal implementation
- If the class has many methods, prioritize the most commonly used ones

### Usage Examples

```markdown
## Usage Examples

### Basic Usage

```typescript
// Example showing basic usage of the class
import * as azdev from "azure-devops-node-api";

const organizationUrl = "https://dev.azure.com/your-organization";
const token = "your-personal-access-token";

// Initialize the auth handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Initialize the connection to Azure DevOps Services
const connection = new azdev.WebApi(organizationUrl, authHandler);

// Use the class
const {clientInstance} = await connection.get{ClientName}();
const {instanceName} = new {ClassName}({
  {parameter1}: {value1},
  {parameter2}: {value2},
});

// Use methods
const result = await {instanceName}.{methodName1}();
console.log(result);
```

### Advanced Usage

```typescript
// Example showing more advanced usage scenarios
// This might include error handling, complex parameters, etc.
```
```

- Provide at least two examples:
  1. A basic example showing the most common usage
  2. An advanced example showing more complex scenarios
- Include complete code samples that can be copied and run with minimal modifications
- Include import statements, initialization, and proper error handling
- For client classes, show how to obtain the client from the WebApi instance
- Explain any specific configuration or setup requirements

### Common Scenarios

```markdown
## Common Scenarios

### Scenario 1: {Descriptive name of scenario}

{Description of a common scenario where this class would be used.}

```typescript
// Code example for this scenario
```

### Scenario 2: {Descriptive name of scenario}

{Description of another common scenario.}

```typescript
// Code example for this scenario
```
```

- Describe 2-3 real-world scenarios where the class would be used
- These should be focused on specific tasks a developer might want to accomplish
- Use realistic scenario names like "Retrieving Work Items with Custom Fields" or "Managing Repository Permissions"
- Include code examples tailored to each scenario
- These examples should be more concise than the usage examples, focusing on the specific scenario

### Error Handling

```markdown
## Error Handling

{Describe common errors that might occur when using this class and how to handle them.}

```typescript
try {
  const result = await {instanceName}.{methodName1}();
  console.log(result);
} catch (error) {
  if (error.statusCode === 404) {
    console.error("Resource not found:", error.message);
  } else {
    console.error("Unexpected error:", error.message);
  }
}
```
```

- Document common errors that might occur when using the class
- Include specific error codes or types when applicable
- Provide recommended error handling approaches
- Include a code example showing proper error handling

### Related Resources

```markdown
## Related Classes

- [{RelatedClass1}](link-to-related-class1.md)
- [{RelatedClass2}](link-to-related-class2.md)

## See Also

- [API Reference](../api-reference/index.md)
- [Getting Started](../getting-started/index.md)
- [{Related Guide}](link-to-related-guide.md)
```

- List related classes that are frequently used with this class
- Include links to their documentation
- In the "See Also" section, link to relevant conceptual guides or tutorials
- Use relative paths for all links to ensure they work across different documentation platforms

## Example Implementation

After completing your class documentation using this template, refer to the example implementations in the `/templates/examples` directory for reference. These examples demonstrate how to apply the template to different types of classes in the Azure DevOps Node API.

## Review Checklist

Before finalizing your class documentation, ensure:

1. All placeholder text has been replaced with actual content
2. Code examples are accurate and follow the established standards
3. All parameters, properties, and methods are documented
4. Links to related resources are valid and use relative paths
5. Technical information is accurate and aligns with the actual implementation
6. The documentation follows the style guide for voice, tone, and formatting
7. Complex concepts are explained clearly with appropriate examples

## Additional Guidelines

- Always document from the perspective of a developer using the API
- Focus on how to use the class, not internal implementation details
- Use consistent terminology that aligns with the Azure DevOps ecosystem
- Keep examples concise but complete enough to be useful
- Include TypeScript type information consistently
- Document async patterns and Promise handling clearly

## Questions?

If you have questions about using this template or documenting a specific class, contact Taylor (Senior Technical Writer) for guidance. 