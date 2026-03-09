# claude-project-template

Opinionated project template for Claude Code. Clone this when starting a new project.

## What's included

### Core files
- **`CLAUDE.md`** — routing protocol for Claude. Points to canonical sources, lists commands, and maps task types to skills. Only non-discoverable info lives here.
- **`constitution.md`** — immutable rules. Three-tier boundary system (always/ask/never), security hard requirements, design principles (YAGNI, KISS, DRY), and testing standards.

### Docs scaffold
| File | Purpose |
|---|---|
| `docs/spec.md` | What & why — user flows, acceptance criteria, non-goals |
| `docs/design.md` | Architecture, tech stack, implementation phases |
| `docs/schema.md` | Database schema SSOT |
| `docs/api.md` | Endpoint contracts SSOT |
| `docs/environment.md` | Env vars, dependency versions, deployment |
| `docs/tasks.md` | Task checklist with validation gates |
| `docs/adr/` | Architecture decision records |

### Claude plugin (`.claude/plugins/generic-project/`)

**Commands:**
- **`/audit-docs`** — dispatches the docs-auditor agent on demand
- **`/quality-gate`** — runs the full quality gate (formatter, linter, tests) as defined in `CLAUDE.md`

**Agents:**
- **`docs-auditor`** — audits documentation in three phases: doc-to-doc consistency, docs vs code, and docs vs external standards. Triggered automatically after significant refactors or phase completions.

**Skills** (auto-loaded based on task type):
| Skill | Triggers on |
|---|---|
| `constitution` | Any code implementation task |
| `database-work` | SQL queries, schema changes, migrations |
| `devops-infra` | Dockerfile, docker compose, env vars, deployment |

### Settings (`.claude/settings.json`)

Enables a base set of Claude plugins: `context7`, `code-review`, `code-simplifier`, `feature-dev`, `security-guidance`, `claude-md-management`, `claude-code-setup`, `plugin-dev`, `skill-creator`.

## Usage

1. Clone the repo
2. Fill in the `TODO` sections in `CLAUDE.md` and `constitution.md`
3. Complete the docs scaffold for your project
4. Add project-specific skills to `.claude/plugins/generic-project/skills/` and update the skill table in `CLAUDE.md`
5. Start at `docs/tasks.md` T001
