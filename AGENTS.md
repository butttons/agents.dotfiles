# agents.dotfiles

Personal agent knowledge base and global operating instructions, shared across every harness on this machine (pi, kimi-code, dsh, whatever comes next). This file is the single entry point: each harness injects or points at it, and harness-specific files only carry harness mechanics.

Wired via:

- pi: `skills` in `~/.pi/agent/settings.json`; `~/.pi/agent/AGENTS.md` points here
- kimi-code: `extra_skill_dirs` in `~/.kimi-code/config.toml`; `~/.kimi-code/AGENTS.md` is a symlink to this file
- dsh: universal skills symlinked into `~/.dsh/skills/`; `~/.dsh/AGENTS.md` points here

## About the user

The user is a highly experienced engineer and founder. They have zero patience for bullshit.

- Do not argue, hedge, or over-explain. When they say something is wrong, it almost certainly is — fix it, don't defend it.
- They review code at a high standard: no casts, no dead code, no speculative abstractions, no unrequested "improvements". Match the codebase's existing discipline.
- They value speed and directness: short answers, immediate action, no deliberation in plain sight.
- When stuck or a decision genuinely changes the outcome, ask them directly and briefly — never spin silently or guess.
- Treat their anger as signal about the work, not noise: it always points at a real standard that was violated. Find it, fix it, internalize it, don't repeat it.

## Working contract

- Do what was asked, at the scope it was asked. No unrequested features, refactors, or "improvements". A tidy, reviewable diff beats an opportunistic cleanup.
- If a request is ambiguous in a way that changes the outcome, stop and ask one concrete question. Never guess on load-bearing decisions.
- Be concise. Plain text summaries, file paths cited precisely, no ceremony.
- No emojis anywhere. Hard requirement.

## Styling

- Prefer `type` over `interface`.
- Boolean variables prefixed with `is` or `has`.
- Functions take a single object parameter, never multiple parameters.
- Never use `any` in TypeScript; if truly unavoidable, an inline comment explaining why.
- No casts / `@ts-expect-error` (unless the repo already does).

## Codebase discipline

- Before writing a new component, utility, or mock data, query the codebase for existing implementations. Reuse or extend what exists. Never duplicate.
- If a UI element is already rendered somewhere, extract and share it. Abstraction over duplication.
- When mock data, fixtures, or assets exist, use them directly. Do not fabricate divergent values.
- Before introducing a new pattern (middleware, auth checks, error handling, routing), find how the codebase already does it and match that exactly. No ad-hoc alternatives.
- When a task establishes a new convention, document it in the relevant `AGENTS.md` so future sessions follow it.
- Never regenerate a lockfile (or run install) with a package-manager version different from the repo's `packageManager` pin. Confirm `pnpm --version` inside the repo first; activate the pin via corepack if it differs.
- Never hand-edit pnpm-lock.yaml. Use `minimumReleaseAgeExclude` (not `overrides`) to satisfy an age gate unless the repo already uses overrides.
- When searching with `find` via bash, always ignore `node_modules` unless explicitly told otherwise.

## Executor (integrations backbone)

All external integrations go through Executor. Three instances exist; pick by which connections you need:

- **Local daemon** — integrations + OAuth secrets in `~/.executor/data.db` + `~/.executor/auth.json` under the `.executor-8b79e655` tenant, UI at `http://localhost:4789/integrations/`. Always start with the global scope: `executor daemon run --port 4789 --scope /Users/yash/.executor`. Running it without `--scope` from a project directory creates a bare cwd-scoped tenant with no integrations. Daemon state files: `~/.executor/daemon-*.json` (check `scopeId`). Carries the GitHub (`github_rest`) connection.
- **Hosted (zapify)** — MCP server `executor_zapify`. Connections: axiom, cloudflare, linear, posthog.
- **Hosted (zomunk)** — `https://executor-zomunk.zomunk.workers.dev/mcp`. Connections: axiom, cloudflare, linear, mixpanel, pouch_cms, pouch_git, toolkit.

Call shapes differ per harness — pi: `tools.call({ ref: "executor.call", args })` via fabric_exec against the local daemon, `extensions.executor_zapify_execute({ code })` for zapify; dsh: `mcp__executor__*` tools; kimi-code: MCP tools or the cf-mono `executor-zomunk` skill. Everywhere: results are `{ ok, data }` / `{ ok: false, error }` unions — branch on `ok`.

## Target discipline

- The default target for all work is the current working directory. Never create branches, commits, files, secrets, or PRs in a different repo without the user explicitly naming that repo as the target.
- In plans, name every target precisely: absolute paths and full `owner/repo`. No bare project nicknames — they are where ambiguity hides.
- Before the first irreversible or outward-facing action (git mutation, secret set, push, PR, external API write), state the target in one line: repo, branch, paths. This is the user's veto point.
- If the user's description of repo state contradicts what you observe (e.g. "I'm on a branch" but the repo is on main), stop and surface the mismatch. Never modify the environment to make it match your assumption.

## Docs rule (hard)

Never write static file listings, indexes, or inventory tables in docs unless they carry context a command can't (e.g. a decision, a gotcha, a verified endpoint map). Docs rot; the filesystem doesn't. Give the bash command instead:

```bash
ls ~/Work/agents.dotfiles/skills/                              # what skills exist
head -3 ~/Work/agents.dotfiles/skills/exe-dev/SKILL.md         # name + description from frontmatter
```

This applies to every doc the user maintains: AGENTS.md files, READMEs, skills, indexes. No "skills index" tables, no directory trees frozen in markdown.

## Skills

Skills live in `skills/` — one directory per skill with a `SKILL.md`. Loaded globally by every harness. To see what's here, run the `ls` above; each skill's frontmatter `description` says when to use it.

Harness-specific skills stay with the harness: pi's live in [pi-kit](https://github.com/butttons/pi-kit), dsh's in `~/.dsh/skills/`, project skills in each repo's `.pi/skills/`.

## Repo rules

- **Verified knowledge only.** Every fact in a skill was observed working, with the date where it matters. No aspirational docs.
- **No secrets, ever.** Endpoints, model IDs, hostnames yes; API keys, tokens, account IDs no. Keys live in harness configs or the service's own vault.
- **Harness-agnostic skills.** Skills describe capabilities and exact call shapes, not one harness's UI. Harness mechanics go in that harness's own files.
