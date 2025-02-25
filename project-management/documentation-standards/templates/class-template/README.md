# Class Template

This directory contains the template for documenting classes in the Azure DevOps Node API.

## Directory Structure

```
class-template/
├── class-template.md         # The main template file
├── class-template-guide.md   # Detailed guide on how to use the template
├── examples/                 # Example implementations
│   └── WorkItemTrackingApi.md # Example of a documented class
└── README.md                 # This file
```

## Purpose

The Class Template provides a standardized structure for documenting classes in the API. It ensures that all class documentation includes consistent sections for:

- Class overview and description
- Constructor
- Properties
- Methods
- Usage examples
- Common scenarios
- Error handling
- Related resources

## When to Use

Use this template when documenting classes, especially:

- Public classes that are part of the API's interface
- Classes with multiple properties and methods
- Classes that require detailed usage examples or error handling information

## Usage

1. Copy the `class-template.md` file to your documentation directory
2. Rename it to match your class name (e.g., `WorkItemTrackingApi.md`)
3. Follow the instructions in `class-template-guide.md` to fill in the template
4. Refer to the example in the `examples/` directory for guidance

## See Also

- [API Method Template](../api-method-template/)
- [Interface Template](../interface-template/)
- [API Reference Template](../api-reference-template/) 