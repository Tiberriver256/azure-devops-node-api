# Interface Template

This directory contains the template for documenting TypeScript interfaces in the Azure DevOps Node API.

## Directory Structure

```
interface-template/
├── interface-template.md         # The main template file
├── interface-template-guide.md   # Detailed guide on how to use the template
├── examples/                     # Example implementations
│   └── IWorkItemTrackingApi.md   # Example of a documented interface
└── README.md                     # This file
```

## Purpose

The Interface Template provides a standardized structure for documenting TypeScript interfaces in the API. It ensures that all interface documentation includes consistent sections for:

- Interface overview and description
- Properties
- Methods
- Type definitions
- Usage examples (both implementation and usage)
- Common scenarios
- Interface extensions
- Type guards
- Related interfaces
- Implementing classes

## When to Use

Use this template when documenting TypeScript interfaces, especially:

- Public interfaces that are part of the API's contract
- Interfaces with multiple properties and methods
- Interfaces that have complex type relationships
- Interfaces that require detailed implementation examples

## Usage

1. Copy the `interface-template.md` file to your documentation directory
2. Rename it to match your interface name (e.g., `IWorkItemTrackingApi.md`)
3. Follow the instructions in `interface-template-guide.md` to fill in the template
4. Refer to the example in the `examples/` directory for guidance

## See Also

- [API Method Template](../api-method-template/)
- [Class Template](../class-template/)
- [API Reference Template](../api-reference-template/) 