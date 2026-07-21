---
name: memory-audit
description: Runs Andrei's cross-project memory/backlog audit — reconciling dev work (GitLab.com projects, Alpine Linux aports on gitlab.alpinelinux.org, GitHub), Leaflet.pub blog drafts, Obsidian notes, and Google Drive into a single status report + prioritized action list. Trigger this whenever Andrei explicitly says "run memory audit" (or close variants like "audit my backlog", "memory check-in"), whenever a monthly or quarterly check-in is due or requested, and whenever Andrei mentions specific tracked projects or drafts by name (nixops-config, aports/gh package, Recap Time Squad, Leaflet.pub drafts, homelab/NixOS config work) in a way that implies "where did I leave this?" Do not wait for Andrei to spell out the full routine each time — this skill already knows the sources, the staleness rules, and the output format.
---

# Memory Audit

A recurring routine that reconciles what Andrei is *actually* working on across scattered
sources — code hosts, a blog platform, personal notes, and Drive — into one Markdown
report he can drop straight into Obsidian or `TASKS.md`.

## Why this exists

Andrei's work lives in five places that don't talk to each other:

- **GitLab.com** — primary host for his own projects (e.g. `nixops-config`)
- **gitlab.alpinelinux.org** — Alpine Linux's self-hosted GitLab, used for `aports` (the `gh` package)
- **GitHub** — mirrors, `gh` CLI usage, some Recap Time Squad repos
- **Leaflet.pub** — draft blog posts (maintainer check-ins, status updates)
- **Obsidian notes / Google Drive** — the personal layer that's supposed to tie it all together but drifts

The audit's job is to notice *drift*: a note that says "in progress" but the repo's been
quiet for two months, a draft that's been sitting since before the last check-in, a task
that shows up in Obsidian but never made it into an issue anywhere.

## When to run

- **Explicit trigger**: "run memory audit," "audit my backlog," "memory check-in," or similar.
- **Scheduled check-ins**: monthly and quarterly. If Andrei references a check-in cadence
  without asking for anything else specific, assume he wants the full audit.
- **Implicit trigger**: Andrei mentions a tracked project/draft by name in a way that reads
  like "where did I leave this?" (e.g. "what's the state of nixops-config" / "did I ever
  finish that Leaflet post"). In this case you can scope the audit to just that item rather
  than running the full sweep — ask if unsure.

## Claude.ai access constraints — read this first

Claude.ai (web/mobile) **cannot** reach:
- Andrei's local Obsidian vault
- gitlab.alpinelinux.org or gitlab.com pages that aren't already surfaced via search results

So each run, be upfront about what you can and can't fetch live:

1. **Try live fetch first** for anything reachable:
   - GitHub: `api.github.com` / `github.com` URLs are fetchable directly.
   - GitLab (either instance): not directly fetchable via `web_fetch` cold — run `web_search`
     for the specific project/issue first (e.g. `site:gitlab.alpinelinux.org aports gh`), then
     `web_fetch` the URLs that search turns up. If nothing turns up, say so plainly.
   - Google Drive: use `google_drive_search` / `google_drive_fetch` directly — this is reliably
     available. (Note: past runs found Drive weak for *personal task lists* specifically — it's
     better for docs than for backlog/todo data. Don't over-trust an empty Drive result as "no
     backlog exists.")
   - Leaflet.pub: not directly crawlable in general — treat as needing Andrei's input unless a
     specific draft URL is already in the conversation or Andrei's memory.
2. **Ask for what you can't reach.** Explicitly list what's missing before writing the report,
   e.g.: "I can't reach your Obsidian vault or the two Leaflet drafts directly — paste/upload
   the relevant notes and I'll fold them in." Don't silently skip a source.
3. **Never fabricate status.** If a source is unavailable and Andrei doesn't supply it, mark
   that project/draft as `⚠️ unknown — source unavailable this run` rather than guessing.

## Staleness rules (weighted, not binary)

Flag an item if **any** of these are true, and note *which* rule fired:

| Signal | Threshold | Weight |
|---|---|---|
| No commits/issues/MR activity | 3+ weeks quiet for active dev projects | Medium |
| Draft post unpublished | Past its own stated target date, or 2+ months old with no edits | Medium |
| Obsidian/notes vs. actual repo state mismatch | Note says a status the repo doesn't support (e.g. "in progress" but repo untouched, or "done" but no merged MR/closed issue) | **High** — this is the most useful signal, surface these first |
| Cross-source duplication | Same task tracked in two places with different states | Low — just note it, don't treat as urgent |

Weight matters for ordering the action list, not for deciding whether to include something —
include everything flagged, just sort mismatches and long-quiet items above minor duplication.

## Output format

Produce a single Markdown file, structured so it can be pasted straight into Obsidian or
appended to `TASKS.md`. Use this shape:

```markdown
# Memory Audit — <date, Asia/Manila>

## TL;DR
- 2-4 bullets: what needs attention *now*, in plain terms.

## Status by project

### <Project name> (<source(s)>)
- **State:** active / stale / done / unknown
- **Last signal:** <date + what happened, or "none found">
- **Flag:** <staleness rule that fired, or "none">

(repeat per project/draft)

## Action list (prioritized)
- [ ] <highest-weight item first>
- [ ] ...

## Sources checked this run
- <what was fetched live, what Andrei supplied, what's still missing>
```

- Keep the per-project write-ups tight — 2-4 lines each. This is a working doc, not a report
  to present to someone else.
- Always include the "Sources checked this run" section — it's the trust layer that lets
  Andrei know what's stale-because-real vs. stale-because-unchecked.
- Save/present as a `.md` file (this qualifies as a standalone artifact — a document meant to
  be dropped into Obsidian/TASKS.md — so create the file rather than just printing it inline).

## Applying Andrei's standing preferences

Per his usual conventions: include a short TL;DR (built into the template above), and
include clickable source links wherever you cite a specific GitHub/GitLab URL, Drive doc, or
Leaflet draft — these double as the "3+ links for cross-validation" he expects, and let him
jump straight to the thing that's stale instead of taking your word for it.

## Notes on tone

Andrei runs this on himself, not to be graded — flag drift plainly and move on. Don't
editorialize about productivity or add motivational framing; just report what's active,
what's stale, and what's next.
