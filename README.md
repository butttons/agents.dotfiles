# agents.dotfiles

Personal agent knowledge base and global operating instructions, shared across every harness on this machine (pi, kimi-code, dsh, whatever comes next).

- **`AGENTS.md`** — master operating instructions: working contract, code style, codebase discipline, docs rules. This is the single file every harness points at.
- **`skills/`** — one directory per skill with a `SKILL.md`. Loaded globally by every harness via symlinks.
- **`scripts/`** — utility scripts for cost analysis and tooling.

## Wiring

- `~/.agents/skills` is a symlink to `~/Work/agents.dotfiles/skills` (this repo is the source of truth).
- pi: `skills` in `~/.pi/agent/settings.json` points to `~/.agents/skills`.
- kimi-code: `extra_skill_dirs` in `~/.kimi-code/config.toml` points to `~/.agents/skills`.
- dsh: `~/.dsh/skills/` is a symlink to `~/.agents/skills`.
- `~/.pi/agent/AGENTS.md` and `~/.kimi-code/AGENTS.md` are symlinks to this file; `~/.dsh/AGENTS.md` points here.

See `AGENTS.md` for the full operating contract.
