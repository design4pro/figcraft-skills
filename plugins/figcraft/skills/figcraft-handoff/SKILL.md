---
name: figcraft-handoff
description: Audit FigCraft frames, read Developer Mode specs or code, and export design assets. Use for implementation handoff, visual review, or delivery of renders and asset files.
---

# FigCraft handoff

## Resolve the source

- Use the FigCraft server chosen for the task. Default to `figcraft-headless` for file-based work, accept a configured alternative such as `figcraft`, and use an attached server such as `figcraft-desktop` for the open editor. Keep document and layer IDs on the server that returned them. If the intended connection is unavailable, identify the missing connection before proceeding.
- Open an explicit input path with `open_file`, or locate a named open document with `doc.list` via `run_command`. Resolve any ambiguity or `opened.stale` before auditing or exporting. Use `inspect_document` to resolve the page and frame IDs, then `get_node` and `screenshot` for the requested frames. Pass the resolved `document` and explicit `id` or `ids` as supported. Use a focused subtree when an inspection is truncated.
- Keep source audits and one-off exports read-only in FigCraft. If the user requests persistent export settings or handoff status updates, discover the command parameters with `list_commands` and group related document edits in a `batch`. After a lost reply, check the affected layers and any requested output files before retrying.

## Inspect design details

Use `list_commands` to check the command schema, then call the relevant command via `run_command`.

- `inspect.properties` takes one layer `id` and reports layout, typography, paints, effects, and their associated styles or variables. Use the rendered frame to check hierarchy, spacing, clipping, or overflow and point findings to the affected layers.
- `inspect.code` takes one layer `id` and a supported `language`. Report the result as a generated snippet or a connected project component according to `connected`. Validate any implementation claims against the target codebase when available.
- `inspect.assets` takes frame `ids` and inventories assets by layer ID, kind, and export settings. Use this inventory to identify which files the handoff needs and reconcile them after export.

## Export and verify

- Use `export` for a one-off output with explicit layer IDs, format, and any requested raster scale or size. Multiple IDs can be composited into one image or exported as pages in a PDF. Use individual calls when separate frame files are required. Pass an absolute output `path` to write a file or omit it to receive MCP content for review.
- For a handoff covering a frame's assets, use `file.exportLayers` with `assets: true`, explicit frame `ids`, and an absolute output `dir`. Inspect the returned `files` against the asset inventory. Layer names containing slashes can create nested output folders. Use an unused output path or an empty export directory unless the user requested replacement.
- Verify that named files exist, have nonzero size, and match the intended dimensions or asset inventory. Inspect a render or preview when visual fidelity matters. Report the actual output paths, missing assets, and any warnings or visible differences. `.fig` exports apply to the whole document and can have compatibility warnings. Android vector exports can report approximated or omitted features.

For format-specific options, consult the [FigCraft MCP export reference](https://github.com/storytold/figcraft/blob/v0.18.1/docs/mcp.md#tools) and [Developer Mode reference](https://github.com/storytold/figcraft/blob/v0.18.1/docs/mcp.md#developer-mode).
