# API Cross-Reference Table

**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > [Integration Patterns](./README.md) > API Cross-Reference Table

## Overview

This document provides a comprehensive cross-reference table showing the relationships between different API clients and their methods. It helps developers understand which methods are commonly used together and which integration patterns they support.

## Work Item, Git, and Build API Relationships

The following table shows the relationships between the Work Item Tracking, Git, and Build APIs:

| API Client | Method | Related API Methods | Integration Patterns |
|:-----------|:-------|:--------------------|:---------------------|
| **Work Item Tracking API** | getWorkItem | Git API: getPullRequests<br>Build API: getBuilds | [Work Item + Git Integration](./work-item-git-integration.md)<br>[Work Item + Build Integration](./work-item-build-integration.md) |
| **Work Item Tracking API** | getWorkItems | Git API: getPullRequests<br>Build API: getBuilds | [Work Item + Git Integration](./work-item-git-integration.md)<br>[Work Item + Build Integration](./work-item-build-integration.md) |
| **Work Item Tracking API** | createWorkItem | Git API: createPullRequest<br>Build API: queueBuild | [Feature Implementation Lifecycle](./cross-api-examples.md#1-feature-implementation-lifecycle) |
| **Work Item Tracking API** | updateWorkItem | Git API: createPullRequest<br>Build API: queueBuild | [Feature Implementation Lifecycle](./cross-api-examples.md#1-feature-implementation-lifecycle) |
| **Work Item Tracking API** | queryByWiql | Git API: getPullRequests<br>Build API: getBuilds | [Pull Request Review Dashboard](./cross-api-examples.md#2-pull-request-review-dashboard) |
| **Git API** | getRepositories | Work Item API: queryByWiql<br>Build API: getDefinitions | [Repository Analysis Pattern](../priority-matrix/api-priority-matrix.md#integration-patterns) |
| **Git API** | getRepository | Work Item API: queryByWiql<br>Build API: getDefinitions | [Repository Analysis Pattern](../priority-matrix/api-priority-matrix.md#integration-patterns) |
| **Git API** | getRefs | Build API: getBuilds<br>Build API: queueBuild | [Git + Build Integration](./git-build-integration.md) |
| **Git API** | getPullRequests | Work Item API: getWorkItems<br>Build API: getBuilds | [Pull Request Review Dashboard](./cross-api-examples.md#2-pull-request-review-dashboard) |
| **Git API** | createPullRequest | Work Item API: updateWorkItem<br>Build API: queueBuild | [Feature Implementation Lifecycle](./cross-api-examples.md#1-feature-implementation-lifecycle) |
| **Build API** | getDefinitions | Git API: getRepositories<br>Git API: getRefs | [Git + Build Integration](./git-build-integration.md) |
| **Build API** | getBuild | Work Item API: getWorkItem<br>Git API: getCommit | [Work Item + Build Integration](./work-item-build-integration.md)<br>[Git + Build Integration](./git-build-integration.md) |
| **Build API** | getBuilds | Work Item API: getWorkItems<br>Git API: getPullRequests | [Pull Request Review Dashboard](./cross-api-examples.md#2-pull-request-review-dashboard) |
| **Build API** | queueBuild | Git API: getRefs<br>Work Item API: updateWorkItem | [Feature Implementation Lifecycle](./cross-api-examples.md#1-feature-implementation-lifecycle) |
| **Build API** | getBuildLogs | Git API: getCommit<br>Work Item API: getWorkItem | [Work Item + Build Integration](./work-item-build-integration.md) |

## Common Integration Scenarios

The following table shows common integration scenarios and the API methods they typically use:

| Integration Scenario | Primary API | Secondary APIs | Key Methods |
|:---------------------|:------------|:---------------|:------------|
| **Feature Implementation Workflow** | Work Item API | Git API, Build API | createWorkItem, updateWorkItem, createPullRequest, queueBuild |
| **Pull Request Review Dashboard** | Git API | Work Item API, Build API | getPullRequests, getWorkItems, getBuilds |
| **Build Status Reporting** | Build API | Work Item API, Git API | getBuilds, getWorkItems, getCommits |
| **Release Readiness Report** | Work Item API | Build API, Git API | queryByWiql, getBuilds, getPullRequests |
| **Repository Health Analysis** | Git API | Build API, Work Item API | getRepositories, getBuilds, queryByWiql |

## API Client Relationship Diagram

```
                             ┌───────────────┐
                             │   Core API    │
                             │ (Projects &   │
                             │    Teams)     │
                             └───────┬───────┘
                                     │
                   ┌─────────────────┼─────────────────┐
                   │                 │                 │
          ┌────────▼────────┐ ┌──────▼───────┐ ┌──────▼───────┐
          │    Git API      │ │  Work Item   │ │    Build     │
          │ (Code & Repos)  │◄┼─────────────►│     API      │
          └────────┬────────┘ └──────┬───────┘ └──────┬───────┘
                   │                 │                 │
                   └─────────────────┼─────────────────┘
                                     │
                             ┌──────▼───────┐
                             │ Integration  │
                             │  Patterns    │
                             └──────────────┘
```

## See Also

- [Work Item + Git Integration](./work-item-git-integration.md)
- [Work Item + Build Integration](./work-item-build-integration.md)
- [Git + Build Integration](./git-build-integration.md)
- [Cross-API Examples](./cross-api-examples.md)
- [API Priority Matrix](../priority-matrix/api-priority-matrix.md) 