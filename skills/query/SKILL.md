---
description: Query the fully managed Iceberg lakehouse. Use when the user asks to analyze, aggregate, or explore tabular data already in the catalog.
---

# Query

Run SQL against tables in the oleander catalog (`oleander.default` by
default). Pick the execution engine from table size before running the
main query.

## Choose the engine

**Threshold:** 50 GB (`53,687,091,200` bytes).

For each Iceberg table the query reads, call `oleander_read_table_size`
with `catalog` `oleander` and `namespace` `default` unless the user
specifies otherwise. Parse `sizeBytes` (returned as a string) and compare
to the threshold.

- **Under 50 GB** — run SQL with `oleander_query_lake` (DuckDB).
- **50 GB or larger** — do not use `oleander_query_lake` for the main
  query. Write Spark-compatible SQL, wrap it in a PySpark script that
  runs `spark.sql(...)`, upload with `oleander_upload_spark_artifact`,
  and submit with `oleander_submit_spark_job`.

When the query joins multiple tables, check each one. Route to Spark if
any referenced table is at or above 50 GB.

Tell the user which engine you chose and the table size(s) that drove
the decision.

`oleander_read_table_size` is for size, record count, data file count,
partition size, and snapshot-specific size — not for row queries or
schema. Use `oleander_query_lake` for cheap metadata reads such as
`DESCRIBE <table>` or `information_schema` lookups regardless of
table size.

Optional `oleander_read_table_size` inputs: `snapshot_id` for a specific
Iceberg snapshot; `partition_filters` as `[{ key, value }]` when the
user asks about a partition's size.

When listing, discovering, or presenting catalog tables, use the format
in `skills/tables/SKILL.md`.

## DuckDB path (< 50 GB)

1. If unsure of the schema, run `DESCRIBE <table>` or inspect
   `information_schema` via `oleander_query_lake`.
2. Write SQL appropriate to the question — aggregation, filtering,
   joins.
3. Run it via `oleander_query_lake`.
4. Present the response using the format in **Present results** below.

## Spark path (>= 50 GB)

1. Confirm schema if needed (metadata-only queries are fine via
   `oleander_query_lake` or `oleander_read_table_metadata`).
2. Write Spark-compatible SQL for the question.
3. Wrap the SQL in a PySpark entrypoint that runs `spark.sql(...)` and
   materializes results (for example to an output Iceberg table the
   user approves).
4. Call `oleander_list_spark_artifacts` before upload — skip upload if
   a matching artifact is already `READY`.
5. Upload with `oleander_upload_spark_artifact` when needed, then submit
   with `oleander_submit_spark_job` (`confirm: true`).
6. Present the response using the format in **Present results** below.

## Present results

After a successful query, lead with a short plain-language answer. Put
the SQL and raw rows in expandable blocks the user can open and copy.

1. One or two sentences with the insight — keep this outside the blocks.
2. SQL in a collapsible copy block:

<details>

```sql
<the executed query>
```

</details>

3. Full result set in a second collapsible copy block. Use CSV inside a
   fenced code block (not a markdown table) so copy works cleanly:

<details>
<summary>(<N> rows)</summary>

```csv
col1,col2,col3
val1,val2,val3
```

</details>

4. One short footer line — row count, engine used (DuckDB or Spark), and
   any limit note (for example, DuckDB `SELECT` results capped at 20
   rows).

For Spark jobs, include the submitted SQL in the first block and either
the output-table reference or post-job query results in the second.

## Notes

DuckDB is the default for interactive analysis on smaller tables. Spark
is for large-table workloads that would be slow or fail in DuckDB.
