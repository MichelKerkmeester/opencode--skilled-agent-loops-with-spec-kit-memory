# sk-vision runtime

The package contains the shared sk-vision execution core and two transports:

- `dist/plugin.js` for the native OpenCode plugin adapter.
- `dist/mcp-server.js` for MCP stdio hosts such as Cursor and Devin.

Both transports use the same 13 definitions from `src/opencode/tools.ts`, the same `PhotonProvider`, and the same `RuntimeClient` NDJSON channel to `python/runtime.py`.

## MCP transport

Build and launch the stable distribution entry:

```bash
bun run build
bun dist/mcp-server.js
```

For source development, use:

```bash
bun run mcp
```

Cursor and Devin MCP configuration uses `command: "bun"` with the absolute built entry as its sole argument:

```json
{
  "command": "bun",
  "args": ["/absolute/path/to/checkout/.opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js"]
}
```

The server advertises `sk_vision_inspect`, `sk_vision_detect`, `sk_vision_point`, `sk_vision_ocr`, `sk_vision_status`, `sk_vision_segment`, `sk_vision_metadata`, `sk_vision_crop`, `sk_vision_zoom`, `sk_vision_colors`, `sk_vision_diff`, `sk_vision_annotate`, and `sk_vision_reverse`.

Environment variables such as `SK_VISION_PYTHON`, `SK_VISION_MODEL`, `SK_VISION_CACHE_DIR`, and `SK_VISION_DISABLE_AUTO_PROVISION` are inherited by the server and its Python child process.

## Verification

```bash
bun run typecheck
bun run build && bun test
```

The MCP integration test starts the stdio server with the official SDK client, expects 13 tools, and calls `sk_vision_status` without loading model weights.
