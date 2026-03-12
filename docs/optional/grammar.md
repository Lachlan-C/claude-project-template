# Grammar Reference

<!-- OPTIONAL DOC: Include this file only if your project defines a formal grammar, language,
     or DSL — anything with a parser and defined syntax.
     Compilers, interpreters, regex engines, shells, template engines, query languages, and
     config formats all need this. Self-contained tools with no syntax to parse usually don't.
     To use: copy this file to docs/grammar.md and add it to the Related files headers
     in design.md, spec.md, tasks.md, and the Canonical Sources table in CLAUDE.md. -->

> **Related files:** [../design.md](../design.md) (system design) | [../adr/](../adr/) (why these choices) | [../../constitution.md](../../constitution.md) (immutable rules)

This document is the single source of truth for the language or DSL grammar — syntax rules, token definitions, operator precedence, and parse tree shapes. **Read this before implementing any lexer, parser, or evaluator change.**

> **This doc owns:** All grammar rules, token types, precedence/associativity tables, reserved words, and AST node shapes. Other docs reference grammar details here by link — they never repeat production rules inline.

---

## Overview

<!-- TODO: one paragraph describing the language or DSL — purpose, paradigm, notable characteristics -->

| Property | Value |
|---|---|
| Paradigm | <!-- TODO: imperative / functional / declarative / expression-only / etc. --> |
| Typing | <!-- TODO: static / dynamic / untyped --> |
| Whitespace | <!-- TODO: significant (Python-style) / insignificant --> |
| Case sensitivity | <!-- TODO: case-sensitive / case-insensitive --> |
| Comments | <!-- TODO: `//` line / `/* */` block / `#` / none --> |
| String delimiters | <!-- TODO: `"..."` / `'...'` / both --> |

---

## Lexer — Token Types

| Token | Pattern / Description | Example |
|---|---|---|
| `INTEGER` | <!-- TODO: e.g. `[0-9]+` --> | `42` |
| `FLOAT` | <!-- TODO: e.g. `[0-9]+\.[0-9]+` --> | `3.14` |
| `STRING` | <!-- TODO: e.g. quoted UTF-8 with escape sequences --> | `"hello"` |
| `IDENT` | <!-- TODO: e.g. `[a-zA-Z_][a-zA-Z0-9_]*` --> | `foo_bar` |
| `KEYWORD` | Reserved word (see Reserved Words) | `if`, `let` |
| <!-- TODO --> | | |

### Reserved Words

```
<!-- TODO: list all keywords, separated by spaces or commas -->
<!-- e.g.: if else while for return let fn true false null -->
```

### Whitespace & Comments

- Whitespace (space, tab, newline) is <!-- TODO: ignored / significant -->.
- Line comments: `<!-- TODO: e.g. // ... -->` — ignored by the lexer.
- Block comments: `<!-- TODO: e.g. /* ... */ -->` — <!-- TODO: nestable or not -->.

---

## Parser — Grammar (EBNF)

```ebnf
(* TODO: fill in your grammar. Use EBNF notation:
   rule     = body ;
   sequence = A B C ;
   choice   = A | B ;
   optional = [ A ] ;
   repeat   = { A } ;
   group    = ( A ) ;
*)

program     = { statement } EOF ;

statement   = (* TODO *) ;

expression  = (* TODO *) ;
```

### Operator Precedence

Listed lowest to highest. Operators on the same row have equal precedence.

| Level | Operators | Associativity |
|---|---|---|
| 1 (lowest) | `||` | left |
| 2 | `&&` | left |
| 3 | `==` `!=` | left |
| 4 | `<` `>` `<=` `>=` | left |
| 5 | `+` `-` | left |
| 6 | `*` `/` `%` | left |
| 7 (highest) | unary `-` `!` | right |

<!-- TODO: adjust table to match your language's actual operators -->

---

## AST — Node Shapes

Each parser production maps to an AST node. Node fields use the same names throughout the codebase.

### `Program`
```
Program {
    body: Vec<Statement>
}
```

### `<!-- TODO: node name -->`
```
<!-- TODO: add one block per node type -->
```

---

## Semantic Rules

Rules enforced after parsing (type checking, name resolution, etc.):

- <!-- TODO: e.g. Variables must be declared before use -->
- <!-- TODO: e.g. Functions must return a value on all paths -->
- <!-- TODO: e.g. Integer overflow is a compile-time error -->

---

## Error Recovery

| Situation | Recovery strategy |
|---|---|
| Unexpected token in statement | <!-- TODO: e.g. skip to next `;` or newline --> |
| Unclosed delimiter | <!-- TODO: e.g. emit error, treat as closed at end of block --> |
| Unknown identifier | <!-- TODO: e.g. emit error, continue as `null` typed --> |

---

## Example Programs

```
<!-- TODO: paste 1–3 short programs that exercise key grammar features -->
```
