# Cross-API Integration Examples

**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > [Integration Patterns](./README.md) > Cross-API Examples

## Overview

This document provides examples of complex integration patterns that involve three or more Azure DevOps APIs working together. These patterns demonstrate how to orchestrate multiple APIs to create comprehensive solutions for real-world scenarios.

## Common Integration Scenarios

### 1. Feature Implementation Lifecycle

Track a feature from work item creation through code implementation, builds, and deployment, using Work Item Tracking, Git, and Build APIs together.

#### Example: Feature Implementation Workflow

```typescript
import * as azdev from "azure-devops-node-api";

async function implementFeatureWorkflow(
    featureTitle: string,
    featureDescription: string,
    assignedTo: string
) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const witApi = await connection.getWorkItemTrackingApi();
    const gitApi = await connection.getGitApi();
    const buildApi = await connection.getBuildApi();
    
    try {
        // 1. Create a feature work item
        const workItemPatch = [
            {
                op: "add",
                path: "/fields/System.Title",
                value: featureTitle
            },
            {
                op: "add",
                path: "/fields/System.Description",
                value: featureDescription
            },
            {
                op: "add",
                path: "/fields/System.AssignedTo",
                value: assignedTo
            },
            {
                op: "add",
                path: "/fields/System.State",
                value: "New"
            }
        ];
        
        const workItem = await witApi.createWorkItem(
            {}, // No custom headers
            workItemPatch,
            projectName,
            "Feature"
        );
        
        console.log(`Created Feature #${workItem.id}: ${featureTitle}`);
        
        // 2. Create a feature branch
        const repository = (await gitApi.getRepositories(projectName))[0]; // Get first repository
        const mainBranch = await gitApi.getBranch(repository.id, "main", projectName);
        
        // Generate branch name from work item
        const branchName = `feature/${workItem.id}-${featureTitle.toLowerCase().replace(/[^a-z0-9]+/g, '-')}`;
        
        // Create the branch
        const ref = [{
            name: `refs/heads/${branchName}`,
            oldObjectId: "0000000000000000000000000000000000000000",
            newObjectId: mainBranch.commit.commitId
        }];
        
        await gitApi.updateRefs(ref, repository.id, projectName);
        console.log(`Created branch: ${branchName}`);
        
        // 3. Update work item with branch information
        const branchPatch = [
            {
                op: "add",
                path: "/fields/System.History",
                value: `Created feature branch: ${branchName}`
            },
            {
                op: "add",
                path: "/fields/System.State",
                value: "Active"
            }
        ];
        
        await witApi.updateWorkItem({}, branchPatch, workItem.id, projectName);
        
        // 4. Queue a validation build for the new branch
        const buildDefinitions = await buildApi.getDefinitions(projectName);
        const validationDefinition = buildDefinitions.find(d => d.name === "Feature Validation");
        
        if (validationDefinition) {
            const build = {
                definition: {
                    id: validationDefinition.id
                },
                project: {
                    id: projectName
                },
                sourceBranch: `refs/heads/${branchName}`,
                reason: 1, // Manual
                parameters: JSON.stringify({
                    workItemId: workItem.id.toString(),
                    featureTitle: featureTitle
                })
            };
            
            const queuedBuild = await buildApi.queueBuild(build, projectName);
            console.log(`Queued validation build: ${queuedBuild.buildNumber}`);
            
            // 5. Add build information to work item
            const buildPatch = [
                {
                    op: "add",
                    path: "/fields/System.History",
                    value: `Queued validation build: [${queuedBuild.buildNumber}](${queuedBuild._links.web.href})`
                }
            ];
            
            await witApi.updateWorkItem({}, buildPatch, workItem.id, projectName);
        }
        
        return {
            workItemId: workItem.id,
            branchName,
            success: true
        };
    } catch (error) {
        console.error(`Error: ${error.message}`);
        return {
            success: false,
            error: error.message
        };
    }
}

// Usage
const result = await implementFeatureWorkflow(
    "Add dark mode support",
    "Implement dark mode theme following the design in Figma",
    "developer@example.com"
);

if (result.success) {
    console.log(`Feature implementation workflow created:`);
    console.log(`- Work Item: #${result.workItemId}`);
    console.log(`- Branch: ${result.branchName}`);
} else {
    console.error(`Failed to create feature workflow: ${result.error}`);
}
```

### 2. Pull Request Review Dashboard

Create a comprehensive dashboard that shows pull requests with their associated work items and build status.

#### Example: Generate Pull Request Review Dashboard

```typescript
import * as azdev from "azure-devops-node-api";

async function generatePullRequestDashboard(projectName: string) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const gitApi = await connection.getGitApi();
    const buildApi = await connection.getBuildApi();
    const witApi = await connection.getWorkItemTrackingApi();
    
    try {
        // 1. Get all repositories in the project
        const repositories = await gitApi.getRepositories(projectName);
        const dashboard = {
            projectName,
            generatedAt: new Date(),
            repositories: []
        };
        
        // 2. Process each repository
        for (const repository of repositories) {
            console.log(`Processing repository: ${repository.name}`);
            
            // Get active pull requests
            const pullRequests = await gitApi.getPullRequests(
                repository.id,
                {
                    status: 1 // Active
                },
                projectName
            );
            
            const repoDashboard = {
                repositoryName: repository.name,
                repositoryId: repository.id,
                pullRequests: []
            };
            
            // 3. Process each pull request
            for (const pr of pullRequests) {
                console.log(`Processing PR #${pr.pullRequestId}: ${pr.title}`);
                
                // Get associated work items
                const workItemRefs = await gitApi.getPullRequestWorkItemRefs(
                    repository.id,
                    pr.pullRequestId,
                    projectName
                );
                
                const workItems = workItemRefs ? 
                    await witApi.getWorkItems(workItemRefs.map(ref => parseInt(ref.id))) : 
                    [];
                
                // Get build status
                const builds = await buildApi.getBuilds(
                    projectName,
                    undefined, // definitions
                    undefined, // buildNumber
                    undefined, // minTime
                    undefined, // maxTime
                    undefined, // requestedFor
                    undefined, // status
                    undefined, // result
                    undefined, // tagFilters
                    undefined, // properties
                    undefined, // top
                    undefined, // continuationToken
                    undefined, // maxBuildsPerDefinition
                    undefined, // deletedFilter
                    undefined, // queryOrder
                    pr.sourceRefName // branchName
                );
                
                // Get the latest build
                const latestBuild = builds.length > 0 ? builds[0] : null;
                
                // Get review status
                const threads = await gitApi.getThreads(
                    repository.id,
                    pr.pullRequestId,
                    projectName
                );
                
                const activeComments = threads.filter(t => t.status === 1); // Active
                const resolvedComments = threads.filter(t => t.status === 2); // Resolved
                
                // Calculate metrics
                const metrics = {
                    daysOpen: Math.floor((Date.now() - new Date(pr.creationDate).getTime()) / (1000 * 60 * 60 * 24)),
                    commitCount: (await gitApi.getPullRequestCommits(repository.id, pr.pullRequestId, projectName)).length,
                    fileCount: (await gitApi.getPullRequestIterationChanges(repository.id, pr.pullRequestId, 1, projectName)).changeEntries.length,
                    activeComments: activeComments.length,
                    resolvedComments: resolvedComments.length
                };
                
                repoDashboard.pullRequests.push({
                    pullRequestId: pr.pullRequestId,
                    title: pr.title,
                    description: pr.description,
                    status: pr.status,
                    createdBy: pr.createdBy.displayName,
                    creationDate: pr.creationDate,
                    sourceBranch: pr.sourceRefName,
                    targetBranch: pr.targetRefName,
                    metrics,
                    workItems: workItems.map(wi => ({
                        id: wi.id,
                        type: wi.fields["System.WorkItemType"],
                        title: wi.fields["System.Title"],
                        state: wi.fields["System.State"],
                        assignedTo: wi.fields["System.AssignedTo"]?.displayName || "Unassigned"
                    })),
                    latestBuild: latestBuild ? {
                        id: latestBuild.id,
                        number: latestBuild.buildNumber,
                        status: latestBuild.status,
                        result: latestBuild.result,
                        url: latestBuild._links.web.href
                    } : null,
                    reviewStatus: {
                        activeComments: activeComments.length,
                        resolvedComments: resolvedComments.length,
                        isApproved: pr.reviewers.every(r => r.vote === 10), // 10 = Approved
                        reviewers: pr.reviewers.map(r => ({
                            displayName: r.displayName,
                            vote: r.vote
                        }))
                    }
                });
            }
            
            dashboard.repositories.push(repoDashboard);
        }
        
        return dashboard;
    } catch (error) {
        console.error(`Error: ${error.message}`);
        throw error;
    }
}

// Usage
const dashboard = await generatePullRequestDashboard("YourProject");

console.log(`Pull Request Dashboard for ${dashboard.projectName}`);
console.log(`Generated at: ${dashboard.generatedAt.toLocaleString()}`);

dashboard.repositories.forEach(repo => {
    console.log(`\nRepository: ${repo.repositoryName}`);
    console.log(`Active Pull Requests: ${repo.pullRequests.length}`);
    
    repo.pullRequests.forEach(pr => {
        console.log(`\n  PR #${pr.pullRequestId}: ${pr.title}`);
        console.log(`  Created by: ${pr.createdBy}`);
        console.log(`  Age: ${pr.metrics.daysOpen} days`);
        console.log(`  Changes: ${pr.metrics.fileCount} files, ${pr.metrics.commitCount} commits`);
        console.log(`  Work Items: ${pr.workItems.length}`);
        pr.workItems.forEach(wi => {
            console.log(`    - #${wi.id}: ${wi.title} (${wi.state})`);
        });
        if (pr.latestBuild) {
            console.log(`  Latest Build: ${pr.latestBuild.number} (${pr.latestBuild.result})`);
        }
        console.log(`  Review Status: ${pr.reviewStatus.activeComments} active comments, ${pr.reviewStatus.resolvedComments} resolved`);
    });
});
```

### 3. Release Readiness Report

Generate a comprehensive report that assesses whether a set of features is ready for release by analyzing work items, code coverage from builds, and test results.

#### Example: Generate Release Readiness Report

```typescript
import * as azdev from "azure-devops-node-api";

async function generateReleaseReadinessReport(releaseWorkItemId: number) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const witApi = await connection.getWorkItemTrackingApi();
    const buildApi = await connection.getBuildApi();
    const testApi = await connection.getTestApi();
    const gitApi = await connection.getGitApi();
    
    try {
        // 1. Get the release work item and its child features
        const releaseWorkItem = await witApi.getWorkItem(releaseWorkItemId, undefined, undefined, 1); // 1 = WorkItemExpand.Relations
        
        // Get child features
        const childLinks = releaseWorkItem.relations.filter(r => r.rel === "System.LinkTypes.Hierarchy-Forward");
        const featureIds = childLinks.map(link => parseInt(link.url.split('/').pop()));
        const features = await witApi.getWorkItems(featureIds);
        
        const report = {
            releaseId: releaseWorkItem.id,
            releaseName: releaseWorkItem.fields["System.Title"],
            targetDate: releaseWorkItem.fields["Microsoft.VSTS.Scheduling.TargetDate"],
            features: [],
            overallStatus: {
                totalFeatures: features.length,
                completedFeatures: 0,
                totalTests: 0,
                passedTests: 0,
                codeCoverage: 0,
                buildStatus: "Unknown"
            }
        };
        
        // 2. Process each feature
        for (const feature of features) {
            console.log(`Processing feature #${feature.id}: ${feature.fields["System.Title"]}`);
            
            // Get associated work items (tasks, bugs)
            const workItemLinks = await witApi.getWorkItemRelations(feature.id);
            const childWorkItemIds = workItemLinks
                .filter(r => r.rel === "System.LinkTypes.Hierarchy-Forward")
                .map(r => parseInt(r.target.id));
            const childWorkItems = await witApi.getWorkItems(childWorkItemIds);
            
            // Get associated builds
            const builds = await buildApi.getBuildsForWorkItem(projectName, feature.id);
            const latestBuild = builds.length > 0 ? await buildApi.getBuild(builds[0].id, projectName) : null;
            
            // Get test results if there's a latest build
            let testResults = [];
            let codeCoverage = null;
            
            if (latestBuild) {
                testResults = await testApi.getTestResults(projectName, latestBuild.id);
                const coverageData = await buildApi.getBuildCodeCoverage(
                    projectName,
                    latestBuild.id,
                    undefined, // coverageId
                    undefined  // flags
                );
                
                if (coverageData && coverageData.length > 0) {
                    codeCoverage = coverageData[0].coverageStats;
                }
            }
            
            // Get associated pull requests
            const pullRequests = [];
            const repositories = await gitApi.getRepositories(projectName);
            
            for (const repo of repositories) {
                const prs = await gitApi.getPullRequestsByWorkItemId(
                    repo.id,
                    feature.id,
                    projectName
                );
                pullRequests.push(...prs);
            }
            
            // Calculate feature metrics
            const metrics = {
                totalWorkItems: childWorkItems.length,
                completedWorkItems: childWorkItems.filter(wi => wi.fields["System.State"] === "Closed").length,
                activeWorkItems: childWorkItems.filter(wi => wi.fields["System.State"] === "Active").length,
                bugs: childWorkItems.filter(wi => wi.fields["System.WorkItemType"] === "Bug").length,
                testsPassed: testResults.filter(tr => tr.outcome === "Passed").length,
                testsFailed: testResults.filter(tr => tr.outcome === "Failed").length,
                testsBlocked: testResults.filter(tr => tr.outcome === "Blocked").length,
                codeCoverage: codeCoverage ? 
                    (codeCoverage.find(s => s.label === "Line")?.covered || 0) / 
                    (codeCoverage.find(s => s.label === "Line")?.total || 1) * 100 : 
                    0
            };
            
            // Add feature to report
            report.features.push({
                id: feature.id,
                title: feature.fields["System.Title"],
                state: feature.fields["System.State"],
                assignedTo: feature.fields["System.AssignedTo"]?.displayName || "Unassigned",
                metrics,
                workItems: childWorkItems.map(wi => ({
                    id: wi.id,
                    type: wi.fields["System.WorkItemType"],
                    title: wi.fields["System.Title"],
                    state: wi.fields["System.State"],
                    assignedTo: wi.fields["System.AssignedTo"]?.displayName || "Unassigned"
                })),
                latestBuild: latestBuild ? {
                    id: latestBuild.id,
                    number: latestBuild.buildNumber,
                    status: latestBuild.status,
                    result: latestBuild.result,
                    url: latestBuild._links.web.href
                } : null,
                pullRequests: pullRequests.map(pr => ({
                    id: pr.pullRequestId,
                    title: pr.title,
                    status: pr.status,
                    isCompleted: pr.status === 3, // 3 = Completed
                    url: pr._links.web.href
                }))
            });
            
            // Update overall metrics
            if (feature.fields["System.State"] === "Closed") {
                report.overallStatus.completedFeatures++;
            }
            report.overallStatus.totalTests += metrics.testsPassed + metrics.testsFailed + metrics.testsBlocked;
            report.overallStatus.passedTests += metrics.testsPassed;
            report.overallStatus.codeCoverage += metrics.codeCoverage;
        }
        
        // Calculate final metrics
        report.overallStatus.codeCoverage = report.overallStatus.codeCoverage / report.features.length;
        report.overallStatus.buildStatus = report.features.every(f => 
            f.latestBuild?.result === 2 // 2 = Succeeded
        ) ? "Succeeded" : "Failed";
        
        return report;
    } catch (error) {
        console.error(`Error: ${error.message}`);
        throw error;
    }
}

// Usage
const report = await generateReleaseReadinessReport(123);

console.log(`Release Readiness Report for ${report.releaseName}`);
console.log(`Target Date: ${new Date(report.targetDate).toLocaleDateString()}`);
console.log("\nOverall Status:");
console.log(`Features: ${report.overallStatus.completedFeatures}/${report.overallStatus.totalFeatures} completed`);
console.log(`Tests: ${report.overallStatus.passedTests}/${report.overallStatus.totalTests} passed`);
console.log(`Code Coverage: ${report.overallStatus.codeCoverage.toFixed(2)}%`);
console.log(`Build Status: ${report.overallStatus.buildStatus}`);

console.log("\nFeature Details:");
report.features.forEach(feature => {
    console.log(`\n${feature.title} (#${feature.id})`);
    console.log(`State: ${feature.state}`);
    console.log(`Assigned To: ${feature.assignedTo}`);
    console.log(`Work Items: ${feature.metrics.completedWorkItems}/${feature.metrics.totalWorkItems} completed`);
    console.log(`Tests: ${feature.metrics.testsPassed} passed, ${feature.metrics.testsFailed} failed`);
    console.log(`Code Coverage: ${feature.metrics.codeCoverage.toFixed(2)}%`);
    if (feature.latestBuild) {
        console.log(`Latest Build: ${feature.latestBuild.number} (${feature.latestBuild.result})`);
    }
    console.log(`Pull Requests: ${feature.pullRequests.filter(pr => pr.isCompleted).length}/${feature.pullRequests.length} completed`);
});
```

## Best Practices for Cross-API Integration

1. **Error Handling and Rollback**: Implement comprehensive error handling and rollback mechanisms for operations that span multiple APIs.

2. **Performance Optimization**: Use batch operations where possible and minimize API calls by caching data that will be reused.

3. **Rate Limiting**: Be aware of API rate limits and implement appropriate throttling when making many calls across different APIs.

4. **Transactional Integrity**: Design operations to maintain data consistency across different APIs, even in case of partial failures.

5. **Logging and Monitoring**: Implement detailed logging to track operations across APIs and monitor for issues.

6. **Caching**: Cache frequently accessed data to reduce API calls and improve performance.

7. **Asynchronous Processing**: Use asynchronous processing for long-running operations that span multiple APIs.

## Common Challenges and Solutions

| Challenge | Solution |
|-----------|----------|
| Maintaining consistency across APIs | Implement compensating transactions and rollback mechanisms |
| Complex error handling | Use a centralized error handling system with specific handlers for each API |
| Performance bottlenecks | Implement caching, batching, and parallel processing where appropriate |
| Rate limiting across multiple APIs | Implement a unified rate limiting strategy that considers all APIs |
| Data synchronization | Use event-based architectures to maintain consistency |

## See Also

- [Work Item + Git Integration](./work-item-git-integration.md)
- [Work Item + Build Integration](./work-item-build-integration.md)
- [Git + Build Integration](./git-build-integration.md)
- [API Priority Matrix](../priority-matrix/api-priority-matrix.md) 