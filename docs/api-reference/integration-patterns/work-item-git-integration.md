# Work Item + Git Integration Patterns

**Navigation**: [Home](../../index.md) > [API Reference](../index.md) > [Integration Patterns](./README.md) > Work Item + Git Integration

## Overview

Integrating the Work Item Tracking API with the Git API enables powerful workflows that connect planning and tracking with source code management. This integration is fundamental to many DevOps practices, providing traceability from requirements to implementation and enabling automation across the development lifecycle.

## Common Integration Scenarios

### 1. Linking Work Items to Commits

One of the most common integration patterns is linking work items to the commits that implement them. This creates traceability from requirements to code changes.

#### Example: Find Commits Associated with a Work Item

```typescript
import * as azdev from "azure-devops-node-api";

async function getCommitsForWorkItem(workItemId: number) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const witApi = await connection.getWorkItemTrackingApi();
    const gitApi = await connection.getGitApi();
    
    try {
        // 1. Get the work item to verify it exists
        const workItem = await witApi.getWorkItem(workItemId);
        console.log(`Found work item: ${workItem.id} - ${workItem.fields["System.Title"]}`);
        
        // 2. Get all repositories in the project
        const repositories = await gitApi.getRepositories(projectName);
        
        // 3. For each repository, search for commits that reference the work item
        const allCommits = [];
        
        for (const repo of repositories) {
            // Search for commits with the work item ID in the comment
            // Note: This uses a simple search pattern that looks for the ID
            // A more robust implementation might use the Azure DevOps-specific syntax: #123, AB#123, etc.
            const searchCriteria = {
                itemVersion: { version: "main" }, // Search in main branch
                compareVersion: { version: "main" },
                fromDate: new Date(new Date().setDate(new Date().getDate() - 90)), // Last 90 days
                toDate: new Date(),
                searchText: `#${workItemId}` // Search for #123 pattern
            };
            
            try {
                const commits = await gitApi.getCommitsBatch(repo.id, searchCriteria, projectName);
                
                if (commits && commits.length > 0) {
                    console.log(`Found ${commits.length} commits in repository ${repo.name}`);
                    
                    commits.forEach(commit => {
                        allCommits.push({
                            repository: repo.name,
                            commitId: commit.commitId,
                            comment: commit.comment,
                            author: commit.author.name,
                            date: commit.author.date
                        });
                    });
                }
            } catch (error) {
                console.error(`Error searching commits in repository ${repo.name}: ${error.message}`);
            }
        }
        
        return {
            workItemId,
            workItemTitle: workItem.fields["System.Title"],
            commits: allCommits
        };
    } catch (error) {
        console.error(`Error: ${error.message}`);
        throw error;
    }
}

// Usage
const result = await getCommitsForWorkItem(123);
console.log(`Found ${result.commits.length} commits for work item ${result.workItemId}`);
result.commits.forEach(commit => {
    console.log(`- ${commit.date.toISOString().split('T')[0]} | ${commit.repository} | ${commit.commitId.substring(0, 8)} | ${commit.author}`);
    console.log(`  ${commit.comment.split('\n')[0]}`); // First line of commit message
});
```

### 2. Creating Work Item Links to Pull Requests

Linking work items to pull requests provides visibility into the code review process for specific requirements or bug fixes.

#### Example: Link a Work Item to a Pull Request

```typescript
import * as azdev from "azure-devops-node-api";

async function linkWorkItemToPullRequest(workItemId: number, pullRequestId: number, repositoryId: string) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const witApi = await connection.getWorkItemTrackingApi();
    const gitApi = await connection.getGitApi();
    
    try {
        // 1. Verify the work item exists
        const workItem = await witApi.getWorkItem(workItemId);
        console.log(`Found work item: ${workItem.id} - ${workItem.fields["System.Title"]}`);
        
        // 2. Verify the pull request exists
        const pullRequest = await gitApi.getPullRequestById(pullRequestId, projectName);
        console.log(`Found pull request: ${pullRequest.pullRequestId} - ${pullRequest.title}`);
        
        // 3. Create a link between the work item and pull request
        // The link is created by adding an artifact link to the work item
        const patchDocument = [
            {
                op: "add",
                path: "/relations/-",
                value: {
                    rel: "ArtifactLink",
                    url: `vstfs:///Git/PullRequestId/${projectName}/${repositoryId}/${pullRequestId}`,
                    attributes: {
                        name: "Pull Request"
                    }
                }
            }
        ];
        
        const updatedWorkItem = await witApi.updateWorkItem(
            {}, // No custom headers
            patchDocument,
            workItemId,
            projectName
        );
        
        console.log(`Successfully linked work item ${workItemId} to pull request ${pullRequestId}`);
        return {
            workItemId,
            pullRequestId,
            success: true
        };
    } catch (error) {
        console.error(`Error: ${error.message}`);
        throw error;
    }
}

// Usage
const result = await linkWorkItemToPullRequest(123, 456, "repository-guid-here");
console.log(`Link created: ${result.success}`);
```

### 3. Finding Work Items Affected by Code Changes

When planning deployments or assessing impact, it's useful to identify which work items are affected by changes to specific files or areas of code.

#### Example: Find Work Items Affected by Changes to a Specific File

```typescript
import * as azdev from "azure-devops-node-api";

async function findWorkItemsAffectedByFile(filePath: string) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const witApi = await connection.getWorkItemTrackingApi();
    const gitApi = await connection.getGitApi();
    
    try {
        // 1. Get all repositories in the project
        const repositories = await gitApi.getRepositories(projectName);
        
        // 2. For each repository, find commits that modified the specified file
        const affectedWorkItems = new Set<number>(); // Use Set to avoid duplicates
        const commitDetails = [];
        
        for (const repo of repositories) {
            try {
                // Get the commit history for the specific file
                const commitCriteria = {
                    itemPath: filePath,
                    itemVersion: { version: "main" },
                    top: 100 // Limit to recent commits
                };
                
                const commits = await gitApi.getCommits(
                    repo.id,
                    commitCriteria,
                    projectName
                );
                
                if (commits && commits.length > 0) {
                    console.log(`Found ${commits.length} commits affecting ${filePath} in ${repo.name}`);
                    
                    // 3. For each commit, extract work item IDs from commit messages
                    for (const commit of commits) {
                        // Look for work item mentions in the commit message
                        // Common formats: #123, AB#123, etc.
                        const workItemMatches = commit.comment.match(/#(\d+)/g) || [];
                        const extractedIds = workItemMatches.map(match => parseInt(match.substring(1)));
                        
                        extractedIds.forEach(id => affectedWorkItems.add(id));
                        
                        commitDetails.push({
                            repository: repo.name,
                            commitId: commit.commitId,
                            author: commit.author.name,
                            date: commit.author.date,
                            comment: commit.comment,
                            workItemIds: extractedIds
                        });
                    }
                }
            } catch (error) {
                console.error(`Error processing repository ${repo.name}: ${error.message}`);
            }
        }
        
        // 4. Get details for all the affected work items
        const workItemIds = Array.from(affectedWorkItems);
        let workItems = [];
        
        if (workItemIds.length > 0) {
            workItems = await witApi.getWorkItems(workItemIds);
            
            console.log(`Found ${workItems.length} work items affected by changes to ${filePath}`);
            workItems.forEach(wi => {
                console.log(`- ${wi.id}: ${wi.fields["System.Title"]} (${wi.fields["System.State"]})`);
            });
        } else {
            console.log(`No work items found related to changes in ${filePath}`);
        }
        
        return {
            filePath,
            commits: commitDetails,
            workItems: workItems.map(wi => ({
                id: wi.id,
                title: wi.fields["System.Title"],
                state: wi.fields["System.State"],
                type: wi.fields["System.WorkItemType"]
            }))
        };
    } catch (error) {
        console.error(`Error: ${error.message}`);
        throw error;
    }
}

// Usage
const result = await findWorkItemsAffectedByFile("src/main/component/critical-feature.ts");
console.log(`Analysis complete. Found ${result.commits.length} commits and ${result.workItems.length} work items.`);
```

### 4. Creating Work Items from Repository Issues

For teams that use repository issues (in GitHub or similar platforms) and want to sync them with Azure DevOps work items.

#### Example: Create Work Items from Repository Issues

```typescript
import * as azdev from "azure-devops-node-api";

// This example assumes you have already retrieved issues from a Git repository
// (e.g., using GitHub API or similar)
async function createWorkItemsFromIssues(issues: any[], repositoryId: string) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const witApi = await connection.getWorkItemTrackingApi();
    const gitApi = await connection.getGitApi();
    
    try {
        // 1. Verify the repository exists
        const repository = await gitApi.getRepository(repositoryId, projectName);
        console.log(`Found repository: ${repository.name}`);
        
        const results = [];
        
        // 2. Process each issue and create a corresponding work item
        for (const issue of issues) {
            // Determine work item type based on issue labels or other criteria
            const workItemType = issue.labels.includes("bug") ? "Bug" : "User Story";
            
            // Create a document for the new work item
            const patchDocument = [
                {
                    op: "add",
                    path: "/fields/System.Title",
                    value: issue.title
                },
                {
                    op: "add",
                    path: "/fields/System.Description",
                    value: `<div>Imported from repository issue #${issue.number}</div><div>${issue.body}</div><div><a href="${issue.html_url}">Original Issue Link</a></div>`
                },
                {
                    op: "add",
                    path: "/fields/System.Tags",
                    value: `Import; Repository; ${issue.labels.join('; ')}`
                }
            ];
            
            // Add additional fields based on issue properties
            if (issue.assignee) {
                patchDocument.push({
                    op: "add",
                    path: "/fields/System.AssignedTo",
                    value: issue.assignee.email || issue.assignee.login
                });
            }
            
            try {
                // Create the work item
                const workItem = await witApi.createWorkItem(
                    {}, // No custom headers
                    patchDocument,
                    projectName,
                    workItemType
                );
                
                console.log(`Created ${workItemType} #${workItem.id} from issue #${issue.number}`);
                
                // Create a link to the repository
                const linkPatch = [
                    {
                        op: "add",
                        path: "/relations/-",
                        value: {
                            rel: "Hyperlink",
                            url: repository.webUrl,
                            attributes: {
                                comment: "Repository Link"
                            }
                        }
                    }
                ];
                
                await witApi.updateWorkItem({}, linkPatch, workItem.id, projectName);
                
                results.push({
                    issueNumber: issue.number,
                    workItemId: workItem.id,
                    workItemType,
                    title: workItem.fields["System.Title"]
                });
            } catch (error) {
                console.error(`Error creating work item from issue #${issue.number}: ${error.message}`);
                results.push({
                    issueNumber: issue.number,
                    error: error.message
                });
            }
        }
        
        return {
            repository: repository.name,
            processedIssues: results
        };
    } catch (error) {
        console.error(`Error: ${error.message}`);
        throw error;
    }
}

// Example usage (with mock issues)
const mockIssues = [
    {
        number: 42,
        title: "Fix navigation bug in header",
        body: "The dropdown menu in the header doesn't close when clicking outside.",
        labels: ["bug", "ui", "priority:high"],
        html_url: "https://github.com/org/repo/issues/42",
        assignee: { login: "developer1", email: "dev1@example.com" }
    },
    {
        number: 43,
        title: "Add dark mode support",
        body: "Implement dark mode theme following the design in Figma.",
        labels: ["enhancement", "ui"],
        html_url: "https://github.com/org/repo/issues/43",
        assignee: null
    }
];

const result = await createWorkItemsFromIssues(mockIssues, "repository-guid-here");
console.log(`Processed ${result.processedIssues.length} issues from repository ${result.repository}`);
```

### 5. Automating Branch Creation from Work Items

Create feature branches automatically based on work item details, establishing the connection from the beginning of development.

#### Example: Create a Feature Branch for a Work Item

```typescript
import * as azdev from "azure-devops-node-api";

async function createBranchForWorkItem(workItemId: number, repositoryId: string) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT;
    const projectName = "YourProject";
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get API clients
    const witApi = await connection.getWorkItemTrackingApi();
    const gitApi = await connection.getGitApi();
    
    try {
        // 1. Get the work item details
        const workItem = await witApi.getWorkItem(workItemId);
        console.log(`Found work item: ${workItem.id} - ${workItem.fields["System.Title"]}`);
        
        // 2. Get the repository details
        const repository = await gitApi.getRepository(repositoryId, projectName);
        console.log(`Found repository: ${repository.name}`);
        
        // 3. Get the default branch to use as source
        const defaultBranch = repository.defaultBranch;
        console.log(`Default branch: ${defaultBranch}`);
        
        // 4. Get the latest commit on the default branch
        const commits = await gitApi.getCommits(
            repositoryId,
            { top: 1, itemVersion: { version: defaultBranch.replace('refs/heads/', '') } },
            projectName
        );
        
        if (!commits || commits.length === 0) {
            throw new Error("Could not find any commits in the repository");
        }
        
        const sourceCommitId = commits[0].commitId;
        console.log(`Latest commit: ${sourceCommitId}`);
        
        // 5. Generate a branch name from the work item
        // Format: feature/ID-sanitized-title
        const workItemType = workItem.fields["System.WorkItemType"].toLowerCase();
        const sanitizedTitle = workItem.fields["System.Title"]
            .toLowerCase()
            .replace(/[^\w\s-]/g, '') // Remove special characters
            .replace(/\s+/g, '-')     // Replace spaces with hyphens
            .substring(0, 50);        // Limit length
        
        const branchName = `${workItemType}/${workItemId}-${sanitizedTitle}`;
        const fullBranchName = `refs/heads/${branchName}`;
        
        // 6. Create the new branch
        const refUpdate = [{
            name: fullBranchName,
            oldObjectId: "0000000000000000000000000000000000000000", // Indicates new branch
            newObjectId: sourceCommitId
        }];
        
        const result = await gitApi.updateRefs(refUpdate, repositoryId, projectName);
        
        if (result && result.length > 0 && result[0].success) {
            console.log(`Successfully created branch: ${branchName}`);
            
            // 7. Update the work item with a link to the new branch
            const patchDocument = [
                {
                    op: "add",
                    path: "/fields/System.History",
                    value: `Created branch [${branchName}](${repository.webUrl}?version=GB${branchName}) for this work item.`
                }
            ];
            
            await witApi.updateWorkItem({}, patchDocument, workItemId, projectName);
            
            return {
                workItemId,
                repositoryId,
                branchName,
                success: true,
                branchUrl: `${repository.webUrl}?version=GB${branchName}`
            };
        } else {
            throw new Error("Failed to create branch");
        }
    } catch (error) {
        console.error(`Error: ${error.message}`);
        return {
            workItemId,
            repositoryId,
            success: false,
            error: error.message
        };
    }
}

// Usage
const result = await createBranchForWorkItem(123, "repository-guid-here");
if (result.success) {
    console.log(`Branch created: ${result.branchName}`);
    console.log(`Branch URL: ${result.branchUrl}`);
} else {
    console.error(`Failed to create branch: ${result.error}`);
}
```

## Best Practices for Work Item + Git Integration

1. **Consistent Linking Conventions**: Establish consistent patterns for referencing work items in commit messages (e.g., "#123", "AB#123").

2. **Bidirectional Traceability**: Ensure links are created in both directions - work items should link to code, and code should reference work items.

3. **Automation Over Manual Linking**: Automate the creation of links where possible to ensure consistency and reduce manual effort.

4. **Branch Naming Conventions**: Use consistent branch naming that includes work item IDs to make the connection clear.

5. **Pull Request Templates**: Include work item references in pull request templates to encourage proper linking.

6. **Validation**: Implement validation to ensure work items are properly linked before allowing code to progress (e.g., requiring work item links for pull requests).

7. **Error Handling**: Implement robust error handling for integration scenarios, as they involve multiple API calls that could fail independently.

## Common Challenges and Solutions

| Challenge | Solution |
|-----------|----------|
| Work item references in commits are inconsistent | Implement a Git hook to validate commit messages and enforce a consistent format for work item references |
| Links between work items and code get out of sync | Create automated processes that periodically scan for unlinked items and create missing links |
| Performance issues when scanning large repositories | Limit searches to specific time periods or branches, and implement pagination for large result sets |
| Permissions vary across different APIs | Use a service account with consistent permissions across all relevant areas |
| Rate limiting when making many API calls | Implement batching and throttling in your integration code |

## See Also

- [Work Item Tracking API Reference](../work-item-tracking/README.md)
- [Git API Reference](../git-api/README.md)
- [Work Item + Build Integration](./work-item-build-integration.md)
- [Cross-API Examples](./cross-api-examples.md) 