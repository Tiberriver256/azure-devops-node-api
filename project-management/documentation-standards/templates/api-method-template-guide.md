# API Method Documentation Template: Implementation Guide

This guide provides instructions and best practices for using the API Method Documentation Template effectively. Follow these guidelines to ensure consistent, high-quality API method documentation across the Azure DevOps Node API documentation.

## Getting Started

1. Create a new markdown file for the method you're documenting, named according to our file naming convention: `methodName.md`
2. Copy the contents of the API Method Template into this new file
3. Follow the step-by-step instructions below to replace placeholder content with actual documentation

## Step-by-Step Implementation

### 1. Method Name and Signature

- Replace `methodName` with the actual method name
- Update the TypeScript signature to match the actual method signature
- Ensure parameter types, return types, and async/await notations are accurate

✅ **Good Example**:
```typescript
public async getRepositories(
    project?: string,
    includeLinks?: boolean,
    includeAllUrls?: boolean
): Promise<GitRepository[]>
```

❌ **Avoid**:
```typescript
public getRepositories(project, includeLinks, includeAllUrls)
```
(Missing types, async notation, and return type)

### 2. Description

- Begin with a clear statement of what the method does
- Use present tense and active voice
- Explain when and why to use this method
- Keep it concise but informative

✅ **Good Example**:
> Gets a list of Git repositories in a project or organization. This method returns basic repository information by default. Use the optional parameters to include additional details like links and remote URLs.

❌ **Avoid**:
> This method gets repositories. It is used to retrieve git repositories from Azure DevOps.
(Too vague and redundant)

### 3. Parameters

- List all parameters in the order they appear in the method signature
- Include accurate type information
- Clearly indicate which parameters are required vs. optional
- Provide default values where applicable
- Include constraints or acceptable value ranges
- Keep descriptions focused on what the parameter does, not how it works internally

✅ **Good Example**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `project` | `string` | No | Project ID or project name. If not specified, repositories from all projects are returned. |
| `includeLinks` | `boolean` | No | Include reference links in the response. Default: `false`. |

❌ **Avoid**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `project` | `string` | No | The project. |
| `includeLinks` | `boolean` | No | Whether to include links or not. |
(Descriptions are too vague and don't provide useful information)

### 4. Options Object

- Only include this section if the method accepts an options object
- Document each property in the options object
- Follow the same guidelines as for regular parameters

### 5. Return Value

- Specify the exact return type
- Describe what successful execution returns
- For complex return types, document key properties and their purpose
- Include any special return values or conditions

✅ **Good Example**:
> **Type:** `Promise<GitRepository[]>`
>
> Returns a Promise that resolves to an array of GitRepository objects. Each object contains properties describing a Git repository in the specified project or organization.

❌ **Avoid**:
> **Type:** `Promise<GitRepository[]>`
>
> Returns git repositories.
(Too vague, doesn't explain the return structure)

### 6. Return Object Properties

- Include this section only for complex return objects
- Document key properties that consumers would need to access
- Include type information and descriptions

✅ **Good Example**:
| Property | Type | Description |
|----------|------|-------------|
| `id` | `string` | The unique identifier of the repository. |
| `name` | `string` | The display name of the repository. |
| `url` | `string` | The REST API URL for this repository. |

### 7. Exceptions

- Document all exceptions that might be thrown during normal operation
- Include the condition that triggers each exception
- Use the official error type names
- Include HTTP status codes when applicable

✅ **Good Example**:
| Exception | Condition |
|-----------|-----------|
| `UnauthorizedError` | Thrown when the user doesn't have permission to view repositories (HTTP 401 or 403). |
| `ProjectNotFoundError` | Thrown when the specified project doesn't exist (HTTP 404). |

### 8. Examples

- Provide complete, runnable examples
- Include proper import statements
- Show authentication and setup code in the basic example
- Provide additional examples showing:
  - Optional parameters
  - Error handling
  - Advanced scenarios

- Use realistic values and variable names
- Include comments explaining key steps

### 9. Response Example

- Provide a JSON example of a typical successful response
- Use realistic data values
- Format the JSON properly
- Include the most important and common properties

### 10. Related Methods

- List methods that are commonly used with this method
- Include methods that provide alternative approaches
- Briefly explain how each related method connects to this one
- Ensure links to related methods are correct

✅ **Good Example**:
- [`getRepository`](./getRepository.md) - Gets a single repository by name or ID
- [`createRepository`](./createRepository.md) - Creates a new repository in a project
- [`deleteRepository`](./deleteRepository.md) - Deletes a repository

### 11. See Also

- Link to relevant conceptual documentation
- Link to tutorials that use this method
- Link to the client class documentation
- Ensure all links are correct and follow the linking standards

### 12. API Details

- Specify the client that exposes this method
- Include version information if relevant
- Document required permissions or scopes

## Best Practices

1. **Accuracy**: Ensure all information is technically accurate and verified
2. **Completeness**: Include all relevant information, but avoid unnecessary details
3. **Clarity**: Use simple, direct language and avoid jargon when possible
4. **Consistency**: Follow the same patterns and terminology across all method documentation
5. **Examples**: Provide realistic, runnable examples that demonstrate common usage patterns
6. **Progressive Detail**: Start with basic information and progressively disclose more complex details

## Common Pitfalls to Avoid

1. **Vague Descriptions**: Don't use generic descriptions that don't provide useful information
2. **Missing Parameters**: Ensure all parameters are documented, even optional ones
3. **Incomplete Return Information**: Fully document what the method returns, especially complex objects
4. **Inadequate Error Handling**: Always include error handling in examples
5. **Outdated Information**: Ensure documentation stays in sync with the actual API
6. **Inconsistent Terminology**: Use the terms defined in our terminology glossary consistently
7. **Missing Cross-References**: Don't forget to link to related methods and documentation

## Quality Checklist

Before submitting your documentation, review it against this checklist:

- [ ] Method signature matches the actual code
- [ ] All parameters are documented accurately
- [ ] Return type and structure are clearly explained
- [ ] All potential exceptions are documented
- [ ] Examples are complete and runnable
- [ ] All sections of the template are completed or consciously removed
- [ ] Code examples follow our code standards
- [ ] Cross-references and links are correct
- [ ] Spelling and grammar are correct
- [ ] Technical information is accurate and verified

## Review Process

All method documentation will be reviewed for:

1. Technical accuracy (by API specialists)
2. Adherence to style guide (by documentation team)
3. Usability and clarity (by UX specialists)

## Template Customization

The template may be customized for specific method types:

- **Getter methods**: Focus on return value documentation
- **Setter methods**: Focus on parameter validation and error conditions
- **CRUD operations**: Include clear relationships between create, read, update, and delete methods
- **Long-running operations**: Include information about polling or callbacks

## Example Implementation

For a complete example of the template in use, see the [`getWorkItems`](../examples/getWorkItems.md) documentation. 