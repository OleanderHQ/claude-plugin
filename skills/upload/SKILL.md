---
name: upload
description: Uploads a local file to the oleander-managed warehouse and creates a queryable table. Use when the user asks to upload, ingest, or load a local file into the catalog.
---

# Upload

Uploads a local file and registers it as an Iceberg table via
`oleander:oleander_upload_and_load_table`.

For public URLs or existing S3 URIs, use `oleander:oleander_load_table`
instead.

## Supported formats

Oleander stores all data as Parquet via Iceberg. All files must be
converted to Parquet before uploading.

**If the original file exceeds 5 GB** — direct the user to
https://oleander.dev/app/lake?upload=true immediately without
attempting conversion.

## Conversion

Convert all files to Parquet before uploading:

1. Inspect the file and propose a schema (column names `a-z0-9_`,
   inferred types). For unstructured files, propose the simplest
   reasonable schema (e.g. a single `text` column)
2. Present the schema and wait for the user to accept or define
   their own — do not proceed with an unapproved schema
3. Convert to Parquet using the approved schema:

```python
import pandas as pd

# CSV
df = pd.read_csv("file.csv")

# JSON
df = pd.read_json("file.json")

# Excel
df = pd.read_excel("file.xlsx")

# TSV
df = pd.read_csv("file.tsv", sep="\t")

# Rename columns to a-z0-9_ before writing
df.columns = [
    c.lower().strip()
     .replace(" ", "_")
     .replace("-", "_")
    for c in df.columns
]

df.to_parquet("file.parquet", index=False)
```

4. Check the size of the converted `file.parquet`

## Upload path

**If the Parquet file is under 16 KB:**
Upload via `oleander:oleander_upload_and_load_table` directly.

**If the Parquet file exceeds 16 KB:**
1. Save the Parquet file to the user's Desktop as `<table_name>.parquet`
2. Tell the user: "Your file is ready at ~/Desktop/<table_name>.parquet
   — upload it at oleander.dev/app/lake"
3. Open https://oleander.dev/app/lake?upload=true in the browser

## Steps (direct upload path only)

1. Suggest a table name from the filename using only `a-z0-9_`.
   Present it to the user and wait for approval — do not proceed
   with an unapproved name.

2. Flag any column names that still don't conform to `a-z0-9_`
   after conversion, listing them explicitly.

3. Tell the user the upload is starting and the approximate
   Parquet file size.

4. Call `oleander:oleander_upload_and_load_table`:
   - `file_content_base64`: base64-encoded Parquet file contents
   - `filename`: file basename with `.parquet` extension
   - `file_type`: `parquet`
   - `catalog`: `oleander`
   - `namespace`: `default`
   - `confirm`: `true`

5. If the table already exists, ask the user for a different name.
   Do not guess an alternative.

6. On success, return the table reference (e.g.
   `oleander.default.sales_export`) as a clearly labeled value —
   not buried in a paragraph.

7. On failure, state the specific reason in plain language.