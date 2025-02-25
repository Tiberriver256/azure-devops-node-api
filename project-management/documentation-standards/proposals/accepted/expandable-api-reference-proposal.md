# Proposal: Expandable View for Complex API Reference Documentation

**Date:** March 23, 2023  
**Author:** Taylor (Senior Technical Writer)  
**Subject:** Applying Multi-File Principles to Complex API Reference Documentation  
**Status:** ACCEPTED (March 29, 2023)

## Executive Summary

This proposal outlines an approach to enhance the usability of complex API reference documentation by applying the successful multi-file principles from our tutorial template. The proposed "Expandable View" would maintain the benefits of comprehensive single-file reference documentation while addressing cognitive load concerns for particularly complex classes, interfaces, and methods.

This approach would be selectively applied only to the most complex components of the Azure DevOps Node API, where the current single-file approach may overwhelm users with excessive information.

## Background

Our recent implementation of a multi-file structure for tutorials has received positive feedback from both the UI/UX team and internal reviewers. The approach successfully addresses cognitive load concerns by breaking complex content into manageable sections with clear navigation.

While reference documentation generally benefits from having all information in a single file for searchability and context, our user research indicates that particularly complex API components (those with numerous methods, properties, and examples) can overwhelm users, making it difficult to find specific information.

## Proposed Solution: Expandable View

The proposed solution would maintain the current single-file approach as the default view while adding an optional "Expandable View" for complex components. This would give users the choice of how they want to consume the documentation based on their needs.

### Key Features

1. **Default Single-File View**: Maintains the comprehensive single-file approach for all API reference documentation
2. **Optional Expansion**: Adds a toggle option for complex components to expand into a multi-file view
3. **Logical Section Breakdown**: Divides complex documentation into logical sections (Overview, Properties, Methods, Examples, etc.)
4. **Consistent Navigation**: Implements the same navigation system we developed for the tutorial template
5. **Persistent Context**: Ensures users maintain context when navigating between expanded sections

### Selection Criteria

We will apply this approach selectively to components that meet specific complexity thresholds:

- Classes/Interfaces with more than 15 methods or properties
- Methods with more than 10 parameters or complex return types
- Components with extensive examples or usage scenarios
- Components that frequently appear in user support inquiries

## Technical Implementation

Due to budget constraints, we will implement this as a directory-based approach using GitHub-hosted markdown files. See the [Split View GitHub Approach](../accepted/split-view-github-approach.md) for implementation details.

## Pilot Implementation

We will implement this approach as a pilot for the `WorkItemTrackingApi` class, which is one of our most complex components with numerous methods, properties, and usage examples.

## Acceptance Notes

This proposal was accepted with the following modifications:
- Implementation will use the GitHub-compatible "Split View" approach instead of the original client-side toggle
- The core principles of reducing cognitive load and improving navigation remain intact
- The pilot implementation for WorkItemTrackingApi has been completed and received positive feedback 