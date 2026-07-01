# Results

Do not narrate tool calls in the response.

## Row limits

Display a maximum of 20 rows by default. If the user asks for more:
- Say "fetching more rows..."
- Re-run via `oleander:oleander_query_lake` and return the expanded
  result set.

If the displayed rows are fewer than the total result, note it in the
footer: "showing 20 of 847 rows — ask for more to fetch additional
results."

## Structure

1. **Insight** — one or two sentences

2. **SQL** — always collapsible:

<details>
<summary>SQL</summary>

```sql
SELECT ...
```

</details>

3. **Results** — always collapsible, summary in `<summary>`:

<details>
<summary>312ms · 5 rows · 67 KB scanned · DuckDB</summary>

```csv
col1,col2
val1,val2
```

</details>

Summary fields (omit missing): execution time · row count · bytes
scanned · engine (DuckDB or Spark)

4. **Footer** — caveats only (e.g. "showing 20 of 847 rows — ask for
more to fetch additional results"). Omit if none.

## Formatting rules

- Opening fence, body, and closing fence each on their own line
- Blank line before and after every fence and every `<details>` tag

## Example

Downtown Scoop leads at $514K — more than the other four locations combined.

<details>
<summary>SQL</summary>

```sql
SELECT l.name, ROUND(SUM(o.revenue), 2) AS total_revenue
FROM oleander.default.daily_orders o
JOIN oleander.default.locations l ON o.location_id = l.location_id
GROUP BY l.name
ORDER BY total_revenue DESC
LIMIT 20
```

</details>

<details>
<summary>312ms · 5 rows · 67 KB scanned · DuckDB</summary>

```csv
location,total_revenue
Downtown Scoop,514224.50
South Lamar Scoop,345308.00
```

</details>