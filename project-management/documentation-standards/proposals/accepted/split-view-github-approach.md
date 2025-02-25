# Split View Approach for Complex API Documentation (GitHub-Compatible)

**Author**: Taylor  
**Date**: March 25, 2023  
**Status**: ACCEPTED (March 29, 2023)  

## Executive Summary

Due to budget constraints eliminating the possibility of hosting documentation on a dedicated website, we need to adapt our documentation strategy to work within the constraints of GitHub-hosted markdown files. This proposal outlines a "split view" approach for complex API components that maintains the benefits of our previously proposed expandable view while working within GitHub's limitations.

## Background

Our previous expandable view proposal relied on JavaScript and client-side interactions that are not supported in GitHub-rendered markdown. However, the core problem remains: complex API components like `WorkItemTrackingApi` with numerous methods and properties create cognitive load issues for users when presented in a single file.

## Proposal: Split View Approach

The split view approach divides complex API documentation into multiple markdown files with consistent navigation patterns, creating a more manageable experience while working within GitHub's constraints.

### Key Features

1. **Directory-Based Structure**: Organize complex component documentation into a directory with multiple markdown files.
2. **Main Overview File**: Create a main README.md file that serves as the entry point with links to all sections.
3. **Logical Section Files**: Separate files for constructor, properties, examples, common scenarios, and error handling.
4. **Methods Directory**: For components with many methods, create a methods directory with:
   - A README.md file listing all methods with brief descriptions
   - Individual files for each method with detailed documentation
5. **Consistent Navigation**: Add navigation links at the top and bottom of each file to maintain context.
6. **Breadcrumb Navigation**: Include breadcrumb-style links at the top of each file to show the current location.

### Implementation Structure

```
work-item-tracking/
├── README.md                     # Main overview and entry point
├── constructor.md                # Constructor documentation
├── properties.md                 # Properties documentation
├── methods/                      # Methods directory
│   ├── README.md                 # Methods overview with categorized listings
│   ├── get-work-item.md          # Individual method documentation
│   ├── create-work-item.md       # Individual method documentation
│   └── ...                       # Other method files
├── examples.md                   # Code examples
├── common-scenarios.md           # Common usage scenarios
└── error-handling.md             # Error handling guidance
```

### Navigation Pattern

Each file will include consistent navigation elements:

1. **Top Navigation**: Links to parent pages and related sections
   ```markdown
   [◀ Back to WorkItemTrackingApi](../README.md) | [◀ Back to Methods](./README.md)
   ```

2. **Bottom Navigation**: Links to main sections
   ```markdown
   ## Navigation
   
   - [WorkItemTrackingApi Overview](../README.md)
   - [Constructor](../constructor.md)
   - [Properties](../properties.md)
   - [Methods](./README.md)
   - [Examples](../examples.md)
   - [Common Scenarios](../common-scenarios.md)
   - [Error Handling](../error-handling.md)
   ```

## Benefits

1. **Reduced Cognitive Load**: Users only need to focus on the specific section they're interested in.
2. **Improved Navigation**: Clear navigation patterns help users maintain context.
3. **Better Maintainability**: Smaller files are easier to update and maintain.
4. **GitHub Compatibility**: Works within the constraints of GitHub-hosted markdown.
5. **Progressive Disclosure**: Information is presented in a logical, progressive manner.
6. **Searchability**: GitHub's search functionality works well with this approach.

## Selection Criteria

We will apply this approach selectively to components that meet specific complexity thresholds:

- Classes/Interfaces with more than 15 methods or properties
- Methods with more than 10 parameters or complex return types
- Components with extensive examples or usage scenarios
- Components that frequently appear in user support inquiries

## Pilot Implementation

We will implement this approach as a pilot for the `WorkItemTrackingApi` class, which is one of our most complex components with numerous methods, properties, and usage examples.

## Considerations and Mitigations

| Consideration | Mitigation |
|---------------|------------|
| Navigation Complexity | Consistent navigation patterns and breadcrumbs in each file |
| Potential for Duplication | Create reusable content snippets for common elements |
| Maintenance Overhead | Develop clear guidelines and templates for consistency |
| User Experience Fragmentation | Standardize formatting and structure across all files |
| GitHub Limitations | Leverage GitHub's native features (anchors, relative links) |

## Acceptance Notes

This proposal was accepted with the following notes:
- The split view approach effectively addresses the cognitive load concerns while working within GitHub constraints
- The pilot implementation for WorkItemTrackingApi has been completed and received positive feedback
- The consistent navigation patterns are essential for maintaining context across multiple files
- This approach will be applied selectively to only the most complex components 