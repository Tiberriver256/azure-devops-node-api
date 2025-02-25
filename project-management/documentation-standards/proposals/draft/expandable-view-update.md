# Expandable View Proposal Update: GitHub Constraints

**Author**: Taylor  
**Date**: March 25, 2023  
**Status**: Draft  

## Executive Summary

Due to budget constraints, we will not be able to host our documentation on a dedicated website as originally planned. This update addresses how our expandable view proposal is affected by the limitations of GitHub-hosted markdown files and outlines alternative approaches that maintain the core benefits while working within GitHub's constraints.

## Impact Assessment

### Original Proposal Elements Affected

1. **Client-Side Toggle**: GitHub-rendered markdown does not support JavaScript, making our preferred client-side toggle implementation impossible.
2. **Dynamic Content Display**: Without JavaScript, we cannot dynamically show/hide content sections.
3. **User Preference Storage**: Local storage for remembering user view preferences is not available.
4. **Sticky Navigation**: GitHub's markdown rendering does not support custom CSS for sticky navigation elements.
5. **Interactive UI Elements**: Custom UI components like the expandable view toggle cannot be implemented.

### Elements That Can Be Preserved

1. **Content Organization**: The logical breakdown of complex components into sections.
2. **Progressive Disclosure**: We can still implement a form of progressive disclosure through linked files.
3. **Reduced Cognitive Load**: By splitting content across multiple files, we can still reduce cognitive load.
4. **Improved Maintainability**: Smaller, focused files remain easier to maintain.

## Alternative Approach: Split View

In place of the expandable view, we propose implementing a "split view" approach that divides complex API documentation into multiple markdown files with consistent navigation patterns. This approach is detailed in the [Split View GitHub Approach](./split-view-github-approach.md) proposal.

### Key Differences from Original Proposal

| Original Expandable View | GitHub-Compatible Split View |
|--------------------------|------------------------------|
| Single file with toggle for expanded sections | Multiple files with navigation links |
| Client-side JavaScript for dynamic content | Static markdown files with explicit navigation |
| User preference stored in local storage | No persistent user preferences |
| Sticky table of contents | Standard GitHub navigation |
| Interactive UI elements | Standard markdown links and formatting |

### Implementation Comparison

#### Original Implementation (Not GitHub-Compatible)

```
+-----------------------------------------------------------------------+
| [View as: (•) Single Page | ( ) Expanded Sections]  [?]               |
|                                                                       |
| # WorkItemTrackingApi                                                 |
|                                                                       |
| The WorkItemTrackingApi class provides access to the Azure DevOps     |
| work item tracking functionality.                                     |
|                                                                       |
| ## Contents                                                           |
| - [Overview](#overview)                                               |
| - [Constructor](#constructor)                                         |
| - [Properties](#properties)                                           |
| - [Methods](#methods)                                                 |
| ...                                                                   |
```

#### GitHub-Compatible Implementation

```markdown
# WorkItemTrackingApi

[◀ Back to API Reference](../../README.md)

---

## Overview

The WorkItemTrackingApi class provides access to the Azure DevOps work item tracking functionality.

## Sections

- [Constructor](./constructor.md)
- [Properties](./properties.md)
- [Methods](./methods/README.md)
- [Examples](./examples.md)
- [Common Scenarios](./common-scenarios.md)
- [Error Handling](./error-handling.md)
- [Related Resources](./related-resources.md)

...
```

## Revised Approach for Complex Documentation

### Standard Documentation (Most Components)

For most API components with moderate complexity, we will continue to use single markdown files with clear section headers and navigation.

### Complex Documentation (Selected Components)

For complex components meeting our selection criteria (15+ methods/properties, 10+ parameters, etc.), we will implement the split view approach with:

1. A main overview file (README.md) serving as the entry point
2. Separate files for constructor, properties, and other major sections
3. A methods directory with individual files for each method
4. Consistent navigation patterns across all files

## Benefits of the Revised Approach

1. **GitHub Compatibility**: Works within the constraints of GitHub-hosted markdown.
2. **Reduced Cognitive Load**: Users only need to focus on the specific section they're interested in.
3. **Improved Navigation**: Clear navigation patterns help users maintain context.
4. **Better Maintainability**: Smaller files are easier to update and maintain.
5. **Progressive Disclosure**: Information is presented in a logical, progressive manner.
6. **Searchability**: GitHub's search functionality works well with this approach.

## Considerations and Mitigations

| Consideration | Mitigation |
|---------------|------------|
| Navigation Complexity | Consistent navigation patterns and breadcrumbs in each file |
| Potential for Duplication | Create reusable content snippets for common elements |
| Maintenance Overhead | Develop clear guidelines and templates for consistency |
| User Experience Fragmentation | Standardize formatting and structure across all files |
| GitHub Limitations | Leverage GitHub's native features (anchors, relative links) |

## Next Steps

1. Finalize the split view approach proposal
2. Create detailed templates for each file type
3. Implement the pilot for WorkItemTrackingApi
4. Collect feedback and refine the approach
5. Develop guidelines for applying this approach to other complex components

## Conclusion

While the budget constraints prevent us from implementing our original expandable view proposal, the split view approach offers a viable alternative that maintains many of the core benefits while working within GitHub's limitations. By focusing on clear organization, consistent navigation, and progressive disclosure through linked files, we can still significantly improve the documentation experience for complex API components. 