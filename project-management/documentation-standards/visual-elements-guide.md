# Visual Elements Guide for Documentation Templates

## Overview

This guide defines standardized visual elements and patterns for our documentation templates. All elements are designed to work within GitHub-flavored markdown constraints while enhancing readability, navigation, and user experience.

## Technical Constraints

Our documentation is hosted exclusively as markdown files in GitHub, which imposes the following constraints:

- No custom HTML/CSS styling (except limited HTML tags like `<details>`)
- No JavaScript or interactive elements
- No custom background colors
- No complex layout controls
- Limited to GitHub's native markdown rendering

## Standardized Elements

### Navigation Elements

#### Breadcrumb Navigation

Use this format at the top of every document:

```markdown
**Navigation**: [Home](../index.md) > [Category](./index.md) > Current Page
```

Example:
> **Navigation**: [Home](../index.md) > [API Reference](./index.md) > WorkItemTrackingApi

#### Table of Contents

For longer documents, include a linked table of contents using GitHub's auto-generated heading anchors. For tutorials and troubleshooting guides, include time estimates and severity indicators where applicable:

```markdown
## Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites) (⏱️ 2 minutes)
- [Basic Implementation](#basic-implementation) (⏱️ 10 minutes)
- [Advanced Features](#advanced-features) (⏱️ 15 minutes)
- [Troubleshooting](#troubleshooting)
  - [Authentication Issues](#authentication-issues) 🔴
  - [Permission Issues](#permission-issues) 🟡
  - [Configuration Issues](#configuration-issues) 🟢
```

For documents with extensive code examples, include a code-specific table of contents:

```markdown
### Code Examples in This Document

1. [Basic Authentication](#basic-authentication) (10 lines)
2. [Custom Header Implementation](#custom-header) (25 lines)
3. [Complete Example](#complete-example) (50 lines)
```

#### See Also Section

Include a consistent "See Also" section at the end of each document:

```markdown
## See Also

- [Related Topic 1](./related-topic-1.md)
- [Related Topic 2](./related-topic-2.md)
- [External Resource](https://example.com)
```

### Visual Indicators

#### Method Importance Indicators

Use these emoji indicators consistently for API methods:

| Emoji | Meaning | Usage |
|-------|---------|-------|
| ⭐ | Commonly Used | Essential methods that most developers will need |
| 🔄 | Core Operation | Fundamental operations for the API component |
| 🆕 | Creation Method | Methods that create new resources |
| 🔍 | Query Method | Methods that retrieve or search for resources |
| 🔧 | Configuration | Methods for setting up or configuring resources |
| ⚠️ | Caution Required | Methods that require special attention or care |

Example:
```markdown
## Methods

### ⭐ getWorkItems
Retrieves multiple work items by ID.

### 🆕 createWorkItem
Creates a new work item.
```

#### Time Estimates

Add time estimates to tutorial sections using this format:

```markdown
## Step 2: Create a connection to Azure DevOps (⏱️ 5 minutes)
```

#### Progress Indicators

For tutorials, include a progress indicator at the top of each section:

```markdown
**Progress**: [✓] Setup → [✓] Authentication → [✓] Configuration → [ ] Implementation → [ ] Testing
```

### Content Organization

#### Visual Separation

Use horizontal rules (`---`) to create visual separation between major sections:

```markdown
## Section 1

Content for section 1...

---

## Section 2

Content for section 2...
```

#### Severity Levels in Troubleshooting Guides

Use consistent formatting for severity levels:

```markdown
## High Severity Issues

### Authentication Failure
🔴 **High Severity** | ⏱️ 5 minutes

[Issue details...]

---

## Medium Severity Issues

### Rate Limiting
🟡 **Medium Severity** | ⏱️ 10 minutes

[Issue details...]

---

## Low Severity Issues

### Missing Optional Parameters
🟢 **Low Severity** | ⏱️ 2 minutes

[Issue details...]
```

#### Quick Fix Summary

Use GitHub's collapsible sections for quick fix summaries:

```markdown
<details>
<summary><b>Quick Fix Summary</b></summary>

- 🔴 Authentication Failure: Regenerate your PAT token
- 🟡 Rate Limiting: Implement exponential backoff
- 🟢 Missing Parameters: Check required parameters documentation

</details>
```

#### Success Criteria Indicators

For tutorials and guides, include clear success criteria with checkable indicators:

```markdown
📊 **Success Criteria**:
□ Connection established to Azure DevOps
□ Authentication successful
□ Work item created successfully
□ Response contains expected data
```

Users can manually check (▣) items as they complete them:

```markdown
📊 **Success Criteria**:
▣ Project initialized with package.json
▣ Dependencies installed successfully
□ Configuration file created
```

### Content Type Indicators

Use these emoji consistently as visual anchors for different content types:

| Emoji | Content Type |
|-------|-------------|
| 📋 | Prerequisites |
| 🔍 | Examples |
| ⚠️ | Warnings/Cautions |
| 💡 | Tips |
| 📝 | Notes |
| 🔗 | Related Links |
| 🛠️ | Troubleshooting |
| 📊 | Data/Results |

Example:
```markdown
## Creating a Work Item

📋 **Prerequisites**:
- Azure DevOps account with appropriate permissions
- Personal Access Token with Work Items (Read, Write) scope

🔍 **Example**:
```typescript
const workItem = await workItemTrackingApi.createWorkItem(
  null,
  patchDocument,
  project,
  workItemType
);
```

⚠️ **Caution**:
Ensure all required fields are included in your patch document.

💡 **Tip**:
Use environment variables to store your PAT token securely.
```

## Implementation Guidelines

### General Principles

1. **Consistency**: Apply these elements consistently across all documentation
2. **Minimalism**: Use visual elements purposefully, not excessively
3. **Clarity**: Ensure visual elements enhance rather than distract from content
4. **Accessibility**: Use text alternatives for all visual elements
5. **Maintainability**: Keep patterns simple enough to be easily maintained

### Template-Specific Implementation

#### API Reference Template

- Add breadcrumb navigation at the top
- Use method importance indicators for all methods
- Group methods by category with visual separation
- Include "See Also" section linking to related components

#### Tutorial Template

- Add breadcrumb navigation at the top
- Include progress indicator below breadcrumbs
- Add time estimates to each step heading
- Use content type indicators consistently
- Include "Next Steps" section at the end

#### Troubleshooting Guide Template

- Add breadcrumb navigation at the top
- Group issues by severity with visual separation
- Include quick fix summary at the top of each category
- Use severity and time indicators consistently
- Include decision tree or flowchart as static image

#### Template Selection Guide

- Add breadcrumb navigation at the top
- Include table of contents for navigation
- Add feature comparison table
- Include decision tree as static image
- Use content type indicators consistently

## Examples

### API Reference Example

```markdown
**Navigation**: [Home](../index.md) > [API Reference](./index.md) > WorkItemTrackingApi

# WorkItemTrackingApi

The WorkItemTrackingApi provides access to work items, queries, and other work item tracking functionality.

## Contents

- [Overview](#overview)
- [Methods](#methods)
- [Examples](#examples)

## Overview

The WorkItemTrackingApi is one of the most complex APIs in the Azure DevOps Node.js client.

---

## Methods

### Work Item Methods

#### ⭐ getWorkItem
Retrieves a single work item by ID.

#### ⭐ getWorkItems
Retrieves multiple work items by ID.

#### 🆕 createWorkItem
Creates a new work item.

### Query Methods

#### 🔍 executeQuery
Executes a work item query.

---

## Examples

🔍 **Basic Usage**:
```typescript
const workItems = await workItemTrackingApi.getWorkItems([1, 2, 3]);
```

## See Also

- [Work Item Tracking Concepts](../concepts/work-item-tracking.md)
- [Creating Work Items Tutorial](../tutorials/creating-work-items.md)
```

### Tutorial Example

```markdown
**Navigation**: [Home](../index.md) > [Tutorials](./index.md) > Creating Work Items

# Creating Work Items Tutorial

**Progress**: [✓] Setup → [✓] Authentication → [ ] Creating Work Items → [ ] Advanced Options → [ ] Error Handling

## Contents

- [Prerequisites](#prerequisites)
- [Step 1: Setup](#step-1-setup-️-5-minutes)
- [Step 2: Authentication](#step-2-authentication-️-10-minutes)
- [Step 3: Creating Work Items](#step-3-creating-work-items-️-15-minutes)

## Prerequisites

📋 **Required**:
- Azure DevOps account with appropriate permissions
- Node.js installed (version 12 or higher)
- Personal Access Token with Work Items (Read, Write) scope

---

## Step 1: Setup (⏱️ 5 minutes)

Install the required packages:

```bash
npm install azure-devops-node-api
```

💡 **Tip**:
Use a package.json file to manage your dependencies.

---

## Step 2: Authentication (⏱️ 10 minutes)

Create a connection to Azure DevOps:

```typescript
const azdev = require('azure-devops-node-api');

// Your organization URL and personal access token
const orgUrl = 'https://dev.azure.com/your-organization';
const token = process.env.AZURE_DEVOPS_TOKEN;

// Create authorization handler
const authHandler = azdev.getPersonalAccessTokenHandler(token);

// Create connection
const connection = new azdev.WebApi(orgUrl, authHandler);
```

⚠️ **Caution**:
Never hardcode your PAT token in your source code.

---

## Step 3: Creating Work Items (⏱️ 15 minutes)

[Content continues...]

## See Also

- [Work Item Tracking API Reference](../api-reference/work-item-tracking-api.md)
- [Updating Work Items Tutorial](./updating-work-items.md)
```

## Maintenance Guidelines

1. **Updates**: When adding new visual elements, update this guide first
2. **Consistency Check**: Periodically review documentation against this guide
3. **Feedback Collection**: Gather user feedback on visual elements' effectiveness
4. **Version Control**: Document significant changes to visual elements in this guide's version history

## Version History

| Version | Date | Updated By | Changes |
|---------|------|------------|---------|
| 1.0 | 2024-07-22 | Taylor | Initial version |