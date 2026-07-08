# [`oleander`](https://oleander.dev/) Claude Plugin

Query and analyze data in the oleander warehouse from Claude Code or Claude Cowork.

## Getting started

1. **Sign up** at [oleander.dev](https://oleander.dev) and create an API key in your account settings.

2. **Set your API key** in your environment:

   ```bash
   export OLEANDER_API_KEY="your-api-key"
   ```

3. **Install the plugin** in Claude Code:

   ```shell
   /plugin marketplace add anthropics/claude-plugins-community
   /plugin install oleander@claude-community
   ```

4. **Ask Claude about your data**, for example:

   - "Describe `oleander.default.flowers` — what columns are in the table?"
   - "Show me 10 rows from `oleander.default.flowers`"
   - "How many rows are in `oleander.default.flowers`, and what are the distinct values in each column?"

Claude uses the oleander MCP server and plugin skills to explore your lake catalog and run SQL.

## Local testing

### Install from this marketplace

```shell
/plugin marketplace add OleanderHQ/claude-plugin
/plugin install oleander@oleander
```

### Claude Code (`--plugin-dir`)

Load this repository directly while developing:

```bash
claude --plugin-dir .
```

> [!TIP]
> Use `/reload-plugins` to pick up changes without restarting.

### Claude Cowork

Zip this repository:

```bash
zip -r oleander.zip .
```

In the Claude desktop app, open **Customize** → **Add plugin** → **Upload plugin** and select `oleander.zip`.

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
