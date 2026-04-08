---
name: goalpath-status
description: "Use when the user wants to see their current GoalPath status, assigned items, or what to work on next. Also use when they say 'status', 'what's next', 'show my items', or want a progress overview."
---

# GoalPath Status Check

Show the current state of work in GoalPath. Optional filter: $ARGUMENTS

## GoalPath Domain Knowledge

### Item States
- **NotStarted**: Ready to work on but not yet picked up
- **Started**: Actively being worked on (WIP)
- **Finished**: Code complete, tests pass, ready for review/acceptance
- **Delivered**: Shipped to production
- **Rejected**: Needs rework. Read comments for context.

### Highlights (Flags)
- **Question**: Needs clarification before work can continue
- **Blocked**: External dependency preventing progress
- **Discussion**: Team needs to align on approach

### Priority
Items are returned in priority order (by weight). First item in the list = highest priority = work on next.

### Estimates
Points use fibonacci scale: 1 (trivial), 2 (small), 3 (medium), 5 (large), 8 (very large). Only Feature items have estimates.

## Step 1: Get Context

List projects using `mcp__goalpath__list_projects`.

If the user provided a project name or ID as an argument, filter to that project. Otherwise show all projects.

## Step 2: Fetch My Items

For each relevant project, use `mcp__goalpath__list_my_items` to get items assigned to the current user.

## Step 3: Display Status

Group and display items by status. Use compact formatting:

**Blocked / Needs Attention**
- Items with `Blocked`, `Question`, or `Discussion` highlights. Show these FIRST
- Show the highlight type, title, estimate, and most recent comment for context

**In Progress** (Started)
- List items with status `Started`, showing title, type, estimate, and milestone
- Note how many subtasks are checked vs total

**Ready to Start** (NotStarted)
- List items with status `NotStarted` assigned to you, in priority order
- Show title, type, and estimate

**Recently Finished**
- List items with status `Finished` (omit Delivered items, they're done)

After the item list, show a quick summary line:
> X in progress | Y ready | Z blocked | N total points in flight

## Step 4: Suggest Next Action

Based on the status, suggest what to work on next:
- If something is blocked, suggest investigating the blocker first
- If nothing is in progress, suggest the highest-priority NotStarted item (first in the list)
- If work is in progress, ask if the user wants to continue with `/goalpath-skills:goalpath-work <id>`
- If items have Question highlights, suggest resolving those before starting new work

Keep the output concise. This is a quick status check, not a detailed report.
