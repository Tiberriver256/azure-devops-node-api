# Simplified Templates Implementation Guide

## Overview

This guide outlines how to use the simplified templates for the MVP documentation of the Azure DevOps Node API. These templates focus on essential information to accelerate documentation delivery while maintaining quality.

## Template Selection

| If you need to document... | Use this template |
|---------------------------|-------------------|
| A complete API client (e.g., WorkItemTracking) | [Simplified API Reference Template](./simplified-api-reference-template.md) |
| A single API method | [Simplified API Method Template](./simplified-api-method-template.md) |
| A step-by-step process | [Simplified Tutorial Template](./simplified-tutorial-template.md) |
| Common errors and solutions | [Simplified Troubleshooting Template](./simplified-troubleshooting-template.md) |

## Implementation Principles

1. **Focus on Essential Information**
   - Include only information critical for developer understanding
   - Prioritize practical usage over comprehensive coverage
   - Ensure technical accuracy of included content

2. **Consistent Structure**
   - Maintain consistent headings across all documents
   - Use standardized formatting for code examples
   - Follow established style guide for terminology

3. **Practical Examples**
   - Include minimal but complete code examples
   - Focus on common use cases
   - Ensure all examples are tested and functional

## Implementation Process

1. **Select Template**
   - Choose the appropriate template from the options above
   - Copy the template to your working directory
   
2. **Fill Required Sections**
   - Complete all sections in the template
   - For API documentation, focus on the top 5-7 most important methods
   - For tutorials, keep steps clear and concise
   
3. **Add Code Examples**
   - Provide working code examples for all functionality
   - Keep examples simple but complete
   - Include error handling where appropriate
   
4. **Review**
   - Ensure all sections are completed
   - Verify technical accuracy with API specialists
   - Check that all code examples compile and run
   
5. **Submit**
   - Add your documentation to the appropriate location
   - Update cross-references in related documents

## Implementation Tips

### API Reference Template Tips

- For the "Common Methods" section, prioritize methods based on:
  - Frequency of use in typical applications
  - Importance for basic functionality
  - Feedback from developers on most-used methods
  
- Use emoji indicators consistently:
  - ⭐ - Most commonly used methods
  - 🔍 - Query/search methods
  - 🆕 - Creation methods

### API Method Template Tips

- Keep parameter descriptions brief but clear
- Include type information for all parameters
- Show at least one complete, working example

### Tutorial Template Tips

- Break complex processes into clear, manageable steps
- Include screenshots for UI-related steps (if applicable)
- Always include a troubleshooting section with common issues

### Troubleshooting Template Tips

- Focus on issues reported by actual users
- Include exact error messages when possible
- Provide clear, step-by-step solutions

## Quality Checklist

Before submitting documentation, ensure it passes this basic quality check:

- [ ] All required sections are completed
- [ ] All code examples compile and run correctly
- [ ] Method signatures are accurate and complete
- [ ] Parameter descriptions are clear and accurate
- [ ] Return value descriptions are clear and accurate
- [ ] Common errors are documented with handling strategies
- [ ] Links to related documentation are included

## Example Implementation

See the `examples` directory for sample implementations of each template type.

## See Also

- [MVP Template Guide](../../project-organization/mvp-template-guide.md)
- [Documentation Style Guide](../../style-guide/README.md)
- [Template Selection Guide](../template-selection-guide.md) 