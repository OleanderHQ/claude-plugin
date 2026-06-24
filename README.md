# Oleander Claude Plugin

Query and analyze data in the oleander warehouse from Claude Code or Claude Cowork.

## Local testing

### Set environment variable

Add your API key to your shell configuration:

```bash
export OLEANDER_API_KEY="your-api-key"
```

### Run with `--plugin-dir`

From the parent of this repository:

```bash
claude --plugin-dir ./claude-plugin
```

Use `/reload-plugins` to pick up changes without restarting.

## Usage

Once the plugin is enabled, ask Claude to query your lakehouse data. For example:

- "Describe `oleander.default.flowers` — what columns are in the table?"
- "Show me 10 rows from `oleander.default.flowers`"
- "How many rows are in `oleander.default.flowers`, and what are the distinct values in each column?"

## Configuration

MCP connection is defined in [`.mcp.json`](.mcp.json):

```json
{
  "headers": {
    "Authorization": "Bearer ${OLEANDER_API_KEY}"
  }
}
```

The API key is read from the `OLEANDER_API_KEY` environment variable at startup — it is never stored in the plugin files.
