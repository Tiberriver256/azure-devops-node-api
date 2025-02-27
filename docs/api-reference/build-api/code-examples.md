# Build API Code Examples

This document provides practical examples of using the Azure DevOps Build API for common scenarios. Each example demonstrates how to use multiple API methods together to accomplish specific tasks.

## Setup

All examples assume you have set up a connection to Azure DevOps:

```typescript
import * as azdev from "azure-devops-node-api";
import * as BuildInterfaces from "azure-devops-node-api/interfaces/BuildInterfaces";

// Connection setup
const orgUrl = "https://dev.azure.com/yourorganization";
// Security Note: In production, always store tokens in environment variables
// or a secure secret management solution, not in your code.
const token = process.env.AZURE_DEVOPS_PAT || "your-personal-access-token";

// Create authentication handler using Personal Access Token
const authHandler = azdev.getPersonalAccessTokenHandler(token);
// Create WebApi instance with organization URL and auth handler
const connection = new azdev.WebApi(orgUrl, authHandler);

// Get Build API client
const buildApi = await connection.getBuildApi();
const projectName = "YourProject";
```

## Example 1: Build Dashboard

The following example demonstrates how to create a dashboard showing recent builds across all definitions:

```typescript
/**
 * Creates a dashboard showing recent builds across all definitions
 * @returns An array of dashboard items with build information
 */
async function createBuildDashboard() {
    try {
        // 1. Get all build definitions
        // This retrieves all build pipeline definitions in the project
        const definitions = await buildApi.getDefinitions(
            projectName,
            undefined, // name
            undefined, // repositoryId
            undefined, // repositoryType
            undefined, // queryOrder
            100        // top - limit to 100 results
        );
        
        console.log(`Found ${definitions.length} build definitions`);
        
        // 2. Get recent builds (last 7 days)
        // Calculate date from 7 days ago to filter builds
        const oneWeekAgo = new Date();
        oneWeekAgo.setDate(oneWeekAgo.getDate() - 7);
        
        const builds = await buildApi.getBuilds(
            projectName,
            undefined, // definitions
            undefined, // queues
            undefined, // buildNumber
            oneWeekAgo, // minTime
            undefined, // maxTime
            undefined, // requestedFor
            undefined, // reasonFilter
            undefined, // statusFilter
            undefined, // resultFilter
            undefined, // tagFilters
            undefined, // properties
            100,       // top
            undefined, // continuationToken
            undefined, // maxBuildsPerDefinition
            undefined, // deletedFilter
            BuildInterfaces.BuildQueryOrder.FinishTimeDescending // queryOrder
        );
        
        console.log(`Found ${builds.length} builds in the last 7 days`);
        
        // 3. Group builds by definition
        // Create a map of definition ID to array of builds
        const buildsByDefinition = {};
        
        builds.forEach(build => {
            const definitionId = build.definition.id;
            if (!buildsByDefinition[definitionId]) {
                buildsByDefinition[definitionId] = [];
            }
            buildsByDefinition[definitionId].push(build);
        });
        
        // 4. Create dashboard data
        // Transform the raw data into a more usable dashboard format
        const dashboard = definitions.map(def => {
            const definitionBuilds = buildsByDefinition[def.id] || [];
            const successCount = definitionBuilds.filter(b => b.result === BuildInterfaces.BuildResult.Succeeded).length;
            const partialCount = definitionBuilds.filter(b => b.result === BuildInterfaces.BuildResult.PartiallySucceeded).length;
            const failedCount = definitionBuilds.filter(b => b.result === BuildInterfaces.BuildResult.Failed).length;
            const canceledCount = definitionBuilds.filter(b => b.result === BuildInterfaces.BuildResult.Canceled).length;
            
            const latestBuild = definitionBuilds.length > 0 ? definitionBuilds[0] : null;
            
            return {
                definitionId: def.id,
                definitionName: def.name,
                path: def.path,
                totalBuilds: definitionBuilds.length,
                successRate: definitionBuilds.length > 0 ? (successCount / definitionBuilds.length) * 100 : 0,
                buildCounts: {
                    succeeded: successCount,
                    partiallySucceeded: partialCount,
                    failed: failedCount,
                    canceled: canceledCount
                },
                latestBuild: latestBuild ? {
                    id: latestBuild.id,
                    number: latestBuild.buildNumber,
                    status: getBuildStatusText(latestBuild.status),
                    result: getBuildResultText(latestBuild.result),
                    startTime: latestBuild.startTime,
                    finishTime: latestBuild.finishTime,
                    duration: latestBuild.finishTime && latestBuild.startTime ? 
                        (new Date(latestBuild.finishTime).getTime() - new Date(latestBuild.startTime).getTime()) / 1000 : 0,
                    requestedBy: latestBuild.requestedBy ? latestBuild.requestedBy.displayName : 'Unknown',
                    sourceBranch: latestBuild.sourceBranch
                } : null
            };
        });
        
        // Sort by success rate (descending)
        dashboard.sort((a, b) => b.successRate - a.successRate);
        
        return dashboard;
    } catch (error) {
        // Handle specific error types
        if (error.statusCode === 404) {
            console.error(`Project '${projectName}' not found. Check the project name.`);
        } else if (error.statusCode === 401) {
            console.error("Authentication error. Check your credentials or token.");
        } else {
            console.error(`Error creating build dashboard: ${error.message}`);
        }
        throw error;
    }
}

/**
 * Converts a build status enum value to a readable text string
 * @param status The numeric status value from the BuildStatus enum
 * @returns A human-readable status string
 */
function getBuildStatusText(status: number): string {
    const statusMap = {
        0: "None",
        1: "In Progress",
        2: "Completed",
        4: "Cancelling",
        8: "Postponed",
        32: "Not Started"
    };
    return statusMap[status] || `Unknown (${status})`;
}

/**
 * Converts a build result enum value to a readable text string
 * @param result The numeric result value from the BuildResult enum
 * @returns A human-readable result string
 */
function getBuildResultText(result: number): string {
    const resultMap = {
        0: "None",
        2: "Succeeded",
        4: "Partially Succeeded",
        8: "Failed",
        32: "Canceled"
    };
    return resultMap[result] || `Unknown (${result})`;
}

// Usage example
(async () => {
    try {
        const dashboard = await createBuildDashboard();
        console.log(`Dashboard created with ${dashboard.length} entries`);
        
        // Display some dashboard information
        dashboard.slice(0, 3).forEach(item => {
            console.log(`${item.definitionName}: ${item.successRate.toFixed(1)}% success rate (${item.totalBuilds} builds)`);
            if (item.latestBuild) {
                console.log(`  Latest build: ${item.latestBuild.result} (${item.latestBuild.duration.toFixed(0)}s)`);
            }
        });
    } catch (error) {
        console.error("Error in main process:", error.message);
    }
})();
```

## Example 2: Queue Builds for Multiple Branches

Queue builds for multiple branches using the same build definition:

```typescript
async function queueBuildsForBranches(definitionId: number, branchNames: string[]) {
    try {
        // Get the build definition to verify it exists
        const definition = await buildApi.getDefinition(projectName, definitionId);
        console.log(`Found build definition: ${definition.name} (ID: ${definitionId})`);
        
        // Queue a build for each branch
        const queuedBuilds = [];
        
        for (const branchName of branchNames) {
            // Create build object
            const build: BuildInterfaces.Build = {
                definition: {
                    id: definitionId
                },
                sourceBranch: branchName,
                reason: BuildInterfaces.BuildReason.Manual,
                priority: BuildInterfaces.BuildPriority.Normal
            };
            
            // Queue the build
            console.log(`Queuing build for branch: ${branchName}`);
            const queuedBuild = await buildApi.queueBuild(build, projectName);
            
            console.log(`Build queued: ${queuedBuild.buildNumber} (ID: ${queuedBuild.id})`);
            queuedBuilds.push(queuedBuild);
        }
        
        return queuedBuilds;
    } catch (error) {
        console.error(`Error queuing builds: ${error.message}`);
        throw error;
    }
}

// Example usage
const branches = ['refs/heads/main', 'refs/heads/develop', 'refs/heads/feature/new-feature'];
const queuedBuilds = await queueBuildsForBranches(123, branches);
```

## Example 3: Build Logs Analysis

Analyze build logs to identify common errors or warnings:

```typescript
async function analyzeBuildLogs(buildId: number, searchPatterns: string[]) {
    try {
        // Get the build
        const build = await buildApi.getBuild(projectName, buildId);
        console.log(`Analyzing logs for build ${build.buildNumber} (ID: ${buildId})`);
        
        // Get all logs for the build
        const logs = await buildApi.getBuildLogs(projectName, buildId);
        console.log(`Found ${logs.length} log files`);
        
        // Process each log file
        const results = [];
        
        for (const log of logs) {
            if (log.id === undefined) continue;
            
            // Get the log content
            const logLines = await buildApi.getBuildLogLines(projectName, buildId, log.id);
            console.log(`Processing log ${log.id} with ${logLines.length} lines`);
            
            // Search for patterns in the log
            const matches = {};
            
            for (const pattern of searchPatterns) {
                matches[pattern] = [];
                
                for (let i = 0; i < logLines.length; i++) {
                    const line = logLines[i];
                    if (line.includes(pattern)) {
                        // Get some context (3 lines before and after)
                        const startLine = Math.max(0, i - 3);
                        const endLine = Math.min(logLines.length - 1, i + 3);
                        const context = logLines.slice(startLine, endLine + 1);
                        
                        matches[pattern].push({
                            lineNumber: i + 1,
                            line,
                            context
                        });
                    }
                }
            }
            
            results.push({
                logId: log.id,
                logType: log.type,
                matches
            });
        }
        
        // Summarize results
        const summary = {
            buildId,
            buildNumber: build.buildNumber,
            totalLogs: logs.length,
            matchCounts: {}
        };
        
        for (const pattern of searchPatterns) {
            summary.matchCounts[pattern] = results.reduce((count, log) => {
                return count + (log.matches[pattern] ? log.matches[pattern].length : 0);
            }, 0);
        }
        
        return {
            summary,
            detailedResults: results
        };
    } catch (error) {
        console.error(`Error analyzing build logs: ${error.message}`);
        throw error;
    }
}

// Example usage
const searchPatterns = ['error', 'warning', 'failed', 'exception'];
const analysis = await analyzeBuildLogs(456, searchPatterns);
console.log('Analysis summary:', analysis.summary);
```

## Example 4: Build Artifacts Management

Download and process build artifacts:

```typescript
import * as fs from 'fs';
import * as path from 'path';
import * as stream from 'stream';
import { promisify } from 'util';

const pipeline = promisify(stream.pipeline);

async function downloadBuildArtifacts(buildId: number, downloadPath: string) {
    try {
        // Get the build
        const build = await buildApi.getBuild(projectName, buildId);
        console.log(`Processing artifacts for build ${build.buildNumber} (ID: ${buildId})`);
        
        // Get all artifacts for the build
        const artifacts = await buildApi.getArtifacts(projectName, buildId);
        console.log(`Found ${artifacts.length} artifacts`);
        
        // Create download directory if it doesn't exist
        if (!fs.existsSync(downloadPath)) {
            fs.mkdirSync(downloadPath, { recursive: true });
        }
        
        // Download each artifact
        for (const artifact of artifacts) {
            console.log(`Downloading artifact: ${artifact.name}`);
            
            // Create artifact directory
            const artifactPath = path.join(downloadPath, artifact.name);
            if (!fs.existsSync(artifactPath)) {
                fs.mkdirSync(artifactPath, { recursive: true });
            }
            
            // Download the artifact content as a zip
            const artifactStream = await buildApi.getArtifactContentZip(projectName, buildId, artifact.name);
            
            // Save the zip file
            const zipPath = path.join(artifactPath, `${artifact.name}.zip`);
            const fileStream = fs.createWriteStream(zipPath);
            
            await pipeline(artifactStream, fileStream);
            console.log(`Artifact downloaded to: ${zipPath}`);
        }
        
        return {
            buildId,
            buildNumber: build.buildNumber,
            artifactsCount: artifacts.length,
            downloadPath
        };
    } catch (error) {
        console.error(`Error downloading build artifacts: ${error.message}`);
        throw error;
    }
}

// Example usage
const downloadPath = './build-artifacts';
const result = await downloadBuildArtifacts(789, downloadPath);
console.log('Download result:', result);
```

## Example 5: Build Definition Management

Create a new build definition based on an existing one:

```typescript
async function cloneBuildDefinition(sourceDefinitionId: number, newName: string, newPath: string, branchName: string) {
    try {
        // Get the source build definition
        const sourceDefinition = await buildApi.getDefinition(projectName, sourceDefinitionId, undefined, undefined, true);
        console.log(`Found source definition: ${sourceDefinition.name} (ID: ${sourceDefinitionId})`);
        
        // Create a new definition based on the source
        const newDefinition: BuildInterfaces.BuildDefinition = {
            ...sourceDefinition,
            id: undefined, // Clear ID so a new one is assigned
            name: newName,
            path: newPath,
            revision: undefined, // Clear revision
            createdDate: undefined, // Clear created date
            
            // Update repository branch if specified
            repository: sourceDefinition.repository ? {
                ...sourceDefinition.repository,
                defaultBranch: branchName || sourceDefinition.repository.defaultBranch
            } : undefined
        };
        
        // Create the new definition
        const createdDefinition = await buildApi.createDefinition(newDefinition, projectName);
        
        console.log(`Created new definition: ${createdDefinition.name} (ID: ${createdDefinition.id})`);
        console.log(`Path: ${createdDefinition.path}`);
        console.log(`Repository: ${createdDefinition.repository?.name}`);
        console.log(`Branch: ${createdDefinition.repository?.defaultBranch}`);
        
        return createdDefinition;
    } catch (error) {
        console.error(`Error cloning build definition: ${error.message}`);
        throw error;
    }
}

// Example usage
const newDefinition = await cloneBuildDefinition(
    123, // Source definition ID
    'Clone of CI Build', // New name
    '\\Build Definitions\\Clones', // New path
    'refs/heads/develop' // Branch name
);
```

## Example 6: Build Retention Management

Manage build retention by keeping important builds and deleting old ones:

```typescript
async function manageBuildRetention(definitionId: number, daysToKeep: number, maxBuildsToKeep: number) {
    try {
        // Get all builds for the definition
        const cutoffDate = new Date();
        cutoffDate.setDate(cutoffDate.getDate() - daysToKeep);
        
        const builds = await buildApi.getBuilds(
            projectName,
            [definitionId], // definitions
            undefined, // queues
            undefined, // buildNumber
            undefined, // minTime
            undefined, // maxTime
            undefined, // requestedFor
            undefined, // reasonFilter
            undefined, // statusFilter
            undefined, // resultFilter
            undefined, // tagFilters
            undefined, // properties
            1000,      // top
            undefined, // continuationToken
            undefined, // maxBuildsPerDefinition
            undefined, // deletedFilter
            BuildInterfaces.BuildQueryOrder.FinishTimeDescending // queryOrder
        );
        
        console.log(`Found ${builds.length} builds for definition ${definitionId}`);
        
        // Separate builds into those to keep and those to delete
        const buildsToKeep = [];
        const buildsToDelete = [];
        
        builds.forEach(build => {
            // Always keep builds marked as "keep forever"
            if (build.keepForever) {
                buildsToKeep.push(build);
                return;
            }
            
            // Keep successful builds within the retention period
            if (build.finishTime && new Date(build.finishTime) >= cutoffDate && 
                build.result === BuildInterfaces.BuildResult.Succeeded) {
                buildsToKeep.push(build);
                return;
            }
            
            // Delete old builds
            buildsToDelete.push(build);
        });
        
        // If we have more builds to keep than the maximum, mark the excess for deletion
        if (buildsToKeep.length > maxBuildsToKeep) {
            // Sort by finish time (oldest first)
            buildsToKeep.sort((a, b) => {
                const aTime = a.finishTime ? new Date(a.finishTime).getTime() : 0;
                const bTime = b.finishTime ? new Date(b.finishTime).getTime() : 0;
                return aTime - bTime;
            });
            
            // Move excess builds to the delete list
            const excessBuilds = buildsToKeep.splice(0, buildsToKeep.length - maxBuildsToKeep);
            buildsToDelete.push(...excessBuilds);
        }
        
        console.log(`Keeping ${buildsToKeep.length} builds`);
        console.log(`Deleting ${buildsToDelete.length} builds`);
        
        // Delete builds
        for (const build of buildsToDelete) {
            console.log(`Deleting build ${build.buildNumber} (ID: ${build.id})`);
            await buildApi.deleteBuild(projectName, build.id);
        }
        
        return {
            definitionId,
            totalBuilds: builds.length,
            keptBuilds: buildsToKeep.length,
            deletedBuilds: buildsToDelete.length
        };
    } catch (error) {
        console.error(`Error managing build retention: ${error.message}`);
        throw error;
    }
}

// Example usage
const result = await manageBuildRetention(
    123, // Definition ID
    30,  // Days to keep
    50   // Maximum builds to keep
);
console.log('Retention management result:', result);
```

## Example 7: Build Status Notification

Monitor build status and send notifications:

```typescript
async function monitorBuildStatus(buildId: number, pollingIntervalSeconds: number = 30, timeoutMinutes: number = 60) {
    try {
        const startTime = Date.now();
        const timeoutMs = timeoutMinutes * 60 * 1000;
        let isCompleted = false;
        let currentBuild = null;
        
        // Initial build check
        currentBuild = await buildApi.getBuild(projectName, buildId);
        console.log(`Monitoring build ${currentBuild.buildNumber} (ID: ${buildId})`);
        console.log(`Initial status: ${getBuildStatusText(currentBuild.status)}`);
        
        if (currentBuild.status === BuildInterfaces.BuildStatus.Completed) {
            isCompleted = true;
            console.log(`Build is already completed with result: ${getBuildResultText(currentBuild.result)}`);
        }
        
        // Poll for status changes
        while (!isCompleted && (Date.now() - startTime) < timeoutMs) {
            // Wait for the polling interval
            await new Promise(resolve => setTimeout(resolve, pollingIntervalSeconds * 1000));
            
            // Get the latest build status
            const previousStatus = currentBuild.status;
            currentBuild = await buildApi.getBuild(projectName, buildId);
            
            // Check if status has changed
            if (currentBuild.status !== previousStatus) {
                console.log(`Status changed: ${getBuildStatusText(previousStatus)} -> ${getBuildStatusText(currentBuild.status)}`);
                
                // Send notification about status change
                await sendStatusChangeNotification(currentBuild);
            }
            
            // Check if build is completed
            if (currentBuild.status === BuildInterfaces.BuildStatus.Completed) {
                isCompleted = true;
                console.log(`Build completed with result: ${getBuildResultText(currentBuild.result)}`);
                
                // Send notification about build completion
                await sendCompletionNotification(currentBuild);
                
                // Get build logs if the build failed
                if (currentBuild.result === BuildInterfaces.BuildResult.Failed) {
                    const logs = await buildApi.getBuildLogs(projectName, buildId);
                    console.log(`Retrieved ${logs.length} log files for failed build`);
                    
                    // Process logs for error information
                    // This is a placeholder for your notification logic
                    console.log(`Sending detailed error information from logs`);
                }
            }
        }
        
        // Check for timeout
        if (!isCompleted) {
            console.log(`Monitoring timed out after ${timeoutMinutes} minutes`);
            return {
                buildId,
                buildNumber: currentBuild.buildNumber,
                status: getBuildStatusText(currentBuild.status),
                result: 'Monitoring timed out',
                timedOut: true
            };
        }
        
        return {
            buildId,
            buildNumber: currentBuild.buildNumber,
            status: getBuildStatusText(currentBuild.status),
            result: getBuildResultText(currentBuild.result),
            timedOut: false,
            duration: currentBuild.finishTime && currentBuild.startTime ? 
                (new Date(currentBuild.finishTime).getTime() - new Date(currentBuild.startTime).getTime()) / 1000 : 0
        };
    } catch (error) {
        console.error(`Error monitoring build status: ${error.message}`);
        throw error;
    }
}

// Placeholder notification functions - replace with your actual notification logic
async function sendStatusChangeNotification(build) {
    console.log(`NOTIFICATION: Build ${build.buildNumber} status changed to ${getBuildStatusText(build.status)}`);
    // Implement your notification logic here (e.g., email, Slack, Teams, etc.)
}

async function sendCompletionNotification(build) {
    console.log(`NOTIFICATION: Build ${build.buildNumber} completed with result ${getBuildResultText(build.result)}`);
    // Implement your notification logic here
}

// Example usage
const result = await monitorBuildStatus(
    789, // Build ID
    15,  // Poll every 15 seconds
    30   // Timeout after 30 minutes
);
console.log('Monitoring result:', result);
```

## Helper Functions

These helper functions are used throughout the examples:

```typescript
// Convert build status enum to readable text
function getBuildStatusText(status: number): string {
    const statusMap = {
        0: "None",
        1: "In Progress",
        2: "Completed",
        4: "Cancelling",
        8: "Postponed",
        32: "Not Started"
    };
    return statusMap[status] || `Unknown (${status})`;
}

// Convert build result enum to readable text
function getBuildResultText(result: number): string {
    const resultMap = {
        0: "None",
        2: "Succeeded",
        4: "Partially Succeeded",
        8: "Failed",
        32: "Canceled"
    };
    return resultMap[result] || `Unknown (${result})`;
}
``` 