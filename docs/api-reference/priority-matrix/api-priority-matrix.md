# API Priority Matrix

## Overview

This document outlines the priority of methods within each Azure DevOps API client. The prioritization is based on frequency of use, ecosystem impact, complexity, and support burden as determined through consultation with the Azure DevOps API team and usage analytics. Each API client section lists methods in descending order of priority, along with common use cases.

Use this matrix to:
- Determine which methods to learn first when using the API
- Understand the most common use cases for each API client
- Focus your integration efforts on the most widely used functionality
- Find the most important methods to migrate when updating from older versions

## Core API

| Priority | Method | Description | Common Use Cases | Complexity |
|:--------:|--------|-------------|-----------------|:----------:|
| 1 | **getProjects** | Lists all available projects | Organization inventory, dashboard creation, cross-project reporting | Low |
| 2 | **getProject** | Gets details of a specific project | Project configuration tools, permission setup, detailed project info | Low |
| 3 | **getTeams** | Lists teams in a project | Team management apps, organization mapping | Low |
| 4 | **getTeam** | Gets team details | Team configuration apps, capacity planning | Low |
| 5 | **getTeamMembers** | Retrieves members of a team | Resource allocation, team dashboards, access management | Medium |

**Example**:
```typescript
// Get all projects in the organization
const coreApi = await connection.getCoreApi();
const projects = await coreApi.getProjects();

console.log(`Found ${projects.length} projects`);
projects.forEach(project => {
    console.log(`- ${project.name} (${project.id})`);
});
```

## Git API

| Priority | Method | Description | Common Use Cases | Complexity |
|:--------:|--------|-------------|-----------------|:----------:|
| 1 | **getRepositories** | Lists all Git repositories | Repository inventory, dashboard creation | Low |
| 2 | **getRepository** | Gets details of a specific repository | Repository analysis, integration with external tools | Low |
| 3 | **getRefs** | Gets branches and tags | Branch management, build integration, reporting | Medium |
| 4 | **getItems**/**getItemContent** | Gets file/folder info or content | Content extraction, code analysis, build processes | Medium |
| 5 | **getPullRequests** | Gets pull requests | PR dashboards, review tools, status reporting | Medium |
| 6 | **createPullRequest** | Creates a new pull request | Automated PR creation, cross-repo integrations | High |
| 7 | **getCommits** | Gets commit history | Audit trails, reporting, changelog generation | Medium |

**Example**:
```typescript
// Get all repositories
const gitApi = await connection.getGitApi();
const repositories = await gitApi.getRepositories();

// Get branches for the first repository
if (repositories.length > 0) {
    const refs = await gitApi.getRefs(
        repositories[0].id,
        undefined,
        "heads/"  // Filter to branches only
    );
    
    console.log(`Repository ${repositories[0].name} has ${refs.length} branches`);
}
```

## Work Item Tracking API

| Priority | Method | Description | Common Use Cases | Complexity |
|:--------:|--------|-------------|-----------------|:----------:|
| 1 | **getWorkItem** | Gets a specific work item | Status dashboards, integration with external tools | Low |
| 2 | **getWorkItems** | Gets multiple work items | Batch processing, reporting, dashboards | Medium |
| 3 | **createWorkItem** | Creates a new work item | Automated ticket creation, external tool integration | High |
| 4 | **updateWorkItem** | Updates an existing work item | Status updates from CI/CD, bulk updates | High |
| 5 | **getQueries** | Gets saved work item queries | Report generation, dashboard creation | Medium |
| 6 | **queryByWiql** | Executes a WIQL query | Custom reporting, complex filtering | High |

**Example**:
```typescript
// Get work item details
const witApi = await connection.getWorkItemTrackingApi();
const workItem = await witApi.getWorkItem(42); // Replace with actual ID

console.log(`Work Item: ${workItem.id}`);
console.log(`Title: ${workItem.fields["System.Title"]}`);
console.log(`State: ${workItem.fields["System.State"]}`);

// Create a new work item
const patchDocument = [
    {
        op: "add",
        path: "/fields/System.Title",
        value: "New bug from API"
    },
    {
        op: "add",
        path: "/fields/System.Description",
        value: "Description of the issue"
    }
];

const newWorkItem = await witApi.createWorkItem(
    patchDocument,
    "MyProject",
    "Bug"
);
```

## Build API

| Priority | Method | Description | Common Use Cases | Complexity |
|:--------:|--------|-------------|-----------------|:----------:|
| 1 | **getDefinitions** | Gets build definitions | Build dashboards, pipeline management | Medium |
| 2 | **getBuild** | Gets a specific build | Build status reporting, artifact location | Low |
| 3 | **getBuilds** | Gets multiple builds | Dashboard creation, trend analysis | Medium |
| 4 | **queueBuild** | Queues a new build | CI/CD integration, automated builds | High |
| 5 | **getBuildLogs** | Gets build logs | Log analysis, troubleshooting tools | Medium |

**Example**:
```typescript
// Get build definitions
const buildApi = await connection.getBuildApi();
const definitions = await buildApi.getDefinitions("MyProject");

console.log(`Found ${definitions.length} build definitions`);

// Queue a build
const build = {
    definition: {
        id: definitions[0].id
    },
    sourceBranch: "refs/heads/main"
};

const queuedBuild = await buildApi.queueBuild(build, "MyProject");
console.log(`Queued build ${queuedBuild.id}`);
```

## Release API

| Priority | Method | Description | Common Use Cases | Complexity |
|:--------:|--------|-------------|-----------------|:----------:|
| 1 | **getReleaseDefinitions** | Gets release definitions | Release dashboard, pipeline management | Medium |
| 2 | **getRelease** | Gets a specific release | Status reporting, deployment tracking | Low |
| 3 | **getReleases** | Gets multiple releases | Deployment tracking, reporting | Medium |
| 4 | **createRelease** | Creates a new release | Automated deployments, CI/CD pipelines | High |
| 5 | **updateReleaseEnvironment** | Updates environment status | Deployment approval automation, status tracking | High |

**Example**:
```typescript
// Get release definitions
const releaseApi = await connection.getReleaseApi();
const definitions = await releaseApi.getReleaseDefinitions("MyProject");

console.log(`Found ${definitions.length} release definitions`);

// Create a new release
if (definitions.length > 0) {
    const release = await releaseApi.createRelease({
        definitionId: definitions[0].id,
        isDraft: false,
        reason: "Manual",
        description: "Release created via API"
    }, "MyProject");
    
    console.log(`Created release ${release.id}`);
}
```

## Test Management API

| Priority | Method | Description | Common Use Cases | Complexity |
|:--------:|--------|-------------|-----------------|:----------:|
| 1 | **getTestRuns** | Gets test runs | Test reporting, quality dashboards | Medium |
| 2 | **getTestResults** | Gets test results | Test analysis, quality metrics | Medium |
| 3 | **getTestPlans** | Gets test plans | Test management, planning tools | Medium |
| 4 | **createTestRun** | Creates a test run | Automated testing, CI integration | High |
| 5 | **updateTestResults** | Updates test results | Test automation, status updates | High |

**Example**:
```typescript
// Get test runs
const testApi = await connection.getTestApi();
const testRuns = await testApi.getTestRuns("MyProject");

console.log(`Found ${testRuns.length} test runs`);
testRuns.forEach(run => {
    console.log(`Run ${run.id}: ${run.name} - ${run.state}`);
});
```

## Task Management API

| Priority | Method | Description | Common Use Cases | Complexity |
|:--------:|--------|-------------|-----------------|:----------:|
| 1 | **getPlanList** | Gets delivery plans | Project planning, timeline visualization | Medium |
| 2 | **getTeamDaysOff** | Gets team capacity info | Resource planning, sprint management | Medium |
| 3 | **getCapacitiesWithIdentityRef** | Gets team member capacity | Capacity planning, workload management | Medium |
| 4 | **getBoard** | Gets a specific board | Kanban visualization, workflow analysis | Medium |
| 5 | **getBoardCardSettings** | Gets card settings | Board customization tools | Medium |

**Example**:
```typescript
// Get team boards
const taskApi = await connection.getTaskAgentApi();
const boards = await taskApi.getBoards("MyProject", "MyTeam");

console.log(`Team has ${boards.length} boards`);
```

## Integration Patterns

Understanding common integration patterns can help you implement the APIs more effectively:

### Repository Analysis Pattern
```typescript
// Get repos, then analyze each one
const gitApi = await connection.getGitApi();
const repos = await gitApi.getRepositories();

for (const repo of repos) {
    // Get stats for each repo
    const stats = await gitApi.getStatistics(repo.id);
    // Get latest commits
    const commits = await gitApi.getCommits(repo.id, { top: 10 });
    
    // Process and report on the data
    console.log(`Repo ${repo.name}: ${commits.length} recent commits`);
}
```

### Work Item Synchronization Pattern
```typescript
// Sync work items with external system
const witApi = await connection.getWorkItemTrackingApi();
const workItems = await witApi.getWorkItems([1, 2, 3, 4, 5]);

// Process each work item
for (const workItem of workItems) {
    // Map Azure DevOps fields to external system
    const externalItem = {
        id: workItem.id,
        title: workItem.fields["System.Title"],
        status: workItem.fields["System.State"],
        // Additional field mapping
    };
    
    // Update external system (pseudocode)
    // await externalSystem.updateItem(externalItem);
}
```

### CI/CD Integration Pattern
```typescript
// Build and release integration
const buildApi = await connection.getBuildApi();
const releaseApi = await connection.getReleaseApi();

// Find successful builds
const builds = await buildApi.getBuilds("MyProject", undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined, "succeeded");

// Create release for latest successful build
if (builds.length > 0) {
    const latestBuild = builds[0];
    const releaseDefinitions = await releaseApi.getReleaseDefinitions("MyProject");
    
    if (releaseDefinitions.length > 0) {
        // Create release using the successful build
        await releaseApi.createRelease({
            definitionId: releaseDefinitions[0].id,
            artifacts: [
                {
                    alias: "_MyBuild",
                    instanceReference: {
                        id: latestBuild.id.toString(),
                        name: latestBuild.buildNumber
                    }
                }
            ]
        }, "MyProject");
    }
}
```

## Prioritization Factors

The methods in this matrix were prioritized based on these factors:

1. **Frequency of Use**: How often the method is called across Azure DevOps integrations
2. **Ecosystem Impact**: How central the method is to DevOps workflows
3. **Complexity**: Whether extensive documentation would significantly help implementation
4. **Support Burden**: Methods that frequently generate support tickets
5. **Logical Flow**: The sequence in which methods would naturally be learned and used

## Conclusion

This API Priority Matrix provides a guide for which methods to focus on first when building integrations with Azure DevOps. The priorities reflect the most common and valuable integration points based on real-world usage patterns. Documentation efforts will follow this prioritization to ensure the most widely used functionality is well-documented first.

For any questions or suggestions about this prioritization, please contact the documentation team. 