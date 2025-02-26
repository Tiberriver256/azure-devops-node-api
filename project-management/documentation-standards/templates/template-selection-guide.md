**Navigation**: [Home](../index.md) > [Documentation Standards](./index.md) > Template Selection Guide

# Template Selection Guide

This guide helps documentation contributors select the most appropriate template for different documentation needs. Each template is designed for specific use cases and content types.

## Contents

- [Quick Selection Guide](#quick-selection-guide)
- [Feature Comparison](#feature-comparison)
- [Detailed Selection Criteria](#detailed-selection-criteria)
- [Decision Tree](#decision-tree)
- [Template Customization](#template-customization)
- [Getting Help](#getting-help)

## Quick Selection Guide

| If you need to document... | Use this template |
|---------------------------|-------------------|
| A complex API component with many methods | [API Reference Template](./api-reference-template/) |
| A single API method or function | [API Method Template](./api-method-template/) |
| A class with fewer than 15 methods | [Class Template](./class-template/) |
| A TypeScript interface | [Interface Template](./interface-template/) |
| A step-by-step process or task | [Tutorial Template](./tutorial-template/) |
| A complex concept or architectural pattern | [Conceptual Guide Template](./conceptual-guide-template/) |
| Solutions to common problems | [Troubleshooting Guide Template](./troubleshooting-guide-template/) |

## Feature Comparison

This table compares the features and characteristics of each template type to help you make an informed decision.

| Feature | API Reference | API Method | Class | Interface | Tutorial | Conceptual | Troubleshooting |
|---------|---------------|------------|-------|-----------|----------|------------|-----------------|
| **Structure** |
| Multi-file structure | ✓ | - | - | - | ✓ | - | ✓ |
| Single-file document | - | ✓ | ✓ | ✓ | - | ✓ | - |
| Method categorization | ✓ | - | ✓ | ✓ | - | - | - |
| Step-by-step format | - | - | - | - | ✓ | - | - |
| Progressive disclosure | ✓ | - | - | - | ✓ | ✓ | ✓ |
| **Content Types** |
| Parameter documentation | ✓ | ✓ | ✓ | ✓ | - | - | - |
| Return type documentation | ✓ | ✓ | ✓ | ✓ | - | - | - |
| Code examples | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Visual diagrams | - | - | - | - | ✓ | ✓ | ✓ |
| Error handling | ✓ | ✓ | ✓ | ✓ | ✓ | - | ✓ |
| Troubleshooting section | - | - | - | - | ✓ | - | ✓ |
| **Use Cases** |
| API documentation | ✓ | ✓ | ✓ | ✓ | - | - | - |
| Learning-oriented content | - | - | - | - | ✓ | ✓ | - |
| Problem-solving content | - | - | - | - | - | - | ✓ |
| Conceptual explanation | - | - | - | - | - | ✓ | - |
| **Complexity** |
| Implementation effort | High | Low | Medium | Medium | Medium | Medium | High |
| Maintenance effort | High | Low | Medium | Medium | Medium | Low | High |
| Navigation complexity | Medium | Low | Low | Low | Medium | Low | Medium |

## Detailed Selection Criteria

### API Reference Template

**Use when:**
- The component has 15+ methods
- The component has complex data structures
- The component is frequently used
- The component generates many support inquiries

**Benefits:**
- Split-view approach reduces cognitive load
- Organized method categorization
- Comprehensive navigation between related elements
- Scalable for very complex components

**Example components:**
- WorkItemTrackingApi
- GitApi
- BuildApi

### API Method Template

**Use when:**
- Documenting a single method that doesn't belong to a complex component
- Creating standalone method documentation
- The method has a straightforward signature and purpose

**Benefits:**
- Focused documentation for a single operation
- Comprehensive parameter and return type documentation
- Simplified maintenance

**Example methods:**
- getWorkItems
- createProject
- queryBuilds

### Class Template

**Use when:**
- Documenting a class with fewer than 15 methods
- The class has a straightforward structure
- The class doesn't require extensive method categorization

**Benefits:**
- All documentation in a single file for easier consumption
- Comprehensive class overview
- Simplified maintenance for smaller components

**Example classes:**
- WebApiTeam
- TeamProjectReference
- TeamContext

### Interface Template

**Use when:**
- Documenting a TypeScript interface
- The interface is not part of a complex component
- Documenting type definitions and contracts

**Benefits:**
- TypeScript-specific documentation features
- Focus on type definitions and contracts
- Documentation of interface extensions and implementations

**Example interfaces:**
- IWorkItemTrackingApi (simplified version)
- IRequestHandler
- IVssRestClientOptions

### Tutorial Template

**Use when:**
- Creating step-by-step guidance for completing a specific task
- Teaching users how to implement a particular feature
- Providing a learning-oriented approach to documentation

**Benefits:**
- Progressive disclosure of information
- User goal-oriented structure
- Comprehensive troubleshooting sections
- Clear prerequisites and next steps

**Example tutorials:**
- "Automating Work Item Creation"
- "Setting Up Continuous Integration with Azure Pipelines"
- "Implementing Custom Work Item Types"

### Conceptual Guide Template

**Use when:**
- Explaining complex concepts or architectural patterns
- Providing background information needed to understand the API
- Documenting high-level design principles

**Benefits:**
- Visual diagrams and explanations
- Progressive complexity from basic to advanced
- Relationship mapping to API components
- Difficulty indicators for user guidance

**Example conceptual guides:**
- "Authentication Concepts"
- "Azure DevOps Resource Areas"
- "API Versioning Concepts"

### Troubleshooting Guide Template

**Use when:**
- Documenting solutions to common problems
- Creating diagnostic guidance
- Providing error resolution steps

**Benefits:**
- Problem-solution structure
- Severity and time indicators
- Comprehensive diagnostic tools
- Decision tree for problem identification

**Example troubleshooting guides:**
- "Connection Problems"
- "Authentication Issues"
- "Rate Limiting and Performance Problems"

## Decision Tree

Still not sure which template to use? Follow this decision tree:

1. **Are you documenting an API component?**
   - Yes → Go to question 2
   - No → Go to question 5

2. **Does the component have 15+ methods OR complex data structures?**
   - Yes → Use the **API Reference Template**
   - No → Go to question 3

3. **Are you documenting a single method?**
   - Yes → Use the **API Method Template**
   - No → Go to question 4

4. **Are you documenting a class or an interface?**
   - Class → Use the **Class Template**
   - Interface → Use the **Interface Template**

5. **What is the primary purpose of your documentation?**
   - Teaching how to complete a task → Use the **Tutorial Template**
   - Explaining a concept → Use the **Conceptual Guide Template**
   - Solving a problem → Use the **Troubleshooting Guide Template**

## Visual Decision Tree

![Template Selection Decision Tree](../images/template-selection-flowchart.png)

*Figure 1: Decision tree for selecting the appropriate template*

Note: The actual image will be created by Jamie and added later.

## Template Customization

All templates can be customized to meet specific documentation needs while maintaining consistency. When customizing:

1. Maintain the core sections required for each template type
2. Follow the style guide for formatting and structure
3. Document any significant deviations in the README
4. Ensure navigation patterns remain consistent

## Getting Help

If you're still unsure which template to use, contact the documentation team for guidance:

- Taylor (Senior Technical Writer): [email/contact info]
- Documentation Team: [email/contact info]

## See Also

- [Documentation Standards Overview](./index.md)
- [Visual Elements Guide](../visual-elements-guide.md)
- [Template Implementation Guidelines](../template-implementation-guidelines.md) 