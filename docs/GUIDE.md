# Setup Guide

## Quick Start

**1.** Copy the harness into your project:

```bash
cp -r .claude/ /path/to/your-project/.claude/
cp CLAUDE.md /path/to/your-project/
mkdir -p /path/to/your-project/docs
cp docs/STATUS.md docs/ROADMAP.md docs/HISTORY.md /path/to/your-project/docs/
```

**2.** Edit `CLAUDE.md` with your project's build commands, service URLs, and rules. Keep it under 200 lines — details belong inside skills.

**3.** Replace `{{PLACEHOLDER}}` markers in `.claude/skills/` with your project values. See [CUSTOMIZATION.md](CUSTOMIZATION.md) for the full placeholder reference.

**4.** Initialize your document memory and domain docs:

```bash
# Temporal layer (required)
docs/STATUS.md          # Current version and project health
docs/ROADMAP.md         # Planned versions and backlog  
docs/HISTORY.md         # Leave empty — auto-populated by /release

# Domain layer (recommended)
docs/architecture/      # Architecture.md, Database_Schema.md
docs/api/               # API endpoint documentation
docs/guides/            # Security.md, permissions, quick start
docs/product-philosophy.md  # What your product is and isn't
```

Templates for all of these are included in this repo. Fill in the relevant sections for your project.

**5.** Start the cycle:

```
/plan v1.0 → developer agent implements → /qa → /pr → /review → /release → repeat
```

---

## What's In the Box

### Skills (13)

**The Cycle (7):**

| Skill | What It Does |
|-------|-------------|
| `/plan` | Creates implementation plan with dependency graphs and parallel/sequential phases |
| `/qa` | Runs unit + E2E + security + AI review in parallel, produces consolidated report |
| `/pr` | Creates GitHub PR with conventional commits and test plan |
| `/review` | Processes PR feedback, delegates fixes to developer agent |
| `/release` | Merges PR, syncs 4 documents, creates git tag and GitHub release |
| `/deploy` | Deploys with pre-checks, health monitoring, and rollback plan |
| `/status` | Reports project health, starts services, shows next milestones |

**The Backlog (2):**

| Skill | What It Does |
|-------|-------------|
| `/issue` | Investigates codebase → creates GitHub issue → places in ROADMAP |
| `/architect` | Evaluates architecture, analyzes risks, compares options |

**Universal (4):**

| Skill | What It Does |
|-------|-------------|
| `/tdd-guide` | Guided test-first development (Red-Green-Refactor) |
| `/security-review` | OWASP Top 10 code security scan |
| `/perf-check` | Performance analysis — N+1 queries, re-renders, bundle size |
| `/commit-msg` | Conventional commit message generation |

### Agents (4)

| Agent | Model | Role |
|-------|-------|------|
| `developer` | Sonnet | Code implementation, testing |
| `code-reviewer` | Sonnet | Code review, test execution |
| `architect` | Opus | Architecture decisions, system design |
| `browser-tester` | Sonnet | E2E browser testing via MCP Chrome |

### Hooks (2)

| Hook | Trigger | Action |
|------|---------|--------|
| PreToolUse | Write/Edit | Blocks modification of `.env`, credentials, secrets |
| PostToolUse | Write/Edit | Auto-runs linter on frontend files |

### Document Memory (4)

`STATUS.md` · `ROADMAP.md` · `HISTORY.md` · `README.md`

Maintained automatically by the `/release` skill. See the main [README](../README.md) for the rationale.

---

## Enterprise Extensions

`examples/enterprise/` contains additional skills for organizational workflows:

- **sync** — Google Sheets feature spec synchronization
- **feature-intake** — Automated feature request pipeline with AI analysis

These extend the core harness without changing it.
