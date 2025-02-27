# API Priority Visualization

This diagram provides a visual summary of the API priorities across the different Azure DevOps API clients.

## API Client Priority Heat Map

```
    High                                            Priority                                         Low
     ┌─────────────────────────────────────────────────────────────────────────────────────────────┐
Core │███████████ getProjects ██████████ getProject █████████ getTeams ██████ getTeam ████████████│
     ├─────────────────────────────────────────────────────────────────────────────────────────────┤
Git  │████ getRepositories ███ getRepository ███ getRefs ███ getItems ██ getPullRequests ██████████│
     ├─────────────────────────────────────────────────────────────────────────────────────────────┤
Work │██████ getWorkItem ██████ getWorkItems ███ createWorkItem ██ updateWorkItem ██ queryByWiql ██│
Item ├─────────────────────────────────────────────────────────────────────────────────────────────┤
Build│█████ getDefinitions ████ getBuild ████ getBuilds ███ queueBuild ███ getBuildLogs ███████████│
     ├─────────────────────────────────────────────────────────────────────────────────────────────┤
Rel. │██ getReleaseDefinitions █ getRelease █ getReleases █ createRelease █ updateReleaseEnvironment│
     └─────────────────────────────────────────────────────────────────────────────────────────────┘
```

## API Client Method Complexity

```
    API Client            Highest Complexity Methods
    ┌───────────────────┬────────────────────────────────────────────────────┐
    │ Git API           │ createPullRequest, createRepository, getPullRequest │
    ├───────────────────┼────────────────────────────────────────────────────┤
    │ Work Item API     │ createWorkItem, updateWorkItem, queryByWiql         │
    ├───────────────────┼────────────────────────────────────────────────────┤
    │ Build API         │ queueBuild, updateBuild                             │
    ├───────────────────┼────────────────────────────────────────────────────┤
    │ Release API       │ createRelease, updateReleaseEnvironment             │
    ├───────────────────┼────────────────────────────────────────────────────┤
    │ Test API          │ createTestRun, updateTestResults                    │
    └───────────────────┴────────────────────────────────────────────────────┘
```

## API Client Relationship Map

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
          │ (Code & Repos)  │ │     API      │ │     API      │
          └────────┬────────┘ └──────┬───────┘ └──────┬───────┘
                   │                 │                 │
          ┌────────▼────────┐        │          ┌─────▼────────┐
          │  Pull Request   │        │          │   Release    │
          │      API        │◄───────┼──────────►     API      │
          └─────────────────┘        │          └──────────────┘
                                     │
                             ┌──────▼───────┐
                             │    Test      │
                             │     API      │
                             └──────────────┘
```

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