# API Method Template

This directory contains the template for documenting individual API methods in the Azure DevOps Node API.

## Directory Structure

```
api-method-template/
├── api-method-template.md         # The main template file
├── api-method-template-guide.md   # Detailed guide on how to use the template
├── examples/                      # Example implementations
│   └── getWorkItems.md            # Example of a documented API method
└── README.md                      # This file
```

## Purpose

The API Method Template provides a standardized structure for documenting individual API methods. It ensures that all method documentation includes consistent sections for:

- Method signature
- Parameters
- Return type
- Examples
- Error handling
- Related methods

## When to Use

Use this template when documenting individual API methods, especially:

- Public methods that are part of the API's interface
- Methods with complex parameters or return types
- Methods that require detailed examples or error handling information

## Usage

1. Copy the `api-method-template.md` file to your documentation directory
2. Rename it to match your method name (e.g., `getWorkItems.md`)
3. Follow the instructions in `api-method-template-guide.md` to fill in the template
4. Refer to the example in the `examples/` directory for guidance

## See Also

- [Class Template](../class-template/)
- [Interface Template](../interface-template/)
- [API Reference Template](../api-reference-template/) 