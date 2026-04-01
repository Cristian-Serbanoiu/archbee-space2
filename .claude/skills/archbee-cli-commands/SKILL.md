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
- `$DOC_SPACE_ID` — primary space ID
- `$API_KEY` — API key for primary space
- `$DOC_SPACE_GROUP_ID` — space group ID
- `$DOC_SPACE_ID_FOR_SPACE_LINKS` — secondary space ID (used for space links)
- `$API_KEY_FOR_DOC_SPACE_USED_FOR_SPACE_LINKS` — API key for secondary space

If the user explicitly provides different values, use those instead. Each space has its own API key — use the correct one for the target space.

## General Rules

- If the user provides an Archbee URL like `https://app.archbee.co/docs/<spaceId>/<docId>`, parse the **space ID** and **doc ID** from it automatically.
- Ask for any missing **required** options if not provided as arguments, conversation context, or `config.env`.
- Always show the full command before running it.

## Known Issues & Bugs

| Command | Issue | Asana Ticket |
|---|---|---|
| `get-doc` | Fails with "Request with GET/HEAD method cannot have body" — sends body on GET request | Filed |
| `delete-doc` | Returns "Not allowed" even with valid API key | Filed |
| `search-docs` | Empty query `""` rejected by CLI parser; use `' '` (space) as workaround. Also only returns untitled/empty docs, misses titled ones | Filed |
| `create-space` | Missing `--enable-branching-system` flag; backend requires `isBranchingSystemEnabled` but CLI doesn't expose it. Also `--doc-space-id` is confusing (only used for auth, not to create inside) | Filed |
| `update-space` | `--protection-type` (all types) broken: "Couldn't trigger space update". `--jwt-*`, `--saml-metadata`, `--conditional-rule-id` return `{"updated": true}` but don't actually persist. Only `--hostname` and `--space-links` work | Filed |
| `info-openapi` | Always returns "Cannot find open api imported files" even with valid doc tree ID from sync-openapi | Filed |
| `clone-space` | CLI-imported docs appear as broken/empty entries in cloned space | Filed |
| `display-rules` | Fails with "Request with GET/HEAD method cannot have body" — same issue as get-doc | Not filed |
| `export` | Fails with "Request with GET/HEAD method cannot have body" — same issue as get-doc | Not filed |

## Command Status Summary

| Command | Status |
|---|---|
| `get-doc` | BROKEN |
| `create-doc` | WORKS |
| `delete-doc` | BROKEN |
| `search-docs` | PARTIALLY BROKEN |
| `import-content` | WORKS (single file and zip) |
| `create-space` | BROKEN (missing flag) |
| `update-space` | PARTIALLY BROKEN (only hostname + space-links work) |
| `publish-space` | WORKS (PREVIEW works; PUBLISHED needs custom domain) |
| `clone-space` | WORKS (cloned space gets its own API key) |
| `sync-openapi` | WORKS |
| `info-openapi` | BROKEN |
| `upload` | WORKS |
| `merge-suggestion` | NOT TESTED |
| `discard-suggestion` | NOT TESTED |
| `export` | BROKEN |
| `display-rules` | BROKEN |

---

## `archbee get-doc`

Retrieve a document in markdown, html, json, or source format.

**STATUS: BROKEN** — sends body on GET request.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--doc-id <id>` | Document ID |

### Optional Options

| Option | Description | Default |
|---|---|---|
| `--format <fmt>` | Output format: `markdown` \| `html` \| `json` \| `source` | `markdown` |

### Example

```bash
archbee get-doc --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --doc-id W9p74nQMtZuybpEIbjUHe
```

---

## `archbee create-doc`

Create or update a document. If `--doc-id` is provided, the document is updated.

**STATUS: WORKS**

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--content <text>` | Markdown or JSON content (required unless `--file` is used) |

### Optional Options

| Option | Description | Default |
|---|---|---|
| `--format <fmt>` | Content format: `markdown` \| `json` | `markdown` |
| `--doc-id <id>` | Existing document ID to update (omit to create new) | |
| `--parent-doc-id <id>` | Parent document ID (empty string = move to root) | |
| `--title <text>` | Document title | |
| `--description <text>` | Document description | |
| `--slug <text>` | URL slug | |
| `--alias <text>` | URL alias | |
| `--preview-img-url <url>` | Preview image URL | |
| `--conditional-rule-id <id>` | Conditional rule ID | |
| `--sorting <type>` | Insertion order: `alphabetical` \| `chronological` | |
| `--hidden` | Mark document as hidden | |
| `--file <path>` | Read content from file instead of `--content` | |

### Known Issue

The contributor/author on created docs shows as "Dragos" instead of the API key owner.

### Examples

```bash
# Create a new doc
archbee create-doc --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --title "My Doc" --file docs/BASIC/heading-expandable-blocks.md

# Update an existing doc
archbee create-doc --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --doc-id W9p74nQMtZuybpEIbjUHe --content "# Updated content"
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
archbee delete-doc --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --doc-id W9p74nQMtZuybpEIbjUHe
```

---

## `archbee search-docs`

Search documents in a space. Supports word-based, AI chat, and AI retrieval search.

**STATUS: PARTIALLY BROKEN** — empty query doesn't work properly.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--query <text>` | Search query (empty string should return all docs but is rejected by CLI) |

### Optional Options

| Option | Description | Default |
|---|---|---|
| `--type <type>` | Search type: `words` \| `ai-chat` \| `ai-retrieval` | `words` |
| `--doc-id <id>` | Return only this document | |
| `--parent-doc-id <id>` | Return only children of this doc (`"null"` for root) | |
| `--search-only-title` | Search only by title | |
| `--data-text-format <fmt>` | Return dataText as: `markdown` \| `html` | |
| `--persist-search` | Keep a SearchSession in the database | |
| `--search-session-id <id>` | Existing SearchSession ID to continue | |

### Known Issues

- `--query ""` is rejected by the CLI parser. Use `--query ' '` (space) as workaround.
- Even with the workaround, only untitled/empty docs are returned. Docs with titles and content are missing.

### Examples

```bash
# Word search
archbee search-docs --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --query "getting started"

# AI chat search
archbee search-docs --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --query "how do I authenticate?" --type ai-chat

# List all docs (workaround — results are incomplete)
archbee search-docs --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --query ' '
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
archbee import-content --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --file docs/BASIC/heading-expandable-blocks.md

# Import a zip of markdown files (preserves folder structure)
archbee import-content --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --file /tmp/archbee-docs.zip
```

---

## `archbee create-space`

Create a new space. The new space inherits the API key.

**STATUS: BROKEN** — missing `--enable-branching-system` flag required by backend.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID (for auth only, not the parent) |
| `--api-key <key>` | API key |

### Optional Options

| Option | Description | Default |
|---|---|---|
| `--name <text>` | Space name | |
| `--enable-llm` | Enable AI features | |
| `--enable-review-system` | Enable review system | |
| `--doc-space-group-id <id>` | Space group ID to add this space to | |

### Known Issues

- Backend requires `isBranchingSystemEnabled` but CLI has no `--enable-branching-system` flag.
- `--doc-space-id` is confusingly named — it's only used for auth, not to create a space inside another space. Spaces are created in the `--doc-space-group-id`.

### Example

```bash
archbee create-space --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --name "My New Space" --doc-space-group-id $DOC_SPACE_GROUP_ID --enable-llm --enable-review-system
```

---

## `archbee update-space`

Update space settings including access control, hostname, and space links.

**STATUS: PARTIALLY BROKEN** — only `--hostname` and `--space-links` work.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |

### Optional Options

| Option | Description | Default | Status |
|---|---|---|---|
| `--protection-type <type>` | Access type: `None` \| `Password` \| `Guest accounts` \| `Private Accounts` \| `Private Link` \| `Magic Link` \| `JWT` \| `SAML` | | BROKEN |
| `--password <text>` | Password (when protection-type is Password) | | BROKEN |
| `--hostname <domain>` | Custom hostname (e.g. docs.example.com) | | WORKS (needs DNS CNAME) |
| `--hostname-path <path>` | Path component for hostname | | WORKS (required with --hostname) |
| `--jwt-validation-type <type>` | JWT validation: `JWT-Secret` \| `JWT-KeySet` | | BROKEN (false success) |
| `--jwt-secret <text>` | JWT secret | | BROKEN (false success) |
| `--jwt-key-url <url>` | JWT key URL | | BROKEN (false success) |
| `--jwt-redirect-url <url>` | JWT redirect URL | | BROKEN (false success) |
| `--saml-metadata <xml>` | SAML metadata XML | | BROKEN (false success) |
| `--conditional-rule-id <id>` | Conditional rule ID | | BROKEN (false success) |
| `--space-links <json>` | JSON array of space links | | WORKS |

### Space Links Format

Each link object MUST include `docSpaceId` (undocumented but required):

```bash
archbee update-space --doc-space-id $DOC_SPACE_ID --api-key $API_KEY \
  --space-links '[{"label":"Space 01","url":"","docSpaceId":"<space-id-1>"},{"label":"Space 02","url":"","docSpaceId":"<space-id-2>"}]'
```

After updating space links, you must **publish both spaces** for the links to take effect on the preview/published site.

### Examples

```bash
# Set custom hostname (requires CNAME to proxy.archbee.com)
archbee update-space --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --hostname docs.example.com --hostname-path "/"

# Set space links (both spaces need this + publish after)
archbee update-space --doc-space-id $DOC_SPACE_ID --api-key $API_KEY \
  --space-links '[{"label":"Space 01","url":"","docSpaceId":"0dr55HycrBQLOo0IwVeKW"},{"label":"Space 02","url":"","docSpaceId":"8mUx-Y8tosy1ijIqpaV8-"}]'
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
archbee publish-space --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --environment PREVIEW

# Publish to production (requires custom domain)
archbee publish-space --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --environment PUBLISHED
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
archbee clone-space --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --target-space-group-id $DOC_SPACE_GROUP_ID
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
archbee sync-openapi --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --file ~/Downloads/petstore-swagger.json --create-intro

# Sync with language examples
archbee sync-openapi --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --file api-spec.yaml --create-intro --language-examples "curl,python,javascript"

# Update an existing API reference tree with a new spec version
archbee sync-openapi --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --file api-spec.json --doc-tree-id M6eF_mzK2K9Fag31kqt89

# Import a Postman collection
archbee sync-openapi --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --file collection.json --type postman
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
archbee info-openapi --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --doc-tree-id M6eF_mzK2K9Fag31kqt89
```

---

## `archbee upload`

Upload a single file to the File Manager.

**STATUS: WORKS** — returns file URL and metadata.

### Required Options

| Option | Description |
|---|---|
| `--doc-space-id <id>` | Document space ID |
| `--api-key <key>` | API key |
| `--file <path>` | Path to file to upload |

### Optional Options

| Option | Description | Default |
|---|---|---|
| `--public` | Make the file publicly accessible | `true` |
| `--no-public` | Make the file private | |

### Example

```bash
archbee upload --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --file ./image.png
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
archbee merge-suggestion --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --doc-id SUGGEST-abc123
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
archbee discard-suggestion --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --doc-id SUGGEST-abc123
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
archbee export --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --team-id myteam123 --export-this-space-only
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
archbee display-rules --doc-space-id $DOC_SPACE_ID --api-key $API_KEY --team-id myteam123
```
