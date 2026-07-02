---
name: query
description: Use query to explore your data; compare seasonal revenue, analyze product changes, count new user signups, or find whatever answers are hidden in your data.
---

# Query

Check how much data the query will actually read, then route to the
correct engine.

## Choose the right query engine for the right data

For each table the query reads:

1. Call `oleander:oleander_read_table_metadata` with `catalog: oleander`,
   `namespace: default` to get schema and the partition spec.
2. From the SQL, identify WHERE clause predicates on partition columns.
   If partition filtering cannot be determined, use the full-table size
   as a conservative estimate.
3. Call `oleander:oleander_read_table_size` with the same `catalog` and
   `namespace`. If the query targets specific partitions, pass
   `partition_filters` as `[{ key, value }]` to return the size for
   only those partitions. Otherwise use the full-table `sizeBytes`.
4. Parse `sizeBytes` (returned as a string) and compare to 50 GB.

- **Under 50 GB** → run with `oleander:oleander_query_lake` (DuckDB)
- **50 GB or above** → run as a Spark SQL job (see **Large tables** below)

A table can be large overall but small for a filtered query when it is
partitioned — always size the partitions the query actually touches,
not the whole table.

When joining multiple tables, route to Spark if any one table's relevant
read size is at or above 50 GB.

Tell the user which engine was chosen and the size that drove it.

Use `oleander:oleander_query_lake` for cheap SQL metadata reads
(`DESCRIBE`, `information_schema`) regardless of table size. Prefer
`oleander:oleander_read_table_metadata` when you also need the partition
spec.

## Small tables (under 50 GB)

1. Confirm schema if needed: `oleander:oleander_read_table_metadata` or
   `DESCRIBE <table>` via `oleander:oleander_query_lake`
2. Write and run the SQL via `oleander:oleander_query_lake`

## Large tables (50 GB and above)

1. Confirm schema if needed: `oleander:oleander_read_table_metadata` or
   `DESCRIBE <table>` via `oleander:oleander_query_lake`
2. Write standard Spark SQL only — no PySpark, no DataFrame API
3. Submit via `oleander:oleander_submit_spark_sql`

**Not supported in Spark SQL:**
- `QUALIFY` — rewrite as subquery with `WHERE`
- `EXCLUDE` in `SELECT` — list columns explicitly
- `TRY_CAST` — use `CAST` with `TRY` separately
- `strftime` — use `DATE_FORMAT(col, 'yyyy-MM-dd')`
- `STRING_AGG` — use `COLLECT_LIST` or `GROUP_CONCAT`