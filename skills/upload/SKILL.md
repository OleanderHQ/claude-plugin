---
description: Upload a local file to oleander staging and create an Iceberg table. Use when the user asks to upload a local file and load it into the catalog.
---

# Upload

Use the `oleander_upload_and_load_table` MCP tool to upload a local dataset
file and create an Iceberg table from it.

If the user only provides a public URL or existing S3 URI, use
`oleander_load_table` instead — not this tool.

**Supported formats**: `parquet`, `csv`, `json` only. If the file is a
different format (Excel, TSV, etc.), convert it to one of these first —
do not attempt to upload it as-is.

**Size limit**: 5 GB. If the local file exceeds this, tell the user it's
too large for this tool rather than attempting the upload and letting it
fail partway through.

1. Suggest a table name derived from the filename, using only lowercase
   letters, numbers, and underscores (`a-z0-9_`) — strip spaces, dashes,
   capitals, and special characters. Show the suggested name to the user
   and have them approve it or provide their own before proceeding. Do
   not proceed with an unapproved name.
2. Apply the same `a-z0-9_` convention when checking or referring to
   column names from the source file; flag any that don't conform so the
   user is aware, since the underlying table may rename or reject them.
3. Read the local file and pass its contents as base64 in
   `file_content_base64`.
4. Set `filename` to the file basename, `file_type` to one of `parquet`,
   `csv`, or `json`, and `confirm` to `true`.
5. Use `catalog` `oleander` and `namespace` `default` unless the user
   specifies otherwise.
6. Call `oleander_upload_and_load_table`. The target table must not
   already exist — if it does, stop and ask the user whether to choose a
   new name, rather than retrying with a guessed alternative.
7. Return the staged destination and created table response to the user.

Optional: set `content_type` when the upload content type matters; the
staging API applies defaults when it is omitted.
