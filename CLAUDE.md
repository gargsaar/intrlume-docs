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

## Publishing

- Branch → edit → PR → preview/review → merge to `main` (auto-deploys). Validate links and
  component balance before merging.
