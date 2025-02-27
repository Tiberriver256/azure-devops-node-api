# Azure DevOps Node API Glossary

**Navigation**: [Home](./index.md) > Glossary

This glossary provides standardized terminology for the Azure DevOps Node API documentation. Using consistent terminology improves clarity and helps users understand the documentation more easily.

## API Client Terminology

| Term | Definition | Usage Guidelines |
|------|------------|------------------|
| **API Client** | A class that provides methods for interacting with a specific Azure DevOps service area. | Use "API Client" (capitalized) when referring to the general concept. Use specific client names (e.g., "Git API client") when referring to a specific client. |
| **Git API** | The API client for interacting with Git repositories in Azure DevOps. | Always use "Git API" (not "GitApi" or "Git Client") when referring to this API client. |
| **Work Item Tracking API** | The API client for interacting with work items in Azure DevOps. | Always use "Work Item Tracking API" (not "WorkItemTrackingApi" or "Work Item API") when referring to this API client. |
| **Build API** | The API client for interacting with build pipelines in Azure DevOps. | Always use "Build API" (not "BuildApi" or "Build Client") when referring to this API client. |
| **Core API** | The API client for interacting with projects and teams in Azure DevOps. | Always use "Core API" (not "CoreApi" or "Core Client") when referring to this API client. |
| **Release API** | The API client for interacting with release pipelines in Azure DevOps. | Always use "Release API" (not "ReleaseApi" or "Release Client") when referring to this API client. |
| **Task Agent API** | The API client for interacting with agent pools and task groups in Azure DevOps. | Always use "Task Agent API" (not "TaskAgentApi" or "Task Agent Client") when referring to this API client. |
| **Test API** | The API client for interacting with test plans, suites, and cases in Azure DevOps. | Always use "Test API" (not "TestApi" or "Test Client") when referring to this API client. |
| **Wiki API** | The API client for interacting with wikis and wiki pages in Azure DevOps. | Always use "Wiki API" (not "WikiApi" or "Wiki Client") when referring to this API client. |

## Azure DevOps Terminology

| Term | Definition | Usage Guidelines |
|------|------------|------------------|
| **Azure DevOps** | Microsoft's development platform that provides version control, reporting, requirements management, project management, automated builds, testing and release management capabilities. | Always use "Azure DevOps" (not "ADO" or "Azure DevOps Services") when referring to the platform. |
| **Organization** | The root namespace in Azure DevOps that represents a collection of related projects. | Use "organization" (not "account" or "collection") when referring to this concept. |
| **Project** | A container for all work within a specific product or initiative in Azure DevOps. | Use "project" (not "team project") when referring to this concept. |
| **Team** | A group of individuals working together on a specific set of work within a project. | Use "team" when referring to this concept. |
| **Repository** | A container for source code in Azure DevOps Git. | Use "repository" (not "repo") when referring to this concept. |
| **Work Item** | A database record used to track work in Azure DevOps. | Use "work item" when referring to this concept. Always use two words, not combined as "workitem". |
| **Pull Request** | A mechanism for a developer to notify team members that they have completed a feature and want to merge their changes. | Use "pull request" (not "PR") when referring to this concept. |
| **Build Pipeline** | A set of processes used to automatically build and test code. | Use "build pipeline" (not "build definition") when referring to this concept. |
| **Release Pipeline** | A set of processes used to automatically deploy code. | Use "release pipeline" (not "release definition") when referring to this concept. |

## Technical Terminology

| Term | Definition | Usage Guidelines |
|------|------------|------------------|
| **WebApi** | The core class in the Azure DevOps Node API that provides access to all API clients. | Always use "WebApi" (capitalized, no space) when referring to this class. |
| **Personal Access Token (PAT)** | A security token used for authentication with Azure DevOps. | Use "Personal Access Token" on first reference, with "(PAT)" immediately following. Use "PAT" for subsequent references. |
| **Promise** | A JavaScript object representing the eventual completion or failure of an asynchronous operation. | Use "Promise" (capitalized) when referring to the JavaScript Promise object. |
| **async/await** | A JavaScript syntax for working with asynchronous code. | Use "async/await" (lowercase, with slash) when referring to this pattern. |
| **TypeScript** | A programming language that builds on JavaScript by adding static type definitions. | Always use "TypeScript" (capitalized, one word) when referring to this language. |
| **Node.js** | A JavaScript runtime built on Chrome's V8 JavaScript engine. | Always use "Node.js" (with the ".js" suffix) when referring to this runtime. |

## Integration Patterns Terminology

| Term | Definition | Usage Guidelines |
|------|------------|------------------|
| **Integration Pattern** | A reusable solution to a common integration problem in Azure DevOps. | Use "integration pattern" when referring to this concept. |
| **Cross-API Example** | An example that demonstrates the use of multiple API clients together. | Use "cross-API example" when referring to this concept. |
| **API Relationship** | The connection between different API clients in Azure DevOps. | Use "API relationship" when referring to this concept. |

## Documentation Structure Terminology

| Term | Definition | Usage Guidelines |
|------|------------|------------------|
| **API Reference** | Documentation that describes the classes, methods, and interfaces of the API. | Use "API reference" when referring to this type of documentation. |
| **Getting Started Guide** | Documentation that helps users begin using the API. | Use "Getting Started guide" when referring to this type of documentation. |
| **Tutorial** | Step-by-step instructions for completing a specific task with the API. | Use "tutorial" when referring to this type of documentation. |
| **Conceptual Guide** | Documentation that explains concepts and patterns in the API. | Use "conceptual guide" when referring to this type of documentation. |
| **Troubleshooting Guide** | Documentation that helps users solve common problems with the API. | Use "troubleshooting guide" when referring to this type of documentation. | 