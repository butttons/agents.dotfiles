# agents.dotfiles

Personal agent knowledge base, shared across every harness on this machine (pi, kimi-code, whatever comes next). One directory, all agents read it.

## What goes here

- `skills/` — Agent Skills (SKILL.md per directory), loaded by both harnesses:
  - pi: `skills` array in `~/.pi/agent/settings.json`
  - kimi-code: `extra_skill_dirs` in `~/.kimi-code/config.toml`

## Rules

- **Verified knowledge only.** Every fact in a skill was observed working, with the date where it matters. No aspirational docs.
- **No secrets, ever.** Endpoints, model IDs, hostnames yes; API keys, tokens, account IDs no. Keys live in the harness configs or the service's own vault.
- **Harness-agnostic.** Skills describe capabilities and exact call shapes, not one tool's UI.
- Update the index below when adding a skill.

## Skills index

| skill | what it covers |
| --- | --- |
| [exe-dev](skills/exe-dev/SKILL.md) | exe.dev VMs: ssh CLI, LLM integration gateway (verified endpoint/model map), domains, gotchas |
