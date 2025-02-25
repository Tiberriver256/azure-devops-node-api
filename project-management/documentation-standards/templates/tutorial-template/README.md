# Tutorial Template (Multi-File Structure)

This directory contains the multi-file structure for creating tutorials for the Azure DevOps Node API. This approach breaks down the tutorial into manageable sections, making it easier to navigate, maintain, and customize.

## Why Multi-File Structure?

Based on feedback from Jamie (UI/UX Lead), we've restructured the tutorial template into multiple files to address these concerns:

1. **Reduced Complexity**: Breaking the content into logical sections makes it less overwhelming
2. **Improved Navigation**: Users can easily jump to specific sections they need
3. **Better Maintainability**: Writers can update individual sections without navigating a large file
4. **Progressive Disclosure**: Information is presented in a logical sequence with clear navigation paths
5. **Modular Reuse**: Sections can be reused or referenced across different tutorials

## Directory Structure

```
tutorial-template/
├── README.md                     # This file
├── index.md                      # Main entry point with navigation
└── sections/
    ├── 01-header-overview.md     # Title, badges, introduction, and overview
    ├── 02-prerequisites.md       # Required software, accounts, and knowledge
    ├── 03-basic-implementation.md # Step-by-step guide for minimal example
    ├── 04-advanced-features.md   # Additional functionality
    ├── 05-common-scenarios.md    # Real-world use cases
    ├── 06-complete-example.md    # Full code example
    ├── 07-troubleshooting.md     # Common issues and solutions
    └── 08-next-steps-resources.md # Related documentation and further learning
```

## How to Use This Template

1. Create a new directory for your tutorial in the appropriate location
2. Copy all files from this template directory to your new directory
3. Edit each section file according to the guidance in the [Tutorial Template Guide](../tutorial-template-guide.md)
4. Update the links in the index file to point to your actual content files
5. Ensure all navigation links between sections are working correctly

## Related Resources

- [Tutorial Template Guide](../tutorial-template-guide.md): Detailed guidance on using this template
- [Example Implementation](../examples/workitem-automation-tutorial.md): A complete example of a tutorial using this structure

## Feedback and Improvements

This multi-file structure is designed to improve the user experience based on UX research and feedback. If you have suggestions for further improvements, please contact the documentation team. 