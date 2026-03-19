# GoalPath Skills for Claude Code

AI-powered project management workflow skills that integrate [GoalPath](https://goalpath.app) with [Claude Code](https://claude.ai/claude-code). Turn rough ideas into shipped features with a structured pipeline.

## The Pipeline

```
discover → plan → implement (or work) → status
```

| Skill | What it does |
|-------|-------------|
| `/goalpath-skills:goalpath-discover` | Turn a rough idea into a milestone with a full PRD |
| `/goalpath-skills:goalpath-plan` | Break a PRD into prioritized, estimatable implementation items |
| `/goalpath-skills:goalpath-implement` | Implement an entire milestone on one branch with one PR |
| `/goalpath-skills:goalpath-work` | Work on a single item — fetch, explore, implement, verify |
| `/goalpath-skills:goalpath-status` | Quick status check — assigned items, blockers, next action |

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed
- A [GoalPath](https://goalpath.app) account with the GoalPath MCP server configured

### Setting up the GoalPath MCP Server

The skills communicate with GoalPath through the GoalPath CLI's built-in MCP server. Install the CLI and add it to your Claude Code MCP configuration:

```bash
npm install -g @goalpath/cli
```

Then add to your `.mcp.json` (project-level) or `~/.claude/.mcp.json` (global):

```json
{
  "mcpServers": {
    "goalpath": {
      "command": "goalpath",
      "args": ["mcp", "serve"]
    }
  }
}
```

You'll need to authenticate first with `goalpath login`.

## Installation

### From the Claude Code plugin marketplace

```
/plugin marketplace add GoalPathApp/goalpath-skills
/plugin install goalpath-skills
```

### Local development

```bash
claude --plugin-dir ./path/to/goalpath-skills
```

Then reload plugins inside Claude Code:
```
/reload-plugins
```

## Usage

### Discover: Idea → PRD

Start with anything — a phrase, a URL to an empty milestone, or a half-formed thought:

```
/goalpath-skills:goalpath-discover "we need milestone progress reports for stakeholders"
```

The discover skill acts as a product sparring partner:
- Probes the idea with focused questions
- Researches competitors and best practices
- Explores your codebase for feasibility
- Drafts and iterates on a PRD
- Saves the result to GoalPath

### Plan: PRD → Items

Once you have a solid PRD, break it into implementation items:

```
/goalpath-skills:goalpath-plan https://goalpath.app/roadmaps/.../milestones/abc-123
```

The plan skill:
- Reads the PRD and explores the codebase
- Proposes items with types, estimates, subtasks, and dependencies
- Resolves ambiguity upfront (no deferred questions)
- Creates items in GoalPath after your approval

### Implement: Milestone → PR

Implement all items in a milestone on a single feature branch:

```
/goalpath-skills:goalpath-implement https://goalpath.app/roadmaps/.../milestones/abc-123
```

The implement skill supervises the full milestone:
- Creates a feature branch
- Dispatches specialist subagents for each item
- Checks off subtasks incrementally
- Posts progress notes on GoalPath items
- Raises highlights for human decisions (without blocking)
- Delivers a single PR with all work

### Work: Single Item → Code

Work on one item at a time:

```
/goalpath-skills:goalpath-work https://goalpath.app/roadmaps/.../items/def-456
```

The work skill handles the full lifecycle:
- Fetches context and sets status to Started
- Explores relevant code
- Implements with appropriate specialist agents
- Checks off subtasks as work progresses
- Verifies build and tests pass
- Marks the item Finished

### Status: Quick Check

See what's on your plate:

```
/goalpath-skills:goalpath-status
```

Shows items grouped by status with blockers highlighted first, plus a suggested next action.

## GoalPath Concepts

| Concept | Description |
|---------|-------------|
| **Milestone** | Container for related work (2-6 weeks). Has a PRD, status, and target date. |
| **Item** | A unit of work. Types: Feature, Bug, Task, Deadline. |
| **Subtask** | Checkbox within an item. Visible to stakeholders for real-time progress. |
| **Highlight** | Flag on an item: Question, Blocked, or Discussion. |
| **Estimate** | Fibonacci points (1-8) on Features only. Calibrated for AI-assisted development. |
| **Status flow** | NotStarted → Started → Finished → Delivered |

## Contributing

Contributions welcome! Please open an issue or PR.

## License

MIT
