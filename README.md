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

### Claude plugin (`.claude/plugins/`)
- **`docs-auditor` agent** — audits documentation in three phases: doc-to-doc consistency, docs vs code, and docs vs external standards. Triggered automatically after significant refactors or phase completions.
- **`/audit-docs` command** — dispatches the docs-auditor agent on demand.

## Usage

1. Clone the repo
2. Fill in the `TODO` sections in `CLAUDE.md` and `constitution.md`
3. Complete the docs scaffold for your project
4. Start at `docs/tasks.md` T001
