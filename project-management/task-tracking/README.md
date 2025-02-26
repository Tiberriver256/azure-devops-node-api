# Task Tracking

This directory contains task tracking information for the Azure DevOps Node API Documentation Project team members.

## Directory Structure

Each team member has their own directory for tracking tasks in their preferred format:

```
task-tracking/
├── README.md                 # This file
├── taylor/                   # Taylor's task tracking
│   ├── task-index.md         # Overview of all Taylor's tasks
│   ├── documentation-standards/  # Task 3 details
│   ├── split-view-implementation/  # Task 9 details
│   ├── getting-started-guide/  # Task 5 details
│   └── ...                   # Other task categories
├── jordan/                   # Jordan's task tracking (when created)
├── casey/                    # Casey's task tracking (when created)
└── ...                       # Other team members
```

## Task Tracking Approach

Each team member is encouraged to organize their tasks in a way that works best for them. The project uses a flexible approach to task tracking with these guidelines:

1. **Individual Organization**: Team members can structure their task tracking in whatever way works best for their workflow.

2. **Task Ownership**: Major tasks are assigned to specific team members who are responsible for tracking progress.

3. **Shared Visibility**: While organization is flexible, task status should be visible to the team.

4. **Bi-Weekly Reporting**: Status updates are provided to Morgan bi-weekly.

## Legacy Files

- `taylor_todo.md` - Taylor's original todo list (being migrated to the new structure)

## Creating Your Task Tracking

To create your own task tracking:

1. Create a directory with your name
2. Organize your tasks in whatever format works best for you
3. Consider including an index or overview file for easy reference

## Task Numbering Convention

Tasks are numbered according to the project task breakdown:

- Tasks 1-9: Phase 1 (Foundation)
- Tasks 10-27: Phase 2 (Core Documentation)
- Tasks 28-41: Phase 3 (Advanced Content)
- Tasks 42-49: Phase 4 (Finalization and Launch)
- Tasks 50-60: Additional API Client Documentation

## File Naming Convention

- `[team-member]_todo.md` - Main task tracking document for each team member
- `[team-member]_weekly_report_[week-number].md` - Weekly progress reports (optional)
- `[team-member]_milestone_[milestone-number].md` - Milestone completion reports

## Task Status Indicators

Tasks should be marked with the following status indicators:

- `[ ]` - Not Started
- `[🔄]` - In Progress
- `[x]` - Completed
- `[!]` - Blocked (requires attention)
- `[~]` - Deferred

## Task Documentation Format

Each task should include:

1. Task number and description
2. Estimated time to complete
3. Dependencies (if applicable)
4. Status indicator
5. Notes on progress, decisions, or issues
6. Collaboration points with other team members

Example:
```markdown
- [x] 3.14 Create API method documentation template (4h)
  * **Dependency**: Requires 3.13 completion
  * **Notes**: Completed development of the API method documentation template along with comprehensive implementation guide. Created example implementation (getWorkItems.md) to demonstrate proper template usage.
  * **Collaboration**: Reviewed with Jordan on Tuesday
```

## Update Frequency

Task tracking documents should be updated:

1. At the beginning of each workday (to plan daily tasks)
2. At the end of each workday (to record progress)
3. When a task status changes
4. After collaboration sessions or key decisions

## Weekly Reports

Weekly progress reports should be submitted by Friday at 4:00 PM and include:

1. Tasks completed during the week
2. Tasks in progress and their status
3. Blockers or issues requiring assistance
4. Plans for the following week
5. Any scope or timeline adjustments needed

## Milestone Reports

Milestone completion reports should be submitted within 2 days of milestone completion and include:

1. Summary of all tasks completed for the milestone
2. Quality assessment of deliverables
3. Lessons learned
4. Recommendations for future milestones 