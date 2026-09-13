# project-view

A single-command Claude Code plugin. `/project-view` turns the current
session's understanding of the current repository into a private,
interactive project-status page — using Claude Code's native Artifact
capability. Nothing else.

## Purpose

When you're deep in a session with Claude Code, you (or a teammate you hand
the session to) often want a quick, honest snapshot: what is this project,
what's actually been done, what's in flight, what's been validated, what's
risky, what's next. `project-view` produces exactly that as one shareable
HTML Artifact, built from real evidence gathered in that moment — not a
fabricated progress percentage, not a stale doc.

## Install

Add the marketplace and install the plugin:

```bash
claude plugin marketplace add FoFxjc/claude-project-view
claude plugin install project-view@project-view
```

## Usage

Inside a Claude Code session, in the repo you want a view of:

```
/project-view
```

Claude will:

1. Re-read the session for the actual current goal and decisions made so far.
2. Check, as relevant, the repo's docs, directory structure, `git status`/`log`/`diff`, any plan/task files, and test/validation evidence (running tests when it's cheap and safe to do so).
3. Classify every claim as a **verified fact**, an **inference**, or a **stated plan** — and keeps that distinction visible in the output.
4. Design and publish one self-contained HTML page — via Claude Code's native `Artifact` tool — covering: Architecture, Current Goal, Current State, Completed, In Progress, Validation, Blockers/Risks, Key Decisions, Next Steps, and Recent Meaningful Changes.
5. Reply in chat with the Artifact link.

The Artifact is created private by default, per Claude Code's normal Artifact
behavior.

## Scope and non-goals

This plugin is intentionally thin. It does **not**:

- Persist anything across sessions — each `/project-view` run is a fresh look, scoped to the current session only.
- Remember or store the Artifact URL anywhere (not in a file, not in memory).
- Run its own web server, HTML renderer, database, or dashboard framework — page generation and hosting are entirely Claude Code's native Artifact tool.
- Modify any file in the project it's reporting on. It is read-only with respect to the target repo.
- Report completion percentages, velocity, or ETAs it can't back with evidence.
- Mark anything `Done`/`Validated` without citing what was actually checked.
- Build in any abstraction for other agent platforms (e.g. Codex). This is Claude Code-specific, by design.

If a section has no honest content, the command omits it rather than padding
the page.

## How it's built

- `.claude-plugin/marketplace.json` and `.claude-plugin/plugin.json` — the
  standard Claude Code plugin/marketplace manifests.
- `commands/project-view.md` — the entire behavior of `/project-view`, as a
  single slash-command prompt. There is no other code in this repo: the
  command instructs Claude to use its own native `artifact-design` (and,
  where warranted, `artifact-diagramming` / `dataviz`) skills and the native
  `Artifact` tool to design and publish the page.

## License

MIT — see [LICENSE](LICENSE).
