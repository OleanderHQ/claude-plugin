---
description: Query the fully managed Iceberg lakehouse via DuckDB. Use when the user asks to analyze, aggregate, or explore tabular data already in the catalog.
---

#  Query

Use the `oleander_query_lake` MCP tool to run SQL against tables in the
oleander catalog (`oleander.default` by default). Queries execute via DuckDB.

1. If unsure of the schema, run `DESCRIBE <table>` or inspect
   `information_schema` via `oleander_query_lake`.
2. Write SQL appropriate to the question — aggregation, filtering, joins.
3. Run it via `oleander_query_lake`.
4. Summarize the result in plain language; don't just dump raw rows unless asked.

Today, all queries run through DuckDB against the catalog. Very large
datasets may be slow or fail until additional engines (Spark, and later
DataFusion/Polars) are supported.
