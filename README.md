# Autodesk Product Help MCP

A [Hermes Agent](https://hermes-agent.nousresearch.com) skill to connect to Autodesk's official Product Help MCP server — providing real-time documentation search across AutoCAD, Civil 3D, Revit, Inventor, Fusion, and 150+ Autodesk products.

## Install

```bash
hermes skills install wyuebei-cloud/autodesk-product-help-mcp
```

Then add the MCP server to `~/.hermes/config.yaml`:

```yaml
mcp_servers:
  autodesk-product-help:
    enabled: true
    url: https://developer.api.autodesk.com/knowledge/public/v1/mcp
```

Restart Hermes or type `/reload-mcp` in a session.

## What it does

- **`get_available_products`** — Lists all supported Autodesk products and releases
- **`search_help_content`** — Searches official Autodesk documentation by keyword, product, version, and locale

No API key required. Public server hosted by Autodesk.

## Details

See [SKILL.md](./SKILL.md) for full usage, parameters, product reference tables, and common pitfalls.

## License

MIT
