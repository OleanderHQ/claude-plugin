---
name: query
description: Runs SQL against the oleander-managed warehouse using the right engine for the dataset size. Use when the user asks to query, analyze, aggregate, filter, or explore tabular data already in the catalog.
---

# Query

Check dataset size first, then route to the correct engine.

## Route by size

For each table the query reads, call `oleander:oleander_read_table_size`
with `catalog: oleander`, `namespace: default`. Parse `sizeBytes`.

- **Under 50 GB** → run with `oleander:oleander_query_lake` (DuckDB)
- **50 GB or above** → run as a Spark SQL job — see [SPARK.md](SPARK.md)

When joining multiple tables, route to Spark if any one table is at or
above 50 GB.

Use `oleander:oleander_query_lake` for schema reads (`DESCRIBE`,
`information_schema`) regardless of table size.

Tell the user which engine was chosen and the table size that drove it.

## DuckDB path

1. Confirm schema if needed: `DESCRIBE <table>` via
   `oleander:oleander_query_lake`
2. Write and run the SQL via `oleander:oleander_query_lake`
3. Present results — see [RESULTS.md](RESULTS.md)

## Spark path

See [SPARK.md](SPARK.md).