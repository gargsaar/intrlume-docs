# Intrlume docs — authoring rules

This repo is the source for the Intrlume documentation site (Documentation.AI Git sync).
Pushing to `main` builds and deploys the site.

## Golden rule

**ALWAYS CHECK THE CODE BEFORE DOCUMENTING ANYTHING.** Never guess UI labels, button
names, flows, fields, limits, or feature behavior. Verify against the actual code in the
`outreach-pro` repo (`frontend-app/` for UI labels and flows, `backend-app/` and
`eval-service/` for behavior and config) before writing. If something can't be confirmed
in code, say so — don't invent it.

Examples of what this prevents: writing "click New" when the button is "Quick Create";
listing eval metrics ("hallucination", "topic adherence") that don't exist in the code.

## Where to verify

- **UI labels, buttons, page/flow steps** → `outreach-pro/frontend-app/src` (e.g.
  `pages/agents/`, `components/layout/AppSidebar.tsx`, `pages/workflows/`).
- **Features, limits, behavior** → `outreach-pro/backend-app` (routes, services, schemas,
  `config/plans_config.yaml`, `lib/limits.py`) and `outreach-pro/eval-service`.

## Writing conventions

- Operators-first, plain English, short sentences; lead with the answer.
- Every page starts with frontmatter: `title` + `description`. Body starts at `##` (no
  repeated `# H1`).
- Internal links use absolute paths from the docs root: `[Quickstart](/quickstart)`.
- Update `documentation.json` (`navigation.tabs`) when adding/renaming/removing pages.
- Documentation.AI MDX components: `Callout`, `Steps`/`Step`, `Tabs`/`Tab`,
  `Columns`/`Card`, `Image`. Close every component; keep MDX valid.

## Images

- Host docs images in `outreach-pro/frontend-app/public/docs/images/` and reference them
  as `https://app.intrlume.tech/docs/images/<file>`. They go live only after `frontend-app`
  is redeployed.

## Updating the docs

The repo is Git-synced to Documentation.AI: **pushing to `main` auto-builds and deploys**
(~15–60s). Live at `https://docs.intrlume.tech`. Never edit `main` directly — always branch,
PR, and merge.

**The loop:**

1. `git checkout main && git pull --ff-only origin main`
2. `git checkout -b docs/<short-topic>`
3. **Check the code first** (golden rule) for any label, flow, or behavior you'll write.
4. Edit the MDX page(s). Add/rename/remove pages in `documentation.json` (`navigation.tabs`).
5. **Validate** before committing:
   - `python3 -c "import json; json.load(open('documentation.json'))"` — JSON must parse.
   - Internal links resolve to real pages; every MDX component is closed.
6. Commit, then `git push -u origin docs/<short-topic>`.
7. `gh pr create --base main --title "…" --body "…"`.
8. `gh pr merge <n> --squash --delete-branch`. If it says the PR isn't mergeable, wait a few
   seconds and retry — GitHub computes mergeability right after creation.
9. `git checkout main && git pull`.
10. **Verify live** (below).

**Verify live — beat the CDN cache.** The page and its `.md` endpoint are CDN-cached, so a
plain fetch can serve the old version. Append a cache-buster to confirm the new content:

```
curl -s "https://docs.intrlume.tech/<path>.md?cb=$RANDOM" | head
```

(Same in the browser — hard-refresh or add `?v=2`.) Build/deploy takes ~15–60s; poll a few times.

**Commits:** author is Sarthak Garg; end the message with
`Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>`. Do **not** add a Sonnet co-author line.

## documentation.json structure

- **Documentation** tab: `tabs[].groups[].pages[]` (each page = `{title, path, icon}`).
- **Help Center** tab: `tabs[].pages[]` holding a mix of page objects and `{group, pages[]}` groups.
- Internal pages use **`path`** (docs-root-relative, no leading slash). External links use
  **`href`** (`https://…`, `mailto:…`).
- The sidebar has **no dedicated bottom slot** — for a bottom link, add a small one-item group
  at the end of a tab's groups. Persistent CTAs go in the top `navbar.actions`.

## Gotchas

- **Bare email in a heading** auto-links and swallows the preceding space (`needsadmin@…`).
  Use an explicit link: `## What needs [admin@intrlume.tech](mailto:admin@intrlume.tech)`.
- **Images** go live only after `frontend-app` redeploys (see Images above).
