# Runbook: staging the rest of the top 10

Everything from `claude-code-top10.md` not already installed, staged with commands verified at source (2026-09-07) and the security fine print. Two items (ccusage, the phone UI) get full setup plus a permission allow-rule; the other six are **documented install-on-demand and deliberately not force-loaded** — every always-on MCP server's tool definitions load into *every* session's context, so a kitchen-sink setup makes the agent slower, pricier, and worse at choosing. That's the "install few, deliberately" principle from the top-10 doc. Add each when a real need appears.

Installed already (for reference): caveman + superpowers plugins, OmniRoute gateway, the security-review + @claude workflows, and document-skills.

---

## #7 — ccusage: token & cost reports

[ryoppippi/ccusage](https://github.com/ryoppippi/ccusage) — reads Claude Code's **local** usage data and prints spend reports. Fully offline; nothing is ever sent anywhere. This is how you prove what caveman mode saves.

```bash
npx ccusage@latest            # default view
npx ccusage@latest daily      # by day
npx ccusage@latest weekly
npx ccusage@latest monthly
npx ccusage@latest session    # by conversation
npx ccusage@latest blocks     # Claude's 5-hour billing windows, with live monitoring
npx ccusage@latest --offline  # cached pricing, no network at all
```

`Bash(npx ccusage@latest*)` is in `docs/claude-settings.example.json`'s allow-list — read-only and local, so a session can run it on request without a prompt. (The rule pins `@latest` deliberately: a bare `ccusage*` glob would also auto-approve a typosquat like `npx ccusage-evil`.) Its `statusline` subcommand (Beta) can feed a live cost readout into Claude Code's status bar.

## #6 — the phone UI (CloudCLI / claudecodeui)

[siteboon/claudecodeui](https://github.com/siteboon/claudecodeui) — a web + mobile interface for your **local** Claude Code sessions (chat, files, git, terminal) from any device. The one item on the list with real security weight, because it exposes your machine's sessions over a network.

```bash
npx @cloudcli-ai/cloudcli
# or: npm install -g @cloudcli-ai/cloudcli && cloudcli
```
Requires **Node.js v22+**. Serves on **http://localhost:3001**. Your Mac must stay awake for the phone to reach it.

**Reaching it from your phone — do NOT just forward port 3001 to the internet.** That would put a terminal into your Mac behind a plain URL. Pick one, safest first:

1. **Tailscale (recommended).** Install on the Mac and phone (same account); open `http://<mac-tailscale-name>:3001`. Nothing is ever exposed publicly — free for personal use.
2. **Cloudflare Tunnel with Access**, or **ngrok with auth** — a public URL gated behind a login you control.
3. **SSH port-forward** from the phone: `ssh -L 3001:localhost:3001 you@mac`.

Claude Code tools are **disabled by default** in CloudCLI — enable only what you need. AGPL-3.0 (copyleft only bites if you modify and serve it to others). Deliberately **not** allow-listed: a command that opens a network server should always prompt.

---

## The other six — documented, add per need

### #3 — wshobson/agents (specialist subagent marketplace) — MARKETPLACE STAGED
The marketplace is staged in `docs/claude-settings.example.json` (`extraKnownMarketplaces` → `wshobson-agents`), so once that's applied it's already "known" here — you just run `/plugin` inside Claude Code and install **only** the pack you want (e.g. a language or domain pack). Don't enable all 200+ — that's the context-bloat trap. Third-party prompts; skim a pack before enabling. To make it known globally instead: `claude plugin marketplace add wshobson/agents`.

### #4 — claude-task-master (cross-session project planning)
Keyless via your Claude Code auth:
```bash
claude mcp add task-master-ai -- npx -y task-master-ai
```
Add it to a specific project when you have a multi-week, many-part effort (an enforcement-file pipeline, a site overhaul) — not to every session. In chat: "Initialize taskmaster in my project."

### #5 — microsoft/playwright-mcp (real browser control) — WIRED INTO THIS REPO
Now declared in this repo's `.mcp.json` and pre-approved in `docs/claude-settings.example.json` (`enabledMcpjsonServers`). On the next `claude` session in this repo, Claude Code offers to enable it (approve the one-time trust prompt; or apply the settings example to skip even that). It earns its place here because QA-ing the published site is exactly what this repo is for — use it to open `index.html`/`tour.html`/`sop.html`, click through, and screenshot after edits. Lighter alternative you already run: [obra/superpowers-chrome](https://github.com/obra/superpowers-chrome). To add it to *another* project manually: `claude mcp add playwright npx @playwright/mcp@latest`.

### #8 — davila7/claude-code-templates (component catalog + analytics)
```bash
npx claude-code-templates@latest
```
Browsable installer ([aitmpl.com](https://aitmpl.com)) for agents/commands/hooks/MCPs, plus an analytics dashboard and setup health-check. Run-on-demand; it *writes* config, so it's left off the allow-list (let it prompt).

### #9 — upstash/context7 (fresh, version-correct docs)
```bash
claude mcp add context7 https://mcp.context7.com/mcp
```
Injects current library docs into context so generated code matches installed versions. Add it to a project on real coding days; free tier, key optional for higher limits.

### #10 — hesreallyhim/awesome-claude-code (the living index)
Nothing to install — [bookmark it](https://github.com/hesreallyhim/awesome-claude-code) and shop there next time instead of a listicle.

---

## Turning on the ccusage allow-rule

The rule is staged in `docs/claude-settings.example.json`. To make it live, re-copy the example over your settings (it's a superset — nothing is lost):
```bash
cp docs/claude-settings.example.json .claude/settings.json
git add .claude/settings.json && git commit -m "Add ccusage to allowlist" && git push
```

## Security review (2026-09-07)

Reviewed the two workflows and the permission allowlists added this session:

- **`.github/workflows/claude.yml` is the real surface.** It grants `contents`/`pull-requests`/`issues: write` and fires on any comment containing `@claude` — i.e. anyone who can comment can instruct a Claude that holds write access. That is acceptable **only while this repo stays private (or "Require approval for all external contributors" is on under Settings → Actions)**. Do not make this repo public without revisiting that — an external `@claude` comment would otherwise be a prompt-injection vector with write perms. No untrusted comment text flows into a shell `run:` step, so there's no script-injection path; the risk is purely the agent's own authority.
- **`security-review.yml`** only exposes its secret to same-repo PRs (GitHub withholds secrets from fork PRs on `pull_request`), and it analyses rather than executes the diff. Its action accepts **only a pay-as-you-go API key** — there is no subscription/OAuth path — so it stays dormant until you add `CLAUDE_API_KEY` + `ENABLE_SECURITY_REVIEW=true`.
- **Allowlist:** tightened the ccusage rule to `Bash(npx ccusage@latest*)` (a bare `ccusage*` would auto-approve typosquats). The rest are exact or read-only (`npm install -g omniroute`, `npm view`, `python3 -m http.server`, read-only WebFetch domains). As a general rule, prefer pinned/exact Bash allow-rules over open-ended globs.

## Security fine print (unchanged from the main toolkit)

- MCP servers see whatever the session sends and hold whatever keys you give them — add **per project**, prefer keyless/local (task-master via Claude auth, ccusage fully local).
- Marketplace agents/commands are prompt-code from strangers: skim before enabling; update deliberately.
- Anything network-exposed (the phone UI) stays on a private mesh or behind auth.
- Client / Close / CRE material stays on direct Anthropic — same rule as the OmniRoute fine print.
