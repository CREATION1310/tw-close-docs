# Runbook: ccusage + the phone UI

Two run-on-demand upgrades from `claude-code-top10.md`, staged here with verified commands (checked at source 2026-09-07) and the security fine print. Neither is a repo file — both are `npx` tools you run on your Mac — so "pre-staged" means: this runbook, plus a permission allow-rule for the safe one so Claude can run it for you without a prompt.

## #7 — ccusage: token & cost reports

[ryoppippi/ccusage](https://github.com/ryoppippi/ccusage) — reads Claude Code's **local** usage data and prints spend reports. Fully offline; nothing is ever sent anywhere. This is how you prove what caveman mode saves.

**Run it (zero setup):**
```bash
npx ccusage@latest            # default view
npx ccusage@latest daily      # by day
npx ccusage@latest weekly
npx ccusage@latest monthly
npx ccusage@latest session    # by conversation
npx ccusage@latest blocks     # Claude's 5-hour billing windows, with live monitoring
npx ccusage@latest --offline  # use cached pricing, no network at all
```

**Pre-staged for frictionless use:** `Bash(npx ccusage*)` is in `docs/claude-settings.example.json`'s allow-list. Once that's live (see below), you can just ask a Claude Code session "show me my token usage this week" and it runs `ccusage` without a permission prompt. It's read-only and local, so auto-approving it is safe.

**Statusline (optional):** `ccusage`'s `statusline` subcommand can feed a live cost readout into Claude Code's status bar — it's Beta; see the repo's statusline docs if you want it.

## #6 — the phone UI (CloudCLI / claudecodeui)

[siteboon/claudecodeui](https://github.com/siteboon/claudecodeui) — a web + mobile interface for your **local** Claude Code sessions (chat, files, git, terminal) from any device. Given how much of your work happens from a phone, this is the sleeper pick — but it's the one item on the list with real security weight, because it exposes your machine's sessions over a network.

**Run it (on the Mac that has your sessions):**
```bash
npx @cloudcli-ai/cloudcli
# or: npm install -g @cloudcli-ai/cloudcli && cloudcli
```
Requires **Node.js v22+**. Serves on **http://localhost:3001**. Your Mac must stay awake and running for the phone to reach it.

**Reaching it from your phone — do NOT just forward port 3001 to the internet.** That would put a terminal into your Mac behind a plain URL. Pick one of these instead, safest first:

1. **Tailscale (recommended).** Install it on the Mac and the phone (same account). They join a private mesh; open `http://<mac-tailscale-name>:3001` on the phone. Nothing is ever exposed publicly — this is the cleanest option and free for personal use.
2. **Cloudflare Tunnel with Access**, or **ngrok with auth** — a public URL, but gated behind a login you control. Only if you can't use a mesh VPN.
3. **SSH port-forward** from the phone (e.g. a terminal app): `ssh -L 3001:localhost:3001 you@mac` — no listener exposed at all, but fiddly on mobile.

**Also:**
- Claude Code tools are **disabled by default** in CloudCLI (good — it won't run commands automatically). Enable only the ones you need, in its Settings.
- **AGPL-3.0 licensed:** fine to self-host as-is; the copyleft only bites if you *modify* it and run it as a service for others.
- It is **not** added to the permission allow-list on purpose — auto-approving a command that opens a network-exposed server is exactly the kind of thing that should always prompt.

## Turning on the ccusage allow-rule

The rule is staged in `docs/claude-settings.example.json`. To make it live in this repo, re-copy the example over your settings (it's a superset of what's already there, so nothing is lost):
```bash
cp docs/claude-settings.example.json .claude/settings.json
git add .claude/settings.json && git commit -m "Add ccusage to allowlist" && git push
```
