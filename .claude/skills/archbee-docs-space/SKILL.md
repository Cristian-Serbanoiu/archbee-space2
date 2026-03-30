---
name: archbee-docs-space
description: >-
  Maintains Archbee GitHub-synced documentation in this repo. Covers docs in
  docs/ALL-BLOCKS/, archbee.json navigation, MDX/block syntax, assets under
  public/, and naming conventions. Use when editing Archbee docs, archbee.json,
  block examples, or when the user mentions ALL-BLOCKS, blocks, or Archbee sync.
---

# Archbee Docs Space

## What this repo is

- **Archbee** documentation synced from/to GitHub.
- **Source of navigation:** `archbee.json` (`structure.docsTree`, `structure.summary`, `docsPath`).
- **Page content:** Markdown under `docs/ALL-BLOCKS/` (folder name is `ALL-BLOCKS`, kebab-case).

## Before you change anything

1. Read `archbee.json` to see current categories and doc paths.
2. After editing `archbee.json`, validate: `python3 -m json.tool archbee.json`.
3. If you add, rename, or remove a doc file, update **every** `path` in `docsTree` and `summary` if it pointed at the old file.

## Directory map

| Path | Role |
|------|------|
| `archbee.json` | Site name, theme, hosting colors, `docsPath`, full sidebar tree |
| `docs/ALL-BLOCKS/*.md` | Block showcase pages (one topic per file is the norm) |
| `public/` | `favicon.ico`, `logo-light.png`, `logo-dark.png` — referenced by `faviconURL` / `publicLogoURL` / `darkPublicLogoURL` |

## Naming and product language (follow these)

- **Category:** sidebar label is **ALL-BLOCKS** (matches folder).
- Prefer titles like **"Headings & Expandable Headings"** — use `&`, avoid tacking **"Blocks"** on every page title unless the user asks.
- **Minitasker** = the droplist/kanban-style block (`DropList`), not "Droplists" in titles.
- **Callouts** = `hint` blocks; **Buttons** = `CtaButton` (not "CTA" in user-facing titles if they prefer Buttons).
- **Link grids** = `LinkArray` block; user may ask for **3 per row** → `itemsPerRow="3"` on `LinkArray`.

## Block syntax (Markdown / MDX)

Use the same colon-fence style as existing files (often `:::::BlockName` or `::::::` depending on nesting). Common blocks in this repo:

- **Expandable:** `ExpandableHeading` with nested heading markdown inside.
- **Buttons:** `CtaButton{label=... docId=... docAnchorId=... externalHref=... openInNewTab=... noFollow=...}`.
- **Callouts:** `hint{type="info"|"success"|"warning"|"danger"}`.
- **Layout:** `VerticalSplit` + `VerticalSplitItem`.
- **Board:** `DropList` + JSON in a fenced `json` code block.
- **Link grids:** `LinkArray{contentSource="CUSTOM" itemsPerRow="3"}` + `LinkArrayItem` with `headerType="COLOR"` or `headerType="IMAGE"` + `headerImage="https://i.imgur.com/....jpeg"`.
- **Workflow:** `WorkflowBlock` / `WorkflowBlockItem` — title line + body content under each step.
- **Tabs:** `Tabs` / `Tab{title="..."}`.

**Internal doc links in buttons:** use `docId` matching the target doc slug, plus `externalHref` like `/docs/ALL-BLOCKS/<slug>` when needed; do not use broken `#/docs/...` hashes unless the product expects them.

**Tables:** Markdown pipes; HTML `<table>` with `lightBackgroundColor` / `darkBackgroundColor`, `colSpan`, `rowSpan`, `align` — see `reference-blocks.md` and existing `table-divider-blocks.md`.

## Images for link grid headers

Use **i.imgur.com** `.jpeg` URLs. The canonical list for this repo (copy-paste + rotation order) lives in **`reference-blocks.md`** under **Link grid header images**.

## Quality checks

- **No duplicate appended sections:** some editors/sync tools can duplicate the tail of a file. If you see repeated frontmatter blocks, keep a single clean copy.
- **Consistency:** frontmatter `title` / `slug` / `icon` match patterns in sibling files.
- **Minimal diffs:** only touch files needed for the task; do not rewrite unrelated docs.

## More detail

- **Full catalog:** [reference-blocks.md](reference-blocks.md) — Imgur URLs, ALL-BLOCKS slugs, inventory table, copy-paste templates for every block (fences match `docs/ALL-BLOCKS/`), HTML table attrs, pitfalls.
