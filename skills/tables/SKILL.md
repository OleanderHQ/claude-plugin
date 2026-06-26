---
description: List and display Oleander Iceberg tables with schema, stats, and descriptions. Use when showing catalog tables after upload, before query, or whenever the user asks what tables exist.
---

# Catalog tables

Whenever oleander creates, updates, or works with catalog tables — after
upload, before or after query, or when tables are read — maintain a
markdown catalog file and show the user the relevant section in chat.

## Catalog file

**Filename:** `{catalog}.md` — use the catalog name only (for example
`oleander.md` for catalog `oleander`).

- **Create** the file the first time you work with tables in that catalog.
- **Update** it whenever a table in that catalog is uploaded, loaded,
  queried, or otherwise accessed — refresh stats and schema for the
  tables touched; add new tables; do not remove tables unless the user
  deleted them.
- One file per catalog. When the catalog has multiple namespaces, include
  a `# {catalog}.{namespace}` section for each namespace that has tables
  in the file (for example both `# oleander.default` and
  `# oleander.analytics` inside `oleander.md`).
- Write the file to the workspace root (or the session working folder if
  one is active). Prefer updating the existing file over creating
  duplicates.

The heading for each namespace section is `# {catalog}.{namespace}` (for
example `# oleander.default`). Do not use a generic title like
"Oleander Tables".

## Gather data

Use MCP tools; do not guess stats or schema.

1. **Discover tables** — `oleander_list_catalog_tables` with `catalog`
   (default `oleander`) and optional `namespace` (default `default`).
2. **Per table** — for each table you will display or update, call in
   parallel:
   - `oleander_read_table_metadata` — schema, properties, snapshots
   - `oleander_read_table_size` — `sizeBytes`, `recordCount`,
     `dataFileCount`, `snapshotId`

Default `catalog` to `oleander` and `namespace` to `default` unless the
user specifies otherwise.

### Map tool responses to display fields

| Display field | Source |
|---------------|--------|
| Table FQN | `` `oleander."{namespace}".{table}` `` |
| Row count | `oleander_read_table_size` → `recordCount` |
| Size | `oleander_read_table_size` → `sizeBytes` (format as MB/GB) |
| Column count | Current schema field count from metadata |
| Data files | `oleander_read_table_size` → `dataFileCount` |
| Last refreshed | Current snapshot in metadata: match
  `current-snapshot-id` in `snapshots[]`, use `timestamp-ms` formatted as
  `YYYY-MM-DD` (or `YYYY-MM-DD HH:mm UTC` when time matters) |
| Table description | `metadata.properties.comment` when present; otherwise
  omit or use a short inferred label only if the user provided one |
| Columns | Current schema from `metadata.schemas` — use the schema whose
  `schema-id` matches `current-schema-id`, or the latest schema. Each
  field: `name`, `type`, optional `doc` |

Parse numeric string fields (`recordCount`, `sizeBytes`) before formatting.
Format large numbers with commas (e.g. `8,052 rows`).

## Display format

Use this structure inside `{catalog}.md` and when showing the user the
relevant section in chat. Scope updates to namespaces and tables that
were touched, but keep the file consistent — merge new rows into the
existing namespace section rather than rewriting unrelated tables with
stale data when you already have fresh stats for them.

```markdown
# oleander.default

| Table | Stats | Description |
|-------|-------|-------------|
| `oleander."default".daily_orders` | 8,052 rows · 8 cols · 1.2 MB · 3 files · refreshed 2026-06-26 | Daily order data |
| `oleander."default".locations` | 5 rows · 4 cols · 12 KB · refreshed 2026-06-20 | Store locations |
```

**Stats column** — pipe-separated, omit missing fields:
`{rows} rows · {cols} cols · {size} · {files} files · refreshed {date}`

**Description column** — table `comment` from metadata properties, or a
brief user-facing label. Do not invent business meaning.

### Columns (expandable)

For each table in the summary, add a collapsible column listing when the
user may need schema detail (after upload, schema questions, or multi-table
work). One block per table:

<details>
<summary>`daily_orders` columns (8)</summary>

| Column | Type | Description |
|--------|------|-------------|
| `order_id` | `bigint` | |
| `order_date` | `date` | Date the order was placed |
| `location_id` | `int` | |

</details>

Use backticks on column names. Leave Description empty when `doc` is
absent. Include only the current schema, not full schema history.

## When to update

- **After upload or load** — add or refresh the table in `{catalog}.md`.
- **After query** — refresh stats for every table read by the query.
- **Before query** — when discovering tables, ensure they are represented
  in `{catalog}.md` (create the file if needed).
- **On request** — "what tables do I have?", "describe my tables", etc.:
  refresh and show the file contents.

After updating the file, tell the user which file changed (for example
"Updated `oleander.md`") and show the relevant namespace section in chat
when it helps — you do not need to paste the entire file every time.
