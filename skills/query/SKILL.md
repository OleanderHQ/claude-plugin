---
name: query
description: Query your oleander warehouse and get instant answers from your data. The right engine for the right SQL, at any dataset size. Use when looking up sales numbers, comparing locations or products, filtering by date or category, or exploring what's in a table.
---

# Query

Check dataset size first, then route to the correct engine.

## Choose the right engine for the right SQL

For each table the query reads, call `oleander:oleander_read_table_size`
with `catalog: oleander`, `namespace: default`. Parse `sizeBytes`.

- **Under 50 GB** → run with `oleander:oleander_query_lake` (DuckDB)
- **50 GB or above** → run as a Spark SQL job (see **Large tables** below)

When joining multiple tables, route to Spark if any one table is at or
above 50 GB.

Use `oleander:oleander_query_lake` for schema reads (`DESCRIBE`,
`information_schema`) regardless of table size.

Tell the user which engine was chosen and the table size that drove it.

## Small tables (under 50 GB)

1. Confirm schema if needed: `DESCRIBE <table>` via
   `oleander:oleander_query_lake`
2. Write and run the SQL via `oleander:oleander_query_lake`

## Large tables (50 GB and above)

1. Confirm schema if needed: `DESCRIBE <table>` via
   `oleander:oleander_query_lake`
2. Write standard Spark SQL only — no PySpark, no DataFrame API
3. Submit via `oleander:oleander_submit_spark_sql`

**Not supported in Spark SQL:**
- `QUALIFY` — rewrite as subquery with `WHERE`
- `EXCLUDE` in `SELECT` — list columns explicitly
- `TRY_CAST` — use `CAST` with `TRY` separately
- `strftime` — use `DATE_FORMAT(col, 'yyyy-MM-dd')`
- `STRING_AGG` — use `COLLECT_LIST` or `GROUP_CONCAT`