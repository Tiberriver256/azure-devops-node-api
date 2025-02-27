# API Cross-Reference Table

**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > [Integration Patterns](./README.md) > API Cross-Reference Table

## Overview

This document provides a comprehensive cross-reference table showing the relationships between different API clients and their methods. It helps developers understand which methods are commonly used together and which integration patterns they support.

## API Method Relationships

### Work Item, Git, and Build API Relationships

The following table shows how methods from the Work Item Tracking, Git, and Build APIs relate to each other. Use this table to identify which methods you might need to use together when implementing integrations.

| API Client | Method | Related API Methods | Integration Patterns |
|:-----------|:-------|:--------------------|:---------------------|
| **Work Item Tracking API** | getWorkItem | Git API: getPullRequests<br>Build API: getBuilds | [Work Item + Git Integration](./work-item-git-integration.md)<br>[Work Item + Build Integration](./work-item-build-integration.md) |
| **Work Item Tracking API** | getWorkItems | Git API: getPullRequests<br>Build API: getBuilds | [Work Item + Git Integration](./work-item-git-integration.md)<br>[Work Item + Build Integration](./work-item-build-integration.md) |
| **Work Item Tracking API** | createWorkItem | Git API: createPullRequest<br>Build API: queueBuild | [Feature Implementation Lifecycle](./cross-api-examples.md#1-feature-implementation-lifecycle) |
| **Work Item Tracking API** | updateWorkItem | Git API: createPullRequest<br>Build API: queueBuild | [Feature Implementation Lifecycle](./cross-api-examples.md#1-feature-implementation-lifecycle) |
| **Work Item Tracking API** | queryByWiql | Git API: getPullRequests<br>Build API: getBuilds | [Pull Request Review Dashboard](./cross-api-examples.md#2-pull-request-review-dashboard) |

### Git API Method Relationships

| API Client | Method | Related API Methods | Integration Patterns |
|:-----------|:-------|:--------------------|:---------------------|
| **Git API** | getRepositories | Work Item API: queryByWiql<br>Build API: getDefinitions | [Repository Analysis Pattern](../priority-matrix/api-priority-matrix.md#integration-patterns) |
| **Git API** | getRepository | Work Item API: queryByWiql<br>Build API: getDefinitions | [Repository Analysis Pattern](../priority-matrix/api-priority-matrix.md#integration-patterns) |
| **Git API** | getRefs | Build API: getBuilds<br>Build API: queueBuild | [Git + Build Integration](./git-build-integration.md) |
| **Git API** | getPullRequests | Work Item API: getWorkItems<br>Build API: getBuilds | [Pull Request Review Dashboard](./cross-api-examples.md#2-pull-request-review-dashboard) |
| **Git API** | createPullRequest | Work Item API: updateWorkItem<br>Build API: queueBuild | [Feature Implementation Lifecycle](./cross-api-examples.md#1-feature-implementation-lifecycle) |

### Build API Method Relationships

| API Client | Method | Related API Methods | Integration Patterns |
|:-----------|:-------|:--------------------|:---------------------|
| **Build API** | getDefinitions | Git API: getRepositories<br>Git API: getRefs | [Git + Build Integration](./git-build-integration.md) |
| **Build API** | getBuild | Work Item API: getWorkItem<br>Git API: getCommit | [Work Item + Build Integration](./work-item-build-integration.md)<br>[Git + Build Integration](./git-build-integration.md) |
| **Build API** | getBuilds | Work Item API: getWorkItems<br>Git API: getPullRequests | [Pull Request Review Dashboard](./cross-api-examples.md#2-pull-request-review-dashboard) |
| **Build API** | queueBuild | Git API: getRefs<br>Work Item API: updateWorkItem | [Feature Implementation Lifecycle](./cross-api-examples.md#1-feature-implementation-lifecycle) |
| **Build API** | getBuildLogs | Git API: getCommit<br>Work Item API: getWorkItem | [Work Item + Build Integration](./work-item-build-integration.md) |

## Integration Scenarios

### Common Integration Scenarios

The following table shows common integration scenarios and the API methods they typically use. These scenarios represent real-world use cases that combine multiple APIs to solve specific problems.

| Integration Scenario | Primary API | Secondary APIs | Key Methods |
|:---------------------|:------------|:---------------|:------------|
| **Feature Implementation Workflow** | Work Item API | Git API, Build API | createWorkItem, updateWorkItem, createPullRequest, queueBuild |
| **Pull Request Review Dashboard** | Git API | Work Item API, Build API | getPullRequests, getWorkItems, getBuilds |
| **Build Status Reporting** | Build API | Work Item API, Git API | getBuilds, getWorkItems, getCommits |
| **Release Readiness Report** | Work Item API | Build API, Git API | queryByWiql, getBuilds, getPullRequests |
| **Repository Health Analysis** | Git API | Build API, Work Item API | getRepositories, getBuilds, queryByWiql |

## API Relationships

### API Client Relationship Structure

The following describes the high-level relationships between the primary APIs:

- The Core API (Projects & Teams) serves as the central component
- Three main APIs branch from the Core API:
  - Git API (Code & Repositories)
  - Work Item Tracking API
  - Build API
- These three APIs have bidirectional relationships with each other
- All APIs contribute to Integration Patterns

### Relationship Implications

Understanding these relationships helps when designing integrations:

- Changes in one API area often require coordinated changes in related areas
- Authentication tokens need permissions for all related APIs
- Error handling should account for dependencies between APIs
- Performance considerations should include all related API calls

## See Also

- [Work Item + Git Integration](./work-item-git-integration.md) - Detailed patterns for integrating work items with Git
- [Work Item + Build Integration](./work-item-build-integration.md) - Patterns for connecting work items with builds
- [Git + Build Integration](./git-build-integration.md) - Patterns for integrating Git repositories with builds
- [Cross-API Examples](./cross-api-examples.md) - Complex scenarios involving multiple APIs
- [API Priority Matrix](../priority-matrix/api-priority-matrix.md) - Understanding the most important API methods 