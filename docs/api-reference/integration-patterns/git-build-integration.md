# Git + Build Integration Patterns

**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > [Integration Patterns](./README.md) > Git + Build Integration

## Overview

Integrating the Git API with the Build API creates powerful automation workflows that connect source code management with continuous integration and delivery processes. This integration is essential for modern DevOps practices, enabling automated builds triggered by code changes, deployment of build artifacts, and comprehensive reporting on build status in relation to code changes.

## Common Integration Scenarios

### 1. Triggering Builds from Git Events

One of the most common integration patterns is automatically triggering builds when code is pushed to specific branches or when pull requests are created or updated.

#### Example: Queue a Build When a Pull Request is Created or Updated

```typescript
import * as azdev from "azure-devops-node-api";

async function triggerBuildForPullRequest(repositoryId: string, pullRequestId: number, buildDefinitionId: number) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const gitApi = await connection.getGitApi();
    const buildApi = await connection.getBuildApi();
    
    try {
        // 1. Get pull request details
        const pullRequest = await gitApi.getPullRequestById(pullRequestId, projectName);
        console.log(`Found pull request: ${pullRequest.pullRequestId} - ${pullRequest.title}`);
        
        // 2. Get the build definition
        const buildDefinition = await buildApi.getDefinition(buildDefinitionId, projectName);
        console.log(`Found build definition: ${buildDefinition.id} - ${buildDefinition.name}`);
        
        // 3. Queue a new build
        const build = {
            definition: {
                id: buildDefinitionId
            },
            project: {
                id: projectName
            },
            sourceVersion: pullRequest.lastMergeSourceCommit.commitId,
            sourceBranch: pullRequest.sourceRefName,
            reason: 1, // Manual
            parameters: JSON.stringify({
                pullRequestId: pullRequestId.toString(),
                repositoryId: repositoryId,
                sourceBranch: pullRequest.sourceRefName,
                targetBranch: pullRequest.targetRefName
            })
        };
        
        const queuedBuild = await buildApi.queueBuild(build, projectName);
        
        console.log(`Successfully queued build ${queuedBuild.id} for pull request ${pullRequestId}`);
        
        // 4. Add a comment to the pull request with the build link
        const comment = {
            content: `Build [${queuedBuild.buildNumber}](${queuedBuild._links.web.href}) has been queued for this pull request.`,
            commentType: 1 // Regular comment
        };
        
        await gitApi.createThread(
            {
                comments: [comment]
            },
            repositoryId,
            pullRequestId,
            projectName
        );
        
        return {
            pullRequestId,
            buildId: queuedBuild.id,
            buildNumber: queuedBuild.buildNumber,
            buildUrl: queuedBuild._links.web.href,
            success: true
        };
    } catch (error) {
        console.error(`Error: ${error.message}`);
        return {
            pullRequestId,
            success: false,
            error: error.message
        };
    }
}

// Usage
const result = await triggerBuildForPullRequest("repository-guid-here", 123, 456);
if (result.success) {
    console.log(`Build queued: ${result.buildNumber}`);
    console.log(`Build URL: ${result.buildUrl}`);
} else {
    console.error(`Failed to queue build: ${result.error}`);
}
```

### 2. Updating Pull Request Status with Build Results

Providing build status feedback directly in pull requests helps developers quickly understand if their changes are ready to be merged.

#### Example: Update Pull Request Status with Build Results

```typescript
import * as azdev from "azure-devops-node-api";

async function updatePullRequestWithBuildStatus(buildId: number) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const gitApi = await connection.getGitApi();
    const buildApi = await connection.getBuildApi();
    
    try {
        // 1. Get build details
        const build = await buildApi.getBuild(buildId, projectName);
        console.log(`Found build: ${build.id} - ${build.buildNumber}`);
        
        // 2. Extract pull request ID from build parameters
        // Assuming the build was queued with parameters including the pull request ID
        const buildParameters = JSON.parse(build.parameters || "{}");
        const pullRequestId = parseInt(buildParameters.pullRequestId);
        const repositoryId = buildParameters.repositoryId;
        
        if (!pullRequestId || !repositoryId) {
            throw new Error("Build is not associated with a pull request");
        }
        
        console.log(`Associated with pull request: ${pullRequestId}`);
        
        // 3. Get pull request details
        const pullRequest = await gitApi.getPullRequestById(pullRequestId, projectName);
        console.log(`Found pull request: ${pullRequest.pullRequestId} - ${pullRequest.title}`);
        
        // 4. Create a status for the pull request
        let state: number;
        let description: string;
        
        switch (build.result) {
            case 2: // Succeeded
                state = 2; // Succeeded
                description = "Build succeeded";
                break;
            case 8: // Failed
                state = 3; // Failed
                description = "Build failed";
                break;
            case 0: // None (in progress)
            case 1: // In Progress
                state = 1; // Pending
                description = "Build in progress";
                break;
            case 4: // Partially Succeeded
                state = 4; // Error
                description = "Build partially succeeded";
                break;
            case 32: // Canceled
                state = 5; // Not Applicable
                description = "Build was canceled";
                break;
            default:
                state = 4; // Error
                description = `Unknown build result: ${build.result}`;
        }
        
        const status = {
            state: state,
            description: description,
            targetUrl: build._links.web.href,
            context: {
                name: "build",
                genre: "continuous-integration"
            }
        };
        
        await gitApi.createPullRequestStatus(status, repositoryId, pullRequestId, projectName);
        
        // 5. Add a comment to the pull request with detailed build results
        let commentContent = `## Build ${build.buildNumber} ${description}\n\n`;
        commentContent += `**Status:** ${build.status}\n`;
        commentContent += `**Result:** ${build.result}\n`;
        commentContent += `**Started:** ${new Date(build.startTime).toLocaleString()}\n`;
        
        if (build.finishTime) {
            commentContent += `**Finished:** ${new Date(build.finishTime).toLocaleString()}\n`;
            
            // Calculate duration
            const duration = new Date(build.finishTime).getTime() - new Date(build.startTime).getTime();
            const minutes = Math.floor(duration / 60000);
            const seconds = Math.floor((duration % 60000) / 1000);
            commentContent += `**Duration:** ${minutes}m ${seconds}s\n`;
        }
        
        commentContent += `\n[View build details](${build._links.web.href})`;
        
        const comment = {
            content: commentContent,
            commentType: 1 // Regular comment
        };
        
        await gitApi.createThread(
            {
                comments: [comment]
            },
            repositoryId,
            pullRequestId,
            projectName
        );
        
        return {
            buildId,
            pullRequestId,
            status: state,
            description,
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

// Usage
const result = await updatePullRequestWithBuildStatus(789);
if (result.success) {
    console.log(`Updated pull request ${result.pullRequestId} with build status: ${result.description}`);
} else {
    console.error(`Failed to update pull request: ${result.error}`);
}
```

### 3. Creating Release Branches with Validated Builds

Automatically create release branches when builds on specific branches pass validation, ensuring only quality code is promoted to release.

#### Example: Create a Release Branch After Successful Build

```typescript
import * as azdev from "azure-devops-node-api";

async function createReleaseBranchFromSuccessfulBuild(buildId: number) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const gitApi = await connection.getGitApi();
    const buildApi = await connection.getBuildApi();
    
    try {
        // 1. Get build details
        const build = await buildApi.getBuild(buildId, projectName);
        console.log(`Found build: ${build.id} - ${build.buildNumber}`);
        
        // 2. Verify build was successful
        if (build.result !== 2) { // 2 = Succeeded
            throw new Error(`Build ${buildId} did not succeed. Current result: ${build.result}`);
        }
        
        // 3. Extract repository information from build
        const repositoryId = build.repository.id;
        const sourceBranch = build.sourceBranch.replace('refs/heads/', '');
        
        // Only create release branches from main/master branch
        if (sourceBranch !== 'main' && sourceBranch !== 'master') {
            throw new Error(`Build source branch is ${sourceBranch}, not main or master`);
        }
        
        // 4. Get the repository details
        const repository = await gitApi.getRepository(repositoryId, projectName);
        console.log(`Found repository: ${repository.name}`);
        
        // 5. Get the commit that was built
        const sourceCommitId = build.sourceVersion;
        console.log(`Source commit: ${sourceCommitId}`);
        
        // 6. Generate a release branch name
        // Format: release/YYYY.MM.DD-buildNumber
        const today = new Date();
        const dateString = `${today.getFullYear()}.${(today.getMonth() + 1).toString().padStart(2, '0')}.${today.getDate().toString().padStart(2, '0')}`;
        const branchName = `release/${dateString}-${build.buildNumber}`;
        const fullBranchName = `refs/heads/${branchName}`;
        
        // 7. Create the new branch
        const refUpdate = [{
            name: fullBranchName,
            oldObjectId: "0000000000000000000000000000000000000000", // Indicates new branch
            newObjectId: sourceCommitId
        }];
        
        const result = await gitApi.updateRefs(refUpdate, repositoryId, projectName);
        
        if (result && result.length > 0 && result[0].success) {
            console.log(`Successfully created branch: ${branchName}`);
            
            // 8. Queue a validation build on the new release branch
            const releaseBuild = {
                definition: {
                    id: build.definition.id
                },
                project: {
                    id: projectName
                },
                sourceVersion: sourceCommitId,
                sourceBranch: fullBranchName,
                reason: 1, // Manual
                parameters: JSON.stringify({
                    isReleaseBuild: "true",
                    sourceCommitId: sourceCommitId,
                    sourceBuildId: buildId.toString()
                })
            };
            
            const queuedBuild = await buildApi.queueBuild(releaseBuild, projectName);
            console.log(`Queued validation build ${queuedBuild.id} for release branch ${branchName}`);
            
            return {
                buildId,
                repositoryId,
                branchName,
                validationBuildId: queuedBuild.id,
                success: true,
                branchUrl: `${repository.webUrl}?version=GB${branchName}`
            };
        } else {
            throw new Error("Failed to create branch");
        }
    } catch (error) {
        console.error(`Error: ${error.message}`);
        return {
            buildId,
            success: false,
            error: error.message
        };
    }
}

// Usage
const result = await createReleaseBranchFromSuccessfulBuild(789);
if (result.success) {
    console.log(`Release branch created: ${result.branchName}`);
    console.log(`Branch URL: ${result.branchUrl}`);
    console.log(`Validation build queued: ${result.validationBuildId}`);
} else {
    console.error(`Failed to create release branch: ${result.error}`);
}
```

### 4. Analyzing Build Failures by Repository Changes

When a build fails, it's useful to analyze which code changes might have caused the failure.

#### Example: Analyze Repository Changes for Failed Build

```typescript
import * as azdev from "azure-devops-node-api";

async function analyzeFailedBuild(buildId: number) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const gitApi = await connection.getGitApi();
    const buildApi = await connection.getBuildApi();
    
    try {
        // 1. Get build details
        const build = await buildApi.getBuild(buildId, projectName);
        console.log(`Found build: ${build.id} - ${build.buildNumber}`);
        
        // 2. Verify build failed
        if (build.result !== 8) { // 8 = Failed
            throw new Error(`Build ${buildId} did not fail. Current result: ${build.result}`);
        }
        
        // 3. Get the previous successful build for the same definition
        const buildDefinitionId = build.definition.id;
        const previousBuilds = await buildApi.getBuilds(
            projectName,
            [buildDefinitionId],
            undefined, // Build numbers
            undefined, // Min time
            new Date(build.startTime), // Max time (before current build)
            undefined, // Request id
            undefined, // Build status
            2, // Result = succeeded
            undefined, // Tag filters
            undefined, // Properties
            undefined, // Top
            undefined, // Continuation token
            1, // Max builds per definition
            undefined, // Delete in progress
            undefined, // Query order
            undefined, // Branch name
            undefined, // Build Ids
            undefined // Repository type
        );
        
        if (!previousBuilds || previousBuilds.length === 0) {
            throw new Error("No previous successful build found for comparison");
        }
        
        const previousBuild = previousBuilds[0];
        console.log(`Found previous successful build: ${previousBuild.id} - ${previousBuild.buildNumber}`);
        
        // 4. Get repository information
        const repositoryId = build.repository.id;
        const repository = await gitApi.getRepository(repositoryId, projectName);
        
        // 5. Get commits between the two builds
        const commitsBetweenBuilds = await gitApi.getCommitsBatch(
            {
                itemVersion: { version: build.sourceVersion },
                compareVersion: { version: previousBuild.sourceVersion },
                includeWorkItems: true
            },
            repositoryId,
            projectName
        );
        
        console.log(`Found ${commitsBetweenBuilds.length} commits between builds`);
        
        // 6. Get build logs to analyze failure
        const logs = await buildApi.getBuildLogs(buildId, projectName);
        const failureLogIds = logs
            .filter(log => log.type === "Error")
            .map(log => log.id);
        
        const failureLogs = [];
        for (const logId of failureLogIds) {
            const logContent = await buildApi.getBuildLogLines(buildId, logId, projectName);
            failureLogs.push({
                id: logId,
                content: logContent
            });
        }
        
        // 7. Analyze which files were changed
        const changedFiles = [];
        for (const commit of commitsBetweenBuilds) {
            // Get the changes for this commit
            const changes = await gitApi.getChanges(commit.commitId, repositoryId, projectName);
            
            if (changes && changes.changes) {
                changes.changes.forEach(change => {
                    changedFiles.push({
                        path: change.item.path,
                        changeType: change.changeType,
                        commitId: commit.commitId,
                        commitMessage: commit.comment,
                        author: commit.author.name,
                        date: commit.author.date
                    });
                });
            }
        }
        
        // 8. Look for patterns in failure logs that might match changed files
        const potentialCulprits = [];
        
        for (const file of changedFiles) {
            // Extract filename from path
            const filename = file.path.split('/').pop();
            
            // Check if filename appears in any error logs
            for (const log of failureLogs) {
                if (log.content.some(line => line.includes(filename))) {
                    potentialCulprits.push({
                        file: file.path,
                        commitId: file.commitId,
                        commitMessage: file.commitMessage,
                        author: file.author,
                        matchedInLog: true
                    });
                    break;
                }
            }
        }
        
        return {
            buildId,
            previousBuildId: previousBuild.id,
            totalCommits: commitsBetweenBuilds.length,
            totalChangedFiles: changedFiles.length,
            failureLogCount: failureLogs.length,
            potentialCulprits,
            commits: commitsBetweenBuilds.map(c => ({
                id: c.commitId,
                message: c.comment,
                author: c.author.name,
                date: c.author.date
            })),
            changedFiles,
            failureLogs: failureLogs.map(log => ({
                id: log.id,
                excerpts: log.content.slice(0, 10) // First 10 lines
            }))
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

// Usage
const result = await analyzeFailedBuild(789);
console.log(`Analysis complete for build ${result.buildId}`);
console.log(`Found ${result.totalCommits} commits and ${result.totalChangedFiles} changed files between builds`);
console.log(`Identified ${result.potentialCulprits.length} potential culprits for the build failure`);
```

### 5. Generating Build Reports by Branch

Create reports that show build status and trends across different branches in a repository.

#### Example: Generate Build Report by Branch

```typescript
import * as azdev from "azure-devops-node-api";

async function generateBuildReportByBranch(repositoryId: string, buildDefinitionId: number, days: number = 30) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const gitApi = await connection.getGitApi();
    const buildApi = await connection.getBuildApi();
    
    try {
        // 1. Get repository details
        const repository = await gitApi.getRepository(repositoryId, projectName);
        console.log(`Found repository: ${repository.name}`);
        
        // 2. Get all branches in the repository
        const refs = await gitApi.getRefs(repositoryId, projectName, "heads/", false, true);
        const branches = refs.map(ref => ({
            name: ref.name.replace('refs/heads/', ''),
            objectId: ref.objectId
        }));
        
        console.log(`Found ${branches.length} branches in repository`);
        
        // 3. Set time range for builds
        const endDate = new Date();
        const startDate = new Date();
        startDate.setDate(startDate.getDate() - days);
        
        // 4. Get builds for each branch
        const branchReports = [];
        
        for (const branch of branches) {
            // Get builds for this branch
            const builds = await buildApi.getBuilds(
                projectName,
                [buildDefinitionId],
                undefined, // Build numbers
                startDate,
                endDate,
                undefined, // Request id
                undefined, // Build status
                undefined, // Result
                undefined, // Tag filters
                undefined, // Properties
                100, // Top
                undefined, // Continuation token
                undefined, // Max builds per definition
                undefined, // Delete in progress
                undefined, // Query order
                `refs/heads/${branch.name}`, // Branch name
                undefined, // Build Ids
                undefined // Repository type
            );
            
            if (builds && builds.length > 0) {
                // Calculate statistics
                const totalBuilds = builds.length;
                const successfulBuilds = builds.filter(b => b.result === 2).length; // 2 = Succeeded
                const failedBuilds = builds.filter(b => b.result === 8).length; // 8 = Failed
                const canceledBuilds = builds.filter(b => b.result === 32).length; // 32 = Canceled
                const partiallySuccessfulBuilds = builds.filter(b => b.result === 4).length; // 4 = PartiallySucceeded
                const inProgressBuilds = builds.filter(b => b.status === 1).length; // 1 = InProgress
                
                // Calculate success rate
                const completedBuilds = successfulBuilds + failedBuilds + partiallySuccessfulBuilds;
                const successRate = completedBuilds > 0 
                    ? Math.round((successfulBuilds / completedBuilds) * 100) 
                    : 0;
                
                // Calculate average duration for successful builds
                let totalDuration = 0;
                let buildsWithDuration = 0;
                
                builds.forEach(build => {
                    if (build.startTime && build.finishTime) {
                        const duration = new Date(build.finishTime).getTime() - new Date(build.startTime).getTime();
                        totalDuration += duration;
                        buildsWithDuration++;
                    }
                });
                
                const averageDuration = buildsWithDuration > 0 
                    ? Math.round(totalDuration / buildsWithDuration / 1000) 
                    : 0;
                
                // Get latest build
                const latestBuild = builds[0];
                
                branchReports.push({
                    branch: branch.name,
                    totalBuilds,
                    successfulBuilds,
                    failedBuilds,
                    canceledBuilds,
                    partiallySuccessfulBuilds,
                    inProgressBuilds,
                    successRate,
                    averageDuration,
                    latestBuild: latestBuild ? {
                        id: latestBuild.id,
                        number: latestBuild.buildNumber,
                        status: latestBuild.status,
                        result: latestBuild.result,
                        startTime: latestBuild.startTime,
                        finishTime: latestBuild.finishTime,
                        url: latestBuild._links.web.href
                    } : null
                });
            } else {
                branchReports.push({
                    branch: branch.name,
                    totalBuilds: 0,
                    successfulBuilds: 0,
                    failedBuilds: 0,
                    canceledBuilds: 0,
                    partiallySuccessfulBuilds: 0,
                    inProgressBuilds: 0,
                    successRate: 0,
                    averageDuration: 0,
                    latestBuild: null
                });
            }
        }
        
        // 5. Sort branches by build count (most active first)
        branchReports.sort((a, b) => b.totalBuilds - a.totalBuilds);
        
        return {
            repository: repository.name,
            buildDefinitionId,
            timeRange: {
                start: startDate,
                end: endDate,
                days
            },
            branchCount: branches.length,
            branchesWithBuilds: branchReports.filter(r => r.totalBuilds > 0).length,
            branchReports
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
const result = await generateBuildReportByBranch("repository-guid-here", 456, 14);
console.log(`Generated build report for ${result.repository}`);
console.log(`Time range: ${result.timeRange.days} days (${new Date(result.timeRange.start).toLocaleDateString()} - ${new Date(result.timeRange.end).toLocaleDateString()})`);
console.log(`Found builds in ${result.branchesWithBuilds} of ${result.branchCount} branches`);

// Display top 5 most active branches
console.log("\nTop 5 most active branches:");
result.branchReports.slice(0, 5).forEach(report => {
    console.log(`${report.branch}: ${report.totalBuilds} builds, ${report.successRate}% success rate`);
});
```

## Best Practices for Git + Build Integration

1. **Consistent Branch Policies**: Implement branch policies that require successful builds before merging pull requests.

2. **Build Definitions per Branch Type**: Create specialized build definitions for different branch types (feature, release, main) with appropriate validation levels.

3. **Artifact Management**: Store build artifacts with clear versioning that relates to the source branch and commit.

4. **Build Triggers**: Configure appropriate build triggers based on branch patterns and file paths to avoid unnecessary builds.

5. **Status Reporting**: Always update pull requests with build status to provide visibility to developers.

6. **Build Parameters**: Pass relevant Git context (branch names, commit IDs, pull request IDs) to builds for traceability.

7. **Retention Policies**: Implement appropriate retention policies for builds based on branch importance.

## Common Challenges and Solutions

| Challenge | Solution |
|-----------|----------|
| Builds triggered unnecessarily | Configure path filters in build triggers to only build when relevant files change |
| Long build times on feature branches | Implement tiered build definitions with faster validation builds for feature branches |
| Difficulty tracking which code changes caused build failures | Use the Git + Build integration to analyze changes between successful and failed builds |
| Managing build artifacts across branches | Implement versioning schemes that include branch information in artifact names |
| Handling merge conflicts in automated processes | Implement retry logic with notifications when automated processes can't resolve conflicts |

## See Also

- [Git API Reference](../git-api/README.md)
- [Build API Reference](../build-api/README.md)
- [Work Item + Git Integration](./work-item-git-integration.md)
- [Work Item + Build Integration](./work-item-build-integration.md)
- [Cross-API Examples](./cross-api-examples.md) 