---
name: autodesk-product-help-mcp
description: "Connect to Autodesk's official Product Help MCP server for documentation search across 150+ products (AutoCAD, Civil 3D, Revit, etc.)"
version: 1.0.0
author: UB
license: MIT
platforms: [windows, macos, linux]
metadata:
  hermes:
    tags: [autodesk, mcp, documentation, autocad, civil3d, revit]
    related_skills: [native-mcp]
---

# Autodesk Product Help MCP

Connect your Hermes agent to Autodesk's official, publicly hosted MCP server for real-time access to product documentation across 150+ Autodesk products.

## Overview

Autodesk hosts a public MCP server that provides two tools:

- **`get_available_products`** — Lists all supported Autodesk products with their product codes and release versions
- **`search_help_content`** — Searches official Autodesk documentation (knowledge base + product help) with keyword, product, version, and locale scoping

No API key or authentication required.

## Configuration

### Via Hermes CLI (Recommended)

```bash
printf "n\ny\n" | hermes mcp add autodesk-product-help \
  --url "https://developer.api.autodesk.com/knowledge/public/v1/mcp"
```

Then in an active session run `/reload-mcp` to inject the tools immediately, or restart Hermes.

### Via Hermes Desktop

Open **Settings > MCP Servers**, add a new server with name `autodesk-product-help` and URL `https://developer.api.autodesk.com/knowledge/public/v1/mcp`.

## Available Tools

After configuration, two tools appear with the `mcp_autodesk_product_help_` prefix:

### `mcp_autodesk_product_help_get_available_products`

No parameters. Returns the full product catalog with `product_name`, `product_code`, and `release_codes` per product.

Use this first to find the exact string to pass to `search_help_content`.

### `mcp_autodesk_product_help_search_help_content`

Parameters:

| Parameter | Required | Description |
|-----------|----------|-------------|
| `query` | Yes | Keyword-rich search text |
| `locale` | Yes | e.g. `en_US`, `zh_CN`, `ja_JP` |
| `product_name` | No | e.g. `AutoCAD`, `Civil 3D`, `Revit` |
| `product_code` | No | e.g. `ACD`, `CIV3D`, `RVT` (preferred over product_name) |
| `release_code` | No | e.g. `2026` |
| `sources` | No | `sfdc_articles` (support), `product_documentation` (help) |
| `max_results` | No | Default 5 |

Note: Use singular parameter names (`product_code`, not `product_codes`). Invalid parameters cause `INVALID_ARGUMENTS` errors.

## Basic Usage Examples

Once configured, just describe what you need in natural language. The agent will call the MCP tools automatically.

### Example 1: Look up a command

> Find me the documentation for AutoCAD's `TEXTTOFRONT` command in 2026

Agent internally calls: `search_help_content(query="TEXTTOFRONT command", product_name="AutoCAD", release_code="2026", locale="en_US")`

### Example 2: Troubleshoot an error

> What does "Error 1603" mean during AutoCAD 2025 install?

Agent internally calls: `search_help_content(query="Error 1603 AutoCAD install", product_name="AutoCAD", release_code="2025", locale="en_US")`

### Example 3: Civil 3D reference text

> How do I set up Reference Text in a Civil 3D label style?

Agent internally calls: `search_help_content(query="Reference Text label style Civil 3D", product_name="Civil 3D", locale="en_US")`

### Example 4: Check available releases

> What releases are available for Robot Structural Analysis?

Agent internally calls: `get_available_products()` and filters for `product_name` matching "Robot Structural".

## Locale Reference

The MCP uses full locale codes (not short codes like `ENU`):

| Language | Locale |
|----------|--------|
| English (US) | `en_US` |
| Chinese (Simplified) | `zh_CN` |
| Japanese | `ja_JP` |
| German | `de_DE` |
| French | `fr_FR` |
| Spanish | `es_ES` |
| Korean | `ko_KR` |

Full list: `cs_CZ`, `da_DK`, `de_AT`, `de_CH`, `de_DE`, `en_AE`, `en_AU`, `en_CA`, `en_GB`, `en_HK`, `en_IN`, `en_MY`, `en_NL`, `en_NZ`, `en_SG`, `en_US`, `en_ZA`, `es_AR`, `es_ES`, `es_MX`, `fi_FI`, `fr_BE`, `fr_CA`, `fr_CH`, `fr_FR`, `hu_HU`, `it_CH`, `it_IT`, `ja_JP`, `ko_KR`, `nl_BE`, `nl_NL`, `no_NO`, `pl_PL`, `pt_BR`, `pt_PT`, `sv_SE`, `tr_TR`, `zh_CN`, `zh_TW`

## Common Product Reference

| Product | product_code | Supported Releases |
|---------|------|-------------------|
| AutoCAD | ACD | 2021–2027 |
| AutoCAD LT | ACDLT | 2021–2027 |
| Civil 3D | CIV3D | 2021–2027 |
| Revit | RVT | 2021–2027 |
| AutoCAD Architecture | ARCHDESK | 2021–2027 |
| AutoCAD MEP | BLDSYS | 2021–2027 |
| AutoCAD Plant 3D | PLNT3D | 2021–2027 |
| Inventor | INVNTOR | 2021–2027 |
| Robot Structural Analysis | RSAPRO | 2021–2027 |
| Navisworks Manage | NAVMAN | — |
| Fusion | FSN | — |

Call `get_available_products()` for the living catalog — product names and releases change over time.

## Pitfalls

1. **MCP not injected mid-session**: After adding the MCP server, run `/reload-mcp` in an active chat session to see the tools. Or restart Hermes.
2. **Wrong locale**: Use full locale codes (`en_US`), not short codes (`ENU`). The latter causes `INVALID_ARGUMENTS`.
3. **Plural parameter names**: `product_codes`, `release_codes`, `page_size` are not valid. Use singular `product_code`, `release_code`.
4. **Product name mismatch**: The `product_name` must match exactly what `get_available_products` returns (e.g. `Civil 3D`, not `Civil3D` or `C3D`).
5. **Server is pure-tool**: No resources or prompts — only tool calls work. `list_resources` and `list_prompts` both return empty.

## Verification

```bash
# Check the server is connected
hermes mcp list | grep autodesk

# In a session, verify tools are injected
# Type /tools and look for mcp_autodesk_product_help_*
```
