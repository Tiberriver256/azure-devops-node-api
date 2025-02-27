# Top 5 Git API Methods

This document provides detailed information about the most frequently used methods in the Azure DevOps Git API.

## 1. getRepositories

Retrieves a list of Git repositories in a project or across the entire organization.

### Syntax

```typescript
getRepositories(
    project?: string,
    includeLinks?: boolean,
    includeAllUrls?: boolean,
    includeHidden?: boolean
): Promise<GitRepository[]>
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| project | string | No | Project ID or project name. If not specified, repositories from the entire organization are returned. |
| includeLinks | boolean | No | Whether to include reference links in the response. Default is false. |
| includeAllUrls | boolean | No | Whether to include all remote URLs. Default is false. |
| includeHidden | boolean | No | Whether to include hidden repositories. Default is false. |

### Return Value

A Promise that resolves to an array of `GitRepository` objects. Each object contains properties such as:

| Property | Type | Description |
|----------|------|-------------|
| id | string | The unique identifier of the repository. |
| name | string | The name of the repository. |
| url | string | The REST API URL for the repository. |
| project | TeamProjectReference | The project that contains the repository. |
| defaultBranch | string | The default branch of the repository (e.g., "refs/heads/main"). |
| size | number | The size of the repository in bytes. |
| remoteUrl | string | The URL that can be used to clone the repository. |
| webUrl | string | The URL for the repository's web interface. |

### Example

```typescript
const gitApi = await connection.getGitApi();

// Get all repositories in a project
const repositories = await gitApi.getRepositories("MyProject");
console.log(`Found ${repositories.length} repositories in MyProject`);

repositories.forEach(repo => {
    console.log(`Repository: ${repo.name}`);
    console.log(`ID: ${repo.id}`);
    console.log(`Default branch: ${repo.defaultBranch}`);
    console.log(`Clone URL: ${repo.remoteUrl}`);
    console.log(`Web URL: ${repo.webUrl}`);
    console.log("----------------------------");
});
```

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| 401 Unauthorized | Invalid or expired authentication token | Ensure your PAT has the correct scopes (Code (Read)) and hasn't expired |
| 404 Not Found | Project doesn't exist | Verify the project name or ID is correct |
| 403 Forbidden | Insufficient permissions | Ensure the user has access to view repositories in the specified project |

## 2. getRepository

Retrieves details for a specific Git repository.

### Syntax

```typescript
getRepository(
    repositoryId: string,
    project?: string
): Promise<GitRepository>
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| repositoryId | string | Yes | The name or ID of the repository to retrieve. |
| project | string | No | Project ID or project name. Required if using repository name instead of ID. |

### Return Value

A Promise that resolves to a `GitRepository` object with the same properties as described in the `getRepositories` method.

### Example

```typescript
const gitApi = await connection.getGitApi();

// Get a repository by ID
const repository = await gitApi.getRepository("abc12345-1234-1234-1234-123456789abc");
console.log(`Repository: ${repository.name}`);

// Get a repository by name (requires project parameter)
const repositoryByName = await gitApi.getRepository("my-repo", "MyProject");
console.log(`Repository ID: ${repositoryByName.id}`);
```

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| 401 Unauthorized | Invalid or expired authentication token | Ensure your PAT has the correct scopes (Code (Read)) and hasn't expired |
| 404 Not Found | Repository or project doesn't exist | Verify the repository and project names or IDs are correct |
| 403 Forbidden | Insufficient permissions | Ensure the user has access to view the specified repository |

## 3. getRefs

Queries a repository for its refs (branches and tags) and returns them.

### Syntax

```typescript
getRefs(
    repositoryId: string,
    project?: string,
    filter?: string,
    includeLinks?: boolean,
    includeStatuses?: boolean,
    includeMyBranches?: boolean,
    latestStatusesOnly?: boolean,
    peelTags?: boolean,
    filterContains?: string
): Promise<PagedList<GitRef>>
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| repositoryId | string | Yes | The name or ID of the repository. |
| project | string | No | Project ID or project name. Required if using repository name instead of ID. |
| filter | string | No | A filter to apply to the refs (starts with). Example: "heads/" to get branches only. |
| includeLinks | boolean | No | Whether to include reference links in the response. Default is false. |
| includeStatuses | boolean | No | Whether to include commit status for each ref. Default is false. |
| includeMyBranches | boolean | No | Whether to only include branches the user owns or favorites. Default is false. Cannot be combined with filter. |
| latestStatusesOnly | boolean | No | Whether to include only the tip commit status. Requires includeStatuses=true. Default is false. |
| peelTags | boolean | No | Whether to populate the PeeledObjectId property for annotated tags. Default is false. |
| filterContains | string | No | A filter to apply to the refs (contains). |

### Return Value

A Promise that resolves to a `PagedList<GitRef>` object. Each `GitRef` object contains properties such as:

| Property | Type | Description |
|----------|------|-------------|
| name | string | The name of the ref (e.g., "refs/heads/main" or "refs/tags/v1.0"). |
| objectId | string | The commit SHA1 that the ref points to. |
| creator | IdentityRef | The identity of the person who created the ref. |
| url | string | The REST API URL for the ref. |
| statuses | GitStatus[] | Status information for the ref (if requested with includeStatuses). |

### Example

```typescript
const gitApi = await connection.getGitApi();

// Get all branches in a repository
const branches = await gitApi.getRefs(
    "abc12345-1234-1234-1234-123456789abc",
    "MyProject",
    "heads/"
);
console.log(`Found ${branches.length} branches`);

branches.forEach(branch => {
    // The branch name typically has format "refs/heads/branchname"
    const branchName = branch.name.replace("refs/heads/", "");
    console.log(`Branch: ${branchName}`);
    console.log(`Commit ID: ${branch.objectId}`);
    console.log("----------------------------");
});

// Get all tags in a repository
const tags = await gitApi.getRefs(
    "abc12345-1234-1234-1234-123456789abc",
    "MyProject",
    "tags/"
);
console.log(`Found ${tags.length} tags`);
```

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| 401 Unauthorized | Invalid or expired authentication token | Ensure your PAT has the correct scopes (Code (Read)) and hasn't expired |
| 404 Not Found | Repository or project doesn't exist | Verify the repository and project names or IDs are correct |
| 403 Forbidden | Insufficient permissions | Ensure the user has access to view the specified repository |

## 4. getPullRequests

Retrieves pull requests matching specified criteria.

### Syntax

```typescript
getPullRequests(
    repositoryId: string,
    searchCriteria: GitPullRequestSearchCriteria,
    project?: string,
    maxCommentLength?: number,
    skip?: number,
    top?: number
): Promise<GitPullRequest[]>
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| repositoryId | string | Yes | The repository ID or name of the pull request's target branch. |
| searchCriteria | GitPullRequestSearchCriteria | Yes | Criteria used to search for pull requests. |
| project | string | No | Project ID or project name. Required if using repository name instead of ID. |
| maxCommentLength | number | No | Not used in current API version. |
| skip | number | No | Number of pull requests to skip, useful for pagination. |
| top | number | No | Number of pull requests to retrieve, useful for pagination. |

### GitPullRequestSearchCriteria Properties

| Property | Type | Description |
|----------|------|-------------|
| creatorId | string | Filter by pull requests created by this identity. |
| includeLinks | boolean | Whether to include _links field on shallow references. |
| repositoryId | string | Filter by pull requests targeting this repository. |
| reviewerId | string | Filter by pull requests that have this identity as a reviewer. |
| sourceRefName | string | Filter by pull requests from this branch. |
| status | PullRequestStatus | Filter by pull requests in this state. Default is Active. |
| targetRefName | string | Filter by pull requests targeting this branch. |

### Return Value

A Promise that resolves to an array of `GitPullRequest` objects. Each object contains properties such as:

| Property | Type | Description |
|----------|------|-------------|
| pullRequestId | number | The unique identifier of the pull request. |
| title | string | The title of the pull request. |
| description | string | The description of the pull request. |
| status | PullRequestStatus | The status of the pull request (Active, Abandoned, Completed). |
| createdBy | IdentityRef | The identity who created the pull request. |
| creationDate | Date | The date the pull request was created. |
| sourceRefName | string | The source branch of the pull request (e.g., "refs/heads/feature"). |
| targetRefName | string | The target branch of the pull request (e.g., "refs/heads/main"). |
| mergeStatus | PullRequestAsyncStatus | The current merge status of the pull request. |
| isDraft | boolean | Whether the pull request is in draft state. |

### Example

```typescript
const gitApi = await connection.getGitApi();

// Get active pull requests in a repository
const searchCriteria: GitPullRequestSearchCriteria = {
    status: 1, // 1 = Active
    repositoryId: "abc12345-1234-1234-1234-123456789abc"
};

const pullRequests = await gitApi.getPullRequests(
    "abc12345-1234-1234-1234-123456789abc",
    searchCriteria,
    "MyProject"
);

console.log(`Found ${pullRequests.length} active pull requests`);
pullRequests.forEach(pr => {
    console.log(`PR #${pr.pullRequestId}: ${pr.title}`);
    console.log(`Created by: ${pr.createdBy.displayName}`);
    console.log(`Source branch: ${pr.sourceRefName.replace("refs/heads/", "")}`);
    console.log(`Target branch: ${pr.targetRefName.replace("refs/heads/", "")}`);
    console.log("----------------------------");
});

// Get pull requests that target the main branch
const mainBranchPRs: GitPullRequestSearchCriteria = {
    targetRefName: "refs/heads/main",
    repositoryId: "abc12345-1234-1234-1234-123456789abc"
};

const mainPullRequests = await gitApi.getPullRequests(
    "abc12345-1234-1234-1234-123456789abc",
    mainBranchPRs,
    "MyProject"
);
console.log(`Found ${mainPullRequests.length} pull requests targeting main`);
```

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| 401 Unauthorized | Invalid or expired authentication token | Ensure your PAT has the correct scopes (Code (Read)) and hasn't expired |
| 404 Not Found | Repository or project doesn't exist | Verify the repository and project names or IDs are correct |
| 403 Forbidden | Insufficient permissions | Ensure the user has access to view pull requests in the specified repository |
| 400 Bad Request | Invalid search criteria | Check the searchCriteria object for invalid values or formats |

## 5. createPullRequest

Creates a new pull request.

### Syntax

```typescript
createPullRequest(
    gitPullRequestToCreate: GitPullRequest,
    repositoryId: string,
    project?: string,
    supportsIterations?: boolean
): Promise<GitPullRequest>
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| gitPullRequestToCreate | GitPullRequest | Yes | The pull request to create. Must include required fields. |
| repositoryId | string | Yes | The repository ID of the pull request's target branch. |
| project | string | No | Project ID or project name. Required if using repository name instead of ID. |
| supportsIterations | boolean | No | If true, subsequent pushes will be individually reviewable. Set to false for large PRs if this functionality isn't needed. |

### GitPullRequest Required Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| sourceRefName | string | Yes | The source branch (e.g., "refs/heads/feature"). |
| targetRefName | string | Yes | The target branch (e.g., "refs/heads/main"). |
| title | string | Yes | The title of the pull request. |
| description | string | No | The description of the pull request. |

### Return Value

A Promise that resolves to a `GitPullRequest` object representing the newly created pull request. It contains the same properties as described in the `getPullRequests` method.

### Example

```typescript
const gitApi = await connection.getGitApi();

// Create a new pull request
const pullRequestToCreate = {
    sourceRefName: "refs/heads/feature-branch",
    targetRefName: "refs/heads/main",
    title: "Add new feature XYZ",
    description: "This pull request adds the new XYZ feature with tests and documentation."
};

try {
    const newPullRequest = await gitApi.createPullRequest(
        pullRequestToCreate,
        "abc12345-1234-1234-1234-123456789abc",
        "MyProject"
    );
    
    console.log(`Pull request created successfully!`);
    console.log(`PR #${newPullRequest.pullRequestId}: ${newPullRequest.title}`);
    console.log(`URL: ${newPullRequest.url}`);
} catch (error) {
    console.error("Failed to create pull request:", error.message);
}
```

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| 401 Unauthorized | Invalid or expired authentication token | Ensure your PAT has the correct scopes (Code (Read & Write)) and hasn't expired |
| 404 Not Found | Repository, project, or branches don't exist | Verify that repository, project, and branch names are correct |
| 403 Forbidden | Insufficient permissions | Ensure the user has permissions to create pull requests |
| 400 Bad Request | Missing required fields or invalid values | Check the pull request object for missing required fields |
| 409 Conflict | A pull request already exists between these branches | Check for existing pull requests between the same branches |

## Summary

These five methods form the foundation for most Git operations in Azure DevOps. By mastering these methods, you'll be able to build powerful integrations, automations, and tools around your Git repositories.

For common code examples involving multiple methods, see the [Code Examples](./code-examples.md) page. For troubleshooting common errors, refer to the [Common Errors](./common-errors.md) page. 