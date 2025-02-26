# Template Implementation Guidelines

## Overview

This document provides step-by-step guidelines for implementing the visual elements and enhancements identified in the template review workshop. These guidelines ensure consistent application of improvements across all documentation templates.

## Prerequisites

Before implementing these enhancements, ensure you have:

1. Reviewed the [Visual Elements Guide](./visual-elements-guide.md)
2. Familiarized yourself with the [Documentation Technology Stack](./docs-tech-stack.md)
3. Understood the constraints of GitHub-flavored markdown

## Implementation Process

Follow this process when updating templates:

1. **Backup**: Create a backup of the template before making changes
2. **Implement**: Apply changes following the guidelines below
3. **Test**: Preview the changes in GitHub to ensure proper rendering
4. **Review**: Have another team member review the changes
5. **Commit**: Commit the changes with a descriptive message

## Common Enhancements for All Templates

### 1. Add Breadcrumb Navigation

Add this at the top of every document:

```markdown
**Navigation**: [Home](../index.md) > [Category](./index.md) > Current Page
```

Ensure the links are correct relative to the document's location in the repository.

### 2. Add Table of Contents

For documents longer than 3 sections, add a table of contents:

```markdown
## Contents

- [Section 1](#section-1)
- [Section 2](#section-2)
- [Section 3](#section-3)
```

### 3. Add See Also Section

Add this at the end of every document:

```markdown
## See Also

- [Related Topic 1](./related-topic-1.md)
- [Related Topic 2](./related-topic-2.md)
```

### 4. Add Visual Separation

Add horizontal rules (`---`) between major sections:

```markdown
## Section 1

Content for section 1...

---

## Section 2

Content for section 2...
```

## Template-Specific Enhancements

### API Reference Template

#### 1. Add Method Importance Indicators

For each method, add an appropriate emoji indicator:

```markdown
### ⭐ getWorkItems
Retrieves multiple work items by ID.

### 🆕 createWorkItem
Creates a new work item.
```

Use these indicators consistently:
- ⭐ - Commonly used methods
- 🔄 - Core operations
- 🆕 - Creation methods
- 🔍 - Query methods
- 🔧 - Configuration methods
- ⚠️ - Methods requiring caution

#### 2. Group Methods by Category

Organize methods into logical categories with headings:

```markdown
## Methods

### Work Item Methods

#### ⭐ getWorkItem
...

#### ⭐ getWorkItems
...

### Query Methods

#### 🔍 executeQuery
...
```

#### 3. Add Content Type Indicators for Examples

Use the 🔍 emoji to mark examples:

```markdown
## Examples

🔍 **Basic Usage**:
```typescript
const workItems = await workItemTrackingApi.getWorkItems([1, 2, 3]);
```
```

### Tutorial Template

#### 1. Add Progress Indicator

Add this below the title:

```markdown
**Progress**: [✓] Setup → [✓] Authentication → [ ] Creating Work Items → [ ] Advanced Options → [ ] Error Handling
```

Update the checkmarks to reflect the current step.

#### 2. Add Time Estimates

Add time estimates to each step heading:

```markdown
## Step 2: Create a connection to Azure DevOps (⏱️ 5 minutes)
```

#### 3. Add Content Type Indicators

Use these consistently:
- 📋 for prerequisites
- 🔍 for examples
- ⚠️ for warnings/cautions
- 💡 for tips

```markdown
📋 **Prerequisites**:
- Azure DevOps account with appropriate permissions
- Personal Access Token with Work Items (Read, Write) scope

💡 **Tip**:
Use environment variables to store your PAT token securely.
```

### Troubleshooting Guide Template

#### 1. Group Issues by Severity

Organize issues by severity level:

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
```

#### 2. Add Quick Fix Summary

Add a collapsible quick fix summary at the top of each category:

```markdown
<details>
<summary><b>Quick Fix Summary</b></summary>

- 🔴 Authentication Failure: Regenerate your PAT token
- 🟡 Rate Limiting: Implement exponential backoff
- 🟢 Missing Parameters: Check required parameters documentation

</details>
```

#### 3. Format Symptoms and Solutions Consistently

Use consistent formatting for symptoms and solutions:

```markdown
### Symptoms

- Bullet points describing how to identify this issue
- Error messages or behaviors to look for

### Solution Steps

1. First step
   ```typescript
   // Code example if applicable
   ```

2. Second step
   [Details...]
```

### Template Selection Guide

#### 1. Add Feature Comparison Table

Add a table comparing features across template types:

```markdown
| Feature | API Reference | Method | Class | Interface | Tutorial | Conceptual | Troubleshooting |
|---------|---------------|--------|-------|-----------|----------|------------|-----------------|
| Method List | ✓ | - | ✓ | ✓ | - | - | - |
| Parameters | ✓ | ✓ | ✓ | ✓ | - | - | - |
| Examples | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
```

#### 2. Prepare for Flowchart Addition

Add a placeholder for the flowchart image:

```markdown
## Visual Decision Tree

![Template Selection Decision Tree](../images/template-selection-flowchart.png)

*Figure 1: Decision tree for selecting the appropriate template*
```

Note: The actual image will be created by Jamie and added later.

## Example Implementation Checklist

Use this checklist when updating templates:

### API Reference Template
- [ ] Add breadcrumb navigation
- [ ] Add table of contents
- [ ] Add method importance indicators
- [ ] Group methods by category
- [ ] Add visual separation between sections
- [ ] Add content type indicators for examples
- [ ] Add See Also section

### Tutorial Template
- [ ] Add breadcrumb navigation
- [ ] Add progress indicator
- [ ] Add table of contents
- [ ] Add time estimates to step headings
- [ ] Add content type indicators
- [ ] Add visual separation between steps
- [ ] Add See Also section

### Troubleshooting Guide Template
- [ ] Add breadcrumb navigation
- [ ] Add table of contents
- [ ] Group issues by severity
- [ ] Add quick fix summary
- [ ] Format symptoms and solutions consistently
- [ ] Add visual separation between severity levels
- [ ] Add See Also section

### Template Selection Guide
- [ ] Add breadcrumb navigation
- [ ] Add table of contents
- [ ] Add feature comparison table
- [ ] Prepare for flowchart addition
- [ ] Add visual separation between sections
- [ ] Add See Also section

## Common Issues and Solutions

### Issue: Links Not Working

**Solution**: Ensure paths are relative to the document's location. Use `../` to go up one directory level.

### Issue: Emojis Not Rendering

**Solution**: Verify emoji syntax. Some emojis may not render consistently across all platforms. Stick to the recommended emojis in the Visual Elements Guide.

### Issue: Table Formatting Issues

**Solution**: Ensure all tables have the correct number of columns in each row. Use the pipe character (`|`) at the beginning and end of each row.

### Issue: Collapsible Sections Not Working

**Solution**: Ensure there are no blank lines between the `<details>`, `<summary>`, and content. The content must start immediately after the `</summary>` tag.

## Version History

| Version | Date | Updated By | Changes |
|---------|------|------------|---------|
| 1.0 | 2024-07-22 | Taylor | Initial version | 