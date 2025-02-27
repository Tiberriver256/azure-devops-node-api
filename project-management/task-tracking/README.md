# MVP Documentation Kanban Board

This directory contains a simplified kanban-style task tracking system for the Azure DevOps Node API MVP documentation project. We're using this approach to keep things simple and easy to maintain.

## How It Works

We're using three files to track tasks through their lifecycle:

1. **[todo.md](./todo.md)**: Tasks that need to be done, sorted by priority
2. **[doing.md](./doing.md)**: Currently empty - tasks move directly from todo to done
3. **[done.md](./done.md)**: Completed tasks

## Task Format

Each task follows this format:

```markdown
- [ ] **Task Name** (@Assignee, Due: Date)
  - Description:
    - Detailed subtask or description
    - Another subtask or description
  - Dependencies: Any dependencies
  - Blockers: (If any) What's blocking progress
```

For completed tasks:

```markdown
- [x] **Task Name** (@Assignee, Completed: Date)
  - Description:
    - Detailed subtask or description
    - Another subtask or description
  - Notes: Any completion notes or lessons learned
```

## How to Use

1. **Completing Work**: When you finish a task, move it from `todo.md` to `done.md`, change `[ ]` to `[x]`, add completion date, and any relevant notes.

2. **Adding New Tasks**: Add new tasks to the appropriate priority section of `todo.md`.

3. **Weekly Review**: During our Thursday status meetings, we'll review all tasks and update as needed.

## Priority Levels

Tasks in `todo.md` are categorized by priority:

- **High Priority**: Critical path tasks that directly impact the delivery timeline
- **Medium Priority**: Important tasks that should be completed on schedule
- **Low Priority**: Tasks that could be deferred if necessary

## Blocked Tasks

If a task is blocked, add a "Blockers:" section to the task in `todo.md` and clearly document what's blocking progress. Blocked tasks should be discussed in the weekly status meeting.

## Reporting

Morgan will review the kanban board before the weekly status meeting and prepare a simple status report focusing on:

1. What was completed last week
2. What's coming up next
3. Any blocked tasks or risks 