# Troubleshooting Guide Template

## Overview

This template provides a standardized structure for creating comprehensive troubleshooting guides for Azure DevOps Node API components. It follows a progressive disclosure pattern, starting with quick solutions and moving to more detailed troubleshooting steps as needed.

## Key Features

- Progressive disclosure approach
- Quick solutions with severity and time indicators
- Comprehensive error code reference
- Guided troubleshooting with decision trees
- Advanced diagnostic tools and techniques
- GitHub-optimized structure with collapsible sections
- Standardized visual indicators and navigation

## Template Structure

```
.
├── index.md                    # Main entry point and overview
├── quick-solutions.md         # Common issues and quick fixes
├── sections/
│   ├── issue-identification.md  # Help users identify their issue
│   ├── error-code-reference.md  # Comprehensive error code listing
│   ├── decision-tree.md        # Guided troubleshooting flow
│   ├── advanced-troubleshooting.md  # Advanced diagnostic tools
│   ├── getting-help.md         # Where to get additional help
│   └── problem-categories/     # Detailed problem documentation
│       ├── category1.md
│       ├── category2.md
│       └── ...
└── changelog-and-feedback.md   # Version history and feedback collection
```

## Visual Style Guide

### Issue Severity Indicators

Use these emoji indicators consistently throughout the documentation:

- 🔴 High Severity - Critical issues requiring immediate attention
- 🟡 Medium Severity - Important issues that should be addressed soon
- 🟢 Low Severity - Minor issues or optimization opportunities

### Time Indicators

Include estimated time to fix using the following format:
> **Quick Fix** | ⏱️ 5 minutes | 🔴 High Severity

### Issue Type Icons

- 🔑 Authentication Issues
- 🌐 Network/Connection Issues
- 💾 Data/Storage Issues
- 🔒 Permission Issues
- ⚡ Performance Issues
- 🔍 Search/Query Issues
- 🔄 Sync/Update Issues

### Section Separators

Use horizontal rules (---) to create clear visual separation between major sections.

---

## Template Components

### 1. Main Entry Point (index.md)

The `index.md` file serves as the main entry point and should include:

- Brief description of the component/feature
- Table of common issue categories with links
- Quick navigation to key sections
- Decision tree reference for guided troubleshooting

### 2. Quick Solutions (quick-solutions.md)

The `quick-solutions.md` file should contain:

- Table of common issues with symptoms and solutions
- Time and severity indicators for each solution
- Code snippets for immediate fixes
- Links to detailed documentation for each issue
- Diagnostic code examples

Example quick solution format:

> **Authentication Failed** | ⏱️ 2 minutes | 🔴 High Severity
>
> Common authentication failures can be resolved by regenerating your Personal Access Token (PAT).
> ```typescript
> // Example fix
> const newPat = await generateNewPat();
> ```

### 3. Issue Identification (sections/issue-identification.md)

This section helps users identify their specific issue:

- Common symptoms and error messages
- Diagnostic tools and code
- Issue categorization guidance
- Links to relevant problem categories

### 4. Error Code Reference (sections/error-code-reference.md)

A comprehensive listing of error codes:

- HTTP status codes
- API-specific error codes
- Error message patterns
- Solutions for each error code

### 5. Problem Categories

Each problem category should have its own file in `sections/problem-categories/` with:

<details>
<summary><b>Category File Template</b></summary>

```markdown
# [Category Name]

> **Navigation**: [Home](../../index.md) > [Category Name]

Brief description of this category of issues.

## In This Category

- [Issue 1](#issue-1)
- [Issue 2](#issue-2)
...

---

## Issue 1
<a id="issue-1"></a>

> ⏱️ [Estimated Time] | [Severity Indicator] | [Issue Type Icon]

### Symptoms

- Bullet points describing how to identify this issue
- Error messages or behaviors to look for
- Related symptoms

### Root Causes

- Common causes of this issue
- Environmental factors
- Configuration problems

### Solution Steps

1. Step-by-step solution
2. Code examples when relevant
3. Verification steps

### Prevention

- How to prevent this issue
- Best practices
- Configuration recommendations

### Related Issues

- Links to related issues
- See also references
- Additional resources

### Next Steps

- What to do if this solution didn't help
- Related issues to check
- Alternative approaches to try

---

## Issue 2
...
```

</details>

### 6. Advanced Troubleshooting (sections/advanced-troubleshooting.md)

Include advanced diagnostic techniques:

- Network traffic analysis
- Logging and tracing
- Memory and performance profiling
- Authentication deep dive

### 7. Getting Help (sections/getting-help.md)

Information about getting additional help:

- Support channels
- Community resources
- Issue reporting guidelines
- Required information for support tickets

## Implementation Steps

1. **Create Directory Structure**
   ```bash
   mkdir -p troubleshooting-guide/sections/problem-categories
   ```

2. **Copy Template Files**
   - Copy the base template files from this directory
   - Rename as needed for your component

3. **Customize Content**
   - Update component-specific information
   - Add relevant error codes
   - Create problem category files
   - Add code examples and solutions

4. **Add Diagnostic Tools**
   - Include component-specific diagnostic code
   - Add logging examples
   - Create troubleshooting utilities

5. **Review and Test**
   - Verify all links work
   - Test code examples
   - Review with technical team
   - Validate with users

## Best Practices

1. **Progressive Disclosure**
   - Start with common solutions
   - Progress to more detailed troubleshooting
   - End with advanced techniques

2. **Code Examples**
   - Include complete, runnable examples
   - Show both problem and solution code
   - Use TypeScript for type safety
   - Include error handling

3. **Collapsible Sections**
   ```markdown
   <details>
   <summary><b>Section Title</b></summary>
   
   Content goes here...
   
   </details>
   ```

4. **Navigation**
   - Consistent breadcrumbs
   - Clear section links
   - Related issue cross-references
   - "Next Steps" section at the end of each issue

5. **Error Messages**
   - Quote exact error messages
   - Include error codes
   - Show stack traces in collapsible sections

6. **Visual Consistency**
   - Use standardized severity indicators
   - Include time estimates for all solutions
   - Apply consistent issue type icons
   - Use horizontal rules for section separation

## Example Implementation

See the `examples/authentication-troubleshooting` directory for a complete example implementation of this template, demonstrating:

- Proper directory structure
- Content organization
- Progressive disclosure
- Code examples
- Navigation patterns
- Error code documentation
- Visual indicators and time estimates

## Contributing

When contributing to this template:

1. Follow the structure defined in this guide
2. Maintain consistent formatting
3. Test all code examples
4. Verify navigation links
5. Update the changelog
6. Follow visual style guidelines

## Version History

| Version | Date | Updated By | Changes |
|---------|------|------------|---------|
| 1.0 | 2024-02-26 | Taylor | Initial template structure |
| 1.1 | 2024-02-26 | Taylor | Added visual style guide and enhanced navigation based on UX review |