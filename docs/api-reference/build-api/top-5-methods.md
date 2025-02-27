# Top 5 Build API Methods

This document provides detailed information about the most commonly used methods in the Azure DevOps Build API.

## getDefinitions

Retrieves a list of build definitions from a project.

### Syntax

```typescript
async getDefinitions(
    project: string,
    name?: string,
    repositoryId?: string,
    repositoryType?: string,
    queryOrder?: BuildInterfaces.DefinitionQueryOrder,
    top?: number,
    continuationToken?: string,
    minMetricsTime?: Date,
    definitionIds?: number[],
    path?: string,
    builtAfter?: Date,
    notBuiltAfter?: Date,
    includeAllProperties?: boolean,
    includeLatestBuilds?: boolean,
    taskIdFilter?: string,
    processType?: number,
    yamlFilename?: string
): Promise<VSSInterfaces.PagedList<BuildInterfaces.BuildDefinitionReference>>
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| project | string | Yes | Project ID or project name |
| name | string | No | Filters to definitions whose names match this pattern |
| repositoryId | string | No | Filters to definitions that use this repository |
| repositoryType | string | No | Filters to definitions that have a repository of this type |
| queryOrder | BuildInterfaces.DefinitionQueryOrder | No | Indicates the order in which definitions should be returned |
| top | number | No | The maximum number of definitions to return |
| continuationToken | string | No | A continuation token for pagination |
| minMetricsTime | Date | No | If specified, indicates the date from which metrics should be included |
| definitionIds | number[] | No | A comma-delimited list of definition IDs to retrieve |
| path | string | No | Filters to definitions under this folder |
| builtAfter | Date | No | Filters to definitions that have builds after this date |
| notBuiltAfter | Date | No | Filters to definitions that do not have builds after this date |
| includeAllProperties | boolean | No | Whether to return the full definitions (true) or shallow representations (false, default) |
| includeLatestBuilds | boolean | No | Whether to return the latest and latest completed builds for this definition |
| taskIdFilter | string | No | Filters to definitions that use the specified task |
| processType | number | No | Filters to definitions with the given process type |
| yamlFilename | string | No | Filters to YAML definitions that match the given filename (requires includeAllProperties=true) |

### Return Value

Returns a Promise that resolves to a paged list of `BuildDefinitionReference` objects. Each object contains:

- **id**: The ID of the build definition
- **name**: The name of the build definition
- **path**: The folder path of the definition
- **project**: A reference to the project
- **revision**: The definition revision number
- **type**: The type of the definition
- **authoredBy**: The author of the definition
- **queue**: The default queue for builds run against this definition
- **latestBuild**: A reference to the latest build
- **latestCompletedBuild**: A reference to the latest completed build

### Example Usage

```typescript
import * as azdev from "azure-devops-node-api";

async function getBuildDefinitions() {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT; // Personal Access Token
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get Build API client
    const buildApi = await connection.getBuildApi();
    
    // Get build definitions
    const projectName = "YourProject";
    const definitions = await buildApi.getDefinitions(
        projectName,
        undefined, // name
        undefined, // repositoryId
        undefined, // repositoryType
        undefined, // queryOrder
        10,        // top - limit to 10 results
        undefined, // continuationToken
        undefined, // minMetricsTime
        undefined, // definitionIds
        undefined, // path
        undefined, // builtAfter
        undefined, // notBuiltAfter
        true,      // includeAllProperties
        true       // includeLatestBuilds
    );
    
    console.log(`Found ${definitions.length} build definitions`);
    
    // Display build definition details
    definitions.forEach(def => {
        console.log(`Definition: ${def.name} (ID: ${def.id})`);
        console.log(`  Path: ${def.path}`);
        console.log(`  Latest Build: ${def.latestBuild ? def.latestBuild.buildNumber : 'None'}`);
        console.log(`  Latest Status: ${def.latestBuild ? def.latestBuild.status : 'N/A'}`);
        console.log(`  Latest Result: ${def.latestBuild ? def.latestBuild.result : 'N/A'}`);
        console.log('---');
    });
}
```

## getBuild

Retrieves a specific build by ID.

### Syntax

```typescript
async getBuild(
    project: string,
    buildId: number,
    propertyFilters?: string
): Promise<BuildInterfaces.Build>
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| project | string | Yes | Project ID or project name |
| buildId | number | Yes | The ID of the build to retrieve |
| propertyFilters | string | No | A comma-delimited list of properties to include in the result |

### Return Value

Returns a Promise that resolves to a `Build` object, which contains:

- **id**: The ID of the build
- **buildNumber**: The build number/name
- **status**: The build status (None, InProgress, Completed, Cancelling, Postponed, NotStarted)
- **result**: The build result (None, Succeeded, PartiallySucceeded, Failed, Canceled)
- **queueTime**: The time the build was queued
- **startTime**: The time the build started
- **finishTime**: The time the build finished
- **definition**: A reference to the build definition
- **project**: A reference to the project
- **requestedBy**: The identity of the user who requested the build
- **repository**: Information about the repository
- **logs**: Information about the build logs
- **sourceBranch**: The source branch for the build
- **sourceVersion**: The source version (commit ID)
- **priority**: The build priority
- **reason**: The reason the build was created

### Example Usage

```typescript
import * as azdev from "azure-devops-node-api";

async function getBuildDetails(buildId: number) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT; // Personal Access Token
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get Build API client
    const buildApi = await connection.getBuildApi();
    
    // Get build details
    const projectName = "YourProject";
    try {
        const build = await buildApi.getBuild(projectName, buildId);
        
        console.log(`Build ${build.buildNumber} (ID: ${build.id})`);
        console.log(`Status: ${getBuildStatusText(build.status)}`);
        console.log(`Result: ${getBuildResultText(build.result)}`);
        console.log(`Started: ${build.startTime}`);
        console.log(`Finished: ${build.finishTime}`);
        console.log(`Requested by: ${build.requestedBy?.displayName}`);
        console.log(`Source branch: ${build.sourceBranch}`);
        console.log(`Source version: ${build.sourceVersion}`);
        
        return build;
    } catch (error) {
        console.error(`Error retrieving build ${buildId}: ${error.message}`);
        throw error;
    }
}

// Helper functions to convert enum values to readable text
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

## getBuilds

Retrieves a list of builds based on various filter criteria.

### Syntax

```typescript
async getBuilds(
    project: string,
    definitions?: number[],
    queues?: number[],
    buildNumber?: string,
    minTime?: Date,
    maxTime?: Date,
    requestedFor?: string,
    reasonFilter?: BuildInterfaces.BuildReason,
    statusFilter?: BuildInterfaces.BuildStatus,
    resultFilter?: BuildInterfaces.BuildResult,
    tagFilters?: string[],
    properties?: string[],
    top?: number,
    continuationToken?: string,
    maxBuildsPerDefinition?: number,
    deletedFilter?: BuildInterfaces.QueryDeletedOption,
    queryOrder?: BuildInterfaces.BuildQueryOrder,
    branchName?: string,
    buildIds?: number[],
    repositoryId?: string,
    repositoryType?: string
): Promise<VSSInterfaces.PagedList<BuildInterfaces.Build>>
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| project | string | Yes | Project ID or project name |
| definitions | number[] | No | A list of definition IDs to filter by |
| queues | number[] | No | A list of queue IDs to filter by |
| buildNumber | string | No | Filter by build number (append * for prefix search) |
| minTime | Date | No | Filter to builds after this date |
| maxTime | Date | No | Filter to builds before this date |
| requestedFor | string | No | Filter to builds requested by this user |
| reasonFilter | BuildInterfaces.BuildReason | No | Filter by the reason the build was created |
| statusFilter | BuildInterfaces.BuildStatus | No | Filter by build status |
| resultFilter | BuildInterfaces.BuildResult | No | Filter by build result |
| tagFilters | string[] | No | Filter to builds with these tags |
| properties | string[] | No | A list of properties to retrieve |
| top | number | No | Maximum number of builds to return |
| continuationToken | string | No | Continuation token for pagination |
| maxBuildsPerDefinition | number | No | Maximum number of builds to return per definition |
| deletedFilter | BuildInterfaces.QueryDeletedOption | No | Whether to include deleted builds |
| queryOrder | BuildInterfaces.BuildQueryOrder | No | The order in which to return builds |
| branchName | string | No | Filter to builds from this branch |
| buildIds | number[] | No | A list of build IDs to retrieve |
| repositoryId | string | No | Filter to builds from this repository |
| repositoryType | string | No | Filter to builds from repositories of this type |

### Return Value

Returns a Promise that resolves to a paged list of `Build` objects. See the `getBuild` method for details on the `Build` object properties.

### Example Usage

```typescript
import * as azdev from "azure-devops-node-api";
import * as BuildInterfaces from "azure-devops-node-api/interfaces/BuildInterfaces";

async function getRecentBuilds() {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT; // Personal Access Token
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get Build API client
    const buildApi = await connection.getBuildApi();
    
    // Get recent builds
    const projectName = "YourProject";
    const builds = await buildApi.getBuilds(
        projectName,
        undefined,                                  // definitions
        undefined,                                  // queues
        undefined,                                  // buildNumber
        new Date(Date.now() - 7 * 24 * 60 * 60000), // minTime (7 days ago)
        undefined,                                  // maxTime
        undefined,                                  // requestedFor
        undefined,                                  // reasonFilter
        BuildInterfaces.BuildStatus.Completed,      // statusFilter
        undefined,                                  // resultFilter
        undefined,                                  // tagFilters
        undefined,                                  // properties
        10,                                         // top
        undefined,                                  // continuationToken
        undefined,                                  // maxBuildsPerDefinition
        undefined,                                  // deletedFilter
        BuildInterfaces.BuildQueryOrder.FinishTimeDescending // queryOrder
    );
    
    console.log(`Found ${builds.length} builds in the last 7 days`);
    
    // Display build details
    builds.forEach(build => {
        console.log(`Build ${build.buildNumber} (ID: ${build.id})`);
        console.log(`  Definition: ${build.definition.name}`);
        console.log(`  Status: ${getBuildStatusText(build.status)}`);
        console.log(`  Result: ${getBuildResultText(build.result)}`);
        console.log(`  Started: ${build.startTime}`);
        console.log(`  Finished: ${build.finishTime}`);
        console.log(`  Branch: ${build.sourceBranch}`);
        console.log('---');
    });
}

// Helper functions to convert enum values to readable text
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

## queueBuild

Queues a new build for a specified definition.

### Syntax

```typescript
async queueBuild(
    build: BuildInterfaces.Build,
    project: string,
    ignoreWarnings?: boolean,
    checkInTicket?: string,
    sourceBuildId?: number,
    definitionId?: number
): Promise<BuildInterfaces.Build>
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| build | BuildInterfaces.Build | Yes | The build to queue |
| project | string | Yes | Project ID or project name |
| ignoreWarnings | boolean | No | Whether to ignore warnings |
| checkInTicket | string | No | The check-in ticket |
| sourceBuildId | number | No | The source build ID |
| definitionId | number | No | Optional definition ID to queue a build without a body (ignored if there's a valid body) |

### Return Value

Returns a Promise that resolves to a `Build` object representing the queued build. See the `getBuild` method for details on the `Build` object properties.

### Example Usage

```typescript
import * as azdev from "azure-devops-node-api";
import * as BuildInterfaces from "azure-devops-node-api/interfaces/BuildInterfaces";

async function queueNewBuild(definitionId: number, sourceBranch: string) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT; // Personal Access Token
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get Build API client
    const buildApi = await connection.getBuildApi();
    
    // Create build object
    const build: BuildInterfaces.Build = {
        definition: {
            id: definitionId
        },
        sourceBranch: sourceBranch,
        reason: BuildInterfaces.BuildReason.Manual,
        priority: BuildInterfaces.BuildPriority.Normal
    };
    
    // Queue the build
    const projectName = "YourProject";
    try {
        const queuedBuild = await buildApi.queueBuild(build, projectName);
        
        console.log(`Build queued successfully!`);
        console.log(`Build ID: ${queuedBuild.id}`);
        console.log(`Build Number: ${queuedBuild.buildNumber}`);
        console.log(`Status: ${getBuildStatusText(queuedBuild.status)}`);
        console.log(`URL: ${queuedBuild._links.web.href}`);
        
        return queuedBuild;
    } catch (error) {
        console.error(`Error queuing build: ${error.message}`);
        throw error;
    }
}

// Helper function to convert enum values to readable text
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
```

## getBuildLogs

Retrieves the logs for a specific build.

### Syntax

```typescript
async getBuildLogs(
    project: string,
    buildId: number
): Promise<BuildInterfaces.BuildLog[]>
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| project | string | Yes | Project ID or project name |
| buildId | number | Yes | The ID of the build |

### Return Value

Returns a Promise that resolves to an array of `BuildLog` objects. Each `BuildLog` object contains:

- **id**: The ID of the log
- **type**: The type of the log location
- **url**: A full link to the log resource
- **createdOn**: The date and time the log was created
- **lastChangedOn**: The date and time the log was last changed
- **lineCount**: The number of lines in the log

### Example Usage

```typescript
import * as azdev from "azure-devops-node-api";

async function getBuildLogsAndContent(buildId: number) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT; // Personal Access Token
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get Build API client
    const buildApi = await connection.getBuildApi();
    
    // Get build logs
    const projectName = "YourProject";
    try {
        const logs = await buildApi.getBuildLogs(projectName, buildId);
        
        console.log(`Found ${logs.length} log files for build ${buildId}`);
        
        // Display log information
        for (const log of logs) {
            console.log(`Log ID: ${log.id}`);
            console.log(`Type: ${log.type}`);
            console.log(`Created: ${log.createdOn}`);
            console.log(`Line count: ${log.lineCount}`);
            console.log(`URL: ${log.url}`);
            
            // Optionally retrieve the actual log content
            if (log.id !== undefined) {
                try {
                    const logLines = await buildApi.getBuildLogLines(projectName, buildId, log.id);
                    console.log(`Log content (first 5 lines):`);
                    logLines.slice(0, 5).forEach((line, i) => {
                        console.log(`  ${i+1}: ${line}`);
                    });
                    console.log(`  ... (${logLines.length - 5} more lines)`);
                } catch (logError) {
                    console.error(`Error retrieving log content: ${logError.message}`);
                }
            }
            
            console.log('---');
        }
        
        return logs;
    } catch (error) {
        console.error(`Error retrieving build logs: ${error.message}`);
        throw error;
    }
}
```

## Related Methods

In addition to the top 5 methods, these related methods are also useful when working with builds:

- **getBuildLog**: Gets an individual log file for a build
- **getBuildLogLines**: Gets the content of a log file as an array of strings
- **getBuildLogsZip**: Gets all logs for a build as a zip file
- **getArtifacts**: Gets the artifacts for a build
- **getArtifact**: Gets a specific artifact for a build
- **getArtifactContentZip**: Gets the content of an artifact as a zip file
- **updateBuild**: Updates a build (e.g., to change its status or tags)
- **deleteBuild**: Deletes a build
- **getBuildChanges**: Gets the changes associated with a build 