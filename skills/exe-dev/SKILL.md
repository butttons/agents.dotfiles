---
name: exe-dev
description: "Work with exe.dev: persistent Linux VMs over SSH, the exe CLI, custom domains, and the LLM integration gateway (verified endpoint/model map for command-code, opencode-go, kimi-code). Use when creating/controlling exe.dev VMs, calling LLMs through exe integrations, or debugging exe networking."
---

# exe.dev

Persistent Linux VMs (Cloud Hypervisor, exeuntu image, systemd + docker 29 present) behind exe.dev's edge: they terminate TLS and proxy to the VM's web servers, and handle `ssh <vm>.exe.xyz`. No public IP per VM.

## SSH access

- CLI: `ssh exe.dev <cmd>` (e.g. `ssh exe.dev ls --json`). VMs: `ssh <vm>.exe.xyz`.
- Account key lives in 1Password: `export SSH_AUTH_SOCK="$HOME/Library/Group Containers/2BUA8C4S2C.com.1password/t/agent.sock"`. If signing fails with "communication with agent failed", run `ssh exe.dev whoami` in an interactive terminal once to unlock.
- New VMs need `ssh -o StrictHostKeyChecking=accept-new <vm>.exe.xyz true` before scripting against them.
- `Please complete registration by running: ssh exe.dev` = the key isn't registered to the account (or the agent offered the wrong key). `-o IdentitiesOnly=yes -i <key>` to force one.

## CLI notes (verified 2026-08-23)

- `ls`, `new --name=<n>`, `rm`, `restart`, `tag`, `domain`, `share`, `whoami`, `ssh-key`, `stat`. VM `ls` takes `--json`; parse it, never scrape stdout.
- `integrations` subcommands: `list`, `add`, `edit`, `remove`, `attach`, `detach`, `setup`, `test`, `catalog`. **`integrations list` has no `--json`** and `test` does not apply to `llm`-type integrations.
- `exe.dev new --name=x` is idempotent-ish; check `ls --json` first.
- `domain add` can fail on stdout with exit 0 and exe's resolver lags — always verify with `domain ls --json` and retry.
- Custom domains: CNAME `<domain>` → `<vm>.exe.xyz`, DNS-only (proxied/orange-cloud breaks exe's TLS), then `ssh exe.dev domain add`.

## LLM integration gateway (verified 2026-08-23)

LLM integrations hold provider keys off-VM; attached VMs call `https://<name>.int.exe.xyz`. The default `llm` integration is attached to `auto:all`. `/v1/models` on the gateway returns an empty list — cosmetic, inference works regardless.

Verified call shapes (from any attached VM, no keys needed):

| provider | endpoint | shape | verified models |
| --- | --- | --- | --- |
| command-code | `POST /command-code/v1/chat/completions` | OpenAI chat | `xiaomi/mimo-v2.5` |
| opencode-go | `POST /opencode-go/v1/chat/completions` | OpenAI chat | `mimo-v2.5`, `deepseek-v4-flash` |
| kimi-code | `POST /kimi-code/v1/messages` | Anthropic messages (needs `anthropic-version: 2023-06-01` header) | `kimi-for-coding` |

Gotchas:

- Provider prefixes route per provider name (`/kimi-code/...`); the generic `/v1/...` routes by model ID.
- Provider base URLs must point at the API root, not the website: opencode-go = `https://opencode.ai/zen/go` (setting `https://opencode.ai` returns the site's HTML SPA instead of JSON). Upstream model list: `GET https://opencode.ai/zen/go/v1/models`.
- command-code upstream = Command Code provider API, base `https://api.commandcode.ai/provider/v1`, model discovery `GET /provider/v1/models`. On exe it is OpenAI-shaped only — `/command-code/v1/messages` returns `unsupported endpoint`, so Claude models don't route through it as configured.
- kimi-code (Kimi for coding) is Anthropic-shaped; chat/completions is rejected.
- Upstream model IDs differ per provider for the same underlying model (e.g. command-code wants `xiaomi/mimo-v2.5`, opencode-go wants `mimo-v2.5`).

## Automation

`hive exe` wraps the common flow: `new` (idempotent VM create), `share` (point exe's HTTPS proxy at the app port), `domain` (CNAME upsert via Cloudflare creds + `domain add`, with the exit-0 gotcha handled). See the hive repo.
