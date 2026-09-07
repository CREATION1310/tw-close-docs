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
