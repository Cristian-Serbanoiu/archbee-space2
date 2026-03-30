---
name: archbee-docs-space
description: >-
  Maintains Archbee GitHub-synced documentation. Covers docs under BASIC/,
  MEDIA/, DEVELOPER/, archbee.json, block syntax, and assets. Use when editing
  Archbee docs, archbee.json, block examples, or Archbee sync.
---

# Archbee Docs Space

## What this repo is

- **Archbee** documentation synced from/to GitHub.
- **Source of navigation:** `archbee.json` (`structure.docsTree`, `structure.summary`, `docsPath`).
- **Docs layout:** `docs/BASIC/`, `docs/MEDIA/`, `docs/DEVELOPER/` — see **Block taxonomy** below.

## Block taxonomy (how Archbee categorizes blocks)

Sidebar categories in **`archbee.json`** match **BASIC**, **MEDIA**, and **DEVELOPER**. Below is the full product list; **showcase file** is filled only where this repo has a page (no new blocks were added—only reorganization).

### BASIC

| Block types (product) | Showcase in this repo |
| --------------------- | --------------------- |
| Heading 1, Expandable Heading 1, Heading 2, Expandable Heading 2, Heading 3, Expandable Heading 3 | `BASIC/heading-expandable-blocks.md` |
| Checklist, Bulleted list, Numbered list | `BASIC/checklist-list-blocks.md` |
| Table, Horizontal divider | `BASIC/table-divider-blocks.md` |
| Button, Callout | `BASIC/buttons-callouts-blocks.md` |
| Vertical Split | `BASIC/vertical-split-blocks.md` |
| Minitasker | `BASIC/vertical-split-blocks.md` |
| LinkGrid | `BASIC/link-grid-block.md` |
| Workflow, Tabs | `BASIC/workflow-tabs-blocks.md` |

### MEDIA

| Block types (product) | Showcase in this repo |
| --------------------- | --------------------- |
| File | `MEDIA/file-block.md` (page title: **Files**) |
| Image | `MEDIA/images-embeds-blocks.md` |
| YouTube or Vimeo | `MEDIA/images-embeds-blocks.md` |
| Map | `MEDIA/map-block.md` |

### DEVELOPER

| Block types (product) | Showcase in this repo |
| --------------------- | --------------------- |
| Code Editor | `DEVELOPER/code-editor-block.md` |
| Code Drawer | `DEVELOPER/code-drawer-block.md` |
| Mermaid diagram, TeX | `DEVELOPER/tex-mermaid-blocks.md` |
| API endpoint | `DEVELOPER/api-endpoint-block.md` |
| Swagger UI | `DEVELOPER/swagger-block.md` |
| Scalar | `DEVELOPER/scalar-block.md` |
| GraphQL | `DEVELOPER/graphql-block.md` |
| Changelog | `DEVELOPER/changelog-block.md` |

## GitHub sync — block patterns (canonical in this repo)

Content authored in GitHub must use patterns Archbee’s parser recognizes. **JSX from the web editor** (`<Changelog>`, `<Map>`, `<embed>`, `<File>`) often shows **empty** after sync; use **colon directives / fences** and **fenced code** instead.

| Need | Pattern | Showcase path |
| ---- | -------- | --------------- |
| **TeX** | ` ```tex` … ` ``` ` | `DEVELOPER/tex-mermaid-blocks.md` |
| **Mermaid** | ` ```mermaid` … ` ``` ` | `DEVELOPER/tex-mermaid-blocks.md` |
| **Changelog** | `:::changelog{title="…"}` + lines `::changelog-item{type="…" description="…"}` + close `:::` | `DEVELOPER/changelog-block.md` |
| **Map** | `:::::Map` + ` ```json` { center, zoom, markerPositions } + `:::::` | `MEDIA/map-block.md` |
| **File** | `::File[]{isUploading="false"}` or `::File[]{src="…" label="…" caption}` | `MEDIA/file-block.md` |
| **Image** | `![](https://example.com/image.png)` | **Images & YouTube Videos** — `MEDIA/images-embeds-blocks.md` |
| **YouTube Video** | `:::::Embed{url="https://youtu.be/…"}` + `:::::` (fallback: `:::::Video{url="…"}` or plain YouTube URL line) | `MEDIA/images-embeds-blocks.md` |
| **Code Editor** | Fenced code block (for example ` ```javascript`) | `DEVELOPER/code-editor-block.md` |
| **Code Drawer** | `::::CodeDrawer{...}` + nested `:::CodeblockTabsExamples/Responses` | `DEVELOPER/code-drawer-block.md` |
| **GraphQL (GraphiQL)** | `:::GraphiQL` + fenced `json` (`endpoint`, `query`) + `:::` | `DEVELOPER/graphql-block.md` |
| **Scalar** | `:::::Scalar` + fenced `json` (`jsonFileLocation`, `headers`) + `:::::` | `DEVELOPER/scalar-block.md` |
| **Swagger** | `:::::Swagger` + fenced `json` (`jsonFileLocation`, `headers`) + `:::::` | `DEVELOPER/swagger-block.md` |
| **API Endpoint** | `:::::ApiMethodV2` + fenced `json` (full method payload) + `:::::` | `DEVELOPER/api-endpoint-block.md` |

For this repo, **do not invent syntax**. Copy from the matching showcase file under `docs/` (symlink `docs/` → `docs/`).  
Known-safe patterns: GraphQL uses `:::GraphiQL ... :::`; Scalar/Swagger/API Endpoint use `:::::BlockName` + inner ` ```json `; Map uses the same wrapper style.

**Icons:** Use only icon shapes confirmed in existing docs (`fa-solid` or known `lucideIcon` values). Avoid `fa-brands`. If tree icon/label is stale, set explicit `name` in `archbee.json` for that doc entry.

**Changelog types:** `added`, `improved`, `fixed`, `broken`, `knownIssue`. **Changelog fences are lowercase** (`changelog`, `changelog-item`).

Copy-paste templates live in **[reference-blocks.md](reference-blocks.md)** → **GitHub sync — canonical patterns**.

## Before you change anything

1. Read `archbee.json` to see categories (**BASIC**, **MEDIA**, **DEVELOPER**) and doc paths.
2. After editing `archbee.json`, validate: `python3 -m json.tool archbee.json`.
3. If you add, rename, or remove a doc file, update **every** `path` in `docsTree` and `summary` if it pointed at the old file.
4. If the **sidebar** still shows an old doc title after changing frontmatter `title`, set **`name`** on that item in `docsTree` (same string as `title`) so Archbee uses it for the tree label.

## Directory map

| Path | Role |
|------|------|
| `archbee.json` | Site name, theme, hosting colors, `docsPath`, full sidebar tree |
| `docs/BASIC/*.md` | BASIC category examples |
| `docs/MEDIA/*.md` | MEDIA category examples |
| `docs/DEVELOPER/*.md` | DEVELOPER category examples |
| `public/` | `favicon.ico`, `logo-light.png`, `logo-dark.png` — referenced by `faviconURL` / `publicLogoURL` / `darkPublicLogoURL` |

## Naming and product language (follow these)

- **Sidebar categories:** **BASIC**, **MEDIA**, **DEVELOPER** (not `ALL-BLOCKS`).
- Prefer titles like **"Headings & Expandable Headings"** — use `&`, avoid tacking **"Blocks"** on every page title unless the user asks.
- **Minitasker** = the droplist/kanban-style block (`DropList`), not "Droplists" in titles.
- **Callouts** = `hint` blocks; **Buttons** = `CtaButton`.
- **Link grids** = `LinkArray`; **3 per row** → `itemsPerRow="3"`.

## File naming convention (strict)

- If a page demonstrates **multiple block types**, filename must end with **`-blocks.md`**.
- If a page demonstrates **one block type**, filename must end with **`-block.md`**.
- Keep existing `slug` values stable unless the user explicitly asks to change slugs.
- When renaming files, update **all** references (`archbee.json`, skill docs, and any internal doc links).

## Block syntax (colon fences — editor-style blocks)

Use the same colon counts as sibling lines in each file. Common blocks:

- **Expandable:** `ExpandableHeading` with nested heading markdown inside.
- **Buttons:** `CtaButton{label=... docId=... docAnchorId=... externalHref=... openInNewTab=... noFollow=...}`.
- **Callouts:** `hint{type="info"|"success"|"warning"|"danger"}`.
- **Layout:** `VerticalSplit` + `VerticalSplitItem`.
- **Board:** `DropList` + JSON in a fenced `json` code block.
- **Link grids:** `LinkArray{contentSource="CUSTOM" itemsPerRow="3"}` + `LinkArrayItem`.
- **Workflow:** `WorkflowBlock` / `WorkflowBlockItem`.
- **Tabs:** `Tabs` / `Tab{title="..."}`.

**Internal doc links in buttons:** use `docId` = target **slug**, and `externalHref` like `/docs/BASIC/<slug>`, `/docs/MEDIA/<slug>`, or `/docs/DEVELOPER/<slug>` depending on which folder holds the doc.

**Tables:** Markdown pipes; HTML `<table>` — see `reference-blocks.md` and `BASIC/table-divider-blocks.md`.

**Files (GitHub sync):** `::File[]{…}` — see `MEDIA/file-block.md`.

## Images for link grid headers

Use **i.imgur.com** `.jpeg` URLs. List in **`reference-blocks.md`** → **Link grid header images**.

## Quality checks

- **No duplicate appended sections** from broken sync.
- **Consistency:** frontmatter `title` / `slug` / `icon` match sibling files.
- **Minimal diffs** for unrelated docs.
- **DO NOT GUESS BLOCK FORMAT:** copy the exact block wrapper/fence from the existing showcase page for that block type.
- **RE-SYNC SAFE:** after edits, verify `archbee.json` is valid and all changed docs are included in `docsTree`.

## More detail

- **Full catalog:** [reference-blocks.md](reference-blocks.md) — GitHub sync patterns, Imgur URLs, doc paths by category, block inventory, HTML table attrs, pitfalls.
