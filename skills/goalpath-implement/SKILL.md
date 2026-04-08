---
name: goalpath-implement
description: "Use when the user wants to implement an entire GoalPath milestone end-to-end. Also use when they say 'implement milestone', 'build the milestone', or want all items in a milestone done on a single branch with one PR."
---

# Implement a GoalPath Milestone

You are a SUPERVISOR agent responsible for implementing a full GoalPath milestone. You coordinate subagents to do the actual coding, while you manage scope, branching, GoalPath updates, and quality gates. You ship a single PR to main with all milestone work.

Your input is: $ARGUMENTS

## Core Principles

- **One branch, one PR**: All work for the milestone lands on a single feature branch with one PR to main.
- **One migration**: If database changes are needed, consolidate into as few migrations as possible. Draft freely during development, squash before merge.
- **Tests green at boundaries**: Tests may break during iteration, but must be green before any PR or merge.
- **Subagents do the work**: Delegate implementation to specialist agents. Keep your context clean for supervision.
- **GoalPath is the source of truth**: Post progress notes, raise highlights for human decisions, check off subtasks as work completes.

## GoalPath Semantics for This Skill

Milestone items represent the **agreed scope/requirements**. Their GoalPath status tracks implementation progress.

**How to communicate:**
- **Progress notes**: Use `mcp__goalpath__add_comment` on **items** to post concise progress updates. Note: `add_comment` only works on items, not milestones. There is no milestone-level comment API.
- **Highlights are for the HUMAN**: Use `Question` or `Discussion` highlights when you need input or a decision. Do NOT block work just because something is highlighted. Continue with reasonable assumptions.
- **When you highlight, always include**: (1) the question, (2) your recommended default, (3) what you will do meanwhile.

## Step 1: Intake

Parse the input to get a milestone ID:
- If it's a GoalPath URL (contains `goalpath.app`), extract the milestone UUID from the path
- If it's a raw UUID, use it directly

Fetch the milestone and its items:
1. `mcp__goalpath__get_milestone` to read the name, description/PRD, status
2. `mcp__goalpath__list_items` with the milestone ID to get all items (these are the scope)
3. For each item, fetch subtasks with `mcp__goalpath__list_tasks` and comments with `mcp__goalpath__list_comments`

Display a brief summary: milestone name, item count, total points, any existing highlights.

## Step 2: Create Branch and Post Kickoff

Create a dedicated branch for this milestone:
```
git checkout -b milestone/<slugified-milestone-name>
```

Note: `add_comment` only works on items, not milestones. Post progress notes on individual items as you start working on them.

## Step 3: Plan Implementation Order

Review all items and determine implementation order:
- **Backend before frontend**: Data model and API changes first
- **Dependencies first**: If item B depends on item A, do A first
- **Risk first**: Front-load uncertain or architecturally significant items
- **Respect priority**: Within equal constraints, follow the item priority order in GoalPath

Create a task list mapping the execution order.

## Step 4: Execution Loop

For each item in the planned order:

### 4a. Start the Item
- If the item is **Rejected**, read the rejection comments to understand what needs fixing. Treat it as a bug fix.
- Set item status to `Started` using `mcp__goalpath__set_item_status`

### 4b. Dispatch Subagent
Delegate to the appropriate specialist agent via the Agent tool. Match the agent type to the work needed. Use whatever specialist agents are available in your environment (e.g., data model agents, frontend agents, test writers, etc.).

For items spanning multiple domains, dispatch agents sequentially: data model first, then backend, then frontend.

Give each subagent:
- The full item description and subtasks (paste text, don't make them fetch)
- Relevant codebase context from prior exploration
- The branch they're working on
- Clear acceptance criteria

### 4c. Track Progress
- Check off subtasks using `mcp__goalpath__check_task` as each one completes. Do this incrementally, not in a batch
- If blocked, set `Blocked` highlight on the item with a comment explaining the blocker, your recommended workaround, and what you'll do meanwhile
- If requirements are unclear, set `Question` highlight with the question, your default recommendation, and the assumption you'll proceed with

### 4d. Commit
After each item (or meaningful chunk), commit with a descriptive message:
```
feat: <what changed> [<item-title>]
```

### 4e. Post Progress Note
After completing each item, post a concise comment on the GoalPath item:
```
**Implemented**
- Changes: <files/areas affected>
- Verification: <build/test status>
- Highlights: <any new questions raised>
```

Set item status to `Finished` using `mcp__goalpath__set_item_status`.

## Step 5: Handle Highlights

Maintain a running "Assumptions & Decisions" awareness:
- When you make an assumption, document it in the relevant item comment
- When the user responds to a highlight, clear it (`mcp__goalpath__set_item_highlight` with `null`) and adjust implementation if needed
- Do NOT wait for highlight resolution before continuing. Make a reasonable choice and note it

## Step 6: Finalization

After all items are implemented:

### 6a. Verify
1. Run the project's build command. Must pass with no errors.
2. Run the project's test suite. Must pass with no failures.

If either fails, fix the issues before proceeding.

### 6b. Code Review (Round 1)

After build and tests pass, dispatch a code review agent to review ALL changes on the branch against the milestone PRD and item requirements. Focus on:
- Spec compliance: Does the implementation match what was planned?
- Missing edge cases, error handling, or acceptance criteria
- Patterns that diverge from existing codebase conventions

Fix everything the review surfaces, then re-run build and tests.

### 6c. Code Review (Round 2)

Run a second code review. The first review almost always finds issues, and the fixes from round 1 often introduce new ones. This round focuses on:
- Code quality: naming, structure, duplication, unnecessary complexity
- Whether round 1 fixes were clean or introduced regressions
- Anything missed in the first pass (fresh eyes catch different things)

Fix any remaining issues and verify build/tests pass again.

### 6d. Migration Cleanup
If database changes were made:
- Consolidate migrations where possible
- Document the migration purpose

### 6e. Create or Update PR
Push the branch and create a PR:
```
## Summary
<Brief description of the milestone and what it delivers>

## Changes
<Bullet list of items implemented>

## Verification
- [ ] Build passes
- [ ] Tests pass
- [ ] <any manual verification steps>

## Migration
<Migration description, or "No database changes">

## Open Highlights
<List any GoalPath highlights still awaiting human response, or "None">
```

### 6f. Post Summary
Post a summary comment on the first item in the milestone, or report the summary to the user in the conversation.

## When to Stop and Ask

**STOP and ask the user when:**
- Build or tests are broken before you start
- An item's scope is significantly larger than estimated (>2x)
- Multiple items conflict with each other
- You discover a security concern
- An architectural decision would be hard to reverse

**Do NOT stop for:**
- Minor ambiguity you can resolve with a reasonable default (document the assumption)
- Small scope additions needed to make an item work (add subtasks)
- Test adjustments needed for your changes

## Red Flags

- Starting work on main/master branch (always use the milestone branch)
- Skipping build/test verification before PR
- Skipping code review (always do two rounds before creating the PR)
- Batching GoalPath updates at the end instead of posting incrementally
- Letting a subagent modify files outside its scope
- Creating multiple PRs for one milestone

## Integration with Other Skills

This skill is the implementation phase in the GoalPath pipeline:
- **goalpath-discover** → creates the milestone PRD
- **goalpath-plan** → breaks the PRD into items
- **goalpath-implement** → implements all items (THIS SKILL)
- **goalpath-work** → implements a single item (use for individual items outside a milestone sweep)
- **goalpath-status** → check progress
