---
name: archbee-cli-commands
description: >-
  Archbee CLI commands reference. Use when the user wants to run archbee CLI
  commands like get-doc, create-doc, or any other archbee CLI operation.
---

# Archbee CLI Commands

This skill contains all available `archbee` CLI commands, their options, usage guidance, and known issues.

## Default Configuration

Before building any command, read `.claude/skills/archbee-cli-commands/config.env` and substitute variables with the values from that file. Available variables:
- `$DOC_SPACE_ID_FIRST_SPACE` — primary space ID
- `$API_KEY_FIRST_SPACE` — API key for primary space
- `$DOC_SPACE_GROUP_ID_FIRST` — space group ID
- `$DOC_SPACE_ID_SECOND_SPACE` — secondary space ID (used for space links)
- `$API_KEY_SECOND_SPACE` — API key for secondary space

If the user explicitly provides different values, use those instead. Each space has its own API key — use the correct one for the target space.

## General Rules

- If the user provides an Archbee URL like `https://app.archbee.co/docs/<spaceId>/<docId>`, parse the **space ID** and **doc ID** from it automatically.
- Ask for any missing **required** options if not provided as arguments, conversation context, or `config.env`.
- Always show the full command before running it.

## Known Issues & Bugs

| Command | Issue | Asana Ticket |
|---|---|---|
| `get-doc` | ~~Fails with "Request with GET/HEAD method cannot have body"~~ — FIXED as of 2026-04-02. `--format source` returns empty output (unclear purpose). `--format html` flattens expandable headings. | Filed |
| `delete-doc` | Returns "Not allowed" even with valid API key | Filed |
| `search-docs` | Empty query `""` rejected by CLI parser; use `' '` (space) as workaround. Also only returns untitled/empty docs, misses titled ones. `--parent-doc-id` doesn't filter results — non-children leak through. | Filed (`1213904574009139`) |
| `create-space` | ~~Missing `--enable-branching-system` flag~~ — FIXED as of 2026-04-02. `--enable-llm`, `--enable-review-system`, `--doc-space-group-id` are listed as optional but backend requires them. `--doc-space-id` is confusing (only used for auth, not to create inside) | Filed |
| `update-space` | ~~`--protection-type` all broken~~ — `None` and `Password` now WORK (2026-04-02). `Guest accounts`, `Private Accounts`, `Private Link`, `Magic Link` fail because CLI is missing required sub-options. `JWT`, `SAML`, `--conditional-rule-id` still broken ("Couldn't trigger space update"). `--hostname`, `--space-links` work. | Filed |
| `info-openapi` | Always returns "Cannot find open api imported files" even with valid doc tree ID from sync-openapi | Filed |
| `create-doc` | `--content` with literal `\n` doesn't parse escape sequences; entire string becomes the title, `--title` flag ignored, body empty. `--format json` crashes with "expected string, received undefined". `--sorting alphabetical` crashes with "localeCompare" error. | Filed (`1213904227098881`) |
| `clone-space` | CLI-imported docs appear as broken/empty entries in cloned space | Filed |
| `display-rules` | ~~Fails with "Request with GET/HEAD method cannot have body"~~ — FIXED as of 2026-04-02. Returns `{ "displayRules": [] }` when no rules configured. | Not filed |
| `export` | ~~GET-with-body bug~~ FIXED. Now broken: sends `exportThisSpaceOnly` and `exportAsLink` as strings instead of booleans. Also `--no-export-as-link` not recognized (same `--no-*` bug as upload). | Filed (`1213904983484133`) |

## Command Status Summary

| Command | Status |
|---|---|
| `get-doc` | WORKS (all formats; `source` returns empty, `html` flattens expandable headings) |
| `create-doc` | PARTIALLY WORKS (`--format json`, `--sorting alphabetical`, `--title` with `--content` broken) |
| `delete-doc` | BROKEN |
| `search-docs` | PARTIALLY WORKS (empty query broken, `--parent-doc-id` doesn't filter; `--query`, `--type`, `--doc-id`, `--search-only-title`, `--data-text-format` all work) |
| `import-content` | WORKS (single file and zip) |
| `create-space` | WORKS (`--enable-llm`, `--enable-review-system`, `--doc-space-group-id` are effectively required) |
| `update-space` | PARTIALLY WORKS (`None`, `Password`, `--hostname`, `--space-links` work; JWT, SAML, conditional rules broken; other protection types need missing sub-options) |
| `publish-space` | WORKS (PREVIEW works; PUBLISHED needs custom domain) |
| `clone-space` | WORKS (cloned space gets its own API key) |
| `sync-openapi` | BROKEN (returns success but no docs created — regression) |
| `info-openapi` | BROKEN |
| `upload` | PARTIALLY WORKS (`--no-public` broken) |
| `merge-suggestion` | NOT TESTED |
| `discard-suggestion` | NOT TESTED |
| `export` | BROKEN (old GET-with-body fixed, now boolean serialization bug + `--no-export-as-link` not recognized) |
| `display-rules` | WORKS (previously broken, GET-with-body fixed) |

---

## `archbee get-doc`

Retrieve a document in markdown, html, json, or source format.

**STATUS: WORKS** — tested 2026-04-02. Previously broken (GET-with-body issue), now fixed.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID (required) |
| `--api-key <key>` | API key (required) |
| `--doc-id <id>` | Document ID (required) |

### Optional Options

| Option | Description | Default | Status |
|---|---|---|---|
| `--format markdown` | Returns markdown with frontmatter (title, slug, tags, dates) and Archbee block syntax (e.g. `:::ExpandableHeading`) | default | WORKS |
| `--format html` | Returns HTML. Expandable headings are flattened — heading titles and content become plain `<h>` and `<p>` tags with no collapsible wrapper | | WORKS (lossy for expandable headings) |
| `--format json` | Returns Archbee's internal Slate-like block JSON with types like `h1`, `expandable-heading1`, `paragraph`, etc. Each block has an `id`, `type`, `children`, and optional `data` | | WORKS |
| `--format source` | Returns empty output (exit code 0, no error). Purpose unclear — may not be implemented for all doc types | | WORKS (empty output) |

### Notes

- Invalid `--doc-id` returns "Document not found!" (exit code 1), not a crash.
- The `markdown` format includes Archbee-specific block syntax (e.g. `:::ExpandableHeading ... :::`) which is useful for round-tripping with `create-doc`.
- The `json` format is the richest representation — includes block IDs, nested children, and block-level data (e.g. `{ "isOpen": false }` for expandable headings).
- The `html` format is lossy — it strips expandable heading wrappers and other Archbee-specific structures.

### Examples

```bash
# Get doc as markdown (default)
archbee get-doc --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --doc-id I6lnGG5rdJNHduxPuf618

# Get doc as HTML
archbee get-doc --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --doc-id I6lnGG5rdJNHduxPuf618 --format html

# Get doc as JSON (internal block structure)
archbee get-doc --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --doc-id I6lnGG5rdJNHduxPuf618 --format json

# Get doc as source (returns empty)
archbee get-doc --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --doc-id I6lnGG5rdJNHduxPuf618 --format source
```

---

## `archbee create-doc`

Create or update a document. If `--doc-id` is provided, the document is updated.

**STATUS: PARTIALLY WORKS** — tested 2026-04-02. Most options work, but `--format json`, `--sorting alphabetical`, and `--title` with `--content` have bugs.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID (required) |
| `--api-key <key>` | API key (required) |
| `--content <text>` | Markdown content (required unless `--file` is used) |

### Optional Options

| Option | Description | Default | Status |
|---|---|---|---|
| `--format markdown` | Content format: markdown | `markdown` | WORKS |
| `--format json` | Content format: JSON (Slate block array) | | BROKEN — "Invalid input: expected string, received undefined" |
| `--doc-id <id>` | Existing document ID to update (omit to create new). Returns `"newRecord": false` on update | | WORKS |
| `--parent-doc-id <id>` | Parent document ID. Empty string `""` moves doc to root | | WORKS |
| `--title <text>` | Document title | | BUGGY — when used with `--content`, the title gets set to the raw content string (with escaped `\n`) instead of the `--title` value. Use `--file` instead of `--content` to avoid this |
| `--description <text>` | Document description | | WORKS |
| `--slug <text>` | URL slug | | WORKS |
| `--alias <text>` | URL alias | | WORKS |
| `--preview-img-url <url>` | Preview image URL | | WORKS (accepted, not verified if persisted in UI) |
| `--conditional-rule-id <id>` | Conditional rule ID | | WORKS (accepted, not verified if persisted in UI) |
| `--sorting alphabetical` | Insert doc in alphabetical order | | BROKEN — "Cannot read properties of undefined (reading 'localeCompare')" |
| `--sorting chronological` | Insert doc in chronological order | | WORKS |
| `--hidden` | Mark document as hidden (excluded from search by default, but found with `--doc-id` filter) | | WORKS |
| `--file <path>` | Read content from file instead of `--content`. File must exist on disk | | WORKS |

### Known Issues

- The contributor/author on created docs shows as "Dragos" in the UI instead of the API key owner. The `createdBy` field in search results is empty.
- `--title` with `--content`: The title is set to the raw `--content` string instead of the `--title` value. Workaround: use `--file` to provide content instead of `--content`.
- `--format json`: Crashes with "expected string, received undefined". Cannot create docs using JSON block format via CLI.
- `--sorting alphabetical`: Crashes with "localeCompare" error. Only `chronological` works.
- `get-doc --format json` on a doc created with `--hidden` returns `[]` (empty array).

### Examples

```bash
# Create a new doc with content inline
archbee create-doc --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --content "# My Doc"

# Create a doc from a file (recommended — avoids --title bug)
archbee create-doc --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --title "My Doc" --file docs/BASIC/heading-expandable-blocks.md

# Create with all working optional flags
archbee create-doc --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --file docs/BASIC/heading-expandable-blocks.md --title "My Doc" --description "A test doc" --slug "my-doc" --alias "my-doc-alias" --hidden

# Update an existing doc
archbee create-doc --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --doc-id m7yavx3wO7cegkO65vUSQ --content "# Updated content"

# Create a child doc under a parent
archbee create-doc --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --content "# Child" --parent-doc-id m7yavx3wO7cegkO65vUSQ

# Move a doc to root
archbee create-doc --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --content "# Moved" --doc-id IdQxYHFbvX-Q0g-z5uBzW --parent-doc-id ""

# Insert doc in chronological order (alphabetical is broken)
archbee create-doc --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --content "# Sorted" --sorting chronological
```

---

## `archbee delete-doc`

Permanently delete a document by ID.

**STATUS: BROKEN** — returns "Not allowed" even with valid API key.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--doc-id <id>` | Document ID to delete |

### Example

```bash
archbee delete-doc --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --doc-id W9p74nQMtZuybpEIbjUHe
```

---

## `archbee search-docs`

Search documents in a space. Supports word-based, AI chat, and AI retrieval search.

**STATUS: PARTIALLY WORKS** — tested 2026-04-02. Most options work, but `--parent-doc-id` doesn't filter, `--persist-search` doesn't return session IDs, and empty query is broken.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID (required) |
| `--api-key <key>` | API key (required) |

### Optional Options

| Option | Description | Default | Status |
|---|---|---|---|
| `--query <text>` | Search query. Omit or empty to return all docs | | WORKS (empty string rejected by CLI, use `' '` workaround — but only returns untitled docs) |
| `--type words` | Word-based search with highlights | `words` | WORKS |
| `--type ai-chat` | AI-powered search returning a `generativeAnswer` field with natural language response (~3s) | | WORKS |
| `--type ai-retrieval` | AI-powered search returning relevant docs without generative answer (~4s) | | WORKS |
| `--doc-id <id>` | Return only this document. Returns full `dataText` content (not just frontmatter) | | WORKS |
| `--parent-doc-id <id>` | Supposed to return only children of this doc (`"null"` for root) | | BUGGY — doesn't strictly filter, non-children leak through |
| `--search-only-title` | Search only by document title | | WORKS |
| `--data-text-format markdown` | Return `dataText` as markdown with frontmatter | | WORKS |
| `--data-text-format html` | Return `dataText` as HTML (title only, no frontmatter) | | WORKS |
| `--persist-search` | Keep a SearchSession in the database | | UNCLEAR — `searchSessionId` returns empty `""` |
| `--search-session-id <id>` | Existing SearchSession ID to continue | | NOT TESTABLE — no session ID available |

### Known Issues

- `--query ""` is rejected by the CLI parser. Use `--query ' '` (space) as workaround.
- Even with the workaround, only untitled/empty docs are returned. Docs with titles and content are missing.
- `--parent-doc-id` does not strictly filter results to children of the specified parent. Non-children with matching query terms appear in results. The filter may only boost ranking rather than exclude. Filed as Asana ticket `1213904574009139`.
- `--persist-search` returns `searchSessionId: ""` — unclear if sessions are being persisted.

### Examples

```bash
# Word search
archbee search-docs --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --query "getting started"

# AI chat search (returns generative answer)
archbee search-docs --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --query "Who is Darius?" --type ai-chat

# AI retrieval search (returns relevant docs)
archbee search-docs --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --query "key objectives" --type ai-retrieval

# Filter to single doc (returns full content in dataText)
archbee search-docs --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --query "Darius" --doc-id vA80SCuG57fQKHN1x0vkK

# Search by title only
archbee search-docs --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --query "MIREL" --search-only-title

# Get dataText as HTML
archbee search-docs --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --query "Darius" --data-text-format html

# List all docs (workaround — results are incomplete)
archbee search-docs --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --query ' '
```

---

## `archbee import-content`

Import a markdown file or zip archive of markdown files as new documents.

**STATUS: WORKS** — zip import preserves folder structure as categories.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--file <path>` | Path to `.md` file or `.zip` archive |

### Tips

- Zip archives preserve directory structure as categories (e.g. `docs/BASIC/*.md` creates a BASIC category).
- To import all docs at once, zip them first: `zip -r docs.zip docs/BASIC/*.md docs/MEDIA/*.md docs/DEVELOPER/*.md`

### Examples

```bash
# Import a single markdown file
archbee import-content --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --file docs/BASIC/heading-expandable-blocks.md

# Import a zip of markdown files (preserves folder structure)
archbee import-content --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --file /tmp/archbee-docs.zip
```

---

## `archbee create-space`

Create a new space. The new space inherits the API key.

**STATUS: WORKS** — tested 2026-04-02. Previously broken (missing `--enable-branching-system`), now fixed.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | An existing space ID with an API key (required, for auth only — not the parent) |
| `--api-key <key>` | API key (required) |

### Optional Options (effectively required by backend)

| Option | Description | Default | Status |
|---|---|---|---|
| `--name <text>` | Space name | | WORKS |
| `--enable-llm` | Enable AI features | | WORKS — listed as optional but backend requires it |
| `--enable-review-system` | Enable review system | | WORKS — listed as optional but backend requires it |
| `--enable-branching-system` | Enable branching/versioning system | | WORKS — previously missing, now fixed |
| `--doc-space-group-id <id>` | Space group ID to add this space to | | WORKS — listed as optional but backend requires it |

### Notes

- `--enable-llm`, `--enable-review-system`, and `--doc-space-group-id` are listed as optional in CLI help but the backend rejects the request without them ("Invalid input: expected boolean/string, received undefined").
- `--doc-space-id` is confusingly named — it's only used for auth, not to create a space inside another space.
- Returns `{ "newDocSpaceId": "<id>" }` — the new space gets its own API key (retrieve from Archbee UI).
- CLI-created spaces are empty ("Space is empty"). Unlike UI-created spaces, they don't get an initial untitled document — you need to add docs via `create-doc` or `import-content`.

### Examples

```bash
# Create a space with all required flags
archbee create-space --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --name "My New Space" --doc-space-group-id $DOC_SPACE_GROUP_ID_FIRST --enable-llm --enable-review-system

# Create with branching enabled
archbee create-space --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --name "Branching Space" --doc-space-group-id $DOC_SPACE_GROUP_ID_FIRST --enable-llm --enable-review-system --enable-branching-system
```

---

## `archbee update-space`

Update space settings including access control, hostname, and space links.

**STATUS: PARTIALLY WORKS** — tested 2026-04-02. `None`, `Password`, `--hostname`, `--space-links` work. JWT, SAML, conditional rules broken. Other protection types need sub-options the CLI doesn't expose.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID (required) |
| `--api-key <key>` | API key (required) |

### Optional Options

| Option | Description | Default | Status |
|---|---|---|---|
| `--protection-type None` | No access control | | WORKS — confirmed persists in UI |
| `--protection-type Password` | Password protection (use with `--password`) | | WORKS — confirmed persists in UI |
| `--protection-type "Guest accounts"` | Guest account access | | FAILS — CLI missing required `publicGuestAccounts` sub-option |
| `--protection-type "Private Accounts"` | Private account access | | FAILS — CLI missing required `publicPrivateAccounts` sub-option |
| `--protection-type "Private Link"` | Private link access | | FAILS — CLI missing required `privateLinkTokens` sub-option |
| `--protection-type "Magic Link"` | Magic link access | | FAILS — CLI missing required `publicMagicLinkAccounts` sub-option |
| `--protection-type JWT` | JWT authentication | | BROKEN — "Couldn't trigger space update" even with all JWT options |
| `--protection-type SAML` | SAML authentication | | BROKEN — "Couldn't trigger space update" even with `--saml-metadata` |
| `--password <text>` | Password (when protection-type is Password) | | WORKS |
| `--hostname <domain>` | Custom hostname (e.g. docs.example.com) | | WORKS (needs DNS CNAME to proxy.archbee.com) |
| `--hostname-path <path>` | Path component for hostname | | WORKS |
| `--jwt-validation-type <type>` | JWT validation: `JWT-Secret` \| `JWT-KeySet` | | BROKEN — "Couldn't trigger space update" |
| `--jwt-secret <text>` | JWT secret | | BROKEN |
| `--jwt-key-url <url>` | JWT key URL | | BROKEN |
| `--jwt-redirect-url <url>` | JWT redirect URL | | BROKEN |
| `--saml-metadata <xml>` | SAML metadata XML | | BROKEN |
| `--conditional-rule-id <id>` | Conditional rule ID | | BROKEN — "Couldn't trigger space update" |
| `--space-links <json>` | JSON array of space links | | WORKS — confirmed persists in UI |

### Space Links Format

Each link object MUST include `docSpaceId`:

```bash
archbee update-space --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE \
  --space-links '[{"label":"Space 01","url":"","docSpaceId":"<space-id-1>"},{"label":"Space 02","url":"","docSpaceId":"<space-id-2>"}]'
```

After updating space links, you must **publish both spaces** for the links to take effect on the preview/published site.

### Examples

```bash
# Set custom hostname
archbee update-space --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --hostname g12.aiurlabs.com --hostname-path cli-space-01-edited

# Set protection to Password
archbee update-space --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --protection-type Password --password "mypassword"

# Reset protection to None
archbee update-space --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --protection-type None

# Set space links
archbee update-space --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE \
  --space-links '[{"label":"Space 01","url":"","docSpaceId":"hYiQO-fZULb1p_6VLXw2N"},{"label":"Space 02","url":"","docSpaceId":"q0ZvgbbJ6RsHgWjvdFcVF"}]'
```

---

## `archbee publish-space`

Publish documents from a space.

**STATUS: WORKS**

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--environment <env>` | `PREVIEW` or `PUBLISHED` |

### Notes

- `PREVIEW` works immediately.
- `PUBLISHED` requires a custom domain configured first.
- After updating space links on multiple spaces, publish ALL linked spaces.
- Returns full space configuration including docsTree, spaceLinks, settings, etc.

### Examples

```bash
# Publish to preview
archbee publish-space --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --environment PREVIEW

# Publish to production (requires custom domain)
archbee publish-space --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --environment PUBLISHED
```

---

## `archbee clone-space`

Clone a space into a target space group.

**STATUS: WORKS** — the cloned space gets its own API key.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID to clone |
| `--api-key <key>` | API key |

### Optional Options

| Option | Description | Default |
|---|---|---|
| `--target-space-group-id <id>` | Target space group ID | |

### Notes

- The cloned space gets a new space ID and its own API key.
- You need to get the new API key from the Archbee UI to use other commands on the cloned space.
- Returns `{ "newDocSpaceId": "<id>" }`.

### Example

```bash
archbee clone-space --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --target-space-group-id $DOC_SPACE_GROUP_ID_FIRST
```

---

## `archbee sync-openapi`

Sync an OpenAPI document with a new or existing API reference tree.

**STATUS: WORKS** — creates a full API reference tree with categories per tag and individual endpoint pages.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--file <path>` | Path to OpenAPI file (`.json`/`.yml`/`.yaml`/`.zip`) |

### Optional Options

| Option | Description | Default |
|---|---|---|
| `--doc-tree-id <id>` | Existing docTree ID to update | |
| `--type <type>` | Import type: `openapi` \| `postman` | `openapi` |
| `--try-it` | Enable API Try It feature | `true` |
| `--show-download` | Show download link for the OpenAPI file | |
| `--create-schema-category` | Create models category | |
| `--create-intro` | Create intro document | |
| `--language-examples <langs>` | Comma-separated language examples (max 5) | |

### How It Works

- Takes a Swagger/OpenAPI JSON or YAML file and generates a full API reference documentation tree in the space.
- Endpoints are grouped by tags (e.g. `pet`, `store`, `user`) as categories.
- Each endpoint gets its own page showing the HTTP method (GET, POST, PUT, DELETE), parameters, request body, and responses.
- Use `--create-intro` to add an Introduction page at the top.
- Use `--doc-tree-id` to update an existing API reference tree instead of creating a new one.
- Returns `{ "docTreeId": "<id>" }` — save this ID to update the tree later with a new spec version.
- Use `--type postman` to import a Postman collection instead of OpenAPI.

### Notes

- Supports Swagger 2.0 and OpenAPI 3.x formats.
- The file can be `.json`, `.yml`, `.yaml`, or a `.zip` containing the spec.
- To update an existing tree (e.g. when your API changes), pass the `--doc-tree-id` from the original sync.
- Use `info-openapi` with the `--doc-tree-id` to get details about an existing tree.

### Examples

```bash
# Sync a new OpenAPI spec with intro page
archbee sync-openapi --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --file ~/Downloads/petstore-swagger.json --create-intro

# Sync with language examples
archbee sync-openapi --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --file api-spec.yaml --create-intro --language-examples "curl,python,javascript"

# Update an existing API reference tree with a new spec version
archbee sync-openapi --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --file api-spec.json --doc-tree-id M6eF_mzK2K9Fag31kqt89

# Import a Postman collection
archbee sync-openapi --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --file collection.json --type postman
```

---

## `archbee info-openapi`

Get info of an existing OpenAPI tree.

**STATUS: BROKEN** — always returns "Cannot find open api imported files" even with valid doc tree ID from `sync-openapi`.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--doc-tree-id <id>` | DocTree ID to get info for |

### Example

```bash
archbee info-openapi --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --doc-tree-id M6eF_mzK2K9Fag31kqt89
```

---

## `archbee upload`

Upload a single file to the File Manager.

**STATUS: PARTIALLY WORKS** — tested 2026-04-02. Upload works, but `--no-public` is broken.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID (required) |
| `--api-key <key>` | API key (required) |
| `--file <path>` | Path to file to upload (required) |

### Optional Options

| Option | Description | Default | Status |
|---|---|---|---|
| `--public` | Make the file publicly accessible | `true` | WORKS |
| `--no-public` | Make the file private | | BROKEN — "Unknown option '--no-public'" even though `--help` lists it. Filed as Asana ticket `1213904657476090` |

### Notes

- Returns file metadata: `id`, `name`, `src` (S3 URL), `isPublic`, `type`, `createdAt`
- All uploads are forced public since `--no-public` doesn't work
- `--public false` also doesn't work ("Unexpected argument 'false'")

### Examples

```bash
# Upload a public image (default)
archbee upload --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --file ./image.png

# Upload with explicit --public flag
archbee upload --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --file ./image.png --public
```

---

## `archbee merge-suggestion`

Merge a suggestion document into the base document.

**STATUS: NOT TESTED**

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--doc-id <id>` | Suggestion document ID (must start with `SUGGEST-`) |

### Example

```bash
archbee merge-suggestion --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --doc-id SUGGEST-abc123
```

---

## `archbee discard-suggestion`

Discard and delete a suggestion document without merging.

**STATUS: NOT TESTED**

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--doc-id <id>` | Suggestion document ID (must start with `SUGGEST-`) |

### Example

```bash
archbee discard-suggestion --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --doc-id SUGGEST-abc123
```

---

## `archbee export`

Export team documentation as a signed S3 URL.

**STATUS: BROKEN** — same GET-with-body issue as `get-doc`.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--team-id <id>` | Team ID |

### Optional Options

| Option | Description | Default |
|---|---|---|
| `--export-this-space-only` | Export only this space | `false` |
| `--no-export-as-link` | Return raw data instead of a link | |

### Example

```bash
archbee export --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --team-id myteam123 --export-this-space-only
```

---

## `archbee display-rules`

List display rules for an organization.

**STATUS: NOT TESTED**

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--team-id <id>` | Team ID |

### Example

```bash
archbee display-rules --doc-space-id $DOC_SPACE_ID_FIRST_SPACE --api-key $API_KEY_FIRST_SPACE --team-id myteam123
```
