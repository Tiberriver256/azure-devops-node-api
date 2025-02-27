# Git API Code Examples

This document provides practical code examples for common scenarios when working with the Azure DevOps Git API. These examples demonstrate how to use multiple Git API methods together to achieve specific tasks.

## Setup

Each example assumes you have already set up a connection to Azure DevOps:

```typescript
import * as azdev from "azure-devops-node-api";

// Connection setup
const orgUrl = "https://dev.azure.com/yourorganization";
// Security Note: In production, always store tokens in environment variables
// or a secure secret management solution, not in your code.
const token = process.env.AZURE_DEVOPS_PAT || "your-personal-access-token";

// Create authentication handler using Personal Access Token
const authHandler = azdev.getPersonalAccessTokenHandler(token);
// Create WebApi instance with organization URL and auth handler
const connection = new azdev.WebApi(orgUrl, authHandler);
// Get Git API client
const gitApi = await connection.getGitApi();
```

## 1. Repository Analysis

This example demonstrates how to analyze repositories in a project by collecting statistics about branches, commits, and pull requests.

```typescript
/**
 * Analyzes repositories in a project and generates a report
 * @param projectName The name of the project to analyze
 */
async function analyzeRepositories(projectName: string) {
    try {
        // Get all repositories in the project
        // This provides a list of all Git repositories the user has access to
        const repositories = await gitApi.getRepositories(projectName);
        console.log(`Found ${repositories.length} repositories in ${projectName}`);
        
        // Analyze each repository
        for (const repo of repositories) {
            console.log(`\nAnalyzing repository: ${repo.name}`);
            
            // Get branches
            // We use "heads/" to filter only branches (not tags or other refs)
            const branches = await gitApi.getRefs(
                repo.id,
                projectName,
                "heads/"
            );
            console.log(`  Branches: ${branches.length}`);
            
            // Get recent commits on default branch
            const defaultBranch = repo.defaultBranch?.replace('refs/heads/', '');
            if (defaultBranch) {
                // Create search criteria to get the most recent commits
                const searchCriteria = {
                    itemVersion: {
                        version: defaultBranch
                    },
                    $top: 10 // Get 10 most recent commits
                };
                
                const commits = await gitApi.getCommits(
                    repo.id,
                    searchCriteria,
                    projectName
                );
                
                console.log(`  Recent commits on ${defaultBranch}: ${commits.length}`);
                
                if (commits.length > 0) {
                    const latestCommit = commits[0];
                    console.log(`  Latest commit: ${latestCommit.comment.split('\n')[0]} by ${latestCommit.author.name} on ${latestCommit.author.date.toLocaleDateString()}`);
                }
            }
            
            // Get active pull requests
            // We filter to only show active PRs (status = 1)
            const prSearchCriteria = {
                repositoryId: repo.id,
                status: 1 // Active
            };
            
            const pullRequests = await gitApi.getPullRequests(
                repo.id,
                prSearchCriteria,
                projectName
            );
            
            console.log(`  Active pull requests: ${pullRequests.length}`);
            pullRequests.forEach(pr => {
                console.log(`    PR #${pr.pullRequestId}: ${pr.title}`);
            });
        }
    } catch (error) {
        // Handle specific error types
        if (error.statusCode === 404) {
            console.error(`Project '${projectName}' not found. Check the project name.`);
        } else if (error.statusCode === 401) {
            console.error("Authentication error. Check your credentials or token.");
        } else {
            console.error("Error analyzing repositories:", error.message);
        }
        throw error;
    }
}

// Usage example
(async () => {
    try {
        await analyzeRepositories("MyProject");
        console.log("Repository analysis completed successfully");
    } catch (error) {
        console.error("Error in main process:", error.message);
    }
})();
```

## 2. Create Branch and Pull Request

This example shows how to create a new branch from an existing one and then create a pull request for it.

```typescript
/**
 * Creates a new branch and a pull request for it
 * @param projectName The name of the project
 * @param repositoryName The name of the repository
 * @param sourceBranch The source branch to create from (e.g., "main")
 * @param newBranchName The name for the new branch (without refs/heads/)
 * @param commitMessage The commit message for the branch creation
 * @param prTitle The title for the pull request
 * @param prDescription The description for the pull request
 */
async function createBranchAndPullRequest(
    projectName: string,
    repositoryName: string,
    sourceBranch: string,
    newBranchName: string,
    commitMessage: string,
    prTitle: string,
    prDescription: string
) {
    try {
        // Step 1: Get the repository
        const repository = await gitApi.getRepository(repositoryName, projectName);
        
        if (!repository) {
            throw new Error(`Repository ${repositoryName} not found in project ${projectName}.`);
        }
        
        // Step 2: Get the source branch ref to get its object ID (commit ID)
        const refs = await gitApi.getRefs(
            repository.id,
            projectName,
            `heads/${sourceBranch}`
        );
        
        if (!refs || refs.length === 0) {
            throw new Error(`Source branch ${sourceBranch} not found.`);
        }
        
        const sourceRef = refs[0];
        const sourceCommitId = sourceRef.objectId;
        
        // Step 3: Create the new branch
        const refUpdate = [{
            name: `refs/heads/${newBranchName}`,
            oldObjectId: "0000000000000000000000000000000000000000", // New branch, no old object ID
            newObjectId: sourceCommitId
        }];
        
        const refUpdateResult = await gitApi.updateRefs(refUpdate, repository.id, projectName);
        
        if (!refUpdateResult || refUpdateResult.length === 0 || !refUpdateResult[0].success) {
            throw new Error(`Failed to create branch: ${JSON.stringify(refUpdateResult)}`);
        }
        
        console.log(`Successfully created branch ${newBranchName} from ${sourceBranch}`);
        
        // Step 4: Create a pull request
        const pullRequestToCreate = {
            sourceRefName: `refs/heads/${newBranchName}`,
            targetRefName: `refs/heads/${sourceBranch}`,
            title: prTitle,
            description: prDescription
        };
        
        const pullRequest = await gitApi.createPullRequest(
            pullRequestToCreate,
            repository.id,
            projectName
        );
        
        console.log(`Successfully created pull request #${pullRequest.pullRequestId}: ${pullRequest.title}`);
        console.log(`Pull request URL: ${pullRequest.url}`);
        
        return {
            branchName: newBranchName,
            pullRequestId: pullRequest.pullRequestId,
            pullRequestUrl: pullRequest.url
        };
    } catch (error) {
        console.error("Error creating branch and pull request:", error.message);
        throw error;
    }
}

// Usage
createBranchAndPullRequest(
    "MyProject",
    "MyRepository",
    "main",
    "feature/new-feature",
    "Create new feature branch",
    "Add new feature implementation",
    "This pull request adds the implementation for the new feature."
);
```

## 3. Find Branches by Commit

This example demonstrates how to find which branches contain a specific commit.

```typescript
/**
 * Finds branches that contain a specific commit
 * @param projectName The name of the project
 * @param repositoryName The name of the repository
 * @param commitId The commit ID to search for
 */
async function findBranchesByCommit(
    projectName: string,
    repositoryName: string,
    commitId: string
) {
    try {
        // Step 1: Get the repository
        const repository = await gitApi.getRepository(repositoryName, projectName);
        
        if (!repository) {
            throw new Error(`Repository ${repositoryName} not found in project ${projectName}.`);
        }
        
        // Step 2: Get all branches in the repository
        const branches = await gitApi.getRefs(
            repository.id,
            projectName,
            "heads/"
        );
        
        if (!branches || branches.length === 0) {
            console.log(`No branches found in repository ${repositoryName}.`);
            return [];
        }
        
        // Step 3: For each branch, check if it contains the commit
        const branchesWithCommit = [];
        
        for (const branch of branches) {
            // Get the branch name without the refs/heads/ prefix
            const branchName = branch.name.replace("refs/heads/", "");
            
            try {
                // Check if the branch contains the commit using searchCriteria
                const searchCriteria = {
                    itemVersion: {
                        version: branchName
                    },
                    ids: [commitId]
                };
                
                const commits = await gitApi.getCommits(
                    repository.id,
                    searchCriteria,
                    projectName
                );
                
                if (commits && commits.length > 0) {
                    branchesWithCommit.push({
                        name: branchName,
                        objectId: branch.objectId
                    });
                }
            } catch (error) {
                console.log(`Error checking branch ${branchName}:`, error.message);
            }
        }
        
        console.log(`Found ${branchesWithCommit.length} branches containing commit ${commitId}:`);
        branchesWithCommit.forEach(branch => {
            console.log(`  ${branch.name}`);
        });
        
        return branchesWithCommit;
    } catch (error) {
        console.error("Error finding branches by commit:", error.message);
        throw error;
    }
}

// Usage
findBranchesByCommit(
    "MyProject",
    "MyRepository",
    "abcdef1234567890abcdef1234567890abcdef12"
);
```

## 4. Compare Branch Differences

This example shows how to compare two branches to determine what commits and changes exist between them.

```typescript
/**
 * Compares two branches to see what commits and changes exist between them
 * @param projectName The name of the project
 * @param repositoryName The name of the repository
 * @param baseBranch The base branch for comparison (e.g., "main")
 * @param targetBranch The target branch to compare against base (e.g., "feature/new-feature")
 */
async function compareBranches(
    projectName: string,
    repositoryName: string,
    baseBranch: string,
    targetBranch: string
) {
    try {
        // Step 1: Get the repository
        const repository = await gitApi.getRepository(repositoryName, projectName);
        
        if (!repository) {
            throw new Error(`Repository ${repositoryName} not found in project ${projectName}.`);
        }
        
        // Step 2: Get commits that are in the target branch but not in the base branch
        // This uses the commitDiffs API
        const commitDiffs = await gitApi.getCommitDiffs(
            repository.id,
            projectName,
            false, // diffCommonCommit
            null, // top
            null, // skip
            {
                version: baseBranch,
                versionOptions: null,
                versionType: null
            }, // baseVersionDescriptor
            {
                version: targetBranch,
                versionOptions: null,
                versionType: null
            } // targetVersionDescriptor
        );
        
        console.log(`Comparing ${targetBranch} to ${baseBranch} in ${repositoryName}:`);
        
        // Step 3: Process the differences
        if (commitDiffs.commits && commitDiffs.commits.length > 0) {
            console.log(`\nFound ${commitDiffs.commits.length} unique commits in ${targetBranch}:`);
            commitDiffs.commits.forEach((commit, index) => {
                console.log(`  ${index + 1}. ${commit.comment.split('\n')[0]} by ${commit.author.name} on ${new Date(commit.author.date).toLocaleDateString()}`);
                console.log(`     Commit ID: ${commit.commitId}`);
            });
        } else {
            console.log(`\nNo unique commits found in ${targetBranch}.`);
        }
        
        if (commitDiffs.changes && commitDiffs.changes.length > 0) {
            console.log(`\nFound ${commitDiffs.changes.length} file changes:`);
            commitDiffs.changes.forEach((change, index) => {
                console.log(`  ${index + 1}. ${change.item.path} (${getChangeTypeDescription(change.changeType)})`);
            });
        } else {
            console.log(`\nNo file changes found.`);
        }
        
        return {
            commits: commitDiffs.commits || [],
            changes: commitDiffs.changes || []
        };
    } catch (error) {
        console.error("Error comparing branches:", error.message);
        throw error;
    }
}

// Helper function to convert change type values to readable descriptions
function getChangeTypeDescription(changeType: number): string {
    const changeTypes = {
        1: "Add",
        2: "Edit",
        4: "Delete",
        8: "Rename",
        16: "SourceRename",
        32: "TargetRename"
    };
    
    const descriptions = [];
    for (const [value, description] of Object.entries(changeTypes)) {
        if ((Number(value) & changeType) !== 0) {
            descriptions.push(description);
        }
    }
    
    return descriptions.join(", ") || "Unknown";
}

// Usage
compareBranches(
    "MyProject",
    "MyRepository",
    "main",
    "feature/new-feature"
);
```

## 5. PR Review Dashboard

This example creates a dashboard of pull requests assigned to a user for review.

```typescript
/**
 * Generates a dashboard of pull requests assigned to a specific reviewer
 * @param projectName The name of the project
 * @param reviewerEmail The email of the reviewer to find pull requests for
 */
async function pullRequestReviewDashboard(
    projectName: string,
    reviewerEmail: string
) {
    try {
        // Step 1: Get all repositories in the project
        const repositories = await gitApi.getRepositories(projectName);
        
        if (!repositories || repositories.length === 0) {
            console.log(`No repositories found in project ${projectName}.`);
            return [];
        }
        
        // Step 2: Find the reviewer's ID
        // For this example, we'll assume we have a method to get user ID from email
        // This would typically use the identitiesClient
        // const reviewerId = await getUserIdFromEmail(reviewerEmail);
        const reviewerId = "reviewer-guid"; // Replace with actual code to get ID
        
        // Step 3: Find all active pull requests assigned to the reviewer across all repositories
        let allReviewerPRs = [];
        
        for (const repo of repositories) {
            const searchCriteria = {
                status: 1, // Active
                reviewerId: reviewerId,
                repositoryId: repo.id
            };
            
            const pullRequests = await gitApi.getPullRequests(
                repo.id,
                searchCriteria,
                projectName
            );
            
            if (pullRequests && pullRequests.length > 0) {
                // Add repository information to each PR for the dashboard
                const repoName = repo.name;
                const enrichedPRs = pullRequests.map(pr => ({
                    ...pr,
                    repositoryName: repoName
                }));
                
                allReviewerPRs = allReviewerPRs.concat(enrichedPRs);
            }
        }
        
        // Step 4: Organize and display the dashboard
        console.log(`\n===== Pull Request Review Dashboard for ${reviewerEmail} =====\n`);
        console.log(`Total pull requests awaiting review: ${allReviewerPRs.length}\n`);
        
        if (allReviewerPRs.length === 0) {
            console.log("No pull requests assigned for review.");
            return [];
        }
        
        // Sort PRs by creation date (newest first)
        allReviewerPRs.sort((a, b) => 
            new Date(b.creationDate).getTime() - new Date(a.creationDate).getTime()
        );
        
        // Group PRs by repository
        const prsByRepo = {};
        allReviewerPRs.forEach(pr => {
            if (!prsByRepo[pr.repositoryName]) {
                prsByRepo[pr.repositoryName] = [];
            }
            prsByRepo[pr.repositoryName].push(pr);
        });
        
        // Display PRs by repository
        for (const [repoName, prs] of Object.entries(prsByRepo)) {
            console.log(`\n${repoName} (${prs.length} PRs):`);
            console.log('-'.repeat(repoName.length + prs.length.toString().length + 6));
            
            prs.forEach((pr: any) => {
                const createdDate = new Date(pr.creationDate).toLocaleDateString();
                const sourceBranch = pr.sourceRefName.replace('refs/heads/', '');
                const targetBranch = pr.targetRefName.replace('refs/heads/', '');
                
                console.log(`  PR #${pr.pullRequestId}: ${pr.title}`);
                console.log(`    Created by: ${pr.createdBy.displayName} on ${createdDate}`);
                console.log(`    Source branch: ${sourceBranch} → Target branch: ${targetBranch}`);
                console.log(`    URL: ${pr.url}`);
                console.log();
            });
        }
        
        return allReviewerPRs;
    } catch (error) {
        console.error("Error generating pull request dashboard:", error.message);
        throw error;
    }
}

// Usage
pullRequestReviewDashboard(
    "MyProject",
    "reviewer@example.com"
);
```

## 6. Repository Clone Report

This example generates a report with clone URLs for all repositories in a project.

```typescript
/**
 * Generates a report with all repository clone URLs
 * @param projectName The name of the project
 */
async function repositoryCloneReport(projectName: string) {
    try {
        // Step 1: Get all repositories in the project
        const repositories = await gitApi.getRepositories(projectName);
        
        if (!repositories || repositories.length === 0) {
            console.log(`No repositories found in project ${projectName}.`);
            return [];
        }
        
        // Step 2: Generate the report
        console.log(`\n===== Repository Clone Report for ${projectName} =====\n`);
        console.log(`Total repositories: ${repositories.length}\n`);
        
        // Sort repositories alphabetically by name
        repositories.sort((a, b) => a.name.localeCompare(b.name));
        
        // Generate a markdown table
        console.log("| Repository Name | HTTPS Clone URL | SSH Clone URL | Default Branch |");
        console.log("|----------------|----------------|--------------|----------------|");
        
        repositories.forEach(repo => {
            const defaultBranch = repo.defaultBranch ? 
                repo.defaultBranch.replace('refs/heads/', '') : 
                'N/A';
            
            // Note: SSH URLs may need to be constructed differently depending on your Azure DevOps setup
            const sshUrl = repo.sshUrl || 
                `git@ssh.dev.azure.com:v3/${orgUrl.split('/').pop()}/${projectName}/${repo.name}`;
            
            console.log(`| ${repo.name} | ${repo.remoteUrl} | ${sshUrl} | ${defaultBranch} |`);
        });
        
        // Step 3: Return data for further processing if needed
        return repositories.map(repo => ({
            name: repo.name,
            httpCloneUrl: repo.remoteUrl,
            sshCloneUrl: repo.sshUrl,
            defaultBranch: repo.defaultBranch ? repo.defaultBranch.replace('refs/heads/', '') : null,
            id: repo.id,
            webUrl: repo.webUrl
        }));
    } catch (error) {
        console.error("Error generating repository clone report:", error.message);
        throw error;
    }
}

// Usage
repositoryCloneReport("MyProject");
```

## 7. Branch Protection Check

This example checks whether branches have protection rules applied.

```typescript
/**
 * Checks whether key branches have protection rules applied
 * @param projectName The name of the project
 * @param repositoryName The name of the repository
 * @param branchesToCheck Array of branch names to check (without refs/heads/ prefix)
 */
async function checkBranchProtection(
    projectName: string,
    repositoryName: string,
    branchesToCheck: string[] = ['main', 'master', 'develop', 'release']
) {
    try {
        // Step 1: Get the repository
        const repository = await gitApi.getRepository(repositoryName, projectName);
        
        if (!repository) {
            throw new Error(`Repository ${repositoryName} not found in project ${projectName}.`);
        }
        
        // Step 2: Get all branches
        const refs = await gitApi.getRefs(
            repository.id,
            projectName,
            "heads/"
        );
        
        const branches = refs.map(ref => ({
            name: ref.name.replace("refs/heads/", ""),
            isLocked: ref.isLocked || false
        }));
        
        // Step 3: Get branch policies (requires Policy API)
        // This is just a placeholder - in a real implementation you would use the Policy API
        // const policyClient = await connection.getPolicyApi();
        // const policies = await policyClient.getPolicyConfigurations(projectName);
        
        // For this example, we'll simulate policy results
        const branchPolicies = {
            'main': { hasPolicy: true, policyTypes: ['Require minimum reviewers', 'Require build validation'] },
            'master': { hasPolicy: true, policyTypes: ['Require minimum reviewers', 'Require build validation'] },
            'develop': { hasPolicy: true, policyTypes: ['Require minimum reviewers'] },
            'release': { hasPolicy: false, policyTypes: [] }
        };
        
        // Step 4: Check protection for each specified branch
        console.log(`\n===== Branch Protection Check for ${repositoryName} =====\n`);
        
        for (const branchName of branchesToCheck) {
            const branch = branches.find(b => b.name === branchName);
            
            if (!branch) {
                console.log(`Branch ${branchName}: Not found`);
                continue;
            }
            
            // Get policy information (in a real implementation, this would come from the Policy API)
            const policy = branchPolicies[branchName] || { hasPolicy: false, policyTypes: [] };
            const isProtected = branch.isLocked || policy.hasPolicy;
            
            console.log(`Branch ${branchName}:`);
            console.log(`  Exists: Yes`);
            console.log(`  Is Locked: ${branch.isLocked ? 'Yes' : 'No'}`);
            console.log(`  Has Policies: ${policy.hasPolicy ? 'Yes' : 'No'}`);
            
            if (policy.policyTypes.length > 0) {
                console.log(`  Policy Types: ${policy.policyTypes.join(', ')}`);
            }
            
            console.log(`  Protected: ${isProtected ? 'Yes' : 'No - ATTENTION REQUIRED'}`);
            console.log();
        }
        
        return {
            repository: repositoryName,
            branchesChecked: branchesToCheck,
            results: branchesToCheck.map(branchName => {
                const branch = branches.find(b => b.name === branchName);
                const policy = branchPolicies[branchName] || { hasPolicy: false, policyTypes: [] };
                return {
                    branchName,
                    exists: !!branch,
                    isLocked: branch ? branch.isLocked : false,
                    hasPolicy: policy.hasPolicy,
                    policyTypes: policy.policyTypes,
                    isProtected: branch ? (branch.isLocked || policy.hasPolicy) : false
                };
            })
        };
    } catch (error) {
        console.error("Error checking branch protection:", error.message);
        throw error;
    }
}

// Usage
checkBranchProtection(
    "MyProject",
    "MyRepository",
    ['main', 'develop', 'release']
);
```

## Conclusion

These examples demonstrate how to use the Git API for common tasks and workflows. You can use these patterns as a foundation and customize them to meet your specific needs. Remember to handle errors appropriately and consider implementing retry logic for network operations, especially when dealing with large repositories or making multiple API calls.

For more information about error handling and specific method details, refer to the [Common Errors](./common-errors.md) and [Top 5 Methods](./top-5-methods.md) documentation. 