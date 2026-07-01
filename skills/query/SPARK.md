# Spark SQL

Write standard Spark SQL only — no PySpark, no DataFrame API.

**Not supported in Spark SQL:**
- `QUALIFY` — rewrite as subquery with `WHERE`
- `EXCLUDE` in `SELECT` — list columns explicitly
- `TRY_CAST` — use `CAST` with `TRY` separately
- `strftime` — use `DATE_FORMAT(col, 'yyyy-MM-dd')`
- `STRING_AGG` — use `COLLECT_LIST` or `GROUP_CONCAT`

Submit via `oleander:oleander_submit_spark_sql` with `confirm: true`.

Present results — see [RESULTS.md](RESULTS.md).