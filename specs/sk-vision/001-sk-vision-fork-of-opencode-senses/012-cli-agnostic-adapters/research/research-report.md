# CLI-Agnostic Adapter Research — sk-vision across Pi, OpenCode, Cursor, Devin

> Findings produced by a GLM 5.2 max (cli-devin) deep-research iteration on the patched runtime. The
> loop did not persist its own `research.md` (a separate `salvage_miss` issue), so these findings are
> salvaged from the leaf's run log and reconciled against the shipped code.

## 1. Question

How do we make the sk-vision JSON-RPC vision runtime (13 tools) usable across four coding-agent CLIs —
Pi, OpenCode, Cursor, Devin — with the least duplication?

## 2. Per-CLI host model (confirmed)

| CLI | Tool/extension model | sk-vision integration |
|-----|----------------------|-----------------------|
| **OpenCode** | In-process JS plugin, auto-discovered under `.opencode/plugins/*.js` | **Exists** — `.opencode/plugins/sk-vision.js` re-exports the vision-runtime plugin |
| **Pi** | In-process `ExtensionFactory` (`.pi/extensions/*.ts`), `pi.registerTool` + input hooks | **Exists** — `.pi/extensions/sk-vision.ts` symlink to `pi/sk-vision.ts` |
| **Cursor** | **MCP-only** via `.cursor/mcp.json` (`mcpServers`: command/args/env for stdio, `url` for HTTP/SSE/Streamable HTTP). No in-process JS plugin API. | **Missing** — needs an MCP server |
| **Devin** | **MCP-only** via `.devin/mcp_config.json` / `~/.config/devin/mcp_config.json` (`mcpServers`; tools namespaced `mcp__<server>__<tool>`). No in-process tool plugin API. | **Missing** — needs an MCP server |

## 3. Key finding

**The shared core (`RuntimeClient` / `PhotonProvider` / `contextBuilder`) is already CLI-agnostic —
only the per-CLI registration shim differs.** OpenCode and Pi each attach the core through their
native in-process plugin surface. Cursor and Devin have **no** in-process plugin surface; both expose
tools **exclusively through MCP**.

## 4. Design conclusion

Add a single **MCP-server transport** that wraps the vision runtime and exposes the 13 tools over MCP
(stdio). This is the **universal fallback** for the two CLIs that lack a native plugin API:

- **Cursor** attaches it via a `.cursor/mcp.json` `mcpServers` entry.
- **Devin** attaches it via a `.devin/mcp_config.json` `mcpServers` entry.
- **OpenCode + Pi** keep their existing native adapters (lower latency, input hooks) — the MCP server
  is additive, not a replacement.
- The shared JSON-RPC/NDJSON core is **untouched**; the MCP server is one more thin shim over it.

## 5. Downstream coverage gaps (for the build)

- **feature-catalog**: add a `host-adapters` entry for the MCP transport + Cursor + Devin.
- **manual-testing-playbook**: add per-CLI scenarios (Cursor via `.cursor/mcp.json`, Devin via
  `.devin/mcp_config.json`, and the MCP server standalone).

## 6. Proposed phases (012 subtree)

1. `001-mcp-server-transport` — the MCP server wrapping the runtime (the core new component).
2. `002-cursor-adapter` — `.cursor/mcp.json` config + docs.
3. `003-devin-adapter` — `.devin/mcp_config.json` config + docs.
4. `004-catalog-and-playbook` — multi-CLI feature-catalog + manual-testing-playbook coverage.
