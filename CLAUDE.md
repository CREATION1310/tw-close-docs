# tw-close-docs

Static GitHub Pages site: Trade Wings Capital's internal Close CRM documentation. No build step, no framework, no dependencies.

## Layout

- `index.html` — landing page linking the two docs
- `tour.html` — "The Close Tour": ten-screen walkthrough of the CRM
- `sop.html` — SOP-CLOSE-001: the standing rulebook (language, queues, channels, DNC, escalation)
- `.nojekyll` — required for Pages; keep it
- `robots.txt` + `noindex` meta — this site is internal; keep both on every page

## Conventions

- Each page is fully self-contained HTML with inline CSS (Apple-ish system-font styling, light/dark via `prefers-color-scheme`). Match that style; keep links relative.
- Preview locally with `python3 -m http.server 8080` — do not add build tooling for content changes.
- Content changes to the SOP should bump its version marker (currently v1.2) and stay consistent between `sop.html` and how `index.html`/`tour.html` reference it.

## Claude tooling

- Recommended plugins (caveman, superpowers) and a pre-approved command allowlist that gives Claude more autonomy in this repo: activate both by copying `docs/claude-settings.example.json` to `.claude/settings.json` (a human does this once — see `docs/claude-toolkit.md`), or install the plugins user-level with the commands documented there.
- Run `/security-review` before merging non-trivial changes; the CI equivalent lives in `.github/workflows/security-review.yml` (activation steps in `docs/claude-toolkit.md`).
- `docs/claude-toolkit.md` documents the full recommended toolchain (OmniRoute gateway included) and its security fine print.
- `docs/claude-code-top10.md` is the researched shortlist of further upgrades (document skills, @claude GitHub app, browser MCP, phone UI, usage analytics) with verified installs.
- `docs/claude-code-runbook.md` stages the rest of that shortlist — ccusage and the phone UI in full (with a safe allow-rule and remote-access security), and the other six as documented install-on-demand (deliberately not force-loaded, to avoid MCP context bloat).

## Available tools — reach for these when the task fits

This section exists so any Claude session working in this repo knows these capabilities are available and *when to invoke them*. Prefer them over improvising:

- **Playwright MCP** (`playwright`, declared in `.mcp.json`, pre-approved via `enabledMcpjsonServers`) — drive a real browser. **Use it to verify the published site**: after editing `index.html`/`tour.html`/`sop.html`, open the page, click through the flow, and screenshot to confirm the change renders before calling it done. Don't guess that HTML/CSS looks right — check it.
- **ccusage** (`npx ccusage@latest …`, allow-listed) — token/cost reports from local data. Run it when the user asks about usage, spend, or whether a change is saving tokens.
- **caveman** (`/caveman lite|full|off`) — compress replies to save output tokens. Default to a light mode for routine work; switch **off** when drafting customer- or team-facing copy for `sop.html`/`tour.html`, where full prose judgment matters.
- **superpowers** (`/brainstorm`, plan, execute) — use the structured design→plan→review flow for any change bigger than a typo to the SOP or Tour, so "what exactly changes and why" is settled before the HTML is touched.
- **wshobson/agents** (marketplace staged in settings) — pull in a specialist subagent pack via `/plugin` when a task needs domain expertise this repo's context doesn't cover. Install only the relevant pack, not all of them.
- **Document skills** (docx/pdf/pptx/xlsx, installed) — when asked to export the SOP or Tour to Word/PDF, or to produce a spreadsheet, use these rather than hand-rolling.
- **@claude on GitHub** — mention `@claude` on an issue/PR to have it act (review, fix, open a PR) directly on the repo.
- Heavier per-need tools (task-master for multi-week projects, context7 for library docs, the phone UI, the templates catalog) are documented in `docs/claude-code-runbook.md` — add them to a project only when a real need appears.

Security reminder for any session: client / Close / CRE material stays on direct Anthropic, never routed through OmniRoute's free third-party providers.
