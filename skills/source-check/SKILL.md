---
name: source-check
description: Run a pre-commit/pre-publish source-check. Git hygiene (secrets handling via Doppler/1Password/dotenvx, license headers, Conventional Commits formatting) applies to every commit. For dev-work changes — a new or bumped dependency, an Alpine/aports package version bump, a nixops-config flake update, vendoring a source — also run upstream dependency/license provenance and release integrity checks (checksums/signatures). For writing/drafts — blog posts, presentation scripts, docs with factual claims — also run media-literacy checks (fact-checking, bias/framing review, gen-AI hallucination checks, further-reading). Trigger on dev-work phrasing like "before I commit this dependency bump", "sanity check this diff/PR/APKBUILD", "review this flake update", on writing phrasing like "fact-check this draft", "review before I publish", "source check this blog post", and generically on "source check", "ready to push this", or whenever reviewing a diff, PR, or draft that will be committed.
---

# Source Check

A combined pre-commit review workflow for two different kinds of "commit": **code changes** (packages, configs, scripts) and **content** (blog posts, docs, presentation scripts). Most commits only need a subset of the checklist below — figure out which kind of change this is first, then apply the relevant section(s). A single commit can trigger more than one section (e.g. a blog post commit with an inline code snippet still gets git-hygiene + media-literacy).

## Step 1: Classify the change

Ask (or infer from the diff/description) which of these apply:

| Change involves... | Run |
|---|---|
| A new or bumped upstream dependency, package, or vendored source (aports `APKBUILD` bump, `flake.lock` update, npm/deno dependency add) | **Upstream Review** (`references/upstream-review.md`) |
| Any commit at all — this always runs | **Git Hygiene** (below) |
| Written content with factual/citable claims (blog draft, NSTP presentation script, documentation, README explaining a concept) | **Media Literacy** (`references/media-literacy.md`) |

Don't skip Git Hygiene even for "just docs" or "just content" commits — secrets and formatting checks apply universally.

## Step 2: Git Hygiene (always run)

Quick checklist — flag anything that fails, don't just silently fix and move on:

1. **Secrets scan.** No plaintext secrets in the diff. Anything sensitive should already be in Doppler or 1Password, or encrypted at rest with `dotenvx` (`.env.keys` never committed, only `.env.vault`/encrypted `.env` files are). If `bash_tool` is available, grep the diff for common leak patterns (`api[_-]?key`, `secret`, `token`, `-----BEGIN`, AWS-style `AKIA`, bare `.env` additions) before declaring it clean.
2. **License headers.** New source files carry the project's `SPDX-License-Identifier` header (or whatever header convention the repo already uses — check a neighboring file). Don't invent a license for the user; ask if unclear.
3. **Formatting matches house style**: 2-space indentation per level (Markdown list nesting uses 4-space on GitHub/GitLab-flavored repos), double-quoted strings, semicolons optional, POSIX `sh`-compatible shell. JSDoc/TypeDoc comments on exported functions in TS/JS.
4. **Commit message follows Conventional Commits**: `type(scope): summary`, imperative mood, no period at end of summary. Valid types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`. Breaking changes get a `BREAKING CHANGE:` footer or `!` after the type/scope. Suggest a properly formatted message if the user hasn't written one yet.

## Step 3: Upstream Review (if a dependency/source changed)

Read `references/upstream-review.md` for the full checklist — it covers dependency/license provenance and release integrity (checksums, signatures, tag verification) with separate notes for Alpine `aports`/`abuild` workflows and Nix flake updates.

## Step 4: Media Literacy (if content/writing changed)

Read `references/media-literacy.md` for the full checklist — it covers fact-checking claims, bias/framing review, gen-AI-specific failure modes (hallucinated citations, fabricated stats), and compiling a further-reading list.

## Step 5: Report back

Summarize findings as a short pass/flag list, not a wall of prose — group by section (Git Hygiene / Upstream Review / Media Literacy), and be explicit about what still needs the user's judgment call (e.g. "this license changed from MIT to a source-available license — you decide if that's acceptable") rather than silently deciding for them. End with a clear go/no-go recommendation, but the user commits, not Claude.
