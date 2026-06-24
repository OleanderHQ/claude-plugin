# [`oleander`](https://oleander.dev/) Claude Plugin

Query and analyze data in the oleander warehouse from Claude Code or Claude Cowork.

## Local testing

First, add your API key:

```bash
export OLEANDER_API_KEY="your-api-key"
```

### Claude Code

From the parent of this repository:

```bash
claude --plugin-dir ./claude-plugin
```

> [!TIP]
> Use `/reload-plugins` to pick up changes without restarting.

### Claude Cowork

Zip this repo:

```bash
cd claude-plugin
zip -r oleander .
```

In Claude desktop app, open **Customize** → **Add plugin** → **Upload plugind** and select `oleander.zip`.

After each change, re-run the zip command and upload the new file to pick up updates.

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
