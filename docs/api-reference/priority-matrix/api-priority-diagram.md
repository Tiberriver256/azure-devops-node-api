# API Priority Visualization

This document provides a visual summary of the API priorities across the different Azure DevOps API clients.

## API Client Priority Heat Map

**API Client Method Priorities (High to Low):**

- **Core API:**
  - getProjects (Highest)
  - getProject
  - getTeams
  - getTeam (Lower)

- **Git API:**
  - getRepositories (Highest)
  - getRepository
  - getRefs
  - getItems
  - getPullRequests (Lower)

- **Work Item API:**
  - getWorkItem (Highest)
  - getWorkItems
  - createWorkItem
  - updateWorkItem
  - queryByWiql (Lower)

- **Build API:**
  - getDefinitions (Highest)
  - getBuild
  - getBuilds
  - queueBuild
  - getBuildLogs (Lower)

- **Release API:**
  - getReleaseDefinitions (Highest)
  - getRelease
  - getReleases
  - createRelease
  - updateReleaseEnvironment (Lower)

## API Client Method Complexity

**API Client Method Complexity Table:**

- **Git API:** createPullRequest, createRepository, getPullRequest
- **Work Item API:** createWorkItem, updateWorkItem, queryByWiql
- **Build API:** queueBuild, updateBuild
- **Release API:** createRelease, updateReleaseEnvironment
- **Test API:** createTestRun, updateTestResults

## API Client Relationship Map

**API Relationship Structure:**

- **Core API (Projects & Teams)** serves as the central component
- Three main APIs branch from the Core API:
  - **Git API (Code & Repositories)**
  - **Work Item API**
  - **Build API**
- The **Git API** connects to the **Pull Request API**
- The **Build API** connects to the **Release API**
- The **Work Item API** connects to both the **Pull Request API** and **Release API**
- The **Work Item API** also connects to the **Test API**

## Documentation Priority Sequence

The following sequence represents the recommended order for documentation efforts:

1. Essential connection methods (WebApi Core)
2. Authentication methods (All authentication options)
3. Core API top methods (Project and team access)
4. Git API top methods (Repository access)
5. Work Item API top methods (Work item operations)
6. Build API top methods (Build operations)
7. Release API top methods (Release operations)
8. Test API top methods (Test operations)
9. Integration patterns (Cross-API workflows)
10. Advanced scenarios (Complex multi-API operations)

This sequence ensures that fundamental functionality is documented first, followed by the most frequently used API clients, and finally more advanced or specialized functionality. 