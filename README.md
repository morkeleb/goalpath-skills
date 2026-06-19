# GoalPath Skills for Claude Code

Go from a rough idea to a merged PR, without leaving your terminal. These skills connect [GoalPath](https://goalpath.app) to [Claude Code](https://claude.ai/claude-code) so your AI assistant can manage the full development lifecycle: scoping, planning, implementing, and tracking progress.

## The Pipeline

```
discover → plan → implement (or work) → status
```

| Skill | Purpose |
|-------|---------|
| `goalpath-discover` | Turn a rough idea into a milestone with a full PRD |
| `goalpath-plan` | Break a PRD into prioritized implementation items with estimates |
| `goalpath-implement` | Implement an entire milestone on one branch, deliver one PR |
| `goalpath-work` | Pick up a single item: explore, implement, verify, done |
| `goalpath-status` | See assigned items, blockers, and what to work on next |

## Quick Start

### 1. Get your API key

Grab one from [goalpath.app/profile/api-keys](https://goalpath.app/profile/api-keys) and export it:

```bash
export GOALPATH_API_KEY="gp_..."
```

(Add it to `~/.zshrc` / `~/.bashrc` so it persists across sessions.)

### 2. Install the plugin

The plugin bundles **both the skills and the hosted GoalPath MCP server** — one install gets you everything.

From inside Claude Code:

```
/plugin marketplace add morkeleb/goalpath-skills
/plugin install goalpath-skills
```

Or from your terminal:

```bash
claude plugin marketplace add morkeleb/goalpath-skills
claude plugin install goalpath-skills
```

### 3. Try it

```
/goalpath-skills:goalpath-discover "we need milestone progress reports for stakeholders"
```

Claude will ask probing questions, research competitors, check your codebase for feasibility, and draft a PRD. When you're happy with it, the PRD is saved as a GoalPath milestone, ready to plan.

## Which install path should I use?

This plugin uses the **hosted MCP server** at `https://goalpath.app/api/mcp` — the recommended path for most users. Schema updates ship the instant we deploy, no client republish or reinstall needed, and the same endpoint works on every Claude surface (Code, Desktop, web).

There's also an **alternative install** via the `@goalpath/cli` package that runs the MCP server locally. The one real reason to pick it: it walks up from your working directory to find a `.goalpath` project-link file, so the MCP automatically knows which project you're in based on which repo you're sitting in. If you switch between several GoalPath projects from different local repos, that auto-binding is a meaningful quality-of-life win — tools like `list_items` and `get_work_queue` Just Work without you setting a project each time.

**Pick the alternative install if** you frequently switch between multiple GoalPath projects from their local repo directories and want the MCP to bind by cwd.

**Stick with the default plugin install if** you mostly work on one or two projects, want the simplest setup, or use Claude on mobile / claude.ai web (which the CLI can't reach). You can still pick the project on a session basis with `set_active_project`.

See [Alternative install: local CLI](#alternative-install-local-cli) below.

## Using GoalPath on Claude Desktop / claude.ai web

The plugin install above is Claude Code only. The same hosted MCP server works on other Claude surfaces — you add it as a custom connector:

**Claude Desktop** — add to `claude_desktop_config.json` (Settings → Developer → Edit Config):

```json
{
  "mcpServers": {
    "goalpath": {
      "type": "http",
      "url": "https://goalpath.app/api/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

**claude.ai web** — Settings → Connectors → Add custom connector. URL: `https://goalpath.app/api/mcp`, auth: Bearer token (your API key).

Skills don't transfer to Desktop / web (they're Claude Code only), but every `mcp__goalpath__*` tool the skills call does — you can drive the same workflows by asking Claude in plain English.

## Skills in Detail

### Discover: Idea → PRD

Start with anything: a phrase, a URL to a barebones milestone, or a half-formed thought.

```
/goalpath-skills:goalpath-discover "we need milestone progress reports for stakeholders"
```

Claude acts as a product sparring partner. It won't just say yes. It challenges scope, surfaces assumptions, and forces trade-offs. The session includes:
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
- Resolves ambiguity during planning, so every item created is ready to start
- Creates items in GoalPath after you approve

### Implement: Milestone → PR

Hand off an entire milestone for implementation:

```
/goalpath-skills:goalpath-implement https://goalpath.app/roadmaps/.../milestones/abc-123
```

Claude creates a feature branch and works through every item:
- Delegates to specialist agents (data model, backend, frontend, tests)
- Checks off subtasks in GoalPath as work completes, so stakeholders see real-time progress
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

Shows your assigned items grouped by status: blockers first, then in-progress, then ready to start. Includes a suggested next action.

## GoalPath Concepts

| Concept | Description |
|---------|-------------|
| **Milestone** | A container for related work, typically 2-6 weeks. Holds a PRD, status, and target date. |
| **Item** | A unit of work: Feature, Bug, Task, or Deadline. |
| **Subtask** | A checkbox within an item. Stakeholders see these update in real time. |
| **Highlight** | A flag on an item (Question, Blocked, or Discussion) to surface issues. |
| **Estimate** | Fibonacci points (1, 2, 3, 5, 8) on Features only. Calibrated so 1 point ≈ 30 min with AI assistance. |
| **Status flow** | NotStarted → Started → Finished → Delivered |

## Alternative install: local CLI

If you want the cwd-aware project resolution (see ["Which install path should I use?"](#which-install-path-should-i-use) above), install the CLI MCP server instead of relying on the hosted one bundled with this plugin.

```bash
npm install -g @goalpath/cli
goalpath login                # paste your API key from goalpath.app/profile/api-keys
goalpath project link         # creates a .goalpath file in the current repo
goalpath mcp init             # auto-detects Claude Code, Cursor, VS Code, etc.
```

`goalpath project link` writes a `.goalpath` file at the repo root. The CLI's MCP server walks up the tree from your current directory to find that file and binds to the matching project — so when Claude Code is running in `~/code/myrepo`, tools auto-scope to the project that repo is linked to.

After installing the CLI, **either**:

- **Use only the CLI MCP**: don't install the `goalpath-skills` plugin's bundled MCP — set `GOALPATH_API_KEY=""` (empty) so the plugin's auth header is invalid and the CLI's `mcp init` takes over the registration. Or skip this plugin entirely and rely on the CLI for both MCP and (manual) skill workflows.
- **Use both, prefer the CLI**: rename the plugin's bundled server so it doesn't shadow the CLI's registration. This is fiddly — most users picking the CLI path won't want the plugin in the same project.

Full setup details: [GoalPath CLI & MCP Server guide](https://goalpath.app/docs/integrations/cli-and-mcp-server).

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
