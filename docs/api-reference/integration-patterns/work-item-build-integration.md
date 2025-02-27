# Work Item + Build Integration Patterns

**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > [Integration Patterns](./README.md) > Work Item + Build Integration

## Overview

Integrating the Work Item Tracking API with the Build API creates powerful workflows that connect planning and tracking with continuous integration. This integration enables traceability from requirements to builds, automated status updates, and comprehensive reporting across the development lifecycle.

## Common Integration Scenarios

### 1. Updating Work Items Based on Build Results

One of the most common integration patterns is automatically updating work items when builds complete, providing visibility into build status directly in work items.

#### Example: Update Work Item Status When Build Completes

```typescript
import * as azdev from "azure-devops-node-api";

async function updateWorkItemOnBuildCompletion(buildId: number) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const buildApi = await connection.getBuildApi();
    const witApi = await connection.getWorkItemTrackingApi();
    
    try {
        // 1. Get build details
        const build = await buildApi.getBuild(buildId, projectName);
        console.log(`Processing build ${build.id} (${build.buildNumber})`);
        
        // 2. Extract work item IDs from the build
        // This assumes work items are associated with the build through code changes
        const workItemRefs = await buildApi.getBuildWorkItemsRefs(projectName, buildId);
        
        if (!workItemRefs || workItemRefs.length === 0) {
            console.log("No work items associated with this build");
            return;
        }
        
        console.log(`Found ${workItemRefs.length} work items associated with the build`);
        
        // 3. Process each work item based on build result
        for (const workItemRef of workItemRefs) {
            // Extract work item ID from the reference URL
            const workItemId = parseInt(workItemRef.id);
            
            // Get the work item
            const workItem = await witApi.getWorkItem(workItemId);
            console.log(`Processing work item #${workItemId}: ${workItem.fields["System.Title"]}`);
            
            // Create update based on build result
            const patchDocument = [];
            
            // Add a comment about the build
            patchDocument.push({
                op: "add",
                path: "/fields/System.History",
                value: `Build ${build.buildNumber} ${getBuildResultText(build.result)} at ${new Date(build.finishTime).toLocaleString()}`
            });
            
            // If the build succeeded and work item is in "In Progress" state, move it to "Ready for Test"
            if (build.result === 2 && workItem.fields["System.State"] === "In Progress") { // 2 = Succeeded
                patchDocument.push({
                    op: "add",
                    path: "/fields/System.State",
                    value: "Ready for Test"
                });
            }
            
            // If the build failed and work item is in "Ready for Test", move it back to "In Progress"
            if (build.result === 8 && workItem.fields["System.State"] === "Ready for Test") { // 8 = Failed
                patchDocument.push({
                    op: "add",
                    path: "/fields/System.State",
                    value: "In Progress"
                });
            }
            
            // Update the work item
            if (patchDocument.length > 0) {
                await witApi.updateWorkItem(
                    {}, // No custom headers
                    patchDocument,
                    workItemId,
                    projectName
                );
                console.log(`Updated work item #${workItemId}`);
            }
        }
        
        return {
            buildId,
            workItemsProcessed: workItemRefs.length,
            success: true
        };
    } catch (error) {
        console.error(`Error: ${error.message}`);
        return {
            buildId,
            success: false,
            error: error.message
        };
    }
}

// Helper function to get readable build result text
function getBuildResultText(result) {
    switch (result) {
        case 2: return "succeeded";
        case 4: return "partially succeeded";
        case 8: return "failed";
        case 32: return "was canceled";
        default: return "completed with result " + result;
    }
}

// Usage
const result = await updateWorkItemOnBuildCompletion(123);
if (result.success) {
    console.log(`Successfully processed ${result.workItemsProcessed} work items for build ${result.buildId}`);
} else {
    console.error(`Failed to process work items: ${result.error}`);
}
```

### 2. Queuing Builds from Work Item Events

Automatically trigger builds when work items reach specific states, enabling continuous integration directly from the planning process.

#### Example: Queue a Build When a Work Item Reaches "Ready for Build" State

```typescript
import * as azdev from "azure-devops-node-api";

async function queueBuildFromWorkItem(workItemId: number) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const witApi = await connection.getWorkItemTrackingApi();
    const buildApi = await connection.getBuildApi();
    
    try {
        // 1. Get work item details
        const workItem = await witApi.getWorkItem(workItemId);
        console.log(`Processing work item #${workItemId}: ${workItem.fields["System.Title"]}`);
        
        // 2. Check if the work item is in the correct state
        if (workItem.fields["System.State"] !== "Ready for Build") {
            console.log(`Work item #${workItemId} is not in 'Ready for Build' state. Current state: ${workItem.fields["System.State"]}`);
            return {
                workItemId,
                success: false,
                reason: "Work item not in 'Ready for Build' state"
            };
        }
        
        // 3. Get the associated branch information
        // This assumes you have a custom field that stores the branch name
        // Alternatively, you could query Git commits that reference this work item
        const branchName = workItem.fields["Custom.BranchName"] || "main";
        
        // 4. Get the build definition to use
        // This could be a fixed definition or determined based on work item properties
        const buildDefinitions = await buildApi.getDefinitions(
            projectName,
            "MyBuildDefinition" // Definition name filter
        );
        
        if (!buildDefinitions || buildDefinitions.length === 0) {
            throw new Error("Build definition not found");
        }
        
        const buildDefinitionId = buildDefinitions[0].id;
        
        // 5. Queue the build
        const build = {
            definition: {
                id: buildDefinitionId
            },
            project: {
                id: projectName
            },
            sourceBranch: `refs/heads/${branchName}`,
            reason: 1, // Manual
            parameters: JSON.stringify({
                workItemId: workItemId.toString(),
                workItemTitle: workItem.fields["System.Title"]
            })
        };
        
        const queuedBuild = await buildApi.queueBuild(build, projectName);
        
        console.log(`Successfully queued build ${queuedBuild.id} for work item #${workItemId}`);
        
        // 6. Update the work item with the build information
        const patchDocument = [
            {
                op: "add",
                path: "/fields/System.History",
                value: `Build ${queuedBuild.buildNumber} queued at ${new Date().toLocaleString()}. [View build](${queuedBuild._links.web.href})`
            },
            {
                op: "add",
                path: "/fields/System.State",
                value: "Building"
            }
        ];
        
        await witApi.updateWorkItem(
            {}, // No custom headers
            patchDocument,
            workItemId,
            projectName
        );
        
        return {
            workItemId,
            buildId: queuedBuild.id,
            buildNumber: queuedBuild.buildNumber,
            success: true
        };
    } catch (error) {
        console.error(`Error: ${error.message}`);
        return {
            workItemId,
            success: false,
            error: error.message
        };
    }
}

// Usage
const result = await queueBuildFromWorkItem(123);
if (result.success) {
    console.log(`Build ${result.buildNumber} queued successfully for work item #${result.workItemId}`);
} else {
    console.error(`Failed to queue build: ${result.error || result.reason}`);
}
```

### 3. Creating Work Item Reports Based on Build History

Generate reports that show work item progress through builds, providing insights into development velocity and quality.

#### Example: Generate a Work Item Build History Report

```typescript
import * as azdev from "azure-devops-node-api";

async function generateWorkItemBuildReport(workItemIds: number[]) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const witApi = await connection.getWorkItemTrackingApi();
    const buildApi = await connection.getBuildApi();
    
    try {
        // 1. Get work item details
        const workItems = await witApi.getWorkItems(workItemIds);
        console.log(`Retrieved ${workItems.length} work items`);
        
        const report = [];
        
        // 2. Process each work item
        for (const workItem of workItems) {
            console.log(`Processing work item #${workItem.id}: ${workItem.fields["System.Title"]}`);
            
            // 3. Find builds associated with this work item
            const associatedBuilds = await buildApi.getBuildsForWorkItem(projectName, workItem.id);
            
            const workItemReport = {
                id: workItem.id,
                title: workItem.fields["System.Title"],
                state: workItem.fields["System.State"],
                type: workItem.fields["System.WorkItemType"],
                assignedTo: workItem.fields["System.AssignedTo"]?.displayName || "Unassigned",
                buildHistory: []
            };
            
            // 4. Get details for each build
            for (const buildRef of associatedBuilds) {
                const build = await buildApi.getBuild(buildRef.id, projectName);
                
                workItemReport.buildHistory.push({
                    buildId: build.id,
                    buildNumber: build.buildNumber,
                    status: build.status,
                    result: build.result,
                    startTime: build.startTime,
                    finishTime: build.finishTime,
                    duration: build.finishTime ? 
                        (new Date(build.finishTime).getTime() - new Date(build.startTime).getTime()) / 1000 : 
                        null,
                    sourceBranch: build.sourceBranch,
                    sourceVersion: build.sourceVersion,
                    requestedBy: build.requestedBy.displayName,
                    url: build._links.web.href
                });
            }
            
            // 5. Sort builds by date (newest first)
            workItemReport.buildHistory.sort((a, b) => 
                new Date(b.startTime).getTime() - new Date(a.startTime).getTime()
            );
            
            // 6. Calculate build statistics
            const successfulBuilds = workItemReport.buildHistory.filter(b => b.result === 2).length; // 2 = Succeeded
            const failedBuilds = workItemReport.buildHistory.filter(b => b.result === 8).length; // 8 = Failed
            const totalBuilds = workItemReport.buildHistory.length;
            
            workItemReport.buildStats = {
                totalBuilds,
                successfulBuilds,
                failedBuilds,
                successRate: totalBuilds > 0 ? (successfulBuilds / totalBuilds) * 100 : 0,
                averageDuration: totalBuilds > 0 ? 
                    workItemReport.buildHistory.reduce((sum, build) => sum + (build.duration || 0), 0) / totalBuilds : 
                    0
            };
            
            report.push(workItemReport);
        }
        
        return {
            projectName,
            generatedAt: new Date(),
            workItemCount: report.length,
            workItems: report,
            success: true
        };
    } catch (error) {
        console.error(`Error: ${error.message}`);
        return {
            projectName,
            generatedAt: new Date(),
            success: false,
            error: error.message
        };
    }
}

// Usage
const workItemIds = [123, 124, 125];
const report = await generateWorkItemBuildReport(workItemIds);

console.log(`Generated report for ${report.workItemCount} work items`);
report.workItems.forEach(wi => {
    console.log(`Work Item #${wi.id}: ${wi.title}`);
    console.log(`  State: ${wi.state}`);
    console.log(`  Build Count: ${wi.buildStats.totalBuilds}`);
    console.log(`  Success Rate: ${wi.buildStats.successRate.toFixed(2)}%`);
    console.log(`  Latest Build: ${wi.buildHistory[0]?.buildNumber || 'None'}`);
    console.log("----------------------------");
});
```

### 4. Linking Builds to Work Items

Create relationships between builds and work items to maintain traceability throughout the development lifecycle.

#### Example: Link Builds to Related Work Items

```typescript
import * as azdev from "azure-devops-node-api";

async function linkBuildsToWorkItems(buildId: number) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const buildApi = await connection.getBuildApi();
    const witApi = await connection.getWorkItemTrackingApi();
    const gitApi = await connection.getGitApi();
    
    try {
        // 1. Get build details
        const build = await buildApi.getBuild(buildId, projectName);
        console.log(`Processing build ${build.id} (${build.buildNumber})`);
        
        // 2. Get the changes included in this build
        const changes = await buildApi.getBuildChanges(projectName, buildId);
        console.log(`Found ${changes.length} changes in this build`);
        
        // 3. Extract work item IDs from commit messages
        // This assumes commit messages reference work items with #123 format
        const workItemIds = new Set<number>();
        
        for (const change of changes) {
            // Look for patterns like "#123" in commit messages
            const matches = change.message.match(/#(\d+)/g) || [];
            
            for (const match of matches) {
                const workItemId = parseInt(match.substring(1));
                if (!isNaN(workItemId)) {
                    workItemIds.add(workItemId);
                }
            }
        }
        
        console.log(`Found ${workItemIds.size} work item references in commit messages`);
        
        // 4. Link the build to each work item
        const linkedWorkItems = [];
        
        for (const workItemId of workItemIds) {
            try {
                // Verify the work item exists
                const workItem = await witApi.getWorkItem(workItemId);
                
                // Create a link between the work item and the build
                const patchDocument = [
                    {
                        op: "add",
                        path: "/relations/-",
                        value: {
                            rel: "ArtifactLink",
                            url: `vstfs:///Build/Build/${buildId}`,
                            attributes: {
                                name: "Build"
                            }
                        }
                    },
                    {
                        op: "add",
                        path: "/fields/System.History",
                        value: `Linked to build [${build.buildNumber}](${build._links.web.href}) (${getBuildResultText(build.result)})`
                    }
                ];
                
                await witApi.updateWorkItem(
                    {}, // No custom headers
                    patchDocument,
                    workItemId,
                    projectName
                );
                
                console.log(`Linked work item #${workItemId} to build ${buildId}`);
                linkedWorkItems.push({
                    id: workItemId,
                    title: workItem.fields["System.Title"],
                    success: true
                });
            } catch (error) {
                console.error(`Error linking work item #${workItemId}: ${error.message}`);
                linkedWorkItems.push({
                    id: workItemId,
                    success: false,
                    error: error.message
                });
            }
        }
        
        return {
            buildId,
            buildNumber: build.buildNumber,
            workItemsFound: workItemIds.size,
            linkedWorkItems,
            success: true
        };
    } catch (error) {
        console.error(`Error: ${error.message}`);
        return {
            buildId,
            buildNumber: build?.buildNumber,
            success: false,
            error: error.message
        };
    }
}

// Helper function to get readable build result text
function getBuildResultText(result) {
    switch (result) {
        case 2: return "succeeded";
        case 4: return "partially succeeded";
        case 8: return "failed";
        case 32: return "was canceled";
        default: return "completed with result " + result;
    }
}

// Usage
const result = await linkBuildsToWorkItems(123);
console.log(`Processed build ${result.buildNumber}`);
console.log(`Found ${result.workItemsFound} work items referenced in commits`);
console.log(`Successfully linked ${result.linkedWorkItems.filter(wi => wi.success).length} work items`);
```

### 5. Creating Build Dashboards by Work Item Type

Generate dashboards that show build performance metrics organized by work item type, providing insights into how different types of work affect build quality.

#### Example: Generate a Build Dashboard by Work Item Type

```typescript
import * as azdev from "azure-devops-node-api";

async function generateBuildDashboardByWorkItemType(projectName: string, days: number = 30) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const buildApi = await connection.getBuildApi();
    const witApi = await connection.getWorkItemTrackingApi();
    
    try {
        // 1. Set time range for builds
        const endDate = new Date();
        const startDate = new Date();
        startDate.setDate(startDate.getDate() - days);
        
        // 2. Get builds from the specified time range
        const builds = await buildApi.getBuilds(
            projectName,
            undefined, // definitions
            undefined, // buildNumber
            startDate,
            endDate,
            undefined, // requestedFor
            undefined, // status
            undefined, // result
            undefined, // tagFilters
            undefined, // properties
            100, // top
            undefined, // continuationToken
            undefined, // maxBuildsPerDefinition
            undefined, // deletedFilter
            undefined, // queryOrder
            undefined, // branchName
            undefined, // buildIds
            undefined // repositoryId
        );
        
        console.log(`Found ${builds.length} builds in the last ${days} days`);
        
        // 3. Initialize dashboard data structure
        const dashboard = {
            projectName,
            timeRange: {
                start: startDate,
                end: endDate,
                days
            },
            totalBuilds: builds.length,
            workItemTypes: {},
            buildsWithoutWorkItems: 0
        };
        
        // 4. Process each build
        for (const build of builds) {
            console.log(`Processing build ${build.id} (${build.buildNumber})`);
            
            // 5. Get work items associated with this build
            const workItemRefs = await buildApi.getBuildWorkItemsRefs(projectName, build.id);
            
            if (!workItemRefs || workItemRefs.length === 0) {
                dashboard.buildsWithoutWorkItems++;
                continue;
            }
            
            // 6. Get work item details
            const workItemIds = workItemRefs.map(ref => parseInt(ref.id));
            const workItems = await witApi.getWorkItems(workItemIds);
            
            // 7. Group work items by type
            for (const workItem of workItems) {
                const workItemType = workItem.fields["System.WorkItemType"];
                
                // Initialize work item type in dashboard if not exists
                if (!dashboard.workItemTypes[workItemType]) {
                    dashboard.workItemTypes[workItemType] = {
                        totalBuilds: 0,
                        successfulBuilds: 0,
                        failedBuilds: 0,
                        partiallySuccessfulBuilds: 0,
                        canceledBuilds: 0,
                        averageDuration: 0,
                        totalDuration: 0,
                        buildsWithDuration: 0
                    };
                }
                
                // Update statistics
                dashboard.workItemTypes[workItemType].totalBuilds++;
                
                // Update build result counters
                switch (build.result) {
                    case 2: // Succeeded
                        dashboard.workItemTypes[workItemType].successfulBuilds++;
                        break;
                    case 8: // Failed
                        dashboard.workItemTypes[workItemType].failedBuilds++;
                        break;
                    case 4: // Partially Succeeded
                        dashboard.workItemTypes[workItemType].partiallySuccessfulBuilds++;
                        break;
                    case 32: // Canceled
                        dashboard.workItemTypes[workItemType].canceledBuilds++;
                        break;
                }
                
                // Update duration statistics if available
                if (build.startTime && build.finishTime) {
                    const duration = new Date(build.finishTime).getTime() - new Date(build.startTime).getTime();
                    dashboard.workItemTypes[workItemType].totalDuration += duration;
                    dashboard.workItemTypes[workItemType].buildsWithDuration++;
                }
            }
        }
        
        // 8. Calculate averages and success rates
        for (const type in dashboard.workItemTypes) {
            const stats = dashboard.workItemTypes[type];
            
            // Calculate average duration
            if (stats.buildsWithDuration > 0) {
                stats.averageDuration = stats.totalDuration / stats.buildsWithDuration;
            }
            
            // Calculate success rate
            const completedBuilds = stats.successfulBuilds + stats.failedBuilds + stats.partiallySuccessfulBuilds;
            stats.successRate = completedBuilds > 0 ? (stats.successfulBuilds / completedBuilds) * 100 : 0;
            
            // Format durations for readability
            stats.averageDurationFormatted = formatDuration(stats.averageDuration);
        }
        
        return dashboard;
    } catch (error) {
        console.error(`Error: ${error.message}`);
        return {
            projectName,
            timeRange: {
                start: startDate,
                end: endDate,
                days
            },
            success: false,
            error: error.message
        };
    }
}

// Helper function to format duration in milliseconds to a readable string
function formatDuration(durationMs: number): string {
    const seconds = Math.floor(durationMs / 1000);
    const minutes = Math.floor(seconds / 60);
    const hours = Math.floor(minutes / 60);
    
    const remainingMinutes = minutes % 60;
    const remainingSeconds = seconds % 60;
    
    return `${hours}h ${remainingMinutes}m ${remainingSeconds}s`;
}

// Usage
const dashboard = await generateBuildDashboardByWorkItemType("YourProject", 30);

console.log(`Build Dashboard for ${dashboard.projectName} (Last ${dashboard.timeRange.days} days)`);
console.log(`Total Builds: ${dashboard.totalBuilds}`);
console.log(`Builds Without Work Items: ${dashboard.buildsWithoutWorkItems}`);
console.log("\nBuild Performance by Work Item Type:");

for (const type in dashboard.workItemTypes) {
    const stats = dashboard.workItemTypes[type];
    console.log(`\n${type}:`);
    console.log(`  Total Builds: ${stats.totalBuilds}`);
    console.log(`  Success Rate: ${stats.successRate.toFixed(2)}%`);
    console.log(`  Average Duration: ${stats.averageDurationFormatted}`);
    console.log(`  Successful: ${stats.successfulBuilds}, Failed: ${stats.failedBuilds}, Partial: ${stats.partiallySuccessfulBuilds}, Canceled: ${stats.canceledBuilds}`);
}
```

## Best Practices for Work Item + Build Integration

1. **Bidirectional Traceability**: Maintain links in both directions - work items should link to builds, and builds should reference work items.

2. **Automated Status Updates**: Implement automated work item state transitions based on build results to keep status information current.

3. **Contextual Information**: Include relevant context when updating work items, such as build numbers, links to build results, and summary information.

4. **Error Handling**: Implement robust error handling for integration scenarios, as they involve multiple API calls that could fail independently.

5. **Performance Considerations**: Be mindful of API rate limits and performance when processing large numbers of work items or builds.

6. **Idempotent Operations**: Design integrations to be idempotent to prevent duplicate updates if operations are retried.

7. **Selective Integration**: Not all work items need to be linked to builds - focus on those that represent implementable features or bugs.

## Common Challenges and Solutions

| Challenge | Solution |
|-----------|----------|
| Identifying which work items are associated with a build | Use commit message patterns (#123), branch naming conventions, or explicit linking |
| Handling work items that span multiple builds | Create aggregate build reports that show all builds associated with a work item |
| Dealing with build failures | Implement notification systems and automatic work item updates to alert team members |
| Managing large volumes of work items and builds | Implement filtering and pagination to process items in manageable batches |
| Maintaining consistency across APIs | Use transactions where possible, or implement compensating actions for failures |

## See Also

- [Work Item Tracking API Reference](../work-item-tracking/README.md)
- [Build API Reference](../build-api/README.md)
- [Work Item + Git Integration](./work-item-git-integration.md)
- [Git + Build Integration](./git-build-integration.md)
- [Cross-API Examples](./cross-api-examples.md) 