# Top 10 GitHub repos to soup up Claude Code

Researched and verified 2026-09-07. Every entry checked at its canonical source (owner, stars, activity, install path) — no lookalikes. Star counts are as-of that date. Ranked for how Trade Wings actually works: document generation, GitHub-centric docs ops, phone-first, token-conscious, autonomy-friendly.

**The one rule before installing anything:** install few, deliberately. Every MCP server's tool definitions load into the context window of every session, so a kitchen-sink setup makes the agent measurably dumber and more expensive. Adopt from this list when a need appears — not all at once.

## The ten

### 1. [anthropics/skills](https://github.com/anthropics/skills) — official Agent Skills (175k★)
Anthropic's own skills library, headlined by the document skills: **docx, pdf, pptx, xlsx** creation and editing. This is the engine for demand letters, SOP exports, spreadsheets — the core Trade Wings paperwork, running locally.
```
claude plugin marketplace add anthropics/skills
claude plugin install document-skills@anthropic-agent-skills
```
Also worth browsing: `example-skills@anthropic-agent-skills` (skill-creator, MCP builder, frontend design, and more).

### 2. [anthropics/claude-code-action](https://github.com/anthropics/claude-code-action) — @claude on GitHub (8.8k★)
Mention `@claude` in any issue or PR comment and Claude answers questions, reviews, fixes, and pushes — no app open. Pairs perfectly with the security-review Action already in this repo.
The workflow is already staged in this repo at `.github/workflows/claude.yml` (dormant). To arm it: install the [Claude GitHub App](https://github.com/apps/claude) on the repo, add repo secret `ANTHROPIC_API_KEY`, and set repo variable `ENABLE_CLAUDE_MENTION=true`. (Running `/install-github-app` inside the Claude Code TUI automates the app + secret; add the variable yourself.)

### 3. [wshobson/agents](https://github.com/wshobson/agents) — the specialist bench (39.5k★)
The canonical subagent collection, now a full marketplace: 202 agents, 183 skills, 105 commands, 16 orchestrators. Install only the packs you need.
```
claude plugin marketplace add wshobson/agents
```
Then `/plugin` to browse. Fine print: these are third-party prompts that steer your sessions — skim what a pack does before enabling it.

### 4. [eyaltoledano/claude-task-master](https://github.com/eyaltoledano/claude-task-master) — project brain (28.1k★)
Turns a requirements doc into a dependency-ordered task list Claude works through across sessions. Built for multi-week, many-moving-parts projects (an enforcement-file pipeline, a site overhaul). Works keyless through your Claude Code auth:
```
claude mcp add task-master-ai -- npx -y task-master-ai
```
Then in chat: "Change the main model to claude-code/sonnet" and "Initialize taskmaster in my project."

### 5. [microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp) — a real browser (36.9k★)
Official Microsoft MCP server: Claude drives Chrome via accessibility snapshots — open the live Pages site, click through the Close Tour, screenshot every screen, fill web forms.
```
claude mcp add playwright npx @playwright/mcp@latest
```
Lighter alternative from an author you already run: [obra/superpowers-chrome](https://github.com/obra/superpowers-chrome) (zero-dependency, controls your existing Chrome).

### 6. [siteboon/claudecodeui](https://github.com/siteboon/claudecodeui) — Claude Code from your phone (13.6k★)
Web + mobile UI (CloudCLI) for your local Claude Code sessions: chat, files, git, terminal from any device. Given how much of this workflow already happens from a phone, this is the sleeper pick.
```
npx @cloudcli-ai/cloudcli
```
Fine print: it exposes your sessions over the network — keep it on localhost/LAN or behind auth, never port-forwarded raw. Sensibly, all Claude Code tools start disabled until you enable them. (AGPL; optional paid cloud tier exists.)

### 7. [ryoppippi/ccusage](https://github.com/ryoppippi/ccusage) — the receipts (18.4k★)
Token and cost reports (daily/weekly/monthly/per-session, 5-hour billing windows) computed entirely from local data — nothing leaves the machine. This is how you *prove* what caveman mode saves.
```
npx ccusage@latest
```

### 8. [davila7/claude-code-templates](https://github.com/davila7/claude-code-templates) — the app store (30.6k★)
Browsable catalog ([aitmpl.com](https://aitmpl.com)) + CLI installer for hundreds of agents, commands, hooks, and MCP configs, plus an analytics dashboard and health-check for your setup.
```
npx claude-code-templates@latest
```

### 9. [upstash/context7](https://github.com/upstash/context7) — fresh docs on demand (61.7k★)
Injects current, version-correct library documentation into context so generated code matches what's actually installed. Matters on the days you do real coding; skippable until then. Free tier; key optional for higher limits.
```
claude mcp add context7 https://mcp.context7.com/mcp
```

### 10. [hesreallyhim/awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code) — the living index (53.6k★)
The best-maintained curated map of the whole ecosystem. Not something you install — it's where you shop next time, instead of trusting a listicle.

## Honorable mentions

- [BeehiveInnovations/zen-mcp-server](https://github.com/BeehiveInnovations/zen-mcp-server) (11.7k★) — multi-model second opinions inside a session; redundant here since OmniRoute already covers multi-provider access.
- [rohitg00/awesome-claude-code-toolkit](https://github.com/rohitg00/awesome-claude-code-toolkit) — the maximalist index (176+ plugins) if the curated one runs dry.

## Suggested adoption order

1. **Today:** #1 document-skills (instant payoff), #7 ccusage (free visibility).
2. **This week:** #2 @claude GitHub app on this repo.
3. **When the need appears:** #5 browser for site QA, #6 phone UI, #4 task-master for the next big project.
4. **Browse, don't binge:** #3, #8, #9, #10.

## Security fine print (same rules as the main toolkit)

- MCP servers see whatever the session sends them and hold whatever keys you give them — add per project, not globally, and prefer keyless/local ones (task-master via Claude auth, ccusage fully local).
- Marketplace agents/commands are prompt-code from strangers: skim before enabling, update deliberately (`claude plugin update`), never auto-trust a pack because it's popular.
- Anything that exposes sessions over a network (#6) stays on LAN or behind auth.
- Client and CRE material stays on direct Anthropic — same rule as the OmniRoute fine print in `claude-toolkit.md`.
