# Troubleshooting Guide Template

## Overview

This template provides a structured approach for creating comprehensive troubleshooting guides for the Azure DevOps Node API. The template uses a multi-file structure to improve:

- **Maintainability**: Each issue category and section is in its own file, making updates easier
- **Navigation**: Users can quickly find solutions to specific problems without scrolling through lengthy documents
- **Searchability**: Clear section headings and error message display improve search engine discovery
- **User Experience**: Progressive disclosure of information guides users from simple to complex solutions

## Directory Structure

```
troubleshooting-guide-template/
├── README.md                      # Overview of template usage (this file)
├── index.md                       # Main entry point with issue categories & navigation 
├── quick-solutions.md             # Common issues and quick fixes
└── sections/
    ├── issue-identification.md    # Symptoms, error messages, diagnostic tools
    ├── problem-categories/
    │   ├── category1-problems.md  # E.g., authentication-issues.md 
    │   ├── category2-problems.md  # E.g., connection-problems.md
    │   └── category3-problems.md  # E.g., data-retrieval-issues.md
    ├── error-code-reference.md    # Complete error code table
    ├── advanced-troubleshooting.md # Complex diagnostic techniques
    ├── decision-tree.md           # Visual troubleshooting flowchart
    └── getting-help.md            # How to proceed if guide doesn't solve the issue
```

## How to Use This Template

1. Create a new directory for your troubleshooting guide in the appropriate location
2. Copy the template files to your new directory
3. Rename the category files to match your specific issue categories
4. Edit each file with your content, maintaining the recommended structure
5. Update all links in the index.md and between files
6. Add any additional files needed for your specific component
7. Test all navigation links before publishing

## Recommended Problem Categories

Based on support ticket analytics, these categories cover most issues across API products:

1. Authentication Issues
2. Connection Problems
3. Permission/Authorization Errors
4. Data Retrieval Issues
5. Data Modification Errors
6. Rate Limiting and Performance
7. Configuration Problems

You may customize these categories based on your specific API component needs.

## Problem File Structure

Each problem file should follow this consistent structure:

```markdown
# [Problem Name]

> **Applies to:** [API versions, components]

## Symptoms
[How to recognize this issue]

## Root Causes
[Why this happens]

## Solution Steps
[Step-by-step resolution]

## Prevention
[How to avoid this in the future]

## Related Issues
[Links to related problems]
```

## Navigation Patterns

To ensure users can navigate effectively through the guide:

1. Include breadcrumb navigation at the top of each file
2. Add "Next" and "Previous" links at the bottom for sequential navigation
3. Include a "Related Issues" section at the end of each problem page
4. Add an "In This Category" mini-table of contents in each category file
5. Link back to the decision tree and issue identification page from all problem pages

## GitHub-Compatible Interactive Elements

Enhance user experience with these GitHub-compatible elements:

1. Collapsible sections with `<details>` tags for code examples and verbose explanations (this is the only HTML we generally permit)
2. Emoji indicators for different types of issues (🔒 for authentication, 🌐 for network, etc.)
3. Checkbox lists for step-by-step verification procedures
4. Code blocks with syntax highlighting for error messages and solutions
5. Standard markdown tables for comparing different scenarios or error types
6. Nested bulleted lists for decision trees instead of ASCII art (which doesn't properly render links)

**Important:** Avoid using HTML tables or other HTML elements besides `<details>` and `<summary>` tags, as they may not render consistently across all environments.

## Example Implementation

See the [`/examples/authentication-troubleshooting/`](./examples/authentication-troubleshooting/) directory for a complete example implementation of this template.

## Customization Options

### Short Guides

For simpler components with fewer issues, you may:
- Combine some category files
- Simplify the decision tree
- Omit the advanced troubleshooting section if not needed

### Complex Components

For components with many issues:
- Add additional category subdirectories
- Create more granular issue files
- Expand the decision tree with more branching options

## Related Resources

- [Documentation Standards](../../documentation-standards/)
- [Tutorial Template](../tutorial-template/)
- [UX Guidelines for Documentation](../../ux-guidelines.md) 