# Archbee blocks & assets — full reference (this repo)

Use **the same colon counts** as below when pasting into `docs/ALL-BLOCKS/*.md`. Inner fences use fewer colons than the outer block.

---

## Link grid header images (Imgur)

Rotate these for `LinkArrayItem` with `headerType="IMAGE"`. All are `.jpeg` on `i.imgur.com`.

| Label | URL |
| ----- | --- |
| Image 1 | `https://i.imgur.com/egDw6na.jpeg` |
| Image 2 | `https://i.imgur.com/paHSk6w.jpeg` |
| Image 3 | `https://i.imgur.com/S9lPyzp.jpeg` |

Copy-paste line:

```text
https://i.imgur.com/egDw6na.jpeg
https://i.imgur.com/paHSk6w.jpeg
https://i.imgur.com/S9lPyzp.jpeg
```

---

## Link grid color headers (examples in repo)

| Card | `headerColor` |
| ---- | ------------- |
| Amber | `#CA8A04` |
| Red | `#DC2626` |
| Cyan | `#0891B2` |

---

## Doc pages in ALL-BLOCKS (slugs for `docId` / paths)

| File | `slug` (typical) |
| ---- | ---------------- |
| `heading-expandable-blocks.md` | `heading-expandable-blocks` |
| `checklist-list-blocks.md` | `checklist-list-blocks` |
| `table-divider-blocks.md` | `table-divider-blocks` |
| `buttons-callouts.md` | `buttons-callouts` |
| `vertical-split-blocks.md` | `vertical-split-blocks` |
| `minitasker.md` | `minitasker` |
| `link-grids-blocks.md` | `link-grids-blocks` |
| `workflow-tabs-blocks.md` | `workflow-tabs-blocks` |

Internal href pattern: `/docs/ALL-BLOCKS/<slug>` (no `.md`).

---

## archbee.json (quick)

- `docsPath`: `"docs/"`.
- `structure.summary`: e.g. `ALL-BLOCKS/heading-expandable-blocks.md`.
- Nav: `structure.docsTree` → category `ALL-BLOCKS` → each child `path`: `ALL-BLOCKS/<file>.md`.
- Logos: `faviconURL`, `publicLogoURL`, `darkPublicLogoURL` under `/public/`.

Validate after edits:

```bash
python3 -m json.tool archbee.json >/dev/null && echo OK
```

---

## Block inventory

| Kind | Component / pattern | Showcase file |
| ---- | ------------------- | --------------- |
| CTA | `CtaButton` | `buttons-callouts.md` |
| Callout | `hint` | `buttons-callouts.md` |
| Expandable | `ExpandableHeading` | `heading-expandable-blocks.md` |
| Columns | `VerticalSplit` + `VerticalSplitItem` | `vertical-split-blocks.md` |
| Board | `DropList` + JSON | `minitasker.md` |
| Link grid | `LinkArray` + `LinkArrayItem` | `link-grids-blocks.md` |
| Workflow | `WorkflowBlock` + `WorkflowBlockItem` | `workflow-tabs-blocks.md` |
| Tabs | `Tabs` + `Tab` | `workflow-tabs-blocks.md` |
| Table | Markdown pipe table | `table-divider-blocks.md` |
| Table | Raw HTML `<table>` | `table-divider-blocks.md` |
| Divider | Markdown `***` | `table-divider-blocks.md` |
| Lists | `- [ ]`, `-`, `1.` | `checklist-list-blocks.md` |

---

## Frontmatter template (new doc)

```yaml
---
title: Page Title
slug: kebab-slug
icon: {"lucideIcon":"IconName"}
# OR: icon: {"faIcon":"fa-solid fa-name"}
# OR: icon: "emoji"
docTags: Revenge
createdAt: 2026-03-30T10:00:00.000Z
updatedAt: 2026-03-30T10:00:00.000Z
---
```

Add the file to `archbee.json` `docsTree` and set `structure.summary` if this should be the landing doc.

---

## CtaButton

Uses **six** colons on the opening line in this repo; body is empty; **five** colons close.

```md
::::::CtaButton{label="LABEL" docId docAnchorId externalHref openInNewTab="true" noFollow="false"}
:::::

::::::CtaButton{label="GO TO PAGE" docId="target-slug" docAnchorId="anchor-id" externalHref="/docs/ALL-BLOCKS/target-slug" openInNewTab="false" noFollow="false"}
:::::

::::::CtaButton{label="EXTERNAL" docId docAnchorId externalHref="https://example.com" openInNewTab="true" noFollow="false"}
:::::
```

---

## hint (callouts)

Types: `info`, `success`, `warning`, `danger`.

```md
:::::hint{type="info"}
Content.
:::::

:::::hint{type="success"}
Content.
:::::

:::::hint{type="warning"}
Content.
:::::

:::::hint{type="danger"}
Content.
:::::
```

---

## ExpandableHeading

Put markdown headings (e.g. `#` through `###`) inside the block.

```md
:::::ExpandableHeading
# Expandable title

Body text.
:::::
```

---

## VerticalSplit

`layout`: `"left"` | `"middle"` | `"right"`.

```md
:::::VerticalSplit{layout="left"}
::::VerticalSplitItem
Left column.
::::

::::VerticalSplitItem
Right column.
::::
:::::
```

---

## DropList (Minitasker)

JSON inside a fenced `json` block; `:::::DropList` wraps it.

````md
:::::DropList
```json
{
  "columns": [
    {
      "id": "col",
      "name": "Column name",
      "items": [
        { "id": "i1", "content": "Task text", "justAdded": false }
      ]
    }
  ]
}
```
:::::
````

---

## LinkArray + LinkArrayItem

Outer: `:::::LinkArray` … `:::::`. Inner items: `::::LinkArrayItem` … `::::`.

`contentSource="CUSTOM"`. `itemsPerRow`: often `"3"`.

**Content only (no header):**

```md
:::::LinkArray{contentSource="CUSTOM" itemsPerRow="3"}
::::LinkArrayItem
Body.
::::

::::LinkArrayItem
Body.
::::
:::::
```

**Color header:**

```md
:::::LinkArray{contentSource="CUSTOM" itemsPerRow="3"}
::::LinkArrayItem{headerType="COLOR" headerColor="#CA8A04"}
Body.
::::
:::::
```

**Image header (use URLs from the table at top):**

```md
:::::LinkArray{contentSource="CUSTOM" itemsPerRow="3"}
::::LinkArrayItem{headerType="IMAGE" headerImage="https://i.imgur.com/egDw6na.jpeg"}
Body.
::::
:::::
```

---

## WorkflowBlock + WorkflowBlockItem

First line of each item is the step title; blank line then body.

```md
:::::WorkflowBlock
::::WorkflowBlockItem
Step title

Step body.
::::

::::WorkflowBlockItem
Another step

More text.
::::
:::::
```

---

## Tabs + Tab

```md
:::::Tabs
::::Tab{title="TAB A"}
Tab A content.
::::

::::Tab{title="TAB B"}
Tab B content.
::::
:::::
```

---

## Markdown table

```md
| Col A | Col B |
| ----- | ----- |
| A1    | B1    |
```

---

## Horizontal rule (divider)

```md
***
```

---

## HTML table (Archbee attributes)

- `<table>`: `isTableHeaderOn="true|false"`, `columnWidths="220,220,220"` (comma-separated numbers).
- `<td>`: `align="left|center|right"`, `lightBackgroundColor="#HEX"`, `darkBackgroundColor="#HEX"`, `colSpan="N"`, `rowSpan="N"`.
- Put content in `<p>` inside cells when needed.

Minimal example:

```html
<table isTableHeaderOn="true" columnWidths="200,200">
  <tr>
    <td lightBackgroundColor="#E5E7EB" darkBackgroundColor="#1F2937" align="center">
      <p><strong>Header</strong></p>
    </td>
  </tr>
</table>
```

---

## Plain markdown lists (no custom component)

```md
- [ ] Checklist item
- Bullet
1. Numbered
```

---

## Pitfalls

- **Sync duplication:** external sync can append the same file twice; if you see repeated YAML frontmatter, keep one clean copy.
- **Fences:** mismatching colon counts breaks parsing — copy from this file or from the matching `docs/ALL-BLOCKS/*.md` page.
