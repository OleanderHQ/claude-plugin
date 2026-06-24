# [`oleander`](https://oleander.dev/) Claude Plugin

Query and analyze data in the oleander warehouse from Claude Code or Claude Cowork.

## Local testing

### Run with `--plugin-dir`

First, add your API key:

```bash
export OLEANDER_API_KEY="your-api-key"
```

Then:

```bash
claude --plugin-dir ./claude-plugin
```

> [!TIP]
> Use `/reload-plugins` to pick up changes without restarting.

## Configuration

MCP connection is defined in [`.mcp.json`](.mcp.json):

```json
{
  "headers": {
    "Authorization": "Bearer ${OLEANDER_API_KEY}"
  }
}
```

## Usage

You can now query and analyze your data:

- "Describe `oleander.default.flowers` — what columns are in the table?"
- "Show me 10 rows from `oleander.default.flowers`"
- "How many rows are in `oleander.default.flowers`, and what are the distinct values in each column?"