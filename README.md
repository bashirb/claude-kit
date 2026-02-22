# claude-kit

A complete [Claude Code](https://docs.anthropic.com/en/docs/claude-code) kit — agents, commands, hooks, and skills — distributed as a plugin marketplace.

## What's included

| Type | What it does |
|---|---|
| **Agents** | Subagents Claude spawns to handle specialized tasks |
| **Commands** | Slash commands you invoke directly (e.g. `/primer`) |
| **Hooks** | Scripts that run automatically on Claude Code events |
| **Skills** | Invocable instruction sets for specific workflows |

## Repo structure

```
claude-kit/
├── .claude/
│   ├── agents/       # Subagent .md files
│   ├── commands/     # Slash command .md files
│   ├── hooks/        # Hook scripts
│   ├── skills/       # Skill directories, each with a SKILL.md
│   └── settings.json # Shared permissions and hook config
├── .claude-plugin/
│   └── marketplace.json
└── template/         # Starter template for new skills
```

## Install via plugin (recommended)

Register this repo as a plugin marketplace:

```
/plugin marketplace add bashirb/claude-kit
```

Then browse and install from the menu, or install directly:

```
/plugin install bashirb-claude-kit@bashirb-claude-kit
```

## Install manually

Copy the `.claude/` folder into your project root (project scope) or `~/` (user scope):

```bash
# Project scope — available in this repo only
cp -r .claude/agents .claude/commands .claude/hooks .claude/skills /your-project/.claude/

# User scope — available across all your projects
cp -r .claude/agents .claude/commands .claude/hooks ~/.claude/
```

## Agents

Subagents Claude spawns automatically or on request. Mention the task and Claude picks the right one, or reference explicitly with `@.claude/agents/<name>.md`.

| Agent | Description |
|---|---|
| [code-reviewer](./.claude/agents/code-reviewer.md) | Reviews code for correctness, security, and maintainability |
| [documentation-manager](./.claude/agents/documentation-manager.md) | Keeps README, docs/, and ARCHITECTURE.md in sync with code changes |
| [validation-agent](./.claude/agents/validation-agent.md) | Runs tests and validation gates, iterates until all pass |

## Commands

Slash commands you invoke directly in Claude Code.

| Command | Description |
|---|---|
| [/primer](./.claude/commands/primer.md) | Reads project structure, CLAUDE.md, README, and docs/ to prime context |
| [/commit-push-pr](./.claude/commands/commit-push-pr.md) | Creates a branch, commits, pushes, and opens a PR in one step |

## Hooks

Scripts that run automatically on Claude Code events. Configured in `.claude/settings.json`.

| Hook | Trigger | Description |
|---|---|---|
| [log-tool.sh](./.claude/hooks/log-tool.sh) | PostToolUse (Edit, Write, MultiEdit) | Logs file edits automatically |

## Skills

Invocable instruction sets loaded dynamically when the task matches.

| Skill | Description |
|---|---|
| [pr-review](./.claude/skills/pr-review/) | Review a pull request for correctness, clarity, and potential issues |

## Adding components

### Agent

1. Create `.claude/agents/<name>.md` with YAML frontmatter:
   ```markdown
   ---
   name: agent-name
   description: "When to use this agent. Be specific."
   tools: Read, Edit, Bash, ...
   ---
   ```
2. Add the path to `agents` in `.claude-plugin/marketplace.json`

### Command

1. Create `.claude/commands/<name>.md` with optional YAML frontmatter:
   ```markdown
   ---
   description: What this command does
   allowed-tools: Bash(git:*), ...
   ---

   # Instructions go here
   ```
2. Add the path to `commands` in `.claude-plugin/marketplace.json`

### Hook

1. Add your script to `.claude/hooks/`
2. Register it in `.claude/settings.json` under the appropriate event

### Skill

1. Create `.claude/skills/<name>/SKILL.md` using the [template](./template/SKILL.md)
2. Add the path to `skills` in `.claude-plugin/marketplace.json`

## Plugin command reference

**Marketplace**

```
/plugin marketplace add bashirb/claude-kit
/plugin marketplace update bashirb-claude-kit
/plugin marketplace remove bashirb-claude-kit
/plugin marketplace list
```

**Install plugins**

```
# Interactive
/plugin

# Direct install
/plugin install bashirb-claude-kit@bashirb-claude-kit

# Explicit scope via terminal
claude plugin install bashirb-claude-kit@bashirb-claude-kit --scope user
claude plugin install bashirb-claude-kit@bashirb-claude-kit --scope project
claude plugin install bashirb-claude-kit@bashirb-claude-kit --scope local
```

**Uninstall / Disable**

```
/plugin uninstall bashirb-claude-kit@bashirb-claude-kit
/plugin disable bashirb-claude-kit@bashirb-claude-kit
/plugin enable bashirb-claude-kit@bashirb-claude-kit
```

**Scopes**

| Scope | Who | Shared via git | Use case |
|---|---|---|---|
| `user` | You, all projects | No | Personal tools across all your work |
| `project` | Everyone on the repo | Yes (`.claude/settings.json`) | Team-wide plugins |
| `local` | You, this repo only | No | Personal override for one repo |

**Nuclear option**

```bash
rm -rf ~/.claude/plugins/cache
```

Then reinstall via `/plugin install`.

## Contributing

1. Fork this repo
2. Add your agent, command, hook, or skill following the structure above
3. Register it in `.claude-plugin/marketplace.json`
4. Open a PR with a short description of what it does and why it's useful
