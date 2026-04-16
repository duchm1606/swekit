# swekit (Duck Skills)

A Claude Code skill ecosystem for structured software development workflows.

## Skills

| Skill | Purpose | Triggers |
|-------|---------|----------|
| **brainstorming** | Vague idea → research direction | "I want to build...", "How might we...", unclear requirements |
| **research** | Evaluate options, gather context | Package selection, architecture decisions, gray areas |
| **planning** | Break work into phases and tasks | Feature specs, bug fixes requiring multiple changes |
| **executing** | Run tasks with subagents | Phase plan ready, independent tasks to parallelize |
| **reviewing** | Verify work against spec | Implementation complete, before merge |
| **debugging** | Root cause analysis | Errors, test failures, unexpected behavior |
| **prompt-leverage** | Structure effective prompts | Delegating to subagents, complex task handoffs |

## Workflow

```
brainstorming → research → planning → executing → reviewing
                              ↑                      ↓
                          debugging ←────────────────┘
```

## Installation

### Option 1: Global Skills (Recommended for personal use)

```bash
git clone https://github.com/duchm1606/swekit.git ~/.claude/skills/duck
```

Skills are available in all your projects as `duck:*`.

### Option 2: Project-local

```bash
cd your-project
git submodule add https://github.com/duchm1606/swekit.git .claude/skills/duck
```

### Updating

```bash
cd ~/.claude/skills/duck  # or your install location
git pull
```

## Usage

Skills are invoked automatically when Claude detects matching triggers, or manually:

```
/duck:brainstorming    # Start with vague idea
/duck:research         # Evaluate packages/approaches
/duck:planning         # Create phase plan
/duck:executing        # Run with subagents
/duck:reviewing        # Verify against spec
/duck:debugging        # Root cause analysis
```

## Inspirations

Built by studying and adapting from:

- **GET SHIT DONE** — context engineering, deep brainstorming
- **Superpowers** — TDD discipline, subagent isolation, systematic debugging
- **Khuym** — beads task graph, validation gates
- **ClaudeKit Engineer** — multi-agent research, domain skills library
