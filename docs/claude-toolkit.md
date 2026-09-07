# Claude Code toolkit — the five recommended repos, sourced and wired up

Set up 2026-09-07. Every source below was verified against the canonical repo / npm registry before anything was written down — no lookalikes.

## What each name actually is

| You asked for | Real project | What it does |
|---|---|---|
| **Omniroute** | [diegosouzapw/OmniRoute](https://github.com/diegosouzapw/OmniRoute) (~62k stars, MIT) | Local AI gateway: one endpoint, 350+ providers (150+ free), quota-aware auto-fallback, token compression. Runs on your machine at `http://localhost:20128/v1`. |
| **Caveman** | [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman) | Claude Code plugin that compresses replies (~65% fewer output tokens) while keeping code, paths, and errors exact. Also the "Caveman" compression engine OmniRoute stacks with RTK. |
| **Superpowers** | [obra/superpowers](https://github.com/obra/superpowers) via [obra/superpowers-marketplace](https://github.com/obra/superpowers-marketplace) | Jesse Vincent's skills framework: brainstorm → written plan → TDD → review → finish-branch workflows that trigger automatically. |
| **Claude code setup** | [OmniRoute's Claude Code Configuration guide](https://github.com/diegosouzapw/OmniRoute/wiki/Claude-Code-Configuration) | The wiring step that points Claude Code at the gateway (`omniroute setup-claude`, `omniroute launch`). Not a separate tool — it's how the pieces above connect. |
| **Claude security** | [anthropics/claude-code-security-review](https://github.com/anthropics/claude-code-security-review) | Anthropic's official AI security review: a GitHub Action for PRs plus the `/security-review` command already built into Claude Code. |

## What landed in this repo

- **`.github/workflows/security-review.yml`** — the Anthropic security-review Action on every PR. It ships dormant (job skips) so nothing turns red before you configure it. Activate with two clicks:
  1. *Settings → Secrets and variables → Actions → New repository secret*: `CLAUDE_API_KEY` — an Anthropic API key enabled for Claude API + Claude Code.
  2. *Variables tab → New repository variable*: `ENABLE_SECURITY_REVIEW` = `true`.
- **`docs/claude-settings.example.json`** — repo-level config: both plugin marketplaces + both plugins enabled, plus a targeted permission allowlist (plugin management commands, the OmniRoute install, the local preview server, and read-only fetches of github.com / npm registry) so Claude needs fewer approvals in this repo. Claude Code's sandbox rightly won't let a session write its own live settings — even when asked in chat, since that's exactly how a hijacked agent would self-escalate — so a human flips the switch:
  ```bash
  mkdir -p .claude && cp docs/claude-settings.example.json .claude/settings.json
  ```
  Commit that, and every future session (cloud sessions included) starts with the plugins offered and those commands pre-approved.
- **`CLAUDE.md`** — conventions for this site so any Claude session edits it correctly.

## One-time setup on your own machine (~2 minutes)

```bash
# Caveman plugin
claude plugin marketplace add JuliusBrussee/caveman
claude plugin install caveman@caveman

# Superpowers plugin
claude plugin marketplace add obra/superpowers-marketplace
claude plugin install superpowers@superpowers-marketplace
# restart Claude Code after plugin installs

# OmniRoute gateway (npm package verified: repository → github.com/diegosouzapw/OmniRoute, v3.8.50, MIT)
npm install -g omniroute
omniroute serve          # gateway at http://localhost:20128/v1 — works with zero API keys via free providers
omniroute setup-claude   # generates per-model Claude Code launch profiles
omniroute launch         # starts Claude Code routed through the gateway
```

## Daily usage, tuned for this repo

- **Token economy**: `/caveman lite` keeps replies readable; `full`/`ultra` squeeze harder. Use `/caveman off` when drafting customer- or team-facing copy for `sop.html` / `tour.html` — you want full prose judgment there, not grunts. `/caveman-stats` shows what you're saving.
- **Structured edits**: for anything bigger than a typo in the SOP or Tour, superpowers' `/brainstorm` → plan → execute flow is worth it; it forces the "what exactly changes and why" step before HTML gets touched.
- **Before merging**: run `/security-review` in Claude Code (same engine as the CI Action), especially since these pages ship to GitHub Pages.
- **OmniRoute**: point high-volume, low-sensitivity work (bulk rewrites, format conversions, experiments) through the gateway; keep sensitive work on your direct Anthropic connection.

## Getting more autonomy from Claude

Three levers, from most to least targeted:

1. **This repo**: run the `cp` command above and commit. The allowlist in it is deliberately narrow — widen it by adding rules to `.claude/settings.json` (`"Bash(npm *)"`-style prefix wildcards; run `/permissions` in Claude Code to manage them interactively).
2. **Your machine, all projects**: `/permissions` in any Claude Code session adds user-level allow rules to `~/.claude/settings.json`; `/config` can set a more permissive default mode (e.g. `acceptEdits`).
3. **Cloud sessions** (claude.ai/code): the permission mode is chosen per session/environment when you launch — the in-session agent can never raise its own mode, by design.

## The fine print (read once, it's short)

- **Where your prompts go**: through OmniRoute, prompts are sent to whichever upstream provider it selects. The project itself flags ~13 free providers as "terms-risk," and free quotas/terms change without notice. **Do not route Trade Wings client data, CRE/collections material, or anything from Close through free third-party providers.** Sensitive work stays on direct Anthropic.
- **"Unlimited free tokens"**: the cost isn't $0 — it's your data plus provider-ToS risk. Treat the claim accordingly.
- **Security-review Action**: Anthropic's own caveat — not hardened against prompt injection, so review trusted PRs only, and keep *Require approval for all external contributors* enabled.
- **Plugins run with your Claude Code's permissions**: both are popular and widely reviewed, but update deliberately (`claude plugin update ...`), not automatically.
