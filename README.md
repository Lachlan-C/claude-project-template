# claude-project-template

Opinionated project template for Claude Code. Works for **any type of programming project** — web apps, CLI tools, libraries, compilers, games, bots, and more.

## What's included

### Core files (always used)
- **`CLAUDE.md`** — routing protocol for Claude. Points to canonical sources, lists commands, and maps task types to skills.
- **`constitution.md`** — immutable rules. Three-tier boundary system (always/ask/never), security requirements, design principles (YAGNI, KISS, DRY), and testing standards.

### Core docs scaffold (always used)
| File | Purpose |
|---|---|
| `docs/spec.md` | What & why — user flows, acceptance criteria, non-goals |
| `docs/design.md` | Architecture, tech stack, implementation phases |
| `docs/tasks.md` | Task checklist with validation gates |
| `docs/adr/` | Architecture decision records |

### Optional docs (use based on project type)
| File | Use when... | Examples |
|---|---|---|
| `docs/optional/schema.md` | Project has a persistent data store | Web apps, search engines, databases, blockchain |
| `docs/optional/api.md` | Project exposes a network or library API | REST APIs, gRPC, WebSocket servers, public libraries |
| `docs/optional/environment.md` | Project uses env vars, Docker, or external services | Web apps, bots, deployed services |
| `docs/optional/protocol.md` | Project implements a binary/text network protocol | Databases, BitTorrent, emulators, distributed systems, custom RPC |
| `docs/optional/file-format.md` | Project reads/writes custom binary file formats | Databases, compilers, game engines, archivers, emulators |
| `docs/optional/grammar.md` | Project defines a formal grammar, language, or DSL | Compilers, interpreters, regex engines, shells, query languages |
| `docs/optional/threat-model.md` | Project handles auth, user data, money, or untrusted network input | Web apps, APIs, databases, bots, browsers, network stacks |
| `docs/optional/runbook.md` | Project is deployed and runs continuously | Web apps, bots, servers, databases, background workers |

### Claude plugin (`.claude/plugins/generic-project/`)

**Commands:**
- **`/audit-docs`** — dispatches the docs-auditor agent on demand
- **`/quality-gate`** — runs the full quality gate (formatter, linter, tests) as defined in `CLAUDE.md`

**Agents:**
- **`docs-auditor`** — audits documentation in three phases: doc-to-doc consistency, docs vs code, and docs vs external standards. Understands which docs are optional.

**Skills** (auto-loaded based on task type):
| Skill | Triggers on | Optional? |
|---|---|---|
| `constitution` | Any code implementation task | No (always) |
| `database-work` | SQL queries, schema changes, migrations | Yes |
| `devops-infra` | Dockerfile, docker compose, env vars, deployment | Yes |

### Settings (`.claude/settings.json`)

Enables a base set of Claude plugins.

## Usage

### 1. Clone and identify your project type

```bash
git clone <this-repo> my-project
cd my-project
```

### 2. Include optional docs (if needed)

Copy any relevant optional docs from `docs/optional/` to `docs/`:

```bash
# Only if your project has a database/data store:
cp docs/optional/schema.md docs/schema.md

# Only if your project exposes an API:
cp docs/optional/api.md docs/api.md

# Only if your project uses env vars, Docker, or external services:
cp docs/optional/environment.md docs/environment.md

# Only if your project implements a network protocol:
cp docs/optional/protocol.md docs/protocol.md

# Only if your project reads/writes custom binary file formats:
cp docs/optional/file-format.md docs/file-format.md

# Only if your project defines a formal grammar or DSL:
cp docs/optional/grammar.md docs/grammar.md

# Only if your project handles auth, user data, money, or untrusted input:
cp docs/optional/threat-model.md docs/threat-model.md

# Only if your project is a deployed, long-running service:
cp docs/optional/runbook.md docs/runbook.md
```

### 3. Uncomment relevant sections

Each core file has `<!-- OPTIONAL: ... -->` comment blocks for project-type-specific content. Uncomment the sections that apply to your project:

- **`constitution.md`** — Uncomment SQL rules (if DB), auth rules (if auth), deployment rules (if Docker), etc.
- **`CLAUDE.md`** — Uncomment skill loading rows and canonical source rows for optional docs you included.
- **`docs/design.md`** — Uncomment Security Architecture, Data Model, and Deployment sections as needed.
- **`docs/tasks.md`** — Uncomment database tasks, env var tasks, and deployment phases as needed.
- **`docs/spec.md`** — Uncomment Security and Data Integrity success criteria sections as needed.

### 4. Fill in the TODOs

Complete the `TODO` sections in `CLAUDE.md`, `constitution.md`, and all docs.

### 5. Start building

Begin at `docs/tasks.md` T001.

## Project type examples

| Project type | Optional docs to include | Key constitution sections to uncomment |
|---|---|---|
| **Web app** (full stack) | schema.md, api.md, environment.md, threat-model.md, runbook.md | SQL, auth, CSRF, deployment |
| **REST API** | schema.md, api.md, environment.md, threat-model.md, runbook.md | SQL, auth, deployment |
| **CLI tool** | (none typically) | (none typically) |
| **Library/SDK** | api.md | (none typically) |
| **Bot** (Discord, Slack) | environment.md, runbook.md | (none typically) |
| **Compiler/interpreter** | grammar.md, file-format.md | (none typically) |
| **Shell** | grammar.md | (none typically) |
| **Game** | (none typically) | (none typically) |
| **Search engine** | schema.md, api.md, environment.md, runbook.md | SQL |
| **Database** (from scratch) | schema.md, api.md, protocol.md, file-format.md | (none typically) |
| **Document DB** (build-your-own) | schema.md, api.md, protocol.md, file-format.md | (none typically) |
| **Emulator** | file-format.md | (none typically) |
