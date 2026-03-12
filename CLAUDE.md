# CLAUDE.md - Protocol & Routing

Only non-discoverable information lives here. Architecture is in `docs/design.md`. Code rules are in `constitution.md`. Read the codebase for everything else.

**Project status:** Pre-implementation. Start with [docs/tasks.md](docs/tasks.md) T001.

---

## Executable Commands

```bash
# Quality gate (run before every commit)
# TODO: replace with your language/framework quality gate
# e.g. Rust:   cargo check && cargo fmt && cargo clippy -- -D warnings && cargo test
# e.g. Node:   npm run lint && npm test
# e.g. Python: ruff check . && pytest
# e.g. Go:     go vet ./... && go test ./...
# e.g. C/C++:  make && make test

# Development
# TODO: replace with your run command
# e.g. cargo run / npm run dev / python -m app / go run . / make run
```

## Environment Setup

<!-- TODO: Uncomment if your project uses env vars (most web apps, servers, bots do):
Copy `.env.example` to `.env` and fill in values. Required vars are defined in
[docs/environment.md](docs/environment.md). The `.env` file must be gitignored
(see [constitution.md](constitution.md)).
-->

## Skill Loading (load the relevant skill before starting work)

| Task type | Load this skill |
|---|---|
| Any code implementation | [constitution](.claude/plugins/generic-project/skills/constitution/SKILL.md) |
<!-- OPTIONAL: Uncomment if your project uses these:
| Database queries / schema changes | [database-work](.claude/plugins/generic-project/skills/database-work/SKILL.md) |
| Docker / deployment / env vars | [devops-infra](.claude/plugins/generic-project/skills/devops-infra/SKILL.md) |
-->
<!-- TODO: add project-specific skills here -->

## Canonical Sources

| File | Read when... |
|---|---|
| [constitution.md](constitution.md) | Before writing any code (immutable rules) |
| [docs/spec.md](docs/spec.md) | Before implementing a feature |
| [docs/design.md](docs/design.md) | When making design/architecture decisions |
| [docs/adr/](docs/adr/) | When questioning a technology choice or trade-off |
| [docs/tasks.md](docs/tasks.md) | To find the next task |

<!-- OPTIONAL: Add rows for optional docs you've included from docs/optional/:
| [docs/environment.md](docs/environment.md) | For env vars, dependency versions, Docker details |
| [docs/schema.md](docs/schema.md) | Before any database/data model change |
| [docs/api.md](docs/api.md) | Before modifying endpoints |
| [docs/protocol.md](docs/protocol.md) | Before implementing any send/receive or framing logic |
| [docs/file-format.md](docs/file-format.md) | Before implementing any read/write path |
| [docs/grammar.md](docs/grammar.md) | Before modifying the lexer, parser, or AST |
| [docs/threat-model.md](docs/threat-model.md) | Before implementing any auth, input handling, or data access |
| [docs/runbook.md](docs/runbook.md) | Before deploying or changing service configuration |
-->
