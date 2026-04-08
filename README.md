# GoalPath Skills for Claude Code

Go from a rough idea to a merged PR — without leaving your terminal. These skills connect [GoalPath](https://goalpath.app) to [Claude Code](https://claude.ai/claude-code) so your AI assistant can manage the full development lifecycle: scoping, planning, implementing, and tracking progress.

## The Pipeline

```
discover → plan → implement (or work) → status
```

| Skill | Purpose |
|-------|---------|
| `goalpath-discover` | Turn a rough idea into a milestone with a full PRD |
| `goalpath-plan` | Break a PRD into prioritized implementation items with estimates |
| `goalpath-implement` | Implement an entire milestone on one branch, deliver one PR |
| `goalpath-work` | Pick up a single item — explore, implement, verify, done |
| `goalpath-status` | See assigned items, blockers, and what to work on next |

## Quick Start

### 1. Install the GoalPath CLI and MCP server

Follow the [GoalPath CLI & MCP Server guide](https://goalpath.app/docs/integrations/cli-and-mcp-server) to get set up:

```bash
npm install -g @goalpath/cli
goalpath login
goalpath mcp init
```

`mcp init` auto-detects your AI assistant (Claude Code, Claude Desktop, Cursor, VS Code) and wires up the connection.

### 2. Install the skills plugin

From your terminal:

```bash
claude plugins marketplace add morkeleb/goalpath-skills
claude plugins install goalpath-skills
```

Or from inside Claude Code:

```
/plugins marketplace add morkeleb/goalpath-skills
/plugins install goalpath-skills
```

### 3. Try it

```
/goalpath-skills:goalpath-discover "we need milestone progress reports for stakeholders"
```

Claude will ask probing questions, research competitors, check your codebase for feasibility, and draft a PRD. When you're happy with it, the PRD is saved as a GoalPath milestone — ready to plan.

## Skills in Detail

### Discover: Idea → PRD

Start with anything — a phrase, a URL to a barebones milestone, or a half-formed thought:

```
/goalpath-skills:goalpath-discover "we need milestone progress reports for stakeholders"
```

Claude acts as a product sparring partner. It won't just say yes — it challenges scope, surfaces assumptions, and forces trade-offs. The session includes:
- Focused questions to understand the problem, not just the solution
- Web research on competitors and best practices
- Codebase exploration to check what's feasible
- An iterative PRD draft you refine together

The result is a milestone in GoalPath with a sharp, opinionated PRD.

### Plan: PRD → Items

Pass a milestone URL and Claude breaks the PRD into work items:

```
/goalpath-skills:goalpath-plan https://goalpath.app/roadmaps/.../milestones/abc-123
```

- Explores your codebase to understand what exists and what needs building
- Proposes items with types (Feature, Task, Bug), estimates, subtasks, and dependencies
- Resolves ambiguity during planning — every item created is ready to start
- Creates items in GoalPath after you approve

### Implement: Milestone → PR

Hand off an entire milestone for implementation:

```
/goalpath-skills:goalpath-implement https://goalpath.app/roadmaps/.../milestones/abc-123
```

Claude creates a feature branch and works through every item:
- Delegates to specialist agents (data model, backend, frontend, tests)
- Checks off subtasks in GoalPath as work completes — stakeholders see real-time progress
- Flags decisions that need your input without blocking on them
- Verifies build and tests pass, then opens a single PR

### Work: Single Item → Code

Pick up one item at a time:

```
/goalpath-skills:goalpath-work https://goalpath.app/roadmaps/.../items/def-456
```

Handles the full lifecycle: sets status to Started, explores context, implements, checks off subtasks incrementally, verifies the build, and marks it Finished.

### Status: Quick Check

```
/goalpath-skills:goalpath-status
```

Shows your assigned items grouped by status — blockers first, then in-progress, then ready to start. Includes a suggested next action.

## GoalPath Concepts

| Concept | Description |
|---------|-------------|
| **Milestone** | A container for related work, typically 2-6 weeks. Holds a PRD, status, and target date. |
| **Item** | A unit of work: Feature, Bug, Task, or Deadline. |
| **Subtask** | A checkbox within an item. Stakeholders see these update in real time. |
| **Highlight** | A flag on an item — Question, Blocked, or Discussion — to surface issues. |
| **Estimate** | Fibonacci points (1, 2, 3, 5, 8) on Features only. Calibrated so 1 point ≈ 30 min with AI assistance. |
| **Status flow** | NotStarted → Started → Finished → Delivered |

## Local Development

To test skills locally without installing from the marketplace:

```bash
claude --plugin-dir ./path/to/goalpath-skills
```

Then inside Claude Code:
```
/reload-plugins
```

## Contributing

Contributions welcome! Please open an issue or PR.

## License

MIT
