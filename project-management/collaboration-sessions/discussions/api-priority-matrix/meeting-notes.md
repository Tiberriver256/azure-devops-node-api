# API Priority Matrix Meeting Notes

**Date**: March 15, 2023  
**Attendees**: 
- Alex (@API Specialist I)
- Taylor (@Senior Technical Writer)
- Marcus (Azure DevOps API Team Lead)
- Sarah (Senior Developer, Git API)
- David (Senior Developer, Work Item Tracking API)
- Jennifer (Senior Developer, Build API)
- Riley (@API Specialist II)
- Quinn (@API Specialist III)

## Meeting Objectives

1. Define priority of methods within each API client
2. Identify top 5-7 methods per client
3. Document common use cases
4. Unblock the API Priority Matrix task, particularly for Git API

## Discussion Summary

### Opening

**Alex**: Thank you everyone for joining today. We're working on the API Priority Matrix for the documentation project, and we need to identify the most important methods in each API client. This will guide our documentation efforts to focus on the most commonly used functionality first. We're particularly blocked on the Git API priorities, but I'd like to cover all main APIs while we're here.

### Git API Discussion

**Sarah (Git API Expert)**: Based on support tickets, usage analytics, and developer feedback, here are the Git API methods that developers use most frequently:

1. **getRepositories**: Almost every Git operation starts with identifying repositories
2. **getRepository**: Getting details of a specific repository
3. **getRefs**: Getting branches and tags
4. **getItems/getItemContent**: Accessing file content
5. **getPullRequests**: Pull requests are fundamental to modern Git workflows
6. **createPullRequest**: Creating PRs programmatically
7. **getCommits**: Retrieving commit history

**Alex**: That's very helpful. For these methods, what are the most common use cases you're seeing?

**Sarah**: 
- For `getRepositories` and `getRepository`, it's mostly listing available repositories and inventory tooling
- For `getRefs` and `getItems`, it's integration with build systems, custom reporting, and content extraction
- For pull request methods, it's automated PR creation from various systems and PR dashboards/reporting
- For commits, it's audit trails, reporting, and integration with issue trackers

### Work Item Tracking API Discussion

**David (Work Item Tracking API Expert)**: For Work Item Tracking, these are the most crucial methods:

1. **getWorkItem**: Retrieving specific work items
2. **getWorkItems**: Batch retrieving multiple work items
3. **createWorkItem**: Creating new work items
4. **updateWorkItem**: Updating existing work items
5. **getQueries**: Accessing saved queries
6. **queryByWiql**: Executing custom queries

**Common Use Cases**:
- Integration with external tools like Jira, GitHub Issues
- Custom reporting dashboards
- Automation of work item creation from other systems
- Status updates from CI/CD pipelines

### Build API Discussion

**Jennifer (Build API Expert)**: For the Build API, these methods are most frequently used:

1. **getDefinitions**: Retrieving build definitions
2. **getBuild**: Getting info about a specific build
3. **getBuilds**: Retrieving multiple builds
4. **queueBuild**: Triggering a build programmatically
5. **getBuildLogs**: Retrieving build logs

**Common Use Cases**:
- Custom dashboards and reporting
- Automating builds from external systems
- Integration with third-party CI/CD tools
- Post-build processing and notifications

### Release API Discussion

**Quinn**: From our experience, the Release API priorities should be:

1. **getReleaseDefinitions**: Getting release pipeline definitions
2. **getRelease**: Info about a specific release
3. **getReleases**: Getting multiple releases
4. **createRelease**: Creating a new release
5. **updateReleaseEnvironment**: Updating environment status

**Common Use Cases**:
- Release dashboards and reporting
- Automated release processes
- Integration with external deployment tools
- Compliance and audit trails

### Core API Discussion

**Taylor**: What about the Core API? It seems fundamental for working with projects and teams.

**Marcus**: Good point. For Core API, these are the most important:

1. **getProjects**: Listing all projects
2. **getProject**: Getting details of a specific project
3. **getTeams**: Listing teams in a project
4. **getTeam**: Getting team details
5. **getTeamMembers**: Retrieving team members

**Use Cases**:
- Organization mapping
- Permission management
- Project/team inventory tools
- Cross-project reporting

## Prioritization Framework

**Marcus**: I suggest we consider these factors when prioritizing:
1. **Frequency of Use**: How often the method is called
2. **Ecosystem Impact**: How central it is to DevOps workflows
3. **Complexity**: Whether documentation would significantly help implementation
4. **Support Burden**: Methods that generate many support tickets

**Taylor**: That makes sense. We should also consider the logical flow of APIs from a user perspective - what would someone need to know first?

**Alex**: Agreed. This will help us create a progressive learning experience in the documentation.

## Action Items

1. **Alex**: Compile the API Priority Matrix based on today's discussion
2. **Team**: Review the matrix within 2 days and provide any additional feedback
3. **Taylor**: Begin incorporating the priorities into the documentation planning

## Conclusion

With this input, we've unblocked the Git API priorities and gathered comprehensive information about priorities across all major API clients. This will guide our documentation efforts to focus on the most important and commonly used methods first.

**Alex**: Thank you everyone for your valuable input. This gives us a clear direction for our documentation priorities. 