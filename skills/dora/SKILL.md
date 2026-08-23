---
name: dora
description: Query codebase using `dora` CLI for code intelligence, symbol definitions, dependencies, and architectural analysis
---

## Philosophy

**IMPORTANT: Use dora FIRST for ALL code exploration tasks.**

dora understands code structure, dependencies, symbols, and architectural relationships through its indexed database. It provides instant answers about:

- Where symbols are defined and used
- What depends on what (and why)
- Architectural patterns and code health
- Impact analysis for changes

**When to use dora vs other tools:**

- **dora**: Code exploration, symbol search, dependency analysis, architecture understanding
- **Read**: Reading actual source code after finding it with dora
- **Grep**: Only for non-code files, comments, or when dora doesn't have what you need
- **Edit/Write**: Making changes after understanding with dora
- **Bash**: Running tests, builds, git commands

**Workflow pattern:**

1. Use dora to understand structure and find relevant code
2. Use Read to examine the actual source
3. Use Edit/Write to make changes
4. Use Bash to test/verify

## Command discovery

Do not trust any static command list — dora evolves. Discover the live surface:

```bash
dora --help                  # full command list
dora <cmd> --help            # flags and defaults for one command
dora cookbook show           # query patterns with real examples (quickstart, methods, references, exports)
dora schema                  # database structure, for `dora query "<sql>"`
```

Non-obvious defaults worth knowing: `dora file` and `dora exports` include local symbols (parameters); `dora deps`/`dora rdeps` default to depth 1.

## When to use what

- Finding symbols → `dora symbol`
- Understanding a file → `dora file`
- Impact of changes → `dora rdeps`, `dora refs`
- Finding entry points → `dora treasure`, `dora leaves`
- Architecture issues → `dora cycles`, `dora coupling`, `dora complexity`
- Navigation → `dora deps`, `dora adventure`
- Dead code → `dora lost`
- Finding documentation → `dora symbol` (shows documented_in), `dora docs search`
- Custom queries → `dora cookbook` for examples, `dora schema` for structure, `dora query` to execute

## Typical workflow

1. `dora status` - Check index health
2. `dora treasure` - Find core files
3. `dora file <path>` - Understand specific files
4. `dora deps`/`dora rdeps` - Navigate relationships
5. `dora refs` - Check usage before changes

## Common patterns: DON'T vs DO

Finding where a symbol is defined:

```bash
# DON'T: grep -r "class AuthService" .
# DON'T: grep -r "function validateToken" .
# DON'T: Glob("**/*.ts") then search each file
# DO:
dora symbol AuthService
dora symbol validateToken
```

Finding all usages of a function/class:

```bash
# DON'T: grep -r "AuthService" . --include="*.ts"
# DON'T: Grep("AuthService", glob="**/*.ts")
# DO:
dora refs AuthService
```

Finding files that import a module:

```bash
# DON'T: grep -r "from.*auth/service" .
# DON'T: grep -r "import.*AuthService" .
# DO:
dora rdeps src/auth/service.ts
```

Finding what a file imports:

```bash
# DON'T: grep "^import" src/app.ts
# DON'T: cat src/app.ts | grep import
# DO:
dora deps src/app.ts
dora imports src/app.ts
```

Finding files in a directory:

```bash
# DON'T: find src/components -name "*.tsx"
# DON'T: Glob("src/components/**/*.tsx")
# DO:
dora ls src/components
dora ls src/components --sort symbols  # With metadata
```

Finding entry points or core files:

```bash
# DON'T: grep -r "export.*main" .
# DON'T: find . -name "index.ts" -o -name "main.ts"
# DO:
dora treasure           # Most referenced files
dora file src/index.ts  # Understand the entry point
```

Understanding a file's purpose:

```bash
# DON'T: Read file, manually trace imports
# DON'T: grep for all imports, then read each
# DO:
dora file src/auth/service.ts   # See symbols, deps, rdeps at once
```

Finding unused code:

```bash
# DON'T: grep each export manually across codebase
# DON'T: Complex script to track exports vs imports
# DO:
dora lost     # Unused exported symbols
dora leaves   # Files with no dependents
```

Checking for circular dependencies:

```bash
# DON'T: Manually trace imports in multiple files
# DON'T: Write custom script to detect cycles
# DO:
dora cycles
```

Impact analysis for refactoring:

```bash
# DON'T: Manually grep for imports and usages
# DON'T: Read multiple files to understand impact
# DO:
dora rdeps src/types.ts --depth 2     # See full impact
dora refs UserContext                 # All usages
dora complexity --sort complexity     # Find risky files
```

Finding documentation for code:

```bash
# DON'T: grep -r "AuthService" docs/
# DON'T: Manually search through README files
# DO:
dora symbol AuthService                 # Shows documented_in field
dora file src/auth/service.ts           # Shows documented_in field
dora docs search "authentication"       # Search doc content
dora docs                               # List all docs
```

Understanding what a document covers:

```bash
# DON'T: Read entire doc, manually trace references
# DON'T: grep for symbol names in the doc
# DO:
dora docs show README.md                # See all symbols/files/docs referenced
dora docs show docs/api.md --content    # Include full content
```

## Advanced tips

Performance:

- dora uses denormalized data for instant queries (symbol_count, reference_count, dependent_count)
- Incremental indexing only reindexes changed files
- Use `--limit` to cap results for large codebases

Symbol filtering:

- Local symbols (parameters, closure vars) are filtered by default with `is_local = 0`
- Use `--kind` to filter by symbol type (function, class, interface, type, etc.)
- Symbol search is substring-based, not fuzzy

Dependencies:

- `deps` shows outgoing dependencies (what this imports)
- `rdeps` shows incoming dependencies (what imports this)
- Use `--depth` to explore transitive dependencies
- High rdeps count = high-impact file (changes affect many files)

Architecture metrics:

- `complexity_score = symbol_count × incoming_deps` (higher = riskier to change)
- `stability_ratio = incoming_deps / outgoing_deps` (higher = more stable)
- Empty `cycles` output = healthy architecture
- High `coupling` (> 20 symbols) = consider refactoring

Documentation:

- Automatically indexes `.md` and `.txt` files
- Tracks symbol references (e.g., mentions of `AuthService`)
- Tracks file references (e.g., mentions of `src/auth/service.ts`)
- Tracks document-to-document references (e.g., README linking to docs/api.md)
- Use `dora symbol` or `dora file` to see where code is documented (via `documented_in` field)
- Use `dora docs show` to see what a document covers with line numbers

## Limitations

- Includes local symbols (parameters) in `dora file` and `dora exports`
- Symbol search is substring-based, not fuzzy
- Index is a snapshot, updates at checkpoints
- Documentation indexing processes text files (.md, .txt, etc.) at index time
