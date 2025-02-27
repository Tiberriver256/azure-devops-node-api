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
| path | string | No | Filters to definitions under this folder path. Must be specified using backslashes (e.g., "\\TeamA\\MicroServices"). Use "\\" for the root folder. |
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
    
    try {
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
        
        return {
            success: true,
            definitions
        };
    } catch (error) {
        console.error(`Error getting build definitions: ${error.message}`);
        return {
            success: false,
            error: error.message
        };
    }
}

// Get all build definitions in a project
const definitions = await buildApi.getDefinitions("MyProject");
console.log(`Found ${definitions.length} build definitions`);

definitions.forEach(def => {
    console.log(`Definition: ${def.name} (ID: ${def.id})`);
    console.log(`Type: ${def.type === BuildInterfaces.DefinitionType.Build ? "Build" : "Release"}`);
    if (def.latestBuild) {
        console.log(`Latest build: ${def.latestBuild.buildNumber}`);
    }
});

// Get build definitions in a specific folder
const folderPath = "\\TeamA\\MicroServices";
const definitionsInFolder = await buildApi.getDefinitions("MyProject", undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined, folderPath);

console.log(`Found ${definitionsInFolder.length} definitions in ${folderPath}`);

// Get build definitions in the root folder
const rootDefinitions = await buildApi.getDefinitions("MyProject", undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined, "\\");

console.log(`Found ${rootDefinitions.length} definitions in the root folder`);

### Definition References vs. Full Definitions

An important distinction to understand is that the `getDefinitions` method returns `BuildDefinitionReference` objects by default, not complete definition objects with all properties. These references are lightweight objects containing basic information about the build definition.

To understand the difference:

1. **Build Definition Reference**: A lightweight representation that includes:
   - Basic properties (id, name, path, revision)
   - References to related entities (project, queue)
   - Latest build information (if requested with `includeLatestBuilds=true`)
   
2. **Full Build Definition**: A complete representation that includes everything in the reference plus:
   - All build steps/tasks
   - All variables and their values
   - Complete repository information
   - All triggers (continuous integration, scheduled, etc.)
   - Retention policy
   - Build options and demands

#### Getting Full Definition Details

To get the complete definition with all details, use the `getDefinition` (singular) method:

```typescript
async function getFullDefinitionDetails(definitionId: number) {
    // Setup connection and API client
    // ... (connection code) ...
    
    const buildApi = await connection.getBuildApi();
    
    // Get the full definition
    const projectName = "YourProject";
    try {
        const fullDefinition = await buildApi.getDefinition(
            projectName,
            definitionId,
            undefined, // revision (optional)
            undefined, // minMetricsTime (optional)
            undefined, // propertyFilters (optional)
            true       // includeLatestBuilds
        );
        
        console.log(`Definition: ${fullDefinition.name}`);
        
        // Access detailed properties only available in the full definition
        console.log(`Number of steps: ${fullDefinition.process?.phases[0]?.steps?.length || 0}`);
        console.log(`Number of variables: ${Object.keys(fullDefinition.variables || {}).length}`);
        console.log(`Continuous integration trigger enabled: ${fullDefinition.triggers?.some(t => t.triggerType === BuildInterfaces.DefinitionTriggerType.ContinuousIntegration)}`);
        
        return fullDefinition;
    } catch (error) {
        console.error(`Error retrieving definition: ${error.message}`);
        throw error;
    }
}
```

#### Performance Considerations

When working with build definitions:

- Use `getDefinitions` when you need basic information about multiple definitions
- Use `includeAllProperties=true` with `getDefinitions` only when you need all details for multiple definitions
- Use `getDefinition` (singular) when you need complete details about a specific definition
- Be aware that retrieving full definitions is more resource-intensive and slower than working with references

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

### Setting Build Variables

To set variables for a build, use the `parameters` property of the build object. The `parameters` property takes a stringified JSON object containing the variable names and values:

```typescript
import * as azdev from "azure-devops-node-api";
import * as BuildInterfaces from "azure-devops-node-api/interfaces/BuildInterfaces";

async function queueBuildWithVariables(definitionId: number, sourceBranch: string) {
    // Setup connection
    const orgUrl = "https://dev.azure.com/yourorganization";
    const token = process.env.AZURE_DEVOPS_PAT; // Personal Access Token
    
    const authHandler = azdev.getPersonalAccessTokenHandler(token);
    const connection = new azdev.WebApi(orgUrl, authHandler);
    
    // Get Build API client
    const buildApi = await connection.getBuildApi();
    
    // Define variables to pass to the build
    const buildVariables = {
        "DEPLOY_ENVIRONMENT": "Production",
        "ENABLE_DEBUG": "false",
        "VERSION": "1.2.3",
        "RUN_INTEGRATION_TESTS": "true"
    };
    
    // Create build object with variables
    const build: BuildInterfaces.Build = {
        definition: {
            id: definitionId
        },
        sourceBranch: sourceBranch,
        reason: BuildInterfaces.BuildReason.Manual,
        priority: BuildInterfaces.BuildPriority.Normal,
        parameters: JSON.stringify(buildVariables)
    };
    
    // Queue the build
    const projectName = "YourProject";
    try {
        const queuedBuild = await buildApi.queueBuild(build, projectName);
        
        console.log(`Build queued successfully with custom variables!`);
        console.log(`Build ID: ${queuedBuild.id}`);
        console.log(`Build Number: ${queuedBuild.buildNumber}`);
        
        return queuedBuild;
    } catch (error) {
        console.error(`Error queuing build: ${error.message}`);
        throw error;
    }
}
```

**Note**: Only variables that are defined in the build definition can be set when queuing a build. Setting undefined variables will have no effect.

### Branch Policy Considerations

When queueing builds, be aware that branch policies can impact whether a build can be successfully queued:

1. **Builds for Protected Branches**: If you try to queue a build for a branch that has branch policies enabled (like requiring a pull request), the build may be rejected depending on your permissions and the specific policies.

2. **Pull Request Validation Builds**: Builds triggered as part of a pull request validation policy behave differently than manually queued builds. They often have different security contexts and variable values.

3. **Build Validation Policy**: If a branch has a build validation policy, you may need to queue builds through a pull request rather than directly against the branch.

Example of queueing a build for a feature branch that will eventually be merged into a protected branch:

```typescript
async function queueFeatureBranchBuild(definitionId: number) {
    // Setup connection and API client
    // ... (connection code) ...
    
    const buildApi = await connection.getBuildApi();
    
    // Use feature branch instead of protected branch
    const build: BuildInterfaces.Build = {
        definition: {
            id: definitionId
        },
        sourceBranch: "refs/heads/feature/my-feature", // Use feature branch
        reason: BuildInterfaces.BuildReason.Manual
    };
    
    try {
        const queuedBuild = await buildApi.queueBuild(build, "YourProject");
        console.log(`Build queued successfully on feature branch`);
        return queuedBuild;
    } catch (error) {
        if (error.message && error.message.includes("branch policy")) {
            console.error("Failed due to branch policy. Try using a feature branch instead.");
        } else {
            console.error(`Error queueing build: ${error.message}`);
        }
        throw error;
    }
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
        
        // Display log metadata
        logs.forEach(log => {
            console.log(`Log #${log.id}: ${log.type}`);
            console.log(`  Created: ${log.createdOn}`);
            console.log(`  Lines: ${log.lineCount}`);
            console.log(`  URL: ${log.url}`);
        });
        
        // Get content for the first log
        if (logs.length > 0) {
            const logContent = await buildApi.getBuildLogLines(projectName, buildId, logs[0].id);
            console.log("\nSample log content (first 10 lines):");
            logContent.slice(0, 10).forEach((line, index) => {
                console.log(`${index + 1}: ${line}`);
            });
        }
        
        return logs;
    } catch (error) {
        console.error(`Error retrieving build logs: ${error.message}`);
        throw error;
    }
}

### Understanding Log Structure

The logs returned by `getBuildLogs` represent metadata about the available logs, not the actual log content. To get the log content, you need to use the `getBuildLogLines` method:

```typescript
async getBuildLogLines(
    project: string,
    buildId: number,
    logId: number,
    startLine?: number,
    endLine?: number
): Promise<string[]>
```

#### Log Types and Their Content

Build logs in Azure DevOps follow a structured format with different types:

1. **Build Process Logs (id: 1)**: Overall build process logs, including preparation and finalization steps.
2. **Task Logs**: Each task in your build pipeline generates its own log with a unique ID.
3. **Console Logs**: Raw console output from build agents.
4. **Warning and Error Logs**: Specific logs containing only warnings or errors.

#### Parsing Log Content Effectively

Log content often contains ANSI color codes and formatting. When displaying logs, you might want to:

1. **Strip ANSI Color Codes**:
   ```typescript
   function stripAnsiCodes(text: string): string {
       return text.replace(/\u001b\[\d+m/g, '');
   }
   ```

2. **Parse Structured Data**:
   ```typescript
   function extractJsonFromLog(logLines: string[]): any[] {
       const results = [];
       for (const line of logLines) {
           // Look for lines with JSON objects (often wrapped in special markers)
           if (line.includes('##[json]')) {
               try {
                   const jsonStr = line.substring(line.indexOf('##[json]') + 8);
                   results.push(JSON.parse(jsonStr));
               } catch (e) {
                   // Handle parsing errors
               }
           }
       }
       return results;
   }
   ```

3. **Search for Specific Patterns**:
   ```typescript
   function findErrorsInLog(logLines: string[]): string[] {
       return logLines.filter(line => 
           line.toLowerCase().includes('error') || 
           line.includes('##[error]'));
   }
   ```

#### Handling Large Logs

For builds with large logs, use the `startLine` and `endLine` parameters of `getBuildLogLines` to retrieve logs in chunks:

```typescript
// Get logs in chunks of 1000 lines
async function getFullLogInChunks(projectName: string, buildId: number, logId: number) {
    const chunkSize = 1000;
    let allLines: string[] = [];
    let startLine = 0;
    let hasMoreLines = true;
    
    while (hasMoreLines) {
        const endLine = startLine + chunkSize - 1;
        const lines = await buildApi.getBuildLogLines(projectName, buildId, logId, startLine, endLine);
        
        allLines = allLines.concat(lines);
        
        if (lines.length < chunkSize) {
            hasMoreLines = false;
        } else {
            startLine += chunkSize;
        }
    }
    
    return allLines;
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