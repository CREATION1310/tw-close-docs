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

`Bash(npx ccusage*)` is in `docs/claude-settings.example.json`'s allow-list — read-only and local, so a session can run it on request without a prompt. Its `statusline` subcommand (Beta) can feed a live cost readout into Claude Code's status bar.

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

### #3 — wshobson/agents (specialist subagent marketplace)
```bash
claude plugin marketplace add wshobson/agents
```
Then `/plugin` inside Claude Code to install **only** the packs you want (e.g. a language or domain pack). Don't enable all 200+ — that's the context-bloat trap. Third-party prompts; skim a pack before enabling.

### #4 — claude-task-master (cross-session project planning)
Keyless via your Claude Code auth:
```bash
claude mcp add task-master-ai -- npx -y task-master-ai
```
Add it to a specific project when you have a multi-week, many-part effort (an enforcement-file pipeline, a site overhaul) — not to every session. In chat: "Initialize taskmaster in my project."

### #5 — microsoft/playwright-mcp (real browser control)
```bash
claude mcp add playwright npx @playwright/mcp@latest
```
Add on the days you need Claude to open the live Pages site, click through the Close Tour, screenshot, or fill web forms. Lighter alternative you already run: [obra/superpowers-chrome](https://github.com/obra/superpowers-chrome).

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

## Security fine print (unchanged from the main toolkit)

- MCP servers see whatever the session sends and hold whatever keys you give them — add **per project**, prefer keyless/local (task-master via Claude auth, ccusage fully local).
- Marketplace agents/commands are prompt-code from strangers: skim before enabling; update deliberately.
- Anything network-exposed (the phone UI) stays on a private mesh or behind auth.
- Client / Close / CRE material stays on direct Anthropic — same rule as the OmniRoute fine print.
